# Phase 1: Setup and Compromise the Service
In this Phase we are going to Setup the two VM, the attacker and the victim, then we will try to use metasploit tool to exploit the victim manually and using a custom Script.

## Step 1: Getting the IP Addresses
To ensure that there's a connection we have to perform a ping from one of them to the other. Pinging require IP Addresses.
## Step 1.1: Kali IP Address (Attacker Machine)
<img src="./Screenshots/Kali%20Ip%20Address.jpg" alt="Kali Ip Address" width="500" height="500"><img/>
### Attacker IP Address : 192.168.100.5
## Step 1.2: Metasploitable3 IP Address (Victim Machine)
<img src="./Screenshots/Metasplotable3%20Ip%20address.png" alt="Metasplotaible3 IP Address" width="500" height="500"><img/>
### Victim IP Address : 192.168.100.6
**Note**: Both machines are in a NAT Network.

## Step 2: Verifying Network Connectivity
<img src="./Screenshots/Checking%20Connectivity.png" alt="Checking Connectivity." width="500" height="500"><img/><br></br>
**Target IP Address**: 192.168.100.6 <br>
**Result**: Since there's no packet loss then there's a connection between the two machines.

## Step 3: Scanning for Open Ports
Now since we are sure that there's a connection, we want to To discover vulnerable services running on the victim machine using `nmap`command<br>
<img src="./Screenshots/Scanning%20open%20ports.png" alt="Scanning Open Ports" width="500" height="500"><img/><br></br>
**Result**: We can find several ports open with different services but we will focus on the FTP service (ProFTPD 1.3.5) since it's common to be vulnerable.

## Step 4: Exploiting ProFTPD 1.3.5
In this step we would like to exploit the service using metasploit tool. We used a specific module on the Victim Machine **unix/ftp/proftpd_modcopy_exec** and the goal is to get a reverse shell back to our Kali machine. <br><br>
<img src="./Screenshots/Exploiting%20ProFTPD%20manually%201.1.png" alt="Scanning Open Ports" width="700" height="700"><img/><br></br>
**Result**: As shown in the uploaded snapshot, you can notice that the payload has been uploaded successfully into the Victim Machine. This allow us to get a reverse shell back to our Kali machine. We triggered the payload by accessing it through the victim’s web server. <br>
**Note**: Netcat listener on port 4444 should be opened before the exploiting. <br><br>
<img src="./Screenshots/Successfully%20open%20a%20reverse%20shell%201.1.png" alt="Scanning Open Ports"><img/><br></br>

### At this Point the task 1.1 from the first Phase has been sucessfully achieved.
### Let's go into the next task which is exploiting using a custom script. <br>

##  Step 5: Exploiting using a Custom Script
<img src="./Screenshots/Running%20the%20script%201.2.png" alt="Running the Script" width="400" height="200"><img/><br></br>
<img src="./Screenshots/Control%20the%20commands%20from%20remote%201.2.png" alt="Control commands from Remote" width="600" height="200"><img/><br></br>
<img src="./Screenshots/Automated%20Exploiting%20success%201.2.png" alt="Sucessfull Automating" width="600" height="200"><img/><br></br>

**Explaination**: <br>
**1-** The script automates the exploitation of the vulnerable ProFTPD 1.3.5 service using the mod_copy module by generating a PHP reverse shell payload, exploiting the FTP server to copy this payload into the Apache web root directory (/var/www/html/) and providing a direct URL to the uploaded exploit.<br>
**2-** After uploading the exploit.php file using the custom script, we leveraged the mod_copy vulnerability to manually execute a command on the victim machine by setting the CMD option in Metasploit.<br><br>
**Result**: The last snapshot shows a successful remote command executed successfully which is a proof of successfull automation.
# End Of Task 1.2 (Custom Script)
At this point, the tasks of phase1 has been successfully achieved. <br>
# Why We Chose FTP (ProFTPD mod_copy)?
**The FTP service (ProFTPD 1.3.5) was selected for this project for several important reasons:** <br>
**1-** Known Exploit <br>
**2- The vulnerability is listed as [CVE-2015-3306], which is:** <br>
&nbsp;&nbsp;&nbsp;**2.1** Easy to exploit.<br>
&nbsp;&nbsp;&nbsp;**2.2** Doesn't require credentials if anonymous access is allowed.<br>
&nbsp;&nbsp;&nbsp;**2.3** Works reliably on Metasploitable3.
