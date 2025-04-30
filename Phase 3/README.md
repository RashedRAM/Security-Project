# Phase 3: Defensive Strategy Proposal
In this Phase we are going to protect the Victim Machine using a defensive strategy and our goal is to prevent the exploit attack that we performed in the Phase 1.<br><br>
**Process**: As we know the vulnerability that we exploit is through FTP ( ProFTPD 1.3.5 ) so we will try to build a FireWall to prevent accessing through FTP.
## Step 1: Building the Firewall (UFW) in the Victim Machine
To accomplish this tips some commands should be written in the victim machine. <br>
`sudo apt-get install ufw`<br>
`sudo ufw deny 21/tcp`<br>
`sudo ufw enable`<br>
`sudo ufw status`<br> <br> <br>
<img src="./Screenshots/PROTECTION%20MECHANISM%20UFW%20FIREWALL.jpg" alt="Firewall Table"><img/> <br> <br>
**Results**: From the Firewall table you can see that the firewall is active any packets will be denied if it's from FTP protocol since it has less security and mostly vulnerable.

## Step 2: Testing the Defense Strategy
<img src="./Screenshots/Exploitation%20After%20Firewall.jpg" alt="Firewall Sucess"><img/> <br> <br>

**Results**: You can see that the exploitation has been denied and the victim machine cannot be reached through this past vulnerable.

## Comparisons Purposes
<img src="./Screenshots/Exploitation%20Before%20Firewall.png" alt="Without Firewall"><img/> <br> <br>

**Usage**: This snapshot just to see how we were able to exploit using the same vulnerability before the firewall.
