<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />

  <h2>Technologies Used</h2>
  <ul>
    <li>Microsoft Azure – for cloud infrastructure</li>
    <li>Windows Server 2022 – for domain controller VM</li>
    <li>Windows 10 – for client VM</li>
    <li>Active Directory Domain Services (AD DS)</li>
    <li>PowerShell – for automation and user creation</li>
    <li>Remote Desktop Protocol (RDP) – for VM access</li>
  </ul>

  <h2>Operating Systems Used</h2>
  <ul>
    <li>Windows Server 2022 (Domain Controller)</li>
    <li>Windows 10 (Client Machine)</li>
  </ul>

  <h2>Step-by-Step Instructions</h2>

  <h3>1. Create the Domain Controller (DC-1)</h3>
  <ul>
    <li>In Azure, create a new Resource Group to group all related resources for this project.</li>
    <img src="https://i.imgur.com/1aJ5rVE.png"/>
    <li>Create a Virtual Network (VNet) and Subnet to ensure both VMs are on the same local network.</li>
    <img src="https://i.imgur.com/lbqwdsr.png"/>
    <li>Deploy a Windows Server 2022 VM named <strong>DC-1</strong> within your VNet and Subnet.</li>
    <img src="https://i.imgur.com/ZupaX7h.png"/>
    <li>Use the following credentials:  
      <code>Username: labuser</code>  
      <code>Password: Cyberlab123!</code></li>
     <img src="https://i.imgur.com/haKlbJ1.png"/>  
    <li>Once the VM is created, set the Network Interface's private IP to static to avoid changes after reboots.</li>
    <img src="https://i.imgur.com/FZkZNm3.png"/> 
    <img src="https://i.imgur.com/qoK2xOR.png"/>
    <li>Log into DC-1 via RDP using DC-1's public IP. Temporarily disable the firewall for simplicity in this lab (not recommended for production).</li>
    <p><img src="https://i.imgur.com/2mKmxfz.png"/><</p>
      <li>Run wf.msc for firewall settings.</li>
    <p><img src="https://i.imgur.com/8OOPwCl.png"/></p>
      <li>Navigate to Windows Firewall Propetries.</li>
    <p><img src="https://i.imgur.com/0Yq3HRb.png"/></p>
      <li>Turn off firewall under Domain, Private, and Public Profile.</li>
    <p><img src="https://i.imgur.com/cxm8cMR.png"/></p>
  </ul>
  

  <h3>2. Create Client VM (Client-1)</h3>
  <ul>
    <li>Deploy a Windows 10 VM named <strong>Client-1</strong> in the same region, VNet, and Subnet as DC-1.</li>
   <img src="https://i.imgur.com/994cbNv.png"/> 
    <li>Use the same credentials as before: <code>labuser / Cyberlab123!</code></li>
    <img src="https://i.imgur.com/nnoTUjL.png"/>
    <li>Before starting the VM, go to the Network Interface settings and manually set the DNS to the static private IP of DC-1.</li>
    <img src="https://i.imgur.com/RUtkNUu.png"/> 
    <img src="https://i.imgur.com/lBJUHfJ.png"/>
    <li>Restart the Client-1 VM to apply the DNS configuration.</li>
    <img src="https://i.imgur.com/kssskRo.png"/>
  </ul>

  <h3>3. Verify Connectivity</h3>
  <ul>
    <li>Log into Client-1 using RDP.</li>
    <img src="https://i.imgur.com/EHyM3NH.png"/>
    <li>Open Command Prompt and use <code>ping &lt;DC-1 IP&gt;</code> to confirm it can communicate with DC-1.</li>
    <img src="https://i.imgur.com/roMWxhe.png"/>
    <li>Run <code>ipconfig /all</code> and verify that the DNS server matches DC-1’s private IP address.</li>
    <img src="https://i.imgur.com/u8pT7dA.png"/>
  </ul>

  <h3>4. Install Active Directory on DC-1</h3>
  <ul>
    <li>On DC-1, open Server Manager &gt; Manage &gt; Add Roles and Features.</li>
    <img src="https://i.imgur.com/C4FaOTK.png"/>
    <br />
    <img src="https://i.imgur.com/0LGHdNz.png"/>
    <li>Select “Active Directory Domain Services” and complete the installation.</li>
    <img src="https://i.imgur.com/Pxz7s6u.png"/>
    <li>After installation, click “Promote this server to a domain controller.”</li>
    <img src="https://i.imgur.com/iSsSdoK.png"/>
    <li>Create a new forest with domain name: <strong>mydomain.com</strong></li>
    <img src="https://i.imgur.com/x2Zc9ZI.png"/>
    <li>Set Directory Services Restore Mode (DSRM) password.</li>
    <img src="https://i.imgur.com/3E8k8OJ.png"/>
    <li>After configuration, the server will restart. Log back in using:  
      <code>mydomain.com\labuser</code></li>
    <img src="https://i.imgur.com/W4YQwAE.png"/>
  </ul>

  <h3>5. Create a Domain Admin User</h3>
  <ul>
    <li>Open “Active Directory Users and Computers” (ADUC).</li>
    <li>Right-click the domain &gt; New &gt; Organizational Unit (OU). Name it <strong>_ADMINS</strong>.</li>
    <img src="https://i.imgur.com/yNGjrzq.png"/>
    <li>Create another OU named <strong>_EMPLOYEES</strong> for standard users.</li>
    <img src="https://i.imgur.com/um8GTDg.png"/>
    <li>Right-click the _ADMINS OU &gt; New &gt; User. Name: <strong>jane_admin</strong>, set password: <code>Cyberlab123!</code></li>
    <img src="https://i.imgur.com/5VRIjCG.png"/>
    <li>Right-click the user &gt; Properties &gt; Member Of &gt; Add to “Domain Admins” group.</li>
    <img src="https://i.imgur.com/n1F4QhP.png"/>
    <li>Log out and log back in as <code>mydomain.com\jane_admin</code> to confirm elevated permissions.</li>
    <img src="https://i.imgur.com/yFzWpk2.png"/>
  </ul>

  <h3>6. Join Client-1 to the Domain</h3>
  <ul>
    <li>Log into Client-1 as local user <code>labuser</code>.</li>
    <li>Open Settings &gt; System &gt; About &gt; Rename this PC (Advanced).</li>
    <img src="https://i.imgur.com/VcwguHg.png"/>
    <li>Click “Change” and select “Domain.” Enter <strong>mydomain.com</strong> and authenticate with jane_admin credentials.</li>
    <img src="https://i.imgur.com/otRtNys.png"/>
    <img src="https://i.imgur.com/lTVUo0O.png"/>
    <li>Restart Client-1 after the domain join is complete.</li>
    <li>Log in with <code>mydomain.com\jane_admin</code> or any other domain user.</li>
    <li>From DC-1, open ADUC and verify Client-1 is listed under “Computers.” Move it to a new OU called <strong>_CLIENTS</strong>.</li>
    <img src="https://i.imgur.com/iSaNS84.png"/>
  </ul>

  <h3>7. Enable Remote Desktop for Domain Users</h3>
  <ul>
    <li>Log into Client-1 with a domain admin account.</li>
    <li>Go to System Properties &gt; Remote &gt; Allow remote connections.</li>
    <img src="https://i.imgur.com/5ZoBLrl.png"/>
    <li>Click “Select Users” and add “Domain Users” to grant RDP access to all domain accounts.</li>
    <img src="https://i.imgur.com/gd6XICC.png"/>
  </ul>

  <h3>8. Create Additional Users with PowerShell</h3>
  <ul>
    <li>On DC-1, log in as <code>jane_admin</code> and open PowerShell ISE as Administrator.</li>
    <li>Create a CSV file with a list of user details (e.g., first name, last name, username).</li>
    <li>Write a script that loops through the CSV and uses <code>New-ADUser</code> to add users to the _EMPLOYEES OU.</li>
    <img src="https://i.imgur.com/WWHLVbL.png"/>
    <li>Example PowerShell snippet:</li>
  </ul>
  <pre style="background-color: #f4f4f4; padding: 10px; border-left: 4px solid #ccc;">
 # ----- Edit these Variables for your own Use Case ----- #
