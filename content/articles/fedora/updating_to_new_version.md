+++
title = 'Updating Fedora'
draft = false
toc = true
type = 'docs'
+++

New versions of Fedora come out every six months (April and October).
It is safest to upgrade one version at a time, e.g. from Fedora 40 to Fedora 41.
Upgrading two versions at a time should also work but I have never tested it. Upgrading more than this is not recommended by Fedora so do it at your own risk. 

Several of these steps are unnecessary but I like to be extra careful and it should only take a couple of extra seconds.

Note that you cannot downgrade in Fedora, once you upgrade (step 4), the only way back is to do a clean installation.

#### Step 1
Reboot - not strictly necessary but I do it to be safe.

#### Step 2
Update your packages, refresh your dnf system and reboot (warning: the last line will automatically and instantly reboot your PC). 
I usually refresh after installing everything just to be on the safe side. It has never found anything the second time (nor should it), but it only takes a couple of seconds to run.

```bash
sudo dnf upgrade --refresh -y
sudo dnf upgrade --refresh -y
shutdown 0 -r
```

#### Step 3
Install the upgrade package and download the updated packages. You should upgrade to the next version number.

```bash
sudo dnf install dnf-plugin-system-upgrade
sudo dnf system-upgrade download --releasever=41
```

Check that the upgrade will allow you to continue. It can sometimes refuse unless you add --allowerasing to the second part of the command. This will uninstall some packages, probably from third party repos. It won't break your computer but may be useful. I usually wait until these have been resolved before upgrading.

### Step 4
Start upgrading. This will automatically reboot once or twice, without countdown or confirmation. When it is back, you will be on the new version.
```bash
sudo dnf system-upgrade reboot
```

### Step 5
You are now on the new version, this step is optional and will clean up your system. Some packages are retired at each version, but won't be removed. These packages won't receive any updates but will still be on your system. You can also remove any packages which are duplicated or not in use.
```bash
sudo dnf install remove-retired-packages -y
remove-retired-packages
sudo dnf remove --duplicates