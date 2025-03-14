# pyvicon-datastream-module
[![PyPI Downloads](https://img.shields.io/pypi/dm/pyvicon-datastream.svg?label=PyPI%20downloads)](
https://pypi.org/project/pyvicon-datastream/)
[![pyvicon-datastream](https://snyk.io/advisor/python/pyvicon-datastream/badge.svg)](/advisor/python/pyvicon-datastream)

This module provides a **platform independent** python 3 wrapper implementation over the Vicon Datastream SDK.

**IMPORTATNT NOTICE:**
This module is currently still in development. So far I only managed to run and compile it on:
- **ARM**: M1 MacBook Pro with macOS Ventura 13.2.1
- **ARM**: M1-Pro MacBook Pro with macOS Ventura 13.2.1
- **ARM**: RaspBerry Pi 4 (4 GB) with Ubuntu 22
- **x86**: some Laptop with Ubuntu 20
- **x86**: Windows 10 -> `ViconDataStreamSDK_CPP.dll` and `ViconDataStreamSDK_CPP.lib` are installed/copied to the wrong path! See section "*Common issues when compiling / building the module*".


### Background information
Why I created this module:
<br>
The python interface currently provided by Vicon itself (see https://docs.vicon.com/display/DSSDK111/Vicon+DataStream+SDK+Quick+Start+Guide+for+Python) only runs on Windows. Other solutions I found like the one from MathGaron https://github.com/MathGaron/pyvicon were only runnable on x86 and implied that you have to download a precompiled version (also there is currently no version available for ARM) of the library from the Vicon website and place it manually in your module.
This module ships with all that nescessary library sources and does the system specific compilation automatically on installation!



## Usage
### pyvicon_datastream
```python
import pyvicon_datastream as pv

VICON_TRACKER_IP = "10.0.108.3"
OBJECT_NAME = "My_Object"

vicon_client = pv.PyViconDatastream()
ret = vicon_client.connect(VICON_TRACKER_IP)

if ret != pv.Result.Success:
    print(f"Connection to {VICON_TRACKER_IP} failed")
else:
    print(f"Connection to {VICON_TRACKER_IP} successful")
```

### Tools / ObjectTracker
```python
from pyvicon_datastream import tools

VICON_TRACKER_IP = "10.0.108.3"
OBJECT_NAME = "My_Object"

mytracker = tools.ObjectTracker(VICON_TRACKER_IP)
while(True):
    pose = mytracker.get_pose(OBJECT_NAME)
    print(f"Position: {pose}")
    time.sleep(0.5)
```


## Build / Install the Module

### Common issues when compiling / building the module:
- You need a C and C++ compiler on your system!
  - **Ubuntu / Linux:** `sudo apt install gcc g++`
  - **MacOS:** install the XCode Developer Tools
  - **Windows:** Building windows wheels for Python 3.9 requires Microsoft Visual Studio 2022 (https://visualstudio.microsoft.com/vs/). Install the Visual Studio C++ Desktop Development components/tools (you will find installation info in the error message)

- fatal error: Python.h: No such file or directory
  - `sudo apt install python3-dev`

- **Windows:** ImportError: DLL load failed while importing _pyvicon_datastream_wrapper: Das angegebene Modul wurde nicht gefunden.
  Cause: `ViconDataStreamSDK_CPP.dll` and `ViconDataStreamSDK_CPP.lib` are installed/copied to the wrong path! Move them manually to module's folder:
  - `(venv)/bin/ViconDataStreamSDK_CPP.dll` => `(venv)/Lib/site-packages/pyvicon_datastream/ViconDataStreamSDK_CPP.dll`
  - `(venv)/Lib/ViconDataStreamSDK_CPP.lib` => `(venv)/Lib/site-packages/pyvicon_datastream/Lib/ViconDataStreamSDK_CPP.lib` (create a folder `Lib` inside `pyvicon_datastream`)


### The module ist available on PyPI!
`pip install pyvicon-datastream`

https://pypi.org/project/pyvicon-datastream/

### Local installation with sources
`pip install pyvicon-datastream-module/.`

For more information and output during install set the verbose flag: `-v`

`pip install pyvicon-datastream-module/. -v`

...somehow it's important to run the command from outside the main directory of the project (propably due to a naming conflict since inside the pyvicon-datastream-module folder there is (already) a folder called "pyvcion_datastream").

### Build / check
```
cd pyvicon-datastream-module
python -m build --sdist
twine check dist/*
```

## License Information
### PyVicon (wrapper implementation)
Files `pyvicon_datastream/pyvicon_datastream_wrapper.cpp` and `pyvicon_datastream/pyvicon_datastream.py` are from the project: https://github.com/MathGaron/pyvicon, a minimal python 3 wrapper implementation for the Vicon Datastream SDK.

License information see `pyvicon_datastream/LICENSE_PVICON_DATASTREAM`


### Vicon Datastream SDK
This Module ships with the Vicon Datastream SDK sources version 1.11.0.

Download Vicon Datastream SDK sources: https://github.com/whoenig/vicon-datastream-sdk

You can find the latest official version at https://vicon.com/downloads/utilities-and-sdk/datastream-sdk



## To Do's:
- WINDOWS ISSUE: It compiles on Windows, but the `ViconDataStreamSDK_CPP.dll` and `ViconDataStreamSDK_CPP.lib` are installed/copied to the wrong path! They should be under `(venv)/Lib/site-packages/pyvicon_datastream` but they end up in the venv's root folder. When moving the files manually to the correct location, the module works.
- Test on other Platforms: Windows
- Clean up Makefile
- Add examples
- Check if another source for downloading the Vicon Datastream SDK sources is available. At the moment the files are fenced behind a registration / email wall.
- Fix the "DOWNLOAD_EXTRACT_TIMESTAMP" warning / error message thrown by Cmake when fetching the boost lib sources zip from GitHub
