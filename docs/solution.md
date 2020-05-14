# One Possible Solution

There are many different techniques you could use to solve this challenge.  Here is one way you could answer each question.

```
KNOW THY SYSTEM!

Open a second CMD prompt as an Administrator and run netstat -nao on your host so you know what your system looks like before it is "infected."
Verify your firewall and AV are disabled.  I am about to start a non-malicious backdoor for you to find.

After you have run netstat press ENTER to continue
```
You could as the question suggests just look at netstat, press enter, run nestat a second time and then compare the results manually.  
But you could also let your system do the hard part for you.  Open up a Powershell prompt running as administrator then record the 
results of `netstat -nao` into a variable called net1.  Then press enter in the other window to let the lab start the backdoor.  
Then type `$net2 = netstat -nao` to capture the updated netstat into another variable named net2.  Then you can use Powershells 
compare-objects command to show you the differences between the two results.

```
PS C:\Users\mark\Documents\504lab_internal> $net1 = netstat -nao
PS C:\Users\mark\Documents\504lab_internal> $net2 = netstat -nao
PS C:\Users\mark\Documents\504lab_internal> Compare-Object $net1 $net2

InputObject                                                                  SideIndicator
-----------                                                                  -------------
  TCP    0.0.0.0:50985          0.0.0.0:0              LISTENING       6392  =>
```

You can see that we now have a new listening port listening on 50985.  Your port number will likely be different because the backdoor chooses a port randomly. Type the port number in to answer the question.

```
What TCP port is the backdoor listening on? 50985

What is the process id number of the backdoor?
```

Now you need to know its process ID number.  That is the other number that appears in the output 6392.  It was displayed because we used the -o option when we called netstat.

```
What is the process id number of the backdoor? 6392

What is the Parent process id number of the backdoor?
```

Now you need to find out what the Parent Process ID is.  The easiest way to see this is to use a graphical tool such as process explorer or process hacker.  But installing a tool on a system for incident response in sloppy.  Instead you can use wmic to query this information.  For example, `wmic process where (processid = 1234) get parentprocessid` would show you the parent processid for process 1234.  You will want to run this one in a command prompt rather than your powershell prompt.

```
C:\Windows\system32>wmic process where (processid = 6392) get parentprocessid
ParentProcessId
7220
```

Now we know the parent process ID number you can answer the next question!.

```
What is the Parent process id number of the backdoor? 7220

Use netcat to connect to the backdoors TCP port.
What is flag printed when you connect to the backdoor?
```

Now use netcat to conect to the TCP port that we identified in step 1 of this lab and retrieve the flag.

```
C:\Windows\system32> nc 127.0.0.1 50985
TheFlagisBlack197168254

```

Once netcat connects the backdoor prints "TheFlagisBlack" followed by a random number.  Hmm perhaps this backdoor must have been created by a 1980s punk rock band.  Anyway now you can answer the next question.

```
What is flag printed when you connect to the backdoor? TheFlagisBlack197168254

What TCP port is the backdoor listening on now?
```

The backdoor must have changed ports becuase we are asking for its new port number.  Well, if it is the same backdoor then the process id number must be the same.  We can use netstat with findstr to look for the process.

```
C:\Windows\system32>netstat -nao | findstr 6392
  TCP    0.0.0.0:51274          0.0.0.0:0              LISTENING       6392  
  TCP    127.0.0.1:50985        127.0.0.1:51273        ESTABLISHED     6392
```

I see two entries here.  The bottom one is "ESTABLISHED" meaning someone is connected to it.  That is the netcat connection that I made to the backdor to get the flag. If you don't close netcat it will keep that connection open.  The first line says that it is "LISTENING".  This is the new port for that backdoor.  So we have the next answer to our questions.

```
What TCP port is the backdoor listening on now? 51274

Now use wmic to kill the process.
Press enter after you have killed the process.
```

To kill the process with wmic I like to do it in two steps.  The first step just displays the process to confirm to me that I typed my syntax properly.  Then, once I know my syntax is complete, i press up arrow and change the word "list brief" to "delete".

```
C:\Windows\system32>wmic process where (processid = 6392) list brief
HandleCount  Name            Priority  ProcessId  ThreadCount  WorkingSetSize
486          powershell.exe  8         6392       8            44756992


C:\Windows\system32>wmic process where (processid = 6392) delete
Deleting instance \\MarksComputer\ROOT\CIMV2:Win32_Process.Handle="6392"
Instance deletion successful.
```

