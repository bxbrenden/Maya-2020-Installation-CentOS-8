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
![Maya starts after installing dependencies]()

18. Click `Single User` to register license
Maya crashes with an error:
```bash
[brenden@errmac Packages]$ maya
maya: Autodesk Maya 2020
A licensing error occurred that Autodesk systems were not able to handle for you. Please contact Autodesk Customer Support for help in resolving this error.
adlsdkAuthorize returned with error code: ADLSDK_STATUS_LICENSE_CHECKOUT_ERROR
The default location for log files to help diagnose the issue is: /usr/tmp
```
