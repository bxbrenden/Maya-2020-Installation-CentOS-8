# Maya-2020-Installation-CentOS-8

## Autodesk Instructions Followed
- [Install Maya on Linux using the rpm Package](https://knowledge.autodesk.com/support/maya/troubleshooting/caas/CloudHelp/cloudhelp/2020/ENU/Installation-Maya/files/GUID-E7E054E1-0E32-4B3C-88F9-BF820EB45BE5-htm.html)
- [Installing Arnold for Maya on Linux](https://docs.arnoldrenderer.com/display/A5AFMUG/Installing+Arnold+for+Maya+on+Linux)

## Steps Attempted for Installation
1. Start with fresh CentOS 8 installation
2. Run:
```bash
sudo dnf update
```
3. Download MtoA / Arnold for Maya 2020 from [the official downloads page](https://www.arnoldrenderer.com/arnold/download/)

The downloaded file is named: 
```
MtoA-4.0.3.1-linux-2020.run
```

Its SHA256 checksum is:
```
5159d4bc5218d042a0cb227d287d77c28750f16d87e74e6da8d11ed454c9be15
```
4. Attempt to install Arnold for Maya 2020
```bash
[brenden@errmac Software]$ sudo sh MtoA-4.0.3.1-linux-2020.run 
Verifying archive integrity... All good.
Uncompressing MtoA for Linux Installer......
./unix_installer.sh: line 1: python: command not found
[brenden@errmac Software]$ which python
/usr/bin/which: no python in (/home/brenden/.local/bin:/home/brenden/bin:/home/brenden/.local/bin:/home/brenden/bin:/usr/local/bin:/usr/local/sbin:/usr/bin:/usr/sbin)
[brenden@errmac Software]$ which python3
/usr/bin/python3
[brenden@errmac Software]$ python3 --version
Python 3.6.8
```

This failed because Arnold for Maya 2020 is still using `python` 2.x which is EOL and not present on CentOS8.

Skipping Arnold installation and moving along.

5. Download Autodesk Maya 2020 Installer
The downloaded file is named:
```
Autodesk_Maya_2020_ML_Linux_64bit.tgz
``` 

Its SHA256 checksum is:
```
715260696f7cc626265a4ca53f86abbb3edce8e531866b7c35508408f3554335
```

6. Extract the Installer
```bash
tar xvf Autodesk_Maya_2020_ML_Linux_64bit.tgz

#lines removed for brevity

[brenden@errmac Maya]$ ls
Autodesk_Maya_2020_ML_Linux_64bit.tgz  manifest  MayaConfig.pit  ODIS  Packages  Setup  SetupRes  setup.xml

cd Packages
```

7. Install ADLM
```
sudo yum install adlmapps17-17.0.49-0.x86_64.rpm
```

8. Install ADSK Licensing 
```
sudo yum install adsklicensing9.2.1.2399-0-0.x86_64.rpm
```

This installs and configures a `systemd` service as seen below:
![adsklicensing systemd service screenshot]()
