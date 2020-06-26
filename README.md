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
![adsklicensing systemd service screenshot](https://github.com/bxbrenden/Maya-2020-Installation-CentOS-8/blob/master/adsklicensing.png)

9. Install ADLM Flexnet IPV6 Server
```bash
sudo yum install adlmflexnetserverIPV6-17.0.50-0.x86_64.rpm
```

10. Install ADLM Flexnet Client
```bash
sudo yum install adlmflexnetclient-17.0.49-0.x86_64.rpm
```

11. Install the Maya rpm
```bash
sudo yum install Maya2020_64-2020.0-235.x86_64.rpm
```

12. Verify Maya installation
```bash
[brenden@errmac Packages]$ /opt/Autodesk/AdskLicensing/9.2.1.2399/helper/AdskLicensingInstHelper list
[
  {
    "feature_id": "MAYA",
    "def_prod_key": "<REDACTED>",
    "def_prod_ver": "2020.0.0.F",
    "sel_prod_key": "<REDACTED>",
    "sel_prod_ver": "2020.0.0.F",
    "supported_lic_methods": [
      2,
      1,
      4
    ],
    "lic_servers": [
      ""
    ],
    "serial_number_sa": "000-00000000",
    "serial_number_nw": "000-00000000",
    "def_prod_code": "MAYA",
    "sel_prod_code": "MAYA"
  }
]
```

13. Install Dependencies
```bash
sudo yum install xorg-x11-fonts-ISO8859-1-100dpi
sudo yum install xorg-x11-fonts-ISO8859-1-75dpi 
sudo dnf install epel-release.noarch   #yum is just a symlink to dnf
sudo dnf makecache
sudo yum install audiofile audiofile-devel
```

13. Attempt to start Maya
Run fails due to missing `libpng15.so.15`.
```bash
[brenden@errmac Packages]$ which maya
/usr/local/bin/maya
[brenden@errmac Packages]$ maya
/usr/autodesk/maya2020/bin/maya.bin: error while loading shared libraries: libpng15.so.15: cannot open shared object file: No such file or directory
```

14. Install libpng15.so.15 and try to start Maya
Run fails due to missing `libGLU.so.1`
```bash
[brenden@errmac Packages]$ sudo yum install libpng15-1.5.30-7.el8.x86_64

[brenden@errmac Packages]$ maya
/usr/autodesk/maya2020/bin/maya.bin: error while loading shared libraries: libGLU.so.1: cannot open shared object file: No such file or directory
```

15. Install libGLU and try to start Maya
Run fails due to missing `libssl.so.10`
```bash
[brenden@errmac Packages]$ sudo yum install libpng15-1.5.30-7.el8.x86_64

[brenden@errmac Packages]$ maya
/usr/autodesk/maya2020/bin/maya.bin: error while loading shared libraries: libssl.so.10: cannot open shared object file: No such file or directory
```

16. Install libssl and try to start Maya
Run fails due to missing `libXp.so.6`
```bash
[brenden@errmac Packages]$ sudo yum install compat-openssl10-1:1.0.2o-3.el8.x86_64

[brenden@errmac Packages]$ maya
/usr/autodesk/maya2020/bin/maya.bin: error while loading shared libraries: libXp.so.6: cannot open shared object file: No such file or directory
```

17. Install libXp and try to start Maya
Maya starts!
```bash
[brenden@errmac Packages]$ sudo yum install libXp-1.0.3-3.el8.x86_64

[brenden@errmac Packages]$ maya
```
![Maya starts after installing dependencies](https://github.com/bxbrenden/Maya-2020-Installation-CentOS-8/blob/master/maya-starts.png)

18. Click `Single-User` to register license
Maya crashes with an error:
```bash
[brenden@errmac Packages]$ maya
maya: Autodesk Maya 2020
A licensing error occurred that Autodesk systems were not able to handle for you. Please contact Autodesk Customer Support for help in resolving this error.
adlsdkAuthorize returned with error code: ADLSDK_STATUS_LICENSE_CHECKOUT_ERROR
The default location for log files to help diagnose the issue is: /usr/tmp
```

19. Check logs for failure details

Contents of `/usr/tmp/MayaCLM-25-06-2020.log`:
```
2020-06-25 17:44:11: ====== BEGIN MAYA CLM LICENSE DIAGNOSTICS (level 2) ======
2020-06-25 17:44:11: INFO: MAYA VERSION: Autodesk Maya 2020
2020-06-25 17:44:11: INFO: Desktop Licensing Runtime Version: 2.6.3.31
2020-06-25 17:44:11: INFO: Desktop Licensing API version in Maya: 2.6.3.31
2020-06-25 17:44:11: INFO: initialize: GetInstance (Success)
2020-06-25 17:45:22: ====== BEGIN MAYA CLM LICENSE DIAGNOSTICS (level 2) ======
2020-06-25 17:45:22: INFO: MAYA VERSION: Autodesk Maya 2020
2020-06-25 17:45:22: INFO: Desktop Licensing Runtime Version: 2.6.3.31
2020-06-25 17:45:22: INFO: Desktop Licensing API version in Maya: 2.6.3.31
2020-06-25 17:45:22: INFO: initialize: GetInstance (Success)
2020-06-25 17:45:27: INFO: Received LicenseUpdateCallback for Maya 2020 (ADLSDK_UPDATE_REASON_LICENSE_UPDATE): NOT Authorized
2020-06-25 17:45:27: ERROR: Error Information: Maya 2020 : command execution error
2020-06-25 17:45:27: ERROR: adlsdkAuthorize returned with error code: ADLSDK_STATUS_LICENSE_CHECKOUT_ERROR
2020-06-25 17:45:27: ERROR: checkinLicense: unable to release authentication handle
2020-06-25 17:45:27: INFO: shutdown: normal exit
```

20. Click `enter a serial number` to try alternate route

Clicked `enter a serial number` and got an error:
```
Unable to initialize adlm.
Internal Error Message: <Service not installed.>
Error Code: <20>
```
![adlm init error](https://github.com/bxbrenden/Maya-2020-Installation-CentOS-8/blob/master/adlm-failed-init.png)

21. Read troubleshooting steps from Autodesk Forums
https://forums.autodesk.com/t5/installation-licensing/maya-2020-centos-7-7-unable-to-initialize-adlm/td-p/9223338

22. Try manual installation of Flexnet toolkit

Running the two scripts suggested in the [forum post](https://forums.autodesk.com/t5/installation-licensing/maya-2020-centos-7-7-unable-to-initialize-adlm/td-p/9223338) seems to indicate the Flexnet service is not running:
```bash
[brenden@errmac bin]$ sudo /opt/Autodesk/Adlm/FLEXnet/bin/toolkitinstall.sh

Checking system...
Configuring for Linux, Trusted Storage path /usr/local/share/macrovision/storage...
/usr/local/share/macrovision/storage already exists...
Setting permissions on /usr/local/share/macrovision/storage...
Permissions set...
Configuration completed successfully.

[brenden@errmac bin]$ sudo ./install_fnp.sh 
ERROR: Unable to locate anchor service to install, please specify correctly on command line
```

23. Try to run Flexnet service manually
Running the service returns the confusing error 'No such file or directory` even though it exists.

This [StackOverlfow post](https://stackoverflow.com/questions/2716702/no-such-file-or-directory-error-when-executing-a-binary) indicates there may be missing libraries.
```bash
[brenden@errmac bin]$ pwd
/opt/Autodesk/Adlm/FLEXnet/bin
[brenden@errmac bin]$ ls
FNPLicensingService  install_fnp.sh  toolkitinstall.sh
[brenden@errmac bin]$ sudo ./FNPLicensingService 
sudo: unable to execute ./FNPLicensingService: No such file or directory
[brenden@errmac bin]$ file FNPLicensingService 
FNPLicensingService: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-lsb-x86-64.so.3, for GNU/Linux 2.6.18, stripped
```

24. Try to find missing libraries
Using the `readelf -a` command and grepping (ignore case) for `request` finds that the `ld-lsb-x86-64.so.3` file is missing.
```bash
[brenden@errmac bin]$ readelf -a FNPLicensingService  | grep -i request
      [Requesting program interpreter: /lib64/ld-lsb-x86-64.so.3]
[brenden@errmac bin]$ file /lib64/ld-lsb-x86-64.so.3
/lib64/ld-lsb-x86-64.so.3: cannot open `/lib64/ld-lsb-x86-64.so.3' (No such file or directory)

[brenden@errmac bin]$ sudo dnf whatprovides "*/ld-lsb-x86-64.so.3"
Last metadata expiration check: 0:37:12 ago on Thu 25 Jun 2020 05:32:38 PM PDT.
redhat-lsb-core-4.1-47.el8.x86_64 : LSB Core module support
Repo        : AppStream
Matched from:
Filename    : /lib64/ld-lsb-x86-64.so.3
```

25. Install `redhat-lsb-core` and try again
```bash
[brenden@errmac bin]$ sudo dnf install redhat-lsb-core-4.1-47.el8.x86_64

[brenden@errmac bin]$ sudo ./FNPLicensingService 
FNPLicensingService -{rkhs}
   -r    Run the FNPLicensingService daemon
	-k    Kill the FNPLicensingService daemon
   -s    Print the status of the  FNPLicensingService daemon
   -h    Print help information

[brenden@errmac bin]$ sudo ./FNPLicensingService -r
Licensing Service daemon activated

[brenden@errmac bin]$ ps -ef | grep -i fnp
root       46841    6954  0 18:17 ?        00:00:00 ./FNPLicensingService -r
brenden    46864   16926  0 18:17 pts/0    00:00:00 grep --color=auto -i fnp
```
