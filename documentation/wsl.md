# Configure WSL for the project

## 1. Install WSL 2

To install WSL 2 and Ubuntu 20.04, Open PowerShell as Administrator and run:

``` powershell
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```
Download the following package for your system and run it : 

> **x64 :**
>
> https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi

> **arm64 :**
>
> https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_arm64.msi

Configure your WSL version 2 by default :

``` powershell
wsl --set-default-version 2
```

### Download Ubuntu 20.04

<span style="color:red">If you already had an old version of ubuntu, uninstall it!</span>

To get ubuntu 20.04, download it from the Windows Store **OR** run these commands in PowerShell :

``` powershell
Invoke-WebRequest -Uri https://aka.ms/wslubuntu2004 -OutFile Ubuntu.appx -UseBasicParsing
Add-AppxPackage .\Ubuntu.appx
```

If the installation above is complete, left click on Windows icon. Select Ubuntu 20.04 LTS and continue with the configuration. Provide the username and password when prompted.

When ubuntu is running, check you have the correct version using the following command in your powershell :

``` powershell
wsl -l -v
```
For more information, see [Ubuntu 20.04 installation](https://ripon-banik.medium.com/how-to-install-wsl2-offline-b470ab6eaf0e)

To ensure having no problem afterwards, open ubuntu as administrator.

## 2. Configure WSL

### Override $PATH variable

To prevent any mismatch between Linux and Windows depedencies, add thes lines in your ```/etc/wsl.conf``` (if it doesn't exist, create it) :

```sh
[interop]
enabled = false # enable launch of Windows binaries; default is true
appendWindowsPath = false # append Windows path to $PATH variable; default is true
```

Then, restart your WSL.

> **OR**

If you want to be sure the ```$PATH``` has no Windows depencies, run the following.

**Run the following only once !**

The command will add this line ``` echo $PATH | tr ':' '\n' | grep -v /mnt/ | tr '\n' ':'` ``` to ```~/.bashrc``` file. This line removes all the Windows references from the ```$PATH```.
``` bash
$ echo "export PATH=`echo $PATH | tr ':' '\n' | grep -v /mnt/ | tr '\n' ':'`" >> ~/.bashrc
```

If you want it to be effective, restart your WSL or run the following
``` bash
$ export PATH=`echo $PATH | tr ':' '\n' | grep -v /mnt/ | tr '\n' ':'`
```

> Check the windows references are gone

Check that your PATH does not contain any Windows references. They always sart with ```/mnt/```
``` bash
$ echo $PATH
```

### WSL memory quota

There should be a file .wslconfig at `C:\Users\{yourUsername}`. Make sure it contains these informations (if your computer has 16GB of RAM):
```
[wsl2]
memory=12GB #Limits VM memory in WSL 2 to 12GB
Processors=5 #Makes the WSL 2 VM use two virtual Processors
swap=0
```

 #### Docker
- *Docker installation*

From WSL bash, run the following commands to setup Docker.

``` bash
# update the package manager and install some prerequisites (all of these aren't technically required)
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common libssl-dev libffi-dev git wget nano

# create a group named docker and add yourself to it
#   so that we don't have to type sudo docker every time
#   note you will need to logout and login before this takes affect (which we do later)
sudo groupadd docker
sudo usermod -aG docker ${USER}

# add Docker key and repo
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

# update the package manager with the new repos
sudo apt-get update

# install docker
sudo apt-get install -y docker-ce containerd.io

# install latest version of docker compose
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose && sudo chmod +x /usr/local/bin/docker-compose

```
Verify the installation with :
``` bash
docker version
docker-compose -v
```

Fore more information, see [Docker installation with Ubuntu 20.04](https://dev.to/bartr/install-docker-on-windows-subsystem-for-linux-v2-ubuntu-5dl7)

If you have already install WSL 1 and have some error installing WSL 2, check this documentation : [WSl 2 terminal wont start](https://github.com/microsoft/WSL/issues/5912)

To launch docker, execute in WSL :
``` bash
sudo service docker start
```

OR 

execute in a new window of WSL bash:
``` bash
sudo dockerd
```
