
# GOAD (Graph of Active Directory) Lab Installation Guide

## 1. Prerequisites

### Host System

- Windows 10 or 11 (64-bit)

### Required Tools

1. **Git Bash**: [https://gitforwindows.org/](https://gitforwindows.org/)
2. **VirtualBox**: [https://www.virtualbox.org/](https://www.virtualbox.org/)
3. **Vagrant**: [https://www.vagrantup.com/](https://www.vagrantup.com/)
4. **Python** (version 3.8 and up, ideally 3.11): [https://www.python.org/downloads/](https://www.python.org/downloads/)

### Environment Variables

Ensure that Python and Vagrant are added to your `PATH` environment variable.  
If you use `poetry` (optional), make sure to manually add its environment variable if needed.

## 2. Cloning the GOAD Project

```bash
git clone https://github.com/Orange-Cyberdefense/GOAD.git
cd GOAD
```

## 3. Initializing the Python Environment

### The `goad.sh` Script

The `goad.sh` script is provided with the project. It allows you to:

- Create a Python virtual environment in `~/.goad/.venv`
- Install dependencies from `requirements.yml` or `requirements_311.yml`
- Launch `goad.py`

### Recommended `goad.sh` Modifications

Replace `source $venv/bin/activate` with `source $venv/Scripts/activate` if you’re on Windows.

Add logs for easier debugging:

```bash
echo "[INFO] Activating the virtual environment..."
echo "[INFO] Launching goad.py with arguments: $@"
```

### Example Execution in Git Bash

```bash
./goad.sh
```

**If a `ModuleNotFoundError` error appears (e.g. for `rich` or `jinja2`):**

```bash
source ~/.goad/.venv/Scripts/activate
pip install rich jinja2
```

## 4. Deploying the Virtual Machines

### Path to the Vagrantfile

```bash
cd GOAD/ad/GOAD/providers/virtualbox/
```

### Example `Vagrantfile` Content

```ruby
Vagrant.configure("2") do |config|
  ip_range = "192.168.56"

  boxes = [
    { :name => "GOAD-DC01",  :ip => "#{ip_range}.10", :box => "StefanScherer/windows_2019", :box_version => "2021.05.15", :cpus => 2, :mem => 3000 },
    { :name => "GOAD-DC02",  :ip => "#{ip_range}.11", :box => "StefanScherer/windows_2019", :box_version => "2021.05.15", :cpus => 2, :mem => 3000 },
    { :name => "GOAD-DC03",  :ip => "#{ip_range}.12", :box => "StefanScherer/windows_2016", :box_version => "2017.12.14", :cpus => 2, :mem => 3000 },
    { :name => "GOAD-SRV02", :ip => "#{ip_range}.22", :box => "StefanScherer/windows_2019", :box_version => "2021.05.15", :cpus => 2, :mem => 6000 },
    { :name => "GOAD-SRV03", :ip => "#{ip_range}.23", :box => "StefanScherer/windows_2016", :box_version => "2019.02.14", :cpus => 2, :mem => 5000 }
  ]

  boxes.each do |opts|
    config.vm.define opts[:name] do |vm_config|
      vm_config.vm.box = opts[:box]
      vm_config.vm.box_version = opts[:box_version]
      vm_config.vm.hostname = opts[:name]
      vm_config.vm.network "private_network", ip: opts[:ip]
      vm_config.vm.provider "virtualbox" do |vb|
        vb.name = opts[:name]
        vb.cpus = opts[:cpus]
        vb.memory = opts[:mem]
      end
    end
  end
end
```

### Starting the Machines

```bash
vagrant up             # starts all VMs
vagrant up GOAD-DC01   # starts only one VM
```

### Total Number of VMs

There are **5 virtual machines** to be deployed with the provided configuration.

## 5. Default VM Credentials

- **Username**: `vagrant`
- **Password**: `vagrant`

## 6. Troubleshooting: WinRM Error

Common error:

```
WSMAN ERROR CODE: 2147942421 - The device is not ready
```

This may be caused by a slow boot or incorrect WinRM configuration.

### Solution

Run the following commands in an Admin PowerShell session *inside the VM*:

```powershell
Set-Item -Path WSMan:\localhost\Service\AllowUnencrypted -Value $true
Set-Item -Path WSMan:\localhost\Service\Auth\Basic -Value $true
Restart-Service WinRM
```

## 7. Example Folder Structure

```
GOAD
├── goad.sh
├── goad.py
├── ad
    ├── GOAD
        ├── providers
            └── virtualbox
                ├── Vagrantfile
                ├── inventory
```

## 8. Launching GOAD

Once the VMs are deployed and Python is ready:

```bash
source ~/.goad/.venv/Scripts/activate
./goad.sh
```

For any other errors, feel free to note them in an additional "FAQ" section.
