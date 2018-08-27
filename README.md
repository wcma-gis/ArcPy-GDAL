# ArcPy-GDAL

Installing GDAL for ArcPy on Windows. GDAL can be difficult to configure for use within ArcPy, but it can be setup without too much trouble. You must use a 32bit installation matched to your Python Archetecture. Your ArcPy script also needs to reference the 32bit installation explicitly, and have the correct libraries installed.

The later of these points causes the most headaches. Often, users will have an 64 bit instance of GDAL installed on their system for PostGIS, QGis or other software where paths to GDAL are referenced in the systems Path. When referenced in the system path, ArcPy will use the system referenced GDAL version and will ultimatly fail with a dll error. The safest way to access the library is to setup your ArcPy environment to reference the correct files.

The steps are outlined below to get it working.

## Know your ArcPy

### MSVC (Compiler) Version
To determine the version of your python install, open the python window in ArcGIS and run `sys.version`. It will return something similar to`2.7.13 (v2.7.13:a06454b1afa1, Dec 17 2016, 20:42:59) [MSC v.1500 32 bit (Intel)]`. In this case, the version is `1500`.

### Archetecture
ArcPy uses 'win32', background processing uses 'x64' but thats not covered here.

### GDAL Version
You should use the most recent version of GDAL to benifit from bug fixes and features.

### Python Location
This can be found by opening the python window in ArcGIS and running 'sys.exec_prefix'

## Download
Download files from [GIS Internals](http://www.gisinternals.com/release.php), matching your `MSVC` and `Arch` versions. Ignore the mapserver suffix, thats not important to ArcPy.
Click on the approatirate link for your system `(release-{msvc}-gdal-{gdal_version}-mapserver-{mapserver_version})`
Download:
   * `gdal-{gdal_version}-{msvc}-core.msi`
   * `gdal-{gdal_version}-{msvc}-cfilegdb.msi` (Optional but recomended)
   * `GDAL-{gdal_version}.{arch}-py2.7.msi`

## Installation
Install gdal-{gdal_version}-{msvc}-core.msi to 'C:\Program Files (x86)\GDAL'
Install gdal-{gdal_version}-{msvc}-cfilegdb.msi
Install GDAL-{gdal_version}.{arch}-py2.7.msi, selecting the ArcGIS Python installation location during setup.

# Python Setup
In order for python to use gdal, some system variables need to be condigured for the instance of python.

```python
import os

def setupgdal():
	GDAL_PATH = "C:\\Program Files (x86)\\GDAL"
	if not os.path.isdir(GDAL_PATH):
		print("GDAL not found on system at {0}".format(GDAL_PATH))
		return False
	_environ = dict(os.environ)
	_environ["PATH"] = "{0};{1}".format(GDAL_PATH, _environ["PATH"])
	_environ["GDAL_DATA"] = "{0}\\gdal-data".format(GDAL_PATH)
	_environ["GDAL_PLUGINS"] = "{0}\\gdalplugins".format(GDAL_PATH)
	os.environ.clear()
	os.environ.update(_environ)
	try:
		import gdal
		print("GDAL load success [Version: {0}]".format(gdal.VersionInfo('VERSION_NUM')))
		return True
	except ImportError:
		print("GDAL Import Failed")
		return False
```
