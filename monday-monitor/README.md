# THM Monday Monitor Write-Up

This is a write-up for the [Monday Monitor Room](https://tryhackme.com/r/room/mondaymonitor) on [TryHackMe](https://tryhackme.com/).
Make sure to check out TryHackMe and learn more about them on their website.

**I encourage everyone to perform the given tasks themselves**, but in the past I found write-ups useful myself in case I got stuck or my answer was not in the right format.

Before starting with the task(s) make sure to start the attached virtual machine by click the "Start Machine" button.
When the machine is fully loaded, access the Wazuh Dashboard using your browser using the provided URL and credentials.
As stated in the introduction, navigate to the Security events module and use the saved query `Monday_Monitor` by clicking on the save icon left of the search field to access the logs.
Next, change the timeframe to `Apr 29, 2024 @ 12:00:00.000`-`Apr 29, 2024 @ 20:00:00.000` to view the events.

# Task 1 - Navigate Through the Endpoint Logs

## Initial access was established using a downloaded file. What is the file name saved on the host?
This was the hardest question of all for me and to be honest I solved it last.
First, I was looking at FileCreation events (EventID: 11) but did not find the file I was searching for.
Network connections (EventID: 3) did not return anything either.

To find relevant events, I switched from a DQL filter to a Lucene filter (switch on the right side of the search field) so that I can use a regex.
Then I tried different regexes for `data.win.eventdata.commandLine` without results.
Finally, after trying many filters, the filter `data.win.eventdata.commandLine: /.*url.*/` returned the relevant events, containing the answer.
Hint: the file extension is `.xlsm`.

## What is the full command run to create a scheduled task?
Since we know that a scheduled task is created using `schtasks.exe`, we can filter for the field `data.win.eventdata.image` for this value.
Then we take the first matching event and look at the parent command line that started the schtasks.exe process.
The complete command line, including the initial `cmd.exe` and the escape characters, is the answer.

## What time is the scheduled task meant to run?
Check the `/st (<Starttime>)` parameter in the above command line.

## What was encoded?
Check the value that was written to the registry using the `/d` parameter in the command line above.
This value can be decoded using CyberChef.

## What password was set for the new user account?
To get the new password, I first filtered for events with the `data.win.eventdata.targetUserName` field since I wanted to find relevant events.
I noticed that around `14:14:13` and `14:14:35` two events with description `User account changed.` were registered. 
Unfortunately, these events do not give us more information, but looking at events right before these events (disable the filter again) gives us the needed information.
As a further hint: Search for events with `net.exe` usage or `data.win.eventdata.description: Net Command`.
I guess there are more direct ways to get this answer, but this was the path I found and took.

## What is the name of the .exe that was used to dump credentials?
To find relevant events, I switched from a DQL filter to a Lucene filter (switch on the right side of the search field) so that I can use a regex.
The specified filter searches for command lines that contain the string `dump`: `data.win.eventdata.commandLine: /.*dump.*/`.
This filter returns 13 events in total and the relevant ones are really obvious, containing the name of the searched .exe.

## Data was exfiltrated from the host. What was the flag that was part of the data?
Since we know that most (if not all flags) here at TryHackMe contain the string `THM`, I simply edited the above filter to search for this string instead of `dump`.
Fortunately, this filter returns a single event that also contains the searched flag.

If you find another (maybe more sophisticated) way please let me know.