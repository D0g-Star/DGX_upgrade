# Starting Point for DGX Upgrade:
*  DGX_OTA_VERSION : 4.6.0 
  *  DGX_PLATFORM: DGX Server for DGX-1
## Step 1: Prep for Upgrade: 
### 1. Rotating the GPG Keys for the DGX Software Stack
This step is only required if you are running DGX OS 5.2 and earlier.
#### 1.1 Download the updated dgx-repo-files tarball and extract its contents onto the root filesystem.
```
curl https://repo.download.nvidia.com/baseos/ubuntu/focal/dgx-repo-files.tgz | sudo tar xzf - -C
```
#### 1.2 Manually revoke the previous DGX and CUDA GPG keys.

```
sudo apt-key del 629C85F2
sudo apt-key del 7FA2AF80
```
OTA updates can now occur as normal.
#### Reference Documentation:
    *https://docs.nvidia.com/dgx/dgx-os-5-user-guide/upgrading.html#rotating-the-gpg-key-for-a-default-installation-or-after-reimaging



## Step 2. Upgrade DGX OS 4 to the Latest Version:
### 2.1 Download information from all configured sources about the latest versions of the packages.
```
sudo apt update
```

#### 2.2 Install all available upgrades for your current DGX OS release.
```
sudo apt -y full-upgrade
```
Note: 
  * Depending on which packages were updated when running sudo apt -y full-upgrade, you might be prompted to reboot the system before performing nvidia-release-upgrade.


#### 2.3 Install the nvidia-release-upgrade package for upgrading to the latest DGX OS 4 release.
```
sudo apt install -y nvidia-release-upgrade
```
### 2.4 Issue the following only if upgrading from DGX OS 4.99.
```
sudo apt install -y nvidia-fabricmanager-450/bionic-updates
--allow-downgrades
```

## Step 3: Performing the Release Upgrade to 5.XX: 

#### 3.1 Start the DGX OS release upgrade process.
```
sudo nvidia-release-upgrade
```
Note:
    * If you are using a proxy server, add the -E option to keep your proxy environment variables. For example:
```
sudo -E nvidia-release-upgrade
```
#### 3.2 Resolve conflicts.
Refer to Resolving Release Upgrade Conflicts for details and instructions.
* Note: https://docs.nvidia.com/dgx/dgx-os-5-user-guide/upgrading.html#resolving-release-upgrade-conflicts

#### 3.3 Wait for the upgrade process to complete, and press return or n at the prompt that appears when the system upgrade is completed.
#### 3.4 Check if nvidia-fabricmanager-<version>, libnvidia-nscq-<version>, or both packages need to be updated by running the following command.
```
dpkg -l | grep -E -i  'nscq|fabricmanager'
```
#### 3.5 If nvidia-fabricmanager-<version>, libnvidia-nscq-<version>, or both packages are installed, compare the installed versions with the NVIDIA Server Driver. 
```
dpkg -l | grep 'NVIDIA Server Driver metapackage'
```
#### 3.6 If the NVIDIA Server Driver version is newer than any of the two packages, the packages must be updated to the same major version as the NVIDIA Server Driver. For example,
```
apt install nvidia-fabricmanager-535 libnvidia-nscq-535
```
The system must be restarted to complete the update process and ensure that any changes are captured by restarted services and runtimes.

#### 3.7 Restart the system to complete the update process.
```
sudo reboot
```
#### 3.8. Verifying the Upgrade to version 5.
Confirm the Linux kernel version.

For example, when you upgrade to DGX OS 5.0, the Linux kernel version is at least 5.4.0-52-generic.
#####  3.8.1 For the minimum Linux kernel version of the release to which you are upgrading, refer to the release notes for that release.
##### 3.8.2  Confirm the NVIDIA Graphics Drivers for Linux version.
```
nvidia-smi
```

### 3.9 Recovering from an Interrupted or Failed Update
If the script is interrupted during the update, because of a loss of power or loss of network connection, depending on the issue, you need to restore power or restore the network connection.

