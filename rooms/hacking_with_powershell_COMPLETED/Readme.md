# Hacking With PowerShell

## Task 1

* In this room, we'll be exploring the following concepts:

1. What is Powershell and how it works
2. Basic Powershell commands
3. Windows enumeration with Powershell
4. Powershell scripting

* You can control the machine in your browser or RDP into the instance with the following credentials:

```
Username: Administrator
Password: BHN2UVw0Q

```

## Task 2

* Powershell is the Windows Scripting Language and shell environment that is built using the .NET framework.

* This also allows Powershell to execute .NET functions directly from its shell. Most Powershell commands, called __cmdlets__, are written in .NET. Unlike other scripting languages and shell environments, the output of these __cmdlets__ are objects - making Powershell somewhat object oriented. This also means that running __cmdlets__ allows you to perform actions on the output object(which makes it convenient to pass output from one __cmdlet__ to another). The normal format of a __cmdlet__ is represented using Verb-Noun; for example the __cmdlet__ to list commands is called `Get-Command`.

* Common verbs to use include:

    * Get
    * Start
    * Stop 
    * Read
    * Write
    * New
    * XOut

* To get the full list of approved verbs, visit [this]() link.

---

* what is the command to get help about a particular __cmdlet__(without any parameters)?

```bat
Get-Help
```

## Task 3

* Now that we've understood how __cmdlets__ works - let's explore how to use them! The main thing to remember here is that Get-Command and Get-Help are your best friends! 

### Using __Get-Help__

* Get-Help displays information about a __cmdlet__. To get help about a particular command, run the following:

```bat
Get-Help Command-Name
```

* You can also understand how exactly to use the command by passing in the `-examples` flag. This would return output like the following: 


### Using __Get-Command__

* Get-Command gets all the __cmdlets__ installed on the current Computer. The great thing about this __cmdlet__ is that it allows for pattern matching like the following

```bat
Get-Command Verb-* 
#or 
Get-Command *-Noun
```

* Running `Get-Command New-*` to view all the ___cmdlets___ for the verb new displays the following: 

### __Object Manipulation__

* In the previous task, we saw how the output of every __cmdlet__ is an object. If we want to actually manipulate the output, we need to figure out a few things:

    * passing output to other ___cmdlets___
    * using specific object __cmdlets__ to extract information

* The Pipeline(|) is used to pass output from one __cmdlet__ to another. A major difference compared to other shells is that instead of passing text or string to the command after the pipe, powershell passes an object to the next __cmdlet__. Like every object in object oriented frameworks, an object will contain methods and properties. You can think of methods as functions that can be applied to output from the __cmdlet__ and you can think of properties as variables in the output from a __cmdlet__. To view these details, pass the output of a __cmdlet__ to the Get-Member __cmdlet__

```bat
Verb-Noun | Get-Member 
```

* An example of running this to view the members for Get-Command is:

```bat
Get-Command | Get-Member -MemberType Method
```

* From the above flag in the command, you can see that you can also select between methods and properties.

### Creating Objects From Previous __cmdlets__

* One way of manipulating objects is pulling out the properties from the output of a __cmdlet__ and creating a new object. This is done using the `Select-Object` __cmdlet__. 

* Here's an example of listing the directories and just selecting the mode and the name:

* You can also use the following flags to select particular information:

    * __first__ - gets the first x object
    * __last__ - gets the last x object
    * __unique__ - shows the unique objects
    * __skip__ - skips x objects

### Filtering Objects

* When retrieving output objects, you may want to select objects that match a very specific value. You can do this using the `Where-Object` to filter based on the value of properties. 

* The general format of the using this __cmdlet__ is 

```bat
Verb-Noun | Where-Object -Property PropertyName -operator Value

Verb-Noun | Where-Object {$_.PropertyName -operator Value}
```

* The second version uses the $_ operator to iterate through every object passed to the Where-Object __cmdlet__.

* __Powershell is quite sensitive so make sure you don't put quotes around the command!__

* Where `-operator` is a list of the following operators:

    * __-Contains__: if any item in the property value is an exact match for the specified value
    * __-EQ__: if the property value is the same as the specified value
    * __-GT__: if the property value is greater than the specified value

* For a full list of operators, use [this](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/where-object?view=powershell-6) link.

### Sort Object

* When a __cmdlet__ outputs a lot of information, you may need to sort it to extract the information more efficiently. You do this by pipe lining the output of a __cmdlet__ to the `Sort-Object` __cmdlet__.

* The format of the command would be

```bat
Verb-Noun | Sort-Object
```

* Now that you've understood the basics of how Powershell works, let try some commands to apply this knowledge!

---

* What is the location of the file "interesting-file.txt"

```
C:\Program Files
```

* Specify the contents of this file

```bat
Get-Content "C:\Program Files\interesting-file.txt.txt"
```

```
notsointerestingcontent
```

* How many cmdlets are installed on the system(only cmdlets, not functions and aliases)?

```bat
(Get-Command | Where-Object -Property CommandType -eq cmdlet).count
```

```
6638
```

* Get the MD5 hash of interesting-file.txt

```bat
Get-FileHash -Algorithm md5 "C:\Program Files\interesting-file.txt.txt"
```

```
49A586A2A9456226F8A1B4CEC6FAB329
```

* What is the command to get the current working directory?

```bat
Get-Location
```

* Does the path "C:\Users\Administrator\Documents\Passwords" Exist(Y/N)?

```bat
Test-Path C:\Users\Administrator\Documents\Passwords
```