$PASSWORD_FOR_USERS   = "Password1"
$NUMBER_OF_ACCOUNTS_TO_CREATE = 10000
# ------------------------------------------------------ #

Function generate-random-name() {
    $consonants = @('b','c','d','f','g','h','j','k','l','m','n','p','q','r','s','t','v','w','x','z')
    $vowels = @('a','e','i','o','u','y')
    $nameLength = Get-Random -Minimum 3 -Maximum 7
    $count = 0
    $name = ""

    while ($count -lt $nameLength) {
        if ($($count % 2) -eq 0) {
            $name += $consonants[$(Get-Random -Minimum 0 -Maximum $($consonants.Count - 1))]
        }
        else {
            $name += $vowels[$(Get-Random -Minimum 0 -Maximum $($vowels.Count - 1))]
        }
        $count++
    }

    return $name

}

$count = 1
while ($count -lt $NUMBER_OF_ACCOUNTS_TO_CREATE) {
    $fisrtName = generate-random-name
    $lastName = generate-random-name
    $username = $fisrtName + '.' + $lastName
    $password = ConvertTo-SecureString $PASSWORD_FOR_USERS -AsPlainText -Force

    Write-Host "Creating user: $($username)" -BackgroundColor Black -ForegroundColor Cyan
    
    New-AdUser -AccountPassword $password `
               -GivenName $firstName `
               -Surname $lastName `
               -DisplayName $username `
               -Name $username `
               -EmployeeID $username `
               -PasswordNeverExpires $true `
               -Path "ou=_EMPLOYEES,$(([ADSI]`"").distinguishedName)" `
               -Enabled $true
    $count++
}
  </pre>
  <ul>
    <li>Verify the new accounts in ADUC and test logging in to Client-1 with one of them.</li>
    <img src="https://i.imgur.com/UbVFYj1.png"/>
    <img src="https://i.imgur.com/HcGOfMW.png"/>
    <img src="https://i.imgur.com/lMTbyJo.png"/>
  </ul>
  <img src="https://via.placeholder.com/800x400?text=PowerShell+User+Script" width="100%" />

  <br />
  <p align="center"><em>Project complete. You now have a fully operational Active Directory lab environment in Azure, complete with domain controller, client machine, OUs, domain users, and PowerShell automation.</em></p>

</body>
