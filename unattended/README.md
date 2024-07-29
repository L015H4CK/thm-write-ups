# THM Unattended Write-Up

This is a write-up for the [Unattended Room](https://tryhackme.com/r/room/unattended) on [TryHackMe](https://tryhackme.com/).
Make sure to check out TryHackMe and learn more about them on their website.

**I encourage everyone to perform the given tasks themselves**, but in the past I found write-ups useful myself in case I got stuck or my answer was not in the right format.

Before starting with the tasks make sure to start the attached virtual machine by click the "Start Machine" button.

# Task 1 - Introduction
Run the Registry Explorer located in the tools directory or via task bar as stated in the introduction.
For me, it really took a long time - so grab a cup of coffee or a glass of water while it starts.

# Task 2 - Windows Forenics review
Read the Registry Forensics Cheatsheet as a refresher while the Registry Explorer is starting up.
If you have not completed the mentioned rooms before, you should do this now.

# Task 3 - Snooping around
After the Registry Explorer is started, load the hive located in `Desktop/kape-results/C/Users/THM-RFedora/NTUSER.DAT`.
In the following dialogues, I first clicked "No" and then "Yes" to load the hive.

## What file type was searched for using the search bar in Windows Explorer?
Check the values in `Software/Microsoft/Windows/CurrentVersion/Explorer/WordWheelQuery`.

## What top-secret keyword was searched for using the search bar in Windows Explorer?
Again, check the values in `Software/Microsoft/Windows/CurrentVersion/Explorer/WordWheelQuery`.

# Task 4 - Can't simply open it
Start the Autopsy Tool from the tools directory.
Click "New case", enter a case name and select a base directory (e.g. the documents directory).
Click "Next", give the case a numer and click "Finish".
Now, select "Generaten new host (...)", "Logical Files", "Add" -> "Desktop/kape-results/C" and "Recent Activity".
Wait for Autopsy to finish the analysis before you continue.

## What is the name of the downloaded file to the Downloads folder?
Check the `Web Downloads` artifacts between 12:05 and 12:45 on 2022-11-19.
There is an exe that looks suspicious.

## When was the file from the previous question downloaded? (YYYY-MM-DD HH:MM:SS UTC)
The exact timestamp is given in the `Data Accessed` column of the above artifact.

## Thanks to the previously downloaded file, a PNG file was opened. When was this file opened? (YYYY-MM-DD HH:MM:SS)
Switch back to the Registry Explorer and look into `Software/Microsoft/Windows/CurrentVersion/Explorer/RecentDocs/.png`.
The `Last write timestamp` column in the left section contains the answer.

# Task 5 - Sending it outside

## A text file was created in the Desktop folder. How many times was this file opened?
Open a command prompt, move to the `C:\Tools\` directory and run `JLECmd -d C:\Users\THM-RFedora\desktop\kape-results\C\Users\THM-RFedora\`.
Check the output for a text file called `launchcode.txt` on the desktop.
The answer is the `Interaction count`.

## When was the text file from the previous question last modified? (MM/DD/YYYY HH:MM)
Switch back to the Registry Explorer and look into `Software/Microsoft/Windows/CurrentVersion/Explorer/RecentDocs/.txt`.
The `Last write timestamp` column in the left section contains the answer.

## The contents of the file were exfiltrated to pastebin.com. What is the generated URL of the exfiltrated data?
Check `Web History` artifacts back in Autopsy and search for `pastebin`.

## What is the string that was copied to the pastebin URL?
Visit the pastebin URL and find the string there.
