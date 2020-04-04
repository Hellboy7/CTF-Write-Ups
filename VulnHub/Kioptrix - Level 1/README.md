# kioptrix - Level 1 <br/>
<b>Download Kioptrix Box from VulnHub.</b><br/>
<b>Link:</b> https://www.vulnhub.com/entry/kioptrix-level-1-1,22/<br/>
<b>Note:</b> Its VMWare Image but it can be configured in Virtual Box too. Please find the below link to configure in Virtual Box.<br/>
<b>Link:</b> http://vivirytech.blogspot.com/2018/01/oscp-journey-001-vm-prep-for-kioptrix.html 

<h3>Step 1: Discover your Kioptrix Box IP.</h3>

Cmd: netdiscover -r 192.168.56.0/24

<image src="Screenshots/1.png">

<h3>Step 2: Check for Open-ports.</h3>

Cmd: nmap 192.168.56.101

<image src="Screenshots/2.png">
 
<h3>Step 3: Enumerate the open ports for the service and versions detections.</h3>

Cmd: nmap -sV 192.168.56.101

<image src="Screenshots/3.png">

<h3>Step 4: Enumerate services on each open ports.</h3>
Since port 80 & 443 is on open state, lets scan with nickto for web servers vulnerabilities.<br/>
Note: (by default nikto will take port 80 or you can specify required eg. nikto –h 192.168.56.101:8080)<br/>

Cmd: nikto -h 192.168.56.101

<image src="Screenshots/5.png">

Findings from scan results.<br/>
1. OpenSSL<br/>
2. Apache<br/>
3. Directory Indexing<br/>

<h3>Step 5: Directory Brute-forcing</h3>
<b>Tool:</b> Disbuster<br/>
Disbuster tool used to explore know file directory in web servers.<br/>

Cmd: dirbuster<br/>
<b>Note:</b> choose wordlist for known directory paths to scan. <br/>
Wordlist file path in kali-linux <b>"/root/usr/share/disbuster"</b> and you can generate report too.<br/>

<image src="Screenshots/6.png">

Could not find any entry point for further exploitation. Looks like deadend.<br/> 

<h3>Step 6: Now Enumerating other open ports. (Port: 111 & 139)</h3>
Port No. 111 (RCP)<br/>
The rpcbind utility is a server that converts RPC program numbers into universal addresses. It must be running on the host to be able to make RPC calls on a server on that machine.<br/>
<b>Link:</b> https://linux.die.net/man/8/rpcbind<br/>

Cmd: rpcinfo –p 192.168.56.101<br/>
Cmd: nmap –script=rpc-grind.nse --script-args 'rpc-grind.threads=8' -p 111 192.168.56.101<br/>

<image src="Screenshots/10.png">

Again its deadend, Let's try with Port No. 139. 

Port No. 139 (Netbios)<br/> 
NetBIOS is a service which allows communication between applications such as a printer or other computer in Ethernet or token ring network via NetBIOS name.<br/>

Cmd: nmap -sT –sU 192.168.56.101<br/> 
Cmd: enum4linux -a 192.168.56.101<br/>
Cmd: nmap -p 139 -vv --script=vuln 192.168.56.101<br/>  

<image src="Screenshots/11.png">

Findings from the above scan results.<br/>
<b>Note</b>: Samba version detect by "enum4linux" tool. 
1. Samba Version 2.2<br/>

<h3>Step 7: Exploiting the Samba vulnerability.</h3>
This time trying with samba 2.2 vulnerable version using "searchspolit".<br/>   

Cmd: searchspolit samba 2.2<br/>

<image src="Screenshots/12.png">

Next, let’s try to compile the remote code execution exploit for Samba < 2.2.8 (Linux/BSD).<br/>
Finally, let’s execute the exploit with our IP as the callback address and the Kioptrix IP as the target.<br/>

<image src="Screenshots/13.png">

<h2><b>Bingo! Got the root access!!!!</b></h2>
