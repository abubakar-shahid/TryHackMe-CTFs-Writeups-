# DeadEnd

## Challenge Information
- **Challenge Name**: DeadEnd
- **Category**: Digital Forensics
- **Difficulty Level**: Hard

## Challenge Description
There are two files given. The .mem file is the dump of RAM of Windows, while the .sys is the dump of virtual memory. Let's use volatility3, as given in the attackbox, and figure out the answers to the required tasks!

## Tools Used
- Volatility 3
- FTK Imager
- Windows Event Viewer
- Registry Explorer

## Memory

1. Since, we are asked for the identification of the suspicious activity in the memory image, lets run the `pslist` command on `memdump.mem` file using the command `../volatility3/vol.py -f memdump.mem windows.pslist` to check the processes running, along with their relations with their parent processes. Also, run the command `../volatility3/vol.py -f memdump.mem windows.pslist` to analyze the details of commands executed by each process. Here, we observe that the `svchost.exe` is not associated with its parent process. Rather, it is linked with `powershell.exe`. This makes this file suspicious as it is ran through powershell with some command line arguments `-e cmd.exe 10.14.74.53 6996` which most probably indicates the connection with some remote user. Hence, this is the malicious process with the complete path `C:\Tools\svchost.exe`. Also observe that this file is located in `C:\Tools\` directory, while this file is found in `C:\Windows\System32\`.

2. Since it is given in the question that the process shares the parent with the process identified in the previous task, that required task id `notepad.exe`, and the full path to the txt file is `C:\Users\Bobby\Documents\tmp\part2.txt`. This file may contain any code that is involved in remote code execution.

## Disk

1. Open the FTK Imager in the new windows machine. Open the `Robert.dd` in the FTK. Click on the image loaded to expand it. Notice a root folder. Expand it and you will see `Users` and `Tools` directories. Right click on both one by one and export objects. We are using these two folders because these are the directories that contained the malicious files in the previous tasks. Now go to the Users, here is the directory of `Bobby`. Expand it and go to `Documents` and then `tmp`. Here are the two files that we were in the search of. This is the same path that we submitted in the previous task. Now open the `connector.ps1`. Notice that it was created using the file `svchost.exe`, which is present in the `C:\Tools\` directory. Hence, go this directory in the FTK and click on the exe file. It will show some files. One file contains a single word that seems to be some name, while the other refers to some path. The path contains a keyword `Autoconnector`. Search for this keyword in the exported directory. It will give an exe file `Autoconnector.exe`. This is the required artifact, with full path `C:\Tools\windows-networking-tools-master\windows-networking-tools-master\LatestBuilds\x64\Autoconnector.exe`.

2. Remember to export all the registry files from the above directories as well as the windows directory. Open all the registry files in the registry explorer. Search for the keyword `autoconnector.exe` using ctrl+F. Some paths will be list down, in which there is a path with a keyword `bam`. This is the correct path as an evidence of the malicious executable `HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Services\bam\State\UserSettings\S-1-5-21-1966530601-3185510712-10604624-1008`.

3. Open the `path2.txt` that we discovered. The content of this file is the answer `faDB3XzJfcDF2T1R9`.

4. In the text file path2.txt, we actually got the second part of the flag. So, we have to find the first part now. Since we are given a disk image, we should now analyze the logs of this image. Let's go to the directory `C:\Windows\System32\winevt\Logs\` which actually contains the windows event files. Since we are playing with a powershell script, so lets find the log file for the keyword powershell. But before that, export all the logs present in the log folder so that we can have access to the log files in real. Now, there is a powershell operational log file which we needed. Open the file in the Windows Event Viewer. Search for the keyword `connector.ps1` which was our powershell script. But no success. To find the first flag, only remaining big actual executables. So, export the Autoconnector.exe file from the directory that we discovered in the previous task. Double click on the executable to run it. After that, check the logs of your windows. You have to examine the same log file that we tried for the image previously. Search for the keyword connector.ps1 and you will see an event in which we can clearly see some flag being set in the registry hive. The value field gives us the first part of the flag `VEhNezZsNERfeTB1X2tOT3d`. Combine both the parts and decode it using base64. It will give us the complete actual flag `THM{6l4D_y0u_kNOw_h0w_2_p1vOT}`.

## Flag
`THM{6l4D_y0u_kNOw_h0w_2_p1vOT}`

---
*Note: This writeup is for educational purposes only.*
