﻿---
Order: 10
Title: Why Boxstarter?
---

# Why Boxstarter?

Boxstarter leverages [Chocolatey](https://chocolatey.org) packages to automate the installation of software and create repeatable, scripted Windows environments. Chocolatey makes installing software very easy with no user intervention. Boxstarter enhances Chocolatey's functionality and provides an environment that is optimized for installing a complete environment on a fresh OS install, as well as some other specific scenarios.

## Reboot Resiliency

Anyone who has installed much software in a Windows environment understands that, like it or not, reboots are often required to complete an installation. Sometimes an install will simply fail with an obscure error if a reboot is pending or other times, the installation will not be complete, until a reboot is performed. The primary objective of a Boxstarter installation, is to launch an environment installation no matter how complex, with no interruption. To achieve this, Boxstarter intercepts all Chocolatey install commands and checks for pending reboots. If a pending reboot is detected, Boxstarter will reboot the machine and automatically log the user back on and resume the installation. Boxstarter will even suspend Bitlocker, if enabled, to avoid prompts for the Bitlocker key upon reboot.

## Remote Installations

All of the features discussed here can be performed either locally or on a Remote machine. Whether it be a Virtual Machine or bare metal, Boxstarter uses a combination of PowerShell Remoting and Scheduled Tasks to allow one to fully configure a Windows environment on another machine. This is ideal for needing to repeatedly tear down and rebuild boxes with the same configuration.

While using Chocolatey in a PowerShell Remoting session will most often succeed, many installs leverage the Windows Update service under the hood for certain installation steps. These will fail in a remote PowerShell session. Boxstarter intercepts all calls to MSIEXEC, DISM and its own Windows Update command and wraps then in a scheduled task that an run with a local administrative token leading to a successful install.

## Integrates with Hyper-V and Windows Azure VMs

Boxstarter can run on a Hyper-V host and setup a Guest VM with no pre-configuration required. It can also be pointed to a Windows Azure VM to quickly provision your cloud servers. For both of these VM providers, Boxstarter can restore to a known saved checkpoint or create a new checkpoint before it executes. Even if your final target environment is a physical machine, working with VM snapshots is a great way to develop and test your ChocolateyInstall scripts! See [installing packages on a virtual machine](vmintegration) for details.

## Runs with Administrative Privileges

Many applications require administrative privileges to install. Chocolatey, for good reason, allows non administrative users to perform installs and will request administrative privileges throughout the installation whenever they are required. Since a Boxstarter installation must not interrupt the session once it begins, Boxstarter runs with administrative privileges the whole way through to avoid these interactive prompts.

## Reduce Installation Conflicts

Shortly after completing a new OS install, which is when a Boxstarter install often runs, there is a lot going on that can conflict with some application installs or cause them to fail.

### Suspend Windows Updates

Depending on the version of Windows installed, there may be well over 100 critical updates that Windows, by default, will immediately start downloading and installing. This consumes disk, processor and network resources that affect the performance of the installations you are trying to install and more importantly, it will cause many installations to terminate unsuccessfully upon finding that other installations are in progress.

### Detect other MSIEXEC processes

Before all MSI based installations, Boxstarter will look for other MSIEXEC processes and wait until those have completed before commencing with the Boxstarter package being installed. This prevents the same installation failures described above.

### Suspend System Center Client

For the same reasons that Boxstarter suspends Windows Updates, Boxstarter suspends the System Center Client which is responsible for installing software on a machine in many enterprise environments.

## Windows Customizations

Boxstarter includes a set of commands that perform a variety of windows customizations. These are largely registry key toggles that can:

- Adjust the task bar size
- Configure common Windows Explorer settings like showing hidden files and folders and more
- Turn off IE Enhanced security
- Enable Remote Desktop
- and many more...

## Check, download and install Windows Updates

Almost any environment install will want to include the installation of Windows Updates. At least those considered "critical." Boxstarter can perform these silently. With Boxstarter's reboot resiliency, it will reboot if the updates require one to complete and Boxstarter will keep checking for updates until all updates have installed without a call to reboot.

## Convenience commands to automate NuGet Package Creation

Boxstarter provides commands that can auto generate both the \*.nuspec file and provide a skeletal ChocolateyInstall.ps1 file. You can even point Boxstarter to a single script file and it will generate a \*.nupkg file that will execute that script.

## Testing Packages and Build Server Integration

Boxstarter can test [Chocolatey](https://chocolatey.org) package installs on one or more deployment targets and evaluate whether the installation was successful. It can also automate the publishing of packages to its [NuGet](https://nuget.org) feed. This functionality can be plugged into a Continuous Integration server so that packages can be tested and published when committed to version control.