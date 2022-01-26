# hernoem server
Rename-Computer -NewName EP1-DC-ALFA

# hernoem netwerk adapters
Rename-NetAdapter -Name "Ethernet 2" -NewName "LAN"
Rename-NetAdapter -Name "Ethernet" -NewName "WAN"

# stel statisch IPaddres & dns in
Set-NetIPInterface -InterfaceAlias "LAN" -Dhcp Disabled
Remove-NetIPAddress -InterfaceAlias "LAN" -Confirm:$false
New-NetIPAddress -InterfaceAlias "LAN" -AddressFamily IPv4 -IPAddress "192.168.10.200" -PrefixLength 24 
Set-DnsClientServerAddress -InterfaceAlias "LAN" -ServerAddresses("192.168.10.200","8.8.8.8")

# installatie en configuratie AD DS
Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools 
Install-ADDSForest -DomainName "EP1-Brent.HoGent" -DomainNetbiosName "EP1-Brent" -NoRebootOnCompletion:$true -Force:$true 

# installatie DHCP
Install-WindowsFeature -Name DHCP -IncludeManagementTools -Confirm:$false
Add-DhcpServerv4Scope -Name "HoGent-EP1" -StartRange 192.168.10.100 -EndRange 192.168.10.150 -SubnetMask 255.255.255.0

# gedeelde map
New-Item "C:\SharedFolders" -Type Directory
New-SmbShare -Name "sharedFolder" -Path "C:\SharedFolders"

# Routing
Install-WindowsFeature -Name RemoteAccess
Install-WindowsFeature -Name RSAT-RemoteAccess-Powershell
Install-WindowsFeature -Name Routing