# The SANS SEC504 Windows Cheat Sheet Lab

## Introduction
This lab is designed to show how a few simple commands documented on the SANS SEC504 Windows Incident Response Cheat Sheet can be used to identify unusual processes running on your host.  This lab will launch non-persistent, benign processes on your host that listen on network ports and establish communications using common malware techniques.   It will then ask you various questions about those processes.  The process id number, TCP ports and other information is chosen randomly so you can run this lab multiple times for practice.

## Download
There are two ways to get a copy of this lab.  First, Attend SANS SEC504 for this lab and many other awesome labs.  Second, you can download it [here](504lab.exe).


## Usage
First, make sure your antivirus software and firewall are disabled. The tool will launch benign processes on hour host that mimic typical behavior of malware. Firewalls and antivirus products may prevent this tool from functioning properly. Once a malware behavior has launched you will be asked to find and investigate it.  This tool will present you with questions about the "malware" that you will need to answer to move on to the next step.  If you are stuck you submit an answer of "help" and it will give you a hint.  Alternatively you can look at the walk-through on the link provided below. To begin run this program and then open a second command prompt that is running as an Administrator. Use the second window to investigate the "malware" and the first window to submit your answers and get the questions.


## Answers
If you get stuck you can type "help" as the answer to your question to receive a hint.
Click [HERE](docs/solution.md) for a walk-through.


## More Information
Click [here](https://www.sans.org/course/hacker-techniques-exploits-incident-handling) for more information on [SANS SEC504 - Hacker Tools, Techniques, Exploits, and Incident Handling](https://www.sans.org/course/hacker-techniques-exploits-incident-handling)

This tool was developed by [Mark Baggett](https://twitter.com/markbaggett) course author of [SEC573 Automating Information Security with Python](https://www.sans.org/course/automating-information-security-with-python)

Updates for this tool can be downloaded from https://github.com/markbaggett

This binary is distributed as part of SANS SEC504 Hacker Tools, Techniques, Exploits and Incident Response course.  You may download and use this tool without modification as you see fit.
All Rights Reserved.
