# Preparation
On MAC:
- install ansible
```
brew install ansible
```
- please make sure that ansible is using python3
```
ansible --version | grep "python version"
```
- install python module
```
pip3 install "pywinrm>=0.3.0"
```
- install chocolatey collection
```
ansible-galaxy collection install chocolatey.chocolatey
```
- if you have macos big sur, please use the below
```
export OBJC_DISABLE_INITIALIZE_FORK_SAFETY=YES
```

On Windows:
- open 'Windows PowerShell' with administrator privileges
- create ansible user with administration privileges and make sure to use the same credentials in group_vars/all.yml file
```
NET USER ansible_user "password" /ADD
NET LOCALGROUP "Administrators" "ansible_user" /ADD
```
- Upgrading PowerShell and .NET Framework
```
$url = "https://raw.githubusercontent.com/jborean93/ansible-windows/master/scripts/Upgrade-PowerShell.ps1"
$file = "$env:temp\Upgrade-PowerShell.ps1"
$username = "ansible_user"
$password = "Password"

(New-Object -TypeName System.Net.WebClient).DownloadFile($url, $file)
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force

# Version can be 3.0, 4.0 or 5.1
&$file -Version 5.1 -Username $username -Password $password -Verbose
```

- WinRM Setup
```
$url = "https://raw.githubusercontent.com/ansible/ansible/devel/examples/scripts/ConfigureRemotingForAnsible.ps1"
$file = "$env:temp\ConfigureRemotingForAnsible.ps1"

(New-Object -TypeName System.Net.WebClient).DownloadFile($url, $file)

powershell.exe -ExecutionPolicy ByPass -File $file
```
- enable basic auth
```
Set-Item -Path WSMan:\localhost\Service\Auth\Basic -Value $true
winrm set winrm/config/client/auth '@{Basic="true"}'
winrm set winrm/config/service/auth '@{Basic="true"}'
winrm set winrm/config/service '@{AllowUnencrypted="true"}'
```

- check WinRM Listner
```
winrm enumerate winrm/config/Listener
```

- enable WinRM on firewall on port 5985
```
winrm quickconfig
```

- Set WinRM start mode to automatic
```
sc.exe config WinRM start= auto
```

- Verify start mode and state - it should be running
```
Get-WmiObject -Class win32_service | Where-Object {$_.name -like "WinRM"}
```

# HowTo
To run the ansible playbook 
```
ansible-playbook -i inventory main.yml
```


# Refs

https://docs.ansible.com/ansible/latest/user_guide/windows_setup.html

https://www.techbeatly.com/2020/12/configure-your-windows-host-to-manage-by-ansible.html#.YCzvcy0RqPx

https://docs.ansible.com/ansible/latest/collections/chocolatey/chocolatey/win_chocolatey_module.html