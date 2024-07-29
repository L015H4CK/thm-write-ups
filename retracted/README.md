# THM Retracted Write-Up

This is a write-up for the [Retracted Room](https://tryhackme.com/r/room/retracted) on [TryHackMe](https://tryhackme.com/).
Make sure to check out TryHackMe and learn more about them on their website.

**I encourage everyone to perform the given tasks themselves**, but in the past I found write-ups useful myself in case I got stuck or my answer was not in the right format.

Before starting with the tasks make sure to start the attached virtual machine by click the "Start Machine" button.

# Task 1 - Introduction
"I'll handle it, Mom."

# Task 2 - The Message

## What is the full path of the text file containing the "message"?
Well, look at this desktop...
I guess at least for now, we can ignore all the "Newsletter" and "Fundraiser" files.
One file does not look like the others - `SOPHIE.txt`.
Right-click and select properties to see the location of the file.
Combine location and name and you get the full path.

## What program was used to create the text file?
Either we can guess or we can use the Windows Event Viewer.
In the event viewer I created a new custom view that uses Sysmon as a log source (`By Log - Applications and Services Logs/Microsoft-Windows-Sysmon/Operational`).
Now we can use `Find...` to search for the text file, revealing an event with Event ID 1 that contains the answer.

## What is the time of execution of the process that created the text file? Timezone UTC (Format YYYY-MM-DD hh:mm:ss)
I found two events that accessed the text file.
The timestamp of the second event, i.e. the event that came first in time, was accepted as answer.

# Task 3 - Something wrong

## What is the filename of this "installer"? (Including the file extension)
To answer this, I looked into events right before the time of the text file creation.
One file with a very suspicious name was executed at `2024-01-08 02:15:00 PM`.

## What is the download location of this installer?
The location is given in the event we investigated above.

## The installer encrypts files and then adds a file extension to the end of the file name. What is this file extension?
Check File Creation events right after the "installer" was executed.
There are several events that resulted from this execution.
Check the `TargetFilename` field value to find the extension.

## The installer reached out to an IP. What is this IP?
Search for `Network connection detected` events, i.e. Event ID 3, right after the "installer" execution.
The IP is given in the `DestinationIp` field.

# Task 4 - Back to Normal

## The threat actor logged in via RDP right after the “installer” was downloaded. What is the source IP?
Again, check Event ID 3 events right after the execution.
The threat actor logged in around `02:19:22 PM` - creating two events.
The answer is in the `SourceIp` field.

## This other person downloaded a file and ran it. When was this file run? Timezone UTC (Format YYYY-MM-DD hh:mm:ss)
Check Process Creation events (Event ID 1) after the RDP connection.
The answer is given in the event around `02:24 PM` where `decryptor.exe` was run.
Hint: Remember to give the answer in 24h format, e.g. `02:24 PM` is `14:24`.

# Task 5 - Doesn't Make Sense

Instead of given answers to the questions, the order of events is given.
Reading the dialogues in the tasks again helps to find the correct order.

1. Download
2. Encryption
3. Reach out for help
4. Someone else logged in
5. Decryption
6. Note creation
7. We arrive

# Task 6 - Conclusion

Well, if this isn't a happy end...