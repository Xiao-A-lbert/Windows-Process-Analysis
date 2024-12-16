# Windows Process Analysis

<h2>Description</h2>
In this task, I used windows command line commands to analyst processes occuring on my windows VM when a reverse tcp shell and a powershell script are executed. Commandlines used include filtering with tasklist, filtering with wmix, and printing with netstat.


<h2>Languages and Utilities Used</h2>

- <b>Windows cmd</b>
- <b>linux CLI</b>


<h2>Environments Used </h2>

- <b>Unbuntu</b>
- <b>Windows 10 Enterprise</b> 

<br />
<br />
Using tasklist to exploire image names, process ids, session names, session #, and memory usage. 

![1) exploring tasklist](https://github.com/user-attachments/assets/75a1ebe7-d86a-4b72-b021-a77af3d94283)

<br />
<br />
Using tasklist /V to display a detailed list of all currently running processes with verbose information. This includes new columns like User Name, CPUT Time, and Window Title. 

![2) tasklist V more verbose](https://github.com/user-attachments/assets/e728801d-a5d2-4eaf-bbbb-55d409651da6)

<br />
<br />  
Using tasklist /FI to find specific process ids or image names. "/M" lists all tasks currently using the given exe/dll name. Since no specific module is specified, it will display all loaded modules for the process with PID 2200

![3) tasklist filtering for pid or imagename or modules](https://github.com/user-attachments/assets/c766d62b-5dc0-4c9a-8c41-8e8aacffb8cd)

<br />
<br />
"wmic" is the Windows Management Instrumentation Command-line utility, which provides a command-line interface for accessing WMI information. "Process" is an alias that refers to the Win32_Process class in WMI, representing running processes on the system. "where processid=2200" is a filter condition that specifies we're looking for the process with PID 2200."get name, parentprocessid, processid" instructs WMIC to retrieve specific properties of the process:
name: The name of the executable file for the process
parentprocessid: The PID of the parent process that spawned this process
processid: The Process ID (which will be 2200 in this case). "| find "5964"" filters the output of the WMIC command, searching for lines containing "5964".

![4) wmic tracing notmalware pid to parentid of cmd](https://github.com/user-attachments/assets/3e4788de-cd63-44ed-a078-974c81cc13e3)

<br />
<br 
This uses wmic to get the commandline used for processid=2200 (notmalware.exe). In this instance, I just ran it on the commandline to execute.

![5) wmic commandline used to run pid](https://github.com/user-attachments/assets/1ecadf27-f4f3-4714-a5b4-044dcd056747)

<br />
<br />
I used a reverse shell generator to create a base62 encoded powershelll script, I detailed the attacking machine (Ubuntu) with 192.168.1.4 on port 3333 using the netcat command "nc -lvnp 3333". "nc" is the Netcat command.
"-l" puts Netcat in listening mode, waiting for incoming connections.
"-v" enables verbose output, providing more detailed information about the connection.
"-n" skips DNS name resolution, using only IP addresses.
"-p 3333" specifies the port number (3333) on which to listen.

![6) reverse powershell base64 generator](https://github.com/user-attachments/assets/847a8382-506d-4981-9ef2-c1279884a3f2)

<br />
<br />
Executing the powershell script on admin cmd in windows establishes a connection to 3333 from 51218 192.168.1.5 (victim machine). 

![7) executing on windows shows connection](https://github.com/user-attachments/assets/7892dc35-cc20-487a-bec8-24088be69ce0)

<br />
<br />
Running netstat -anob shows the powershell tcp connection. "netstat" is the network statistics utility.
"-a" displays all active connections and listening ports.
"-n" shows addresses and port numbers in numerical form.
"-o" displays the owning process ID associated with each connection.
"-b" shows the executable involved in creating each connection or listening port.

![8) netstat anob to show powershell rsh](https://github.com/user-attachments/assets/87585488-fa9b-47a2-91f2-83ebb221194e)

<br />
<br />
Using the wmic command to find the command line for the powershell script (processid=3796) prints the base64 encoded script. 

![9)wmic pid of pwsh shows cmd](https://github.com/user-attachments/assets/d318c1dc-ad55-451f-80f4-91cd0957055c)

<br />
<br />
Decoding the script on Ubuntu with the base64 -d command after echoing the script prints the unencoded powershell script. 

![10) decoding base64 pwshl string](https://github.com/user-attachments/assets/e69b7ebc-9100-4759-9123-24956841f142)

<br />
<br />
