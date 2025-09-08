# THM IR Timeline Analysis Write-Up

This is a write-up for the [IR Timeline Analysis Room](https://tryhackme.com/room/dfirtimelineanalysis) on [TryHackMe](https://tryhackme.com/).
Make sure to check out TryHackMe and learn more about them on their website.

**I encourage everyone to perform the given tasks themselves**, but in the past I found write-ups useful myself in case I got stuck or my answer was not in the right format.

# Task 2 - Basics of Timeline Analysis

## When security events are reviewed sequentially against time, what is this known as?
The answer is the topic of this room - timeline analysis.

## When a file is created, what timestamp tag would it have?
Possible timestamp tags are modified, accessed, changed, and **birth**.

## Converting event timestamps into UTC can be described as?
This is called **time synchronization**. 

# Task 3 - Artifact Acquisition & Processing 

## What specific data source provides detailed information regarding user interactions within a digital environment?
This information is found in the **file system metadata**.

# Task 4 - Timelines with Log2Timeline

## What option can be used with Log2Timeline to indicate the timeline output file?
The `--storage-file` option.

## Based on the Jimmy_timeline.plaso file, how many event sources are parsed after running pinfo.py against the storage file?
Run `pinfo Jimmy_timeline.plaso` and check event sources output.

## On the same timeline file, how many events were generated for the firefox_history? 
In the above output, look for firefox_history in the events generated per parser info.

## Based on the B4DM755 timeline, what time was the interview.txt file created? (hh:mm:ss)
Generate .csv file from the B4DM755 timeline (`psort.py -w output.csv b4dm755.plaso`).
Run `grep "interview" output.csv` and check output for the creation date.

# Task 5 - Timeline Analysis with Timesketch

For this task, we use the Timesketch instance running on port 8080 of the virtual machine and select the "Jimmy Supertimeline".

## How many data types were in the Jimmy Supertimeline sketch?
Check the `Data Types` in the sidebar.

## How many entries were in the EVTX Gap Analysis under the Jimmy Supertimeline?
Run the analzyer EVTX Gap from the sidebar and check the analysis results in the generated story (under `Stories` in sidebar).
You might have to refresh the site for the results to be shown.

## Which search engine did Jimmy Wilson use to search for "how to disappear without a trace?"
Use the "Browser Search" in the `Saved Searches` and add `url:"*disappear*"` to find the relevant events.

## What is the path of the program that was called to initiate Microsoft Antimalware Service?
Visit the "Windows services" graph (sidebar: `Graphs`), look for the "Microsoft Antimalware Service" and click it.
The answer is in the message field.

# Task 6 - Timeline Analysis Practical

## How many event sources were identified?
Run `pinfo.py Timeline_Challenge.plaso` and check output for event sources.

## How many events were generated from the dpkg parser?
In the generated output check for `dpkg` in events generated per parser info.

## How many total tags were set?
For this question, we use `tag_linux.txt` in `/usr/share/plaso/`.
Run: `psort.py --analysis tagging --tagging-file=/usr/share/plaso/tag_linux.txt Timeline_Challenge.placo -w output.csv` and check the output.

## What is the highest tagged element?
To be fair, I struggled with this question because I did not understand what was asked for.
In case you need a direction for this: The questions asks for the name of the tag that was tagged the most.

Run `cut -d ',' output.csv -f 8 | sort | uniq -c` (or similar commands) to find the answer.

## Under which username does the cronjob that executes app.py run?
Visit Timesketch and search for `message: "*app.py*"`.
The answer can be found in the found events.

## What is the hash of the successful SSH login with the PID 1669?
Search for `pid:"1669" message:"*successful login*"` and you find the answer in the found event.