If the system encounters a kernel panic after you restore power and reboot the DGX system, you cannot perform the over-the-network update. You need to reinstall DGX OS 5 with the latest image instead. See Reimaging.

This section provides information about how to install the DGX OS for instructions and complete the network update.

#### 3.9.1 Reconfigure the packages.

```
dpkg -a -configure
```
#### 3.9.2 Fix the broken package installs.

```
apt -f install -y
```
#### 3.9.3 Determine where the release-upgrader was extracted.
```
/tmp/ubuntu-release-upgrader-<random-string>
```
#### 3.9.4 Determine where the release-upgrader was extracted.
```
sudo bash
cd /tmp/ubuntu-release-upgrader-<random-string>
RELEASE_UPGRADER_ALLOW_THIRD_PARTY=1 ./focal --frontend=DistUpgradeViewText
```
#### 3.9.5 Do Not Reboot
#### 3.9.6 Issue the following command and reboot

```
bash /usr/bin/nvidia-post-release-upgrade
reboot
```
---
## Step 4: Preparation for Upgrading to Version 6.

### 4.1 Connect to the DGX System Console.
Connect to the console of the DGX system using a direct connection or a remote connection through the BMC.

Note: SSH can be used to perform the upgrade. However, if the Ethernet port is configured for DHCP, the IP address might change after the DGX server is rebooted during the upgrade, which results in the loss of connection. A loss of connection might also occur if you are connecting through a VPN. If this happens, connect by using a direct connection or through the BMC to continue the upgrade process. Warning: Connect directly to the DGX server console if the DGX is connected to a 172.17.xx.xx subnet.


### 4.2 Verifying the DGX System Connection to the Repositories.
Before you attempt to complete the update, you can verify that the network connection for your DGX system can access the public repositories and that the connection is not blocked by a firewall or proxy.

On the DGX system, enter the following:
```
wget -O f1-changelogs http://changelogs.ubuntu.com/meta-release-lts

wget -O f2-archive http://archive.ubuntu.com/ubuntu/dists/jammy/Release

wget -O f3-security http://security.ubuntu.com/ubuntu/dists/jammy/Release
wget -O f4-nvidia-baseos http://repo.download.nvidia.com/baseos/ubuntu/jammy/x86_64/dists/jammy/Release

wget -O f5-nvidia-cuda https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/Release

```
The wget commands should be successful, and there should be five files in the directory with non-zero content.


### 4.3 Upgrade DGX OS 5 to the Latest Version.
#### 4.3.1 Download information from all configured sources about the latest versions of the packages.

```
sudo apt update
```

#### 4.3.2 Download information from all configured sources about the latest versions of the packages.
```
sudo apt -y full-upgrade
```
Note: Depending on which packages were updated when running sudo apt -y full-upgrade, you might be prompted to reboot the system before performing nvidia-release-upgrade


### 4.3.3 Install the nvidia-release-upgrade package for upgrading to the latest DGX OS 5 release
```
sudo apt install -y nvidia-release-upgrade
```


## Step 5: Performing the Release Upgrade to 6.XX
 
### 5.1 Start the DGX OS release upgrade process.
```
sudo nvidia-release-upgrade
```
If you are using a proxy server, add the -E option to keep your proxy environment variables.
```
sudo -E nvidia-release-upgrade
```
### 5.2 Resolve conflicts.

Refer to Resolving Release Upgrade Conflicts for details and instructions
    * https://docs.nvidia.com/dgx/dgx-os-6-user-guide/upgrading-the-os.html#resolving-release-upgrade-conflicts

### 5.3 Wait for the upgrade process to complete and press y at the prompt that appears when the system upgrade is completed.
```
System upgrade is complete. Restart required To finish the upgrade, a
restart is required. If you select 'y' the system will be restarted.
Continue [yN]
```
Note: If no reboot prompt appeared or if you did not restart the system when prompted, then reboot to complete the update process.
```
sudo reboot
```
Notes:
    * https://docs.nvidia.com/dgx/dgx-os-6-user-guide/upgrading-the-os.html#resolving-release-upgrade-conflicts

