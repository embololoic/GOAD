# GOAD (Game of Active Directory) Lab Setup

## Overview

Welcome!  
This repository provides step-by-step instructions and files to help you deploy a **GOAD (Game Of Active Directory)** lab environment, especially tailored for Windows users.

GOAD is an excellent tool for learning about Active Directory environments, attack techniques, and security practices, commonly used for cybersecurity training and CTFs.

## Features

- Adapted for Windows users (tested on Windows 10/11)
- Modified `goad.sh` and `Vagrantfile` for smoother deployment
- Troubleshooting guidance for common installation issues

## Prerequisites

- Windows 10/11 (other versions may work but are untested)
- [Vagrant](https://www.vagrantup.com/downloads)
- [VirtualBox](https://www.virtualbox.org/)
- [Git](https://git-scm.com/downloads)
- GitBash
- Basic knowledge of Windows command line

## Installation

1. **Clone the Repository**
    ```bash
    git clone https://github.com/yourusername/goad-windows-lab.git
    cd goad-windows-lab
    ```

2. **Install Prerequisites**
    - Make sure Vagrant, VirtualBox, and Git are installed and available in your PATH.

3. **Configuration**
    - Review and, if needed, adjust variables in the `Vagrantfile`.
    - Check the modified `goad.sh` script for Windows-specific tweaks.

4. **Deployment**
    ```bash
    vagrant up
    ```
    - The lab environment will be provisioned automatically.

5. **Accessing the Lab**
    - Follow the documentation in [`doc/lab_setup.md`](doc/lab_setup_english.md) for post-deployment steps and credentials.

## Documentation

- [Full step-by-step guide](doc/lab_setup.md)
- [Troubleshooting guide](doc/troubleshooting.md)
- [FAQ](doc/faq.md) *(if needed)*

## Support

If you encounter issues or have questions, feel free to open an Issue or contact me directly.

**Happy hacking and learning!**
