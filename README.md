# PowerShell SSH Brocade

Module and script collection for PowerShell to administrate Brocade switches over SSH

## Description

This module and collection of usefull scripts, allows you to administrate your Brocade switches over SSH-Protocol with the Windows PowerShell.

With the "Brocade"-Module you can establish a connection via SSH to one ore more Brocade switch devices and executing commands without receiving the following error message: `☺Protocol error, doesn't start with scp!`

I will constantly write new scripts (depends on what i/we need), to simplify tasks like backup to a TFTP-Server or to get and set VLANs.

## Requirements

You must have installed the following PowerShell-Module:

- [Posh-SSH](https://github.com/darkoperator/Posh-SSH) by darkoperator ([latest release](https://github.com/darkoperator/Posh-SSH/releases/latest))

## Tested devices (others may also work)

* ICX6430-24 & ICX6430-48
* FastIron WS 648

## Download and Install

The Module can be installed like every other PowerShell-Module. If you don't know how... follow this steps:

* Download the latest version of the module and all scripts from GitHub ([latest release](https://github.com/BornToBeRoot/PowerShell-SSH-Brocade/releases/latest))
* Copy the folder `Module\Brocade` in your profile under `C:\Users\%username%\Documents\WindowsPowerShell\Modules`
* Open a PowerShell-Console and import the "Brocade"-Module with the command `Import-Module Brocade`

The folder with scripts can be stored anywhere you want.

## Syntax (with examples) of the "Brocade"-Module

The following commands are available in the "Brocade"-Module. You can also use `Get-Help BROCADECOMMAND -Full` to get the syntax and examples.

### New-BrocadeSession

```powershell
New-BrocadeSession [-ComputerName] <String[]> [[-Credentials] <PSCredential>] [<CommonParameters>]
```

```PowerShell
> New-BrocadeSession -ComputerName TEST_DEVICE1


SessionID ComputerName Session        Stream
--------- ------------ -------        ------
        0 TEST_DEVICE1 SSH.SshSession Renci.SshNet.ShellStream
```

### Get-BrocadeSession

```powershell
Get-BrocadeSession [[-SessionID] <Int32[]>] [<CommonParameters>]

Get-BrocadeSession [[-ComputerName] <String[]>] [[-ExactMatch]] [<CommonParameters>]
```

```powershell
> Get-BrocadeSession -SessionID 0,2


SessionID ComputerName Session        Stream
--------- ------------ -------        ------
        0 TEST_DEVICE1 SSH.SshSession Renci.SshNet.ShellStream
	    2 TEST_DEVICE3 SSH.SshSession Renci.SshNet.ShellStream


> Get-BrocadeSession -ComputerName *TEST*

SessionID ComputerName Session        Stream
--------- ------------ -------        ------
        0 TEST_DEVICE1 SSH.SshSession Renci.SshNet.ShellStream
		1 TEST_DEVICE2 SSH.SshSession Renci.SshNet.ShellStream
	    2 TEST_DEVICE3 SSH.SshSession Renci.SshNet.ShellStream
```

### Invoke-BrocadeCommand

```powershell
Invoke-BrocadeCommand [-Session] <PSObject[]> [-Command] <String> [[-WaitTime] <Int32>] [<CommonParameters>]

Invoke-BrocadeCommand [-SessionID] <Int32[]> [-Command] <String> [[-WaitTime] <Int32>] [<CommonParameters>]
```

```powershell
> Invoke-BrocadeCommand -SessionID 0 -Command "sh clock" -WaitTime 500


ComputerName Result
------------ ------
TEST_DEVICE1 {sh clock, 16:55:07 GMT+01 Wed Mar 30 2016, SSH@TEST_DEVICE1#}


> (Get-BrocadeSession | Invoke-BrocadeCommand -Command "sh clock" -WaitTime 500).Result

sh clock
16:56:48 GMT+01 Wed Mar 30 2016
SSH@TEST_DEVICE1#
```

### Remove-BrocadeSession

```powershell
Remove-BrocadeSession [-Session] <PSObject[]> [<CommonParameters>]

Remove-BrocadeSession [-SessionID] <Int32[]> [<CommonParameters>]
```

```powershell
> Remove-BrocadeSession -SessionID 0

> Get-BrocadeSession | Remove-BrocadeSession
``` 

## Available scripts

* [Brocade-CopyConfigToTFTP.ps1](Scripts/Brocade-CopyConfigToTFTP.ps1) - Script to copy the running or startup config to a TFTP-Server. Useful as 
	automatic backup using windows task. ([view Doku](Scripts/Brocade-CopyConfigToTFTP.README.md))

## ChangeLog

### 30.03.2016
* Code improved
* Output of `Invoke-BrocadeCommand` changed --> Now returns a custom PSObject with ComputerName and Result
* Manage-Credentials.ps1 and ScanNetworkAsync.ps1 updated

### 12.02.2016
* Added Get-BrocadeVLAN Cmdlets in "Brocade"-Module
* Script and Module Improved
* Added Brocade-CopyConfigToTFTP.ps1 to scripts (Copy Running-/Starup-Config to a TFTP-Server) - Removed old RunningConfigToTFTP.ps1 from scripts
* Added Get-Help to the most of the Cmdlets/Scripts

## Inspired by

The basic idea to invoke commands in an ssh stream and wait for the result, comes from the contributions on StackOverflow and Reddit that describe the procedure:
- https://stackoverflow.com/questions/30603219/executing-command-using-paramiko-on-brocade-switch
- https://www.reddit.com/r/PowerShell/comments/3tgql4/poshssh_shell_reading/