```
N
```

* What command would you use to make a request to a web server?

```bat
Invoke-WebRequest
```

* Base64 decode the file b64.txt on Windows. 

```bat
$file = 'C:\Users\Administrator\b64.txt'
[System.Convert]::FromBase64String((Get-Content $file)) | Set-Content decoded_b64.txt -Encoding Byte
Get-Content "C:\Users\Administrator\decoded_b64.txt"
```

```
ihopeyoudidthisonwindows
```

## Task 4

* The first step when you have gained initial access to any machine would be to enumerate. We'll be enumerating the following:

    * users
    * basic networking information
    * file permissions
    * registry permissions
    * scheduled and running tasks
    * insecure files

* Your task will be to answer the following questions to enumerate the machine using Powershell commands! 
---

* How many users are there on the machine?

```bat
Get-LocalUser
```

```
5
```

* Which local user does this SID(S-1-5-21-1394777289-3961777894-1791813945-501) belong to?

```bat
Get-LocalUser | Select-Object Name,sid
```

```
Guest
```

* How many users have their password required values set to False?

```bat
Get-LocalUser | Select-Object Name,PasswordRequired
```

```
4
```

* How many local groups exist?

```bat
(Get-LocalGroup).count
```

```
24
```

* What command did you use to get the IP address info?

```bat
Get-NetIPAddress
```

* How many ports are listed as listening?

```bat
(Get-NetTCPConnection | Where-Object -Property State -eq Listen).count
```

```
20
```

* What is the remote address of the local port listening on port 445?

```
::
```

* How many patches have been applied?

```bat
(Get-HotFix).count
```

```
20
```

* When was the patch with ID KB4023834 installed?

```
6/15/2017 12:00:00 AM
```

* Find the contents of a backup file.

```bat
Get-ChildItem -Path C:\ -Include *.bak.* -File -Recurse
Get-Content "C:\Program Files (x86)\Internet Explorer\Passwords.bak.txt"
```

```
backpassflag
```

* Search for all files containing API_KEY

```bat
Get-ChildItem -Path C:\ -File -Recurse | Select-String -pattern "API_KEY"
```

```
fakekey123
```

* What command do you do to list all the running processes?

```bat
Get-Process
```

* What is the path of the scheduled task called new-sched-task?

```
/
```
* Who is the owner of the C:\

```bat
Get-Acl C:\
```

```
NT SERVICE\TrustedInstaller
```


## Task 5

* Now that we have run powershell commands, let's actually try write and run a script to do more complex and powerful actions. 

* For this ask, we'll be using PowerShell ISE(which is the Powershell Text Editor). To show an example of this script, let's use a particular scenario. Given a list of port numbers, we want to use this list to see if the local port is listening. Open the listening-ports.ps1 script on the Desktop using Powershell ISE. Powershell scripts usually have the .ps1 file extension. 

```bat
$system_ports = Get-NetTCPConnection -State Listen

$text_port = Get-Content -Path C:\Users\Administrator\Desktop\ports.txt

foreach($port in $text_port){

    if($port -in $system_ports.LocalPort){
        echo $port
     }

}
```

* On the first line, we want to get a list of all the ports on the system that are listening. We do this using the Get-NetTCPConnection cmdlet. We are then saving the output of this cmdlet into a variable. The convention to create variables is used as:

```bat
$variable_name = value
```

* On the next line, we want to read a list of ports from the file. We do this using the Get-Content cmdlet. Again, we store this output in the variables. The simplest next step is iterate through all the ports in the file to see if the ports are listening. To iterate through the ports in the file, we use the following

```bat
foreach($new_var in $existing_var){}
```

* This particular code block is used to loop through a set of object. Once we have each individual port, we want to check if this port occurs in the listening local ports. Instead of doing another for loop, we just use an if statement with the `-in` operator to check if the port exists the LocalPort property of any object. A full list of if statement comparison operators can be found [here](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_comparison_operators?view=powershell-6). To run script, just call the script path using Powershell or click the green button on Powershell ISE:

* Now that we've seen what a basic script looks like - it's time to write one of your own. The emails folder on the Desktop contains copies of the emails John, Martha and Mary have been sending to each other(and themselves). Answer the following questions with regards to these emails(try not to open the files and use a script to answer the questions). 

* Scripting may be a bit difficult, but [here](https://learnxinyminutes.com/docs/powershell/) is a good resource to use: 

---

* What file contains the password?

```bat
# inside emails Folder
Get-ChildItem -File -Recurse | Select-String -Pattern "password"
```

```
Doc3M
```

* What is the password?

```
johnisalegend99
```

* What files contains an HTTPS link?

```bat
# inside emails Folder
Get-ChildItem -File -Recurse | Select-String -Pattern "HTTPS"
```

```
Doc2Mary
```

## Task 6

* Now that you've learnt a little bit about how scripting works - let's try something a bit more interesting. Sometimes we may not have utilities like nmap and python available, and we are forced to write scripts to do very rudimentary tasks. Why don't you try writing a simple port scanner using Powershell. Here's the general approach to use: 

    * Determine IP ranges to scan(in this case it will be localhost) and you can provide the input in any way you want
    * Determine the port ranges to scan
    * Determine the type of scan to run(in this case it will be a simple TCP Connect Scan)

---

* How many open ports did you find between 130 and 140(inclusive of those two)?

```bat
for($i=130; $i -le 140; $i++){
    Test-NetConnection localhost -Port $i
}
```

```
11
```