Alternatively you could do this with powershell.
```
PS C:\Users\mark\Documents\504lab_internal> get-process -PID 6392 | stop-process
```
Once the backdoor has been killed go back to the lab and press ENTER.

```
Press enter after you have killed the process.

This Powershell backdoor was easy to find because it listened on a TCP port.  A more typical Powershell backdoor will not.  Instead it makes periodic client connections to a command and control server.  Now I'm creating a new Powershell process that does not listen on a port.

What is the process id number of the backdoor?
```

The next backdoor is more difficult to find.  Now it is not listening on a TCP port.  Had we known this was going to happen we could have recorded a list of running processes in a varaible and compared it after the process was launched similar to what we did with netstat earlier.  But it is too late for that.  The process is already running.  Fortunetly the question does tell us that it was a powershell process. Get a list of powershell processes and submit their process id numbers until you find out which one is the backdoor it is looking for.  You could do this with wmic like this `wmic process where (name like "powershell%") list brief` or with Powershell like this `Get-Process -name powershell`.  But we probably have several processes that started so how do you know which one it is?   Since it just started a few minutes ago we can ask powershell to show us the process start times.  `Get-Process -name powershell | Select-Object -Property id,starttime`  That should make one of your processes stand out from the rest.  Enter that Process ID number to get the next question.

```
What is the process id number of the backdoor? 6512

Use wmic to retrieve the CommandLine and answer the following.

What is the flag contained in the script executed by the backdoor?
```
Now we need to retrieve the command line that was used to launch the backdoor.

```
C:\Windows\system32>wmic process where (processid = 6512) get commandline
CommandLine                                                                                                                            
powershell.exe -nop -exec bypass -enc dwBoAGkAbABlACgAJAB0AHIAdQBlACkAewAkAGYAbABhAGcAIAA9ACAAIgBTAGEAcwBxAHUAYQBjAGgAZQAzADAAMwAyADEAMQA4ADIAMwAyACIAOwAgAFsAUwB5AHMAdABlAG0ALgBUAGgAcgBlAGEAZABpAG4AZwAuAFQAaAByAGUAYQBkAF0AOgA6AFMAbABlAGUAcAAoADEAMAAwADAAMAApAH0AOwA=
```

Ok, we can see the command line, but no flag. Powershell backdoors are quite often BASE64 encoded. Anytime you see the "-enc"
option it will be followed by a base64 encoded payload. So lets decode that thing using Powershell.  Typing "help" as your answer will give you a nice hint and some poewrshell syntax you can copy and paste. Lets plug the BASE64 string from the command line above into that syntax to decode it.

```
PS C:\Users\mark_\Documents\504lab_internal> [System.Text.Encoding]::Unicode.GetString([System.Convert]::FromBase64String("dwBoAGkAbABlACgAJAB0AHIAdQBlACkAewAkAGYAbABhAGcAIAA9ACAAIgBTAGEAcwBxAHUAYQBjAGgAZQAzADAAMwAyADEAMQA4ADIAMwAyACIAOwAgAFsAUwB5AHMAdABlAG0ALgBUAGgAcgBlAGEAZABpAG4AZwAuAFQAaAByAGUAYQBkAF0AOgA6AFMAbABlAGUAcAAoADEAMAAwADAAMAApAH0AOwA="))
while($true){$flag = "Sasquache3032118232"; [System.Threading.Thread]::Sleep(10000)};
```
How you have the final flag! It is Sasquache followed by a large number. Perhaps the backdoor author was a 9 foot hairy beast. No matter.  Now you can answer the next question!

```
What is the flag contained in the script executed by the backdoor? Sasquache3032118232


Now use wmic to kill the process.
Press enter after you have killed the process.
```

Last you need to keep the backdoor process. Last time we used wmic.  Lets use powershell here.  Again, I like to do this in two steps.  The first one confirms I have the correct process.  The second step kills it.

```
PS C:\Users\mark_\Documents\504lab_internal> Get-Process -pid 6512

Handles  NPM(K)    PM(K)      WS(K)     CPU(s)     Id  SI ProcessName
-------  ------    -----      -----     ------     --  -- -----------
    468      23    48684      50880       0.31   6512   1 powershell


PS C:\Users\mark_\Documents\504lab_internal> Get-Process -pid 6512 |stop-process
```

Then press enter in the lab to receive your fabulous prizes.

```
You have done well.  The evil hackers have been thwarted.
Press enter to end this lab.
```

Well done!