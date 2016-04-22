# MetaProject
MetaProject for CMake, clones and builds all (selected) projects

## Building

This project uses CMake as a project/make generator, all you need to do is:

* Recursively clone the project:

`git clone --recursive https://github.com/ReverseTale/MetaProject.git`
 
* Create a folder inside MetaProject: `MetaProject/Build`

`cd MetaProject && mkdir Build`

* Install Boost:
> * Ubuntu users may do: `apt-get install libboost-all-dev`
> * Windows users may download 1.60 prebuilt binaries from: https://sourceforge.net/projects/boost/files/boost-binaries/1.60.0/

See either `Binaries Only Build` if you only want the binaries or the `Source Code Build` section if you want your solution to include source code.

In case you have any error, in either of the two versions, refer to the `Trobleshooting` section!.


Binaries Only Build
------------------

1. Using the console:
> * To build the binaries-only version run:

`cd Build && cmake ..`

2. Using the GUI (windows only), set: 
> * Where is the source code: Path to `MetaProject`
> * Where to build the binaries: Path to `MetaProject/Build`
> * Press `[Configure]`
> * Press `[Generate]`
> * Inside `MetaProject/Build` you will have the MSVC project


Source Code Build
------------------

1. Using the console:
> * To build the source code version run:

`cd Build && cmake -DReverseTale_DEV=ON -DReverseTale_INSTALL_BASE=/some_path/outside/MetaProject ..`

2. Using the GUI (windows only), set: 
> * Where is the source code: Path to `MetaProject`
> * Where to build the binaries: Path to `MetaProject/Build`
> * Press `[Configure]`
> * Select the option `ReverseTale_DEV`
> * Enter a path, outside MetaProject folder, in `ReverseTale_INSTALL_BASE`
> * Press `[Generate]`
> * Inside `MetaProject/Build` you will have the MSVC project


Futher customizing the build
----------------------------
The CMake installer also comes with the following options:

* `ReverseTale_BUILD_BOT`: Defaults to true, whether to build the bot or not. Disable with:

  `-DReversTale_BUILD_BOT=OFF` or unchecking the option in the GUI
  
* `ReverseTale_BUILD_HEADLESS`: Defaults to true, whether to build the headless bot or not. Disable with:

  `-ReverseTale_BUILD_HEADLESS=OFF` or unchecking the option in the GUI
  
* `ReverseTale_BUILD_GAMESERVER`: Defaults to true, whether to build the gameserver or not. Disable with:

  `-ReverseTale_BUILD_GAMESERVER=OFF` or unchecking the option in the GUI
  
* `ReverseTale_BUILD_LOGINSERVER`: Defaults to true, whether to build the loginserver or not. Disable with:

  `-ReverseTale_BUILD_LOGINSERVER=OFF` or unchecking the option in the GUI
   
* `USE_SYSTEM_X`: Controls whether the library X is built (default) or searched in the system (if checked/enabled)


Trobleshooting
--------------
##### Include could not find load file: `cmake-common/Utils.cmake`

You did not clone with the `--recursive` option. Go to the `MetaProject` directory and run:

```
git submodule init
git submodule update --recursive
```

##### Building Boost is still not supported

You unset or disabled the `USE_SYSTEM_BOOST` option. As of now Boost can not be automatically built, reenable it with either:

1. If using the console: `-DUSE_SYSTEM_BOOST=ON`

2. If using the GUI: checking the `USE_SYSTEM_BOOST` field

##### Please set BOOST_ROOT to point your Boost installation

CMake could not find a valid Boost installation. If you have Boost on your system, set the `BOOST_ROOT` variable, either with:

1. If using the console: `-DBOOST_ROOT=/absolute_path`

2. If using the GUI: Setting the `BOOST_ROOT` field to the path


##### ReverseTale_INSTALL_BASE Should be set

You are trying to build the source code version with `ReverseTale_DEV` enabled but you did not provide a path in `ReverseTale_INSTALL_BASE`.

See the `Source Code Build` for how to do this.


##### ReverseTale_INSTALL_BASE Should be set outside of {...}

The path provided in `ReverseTale_INSTALL_BASE` can not be inside the same folder as the MetaProject cloned project. The following, for example would be invalid:

> Cloned in: `/home/sources/MetaProject`

> Building in: `/home/sources/MetaProject/Build`

> ReverseTale_INSTALL_BASE: `/home/sources/MetaProject/Install`

A valid value for `ReverseTale_INSTALL_BASE` might be, for example:

> Cloned in: `/home/sources/MetaProject`

> Building in: `/home/sources/MetaProject/Build`

> ReverseTale_INSTALL_BASE: `/home/sources/Install`

Because it is outside (is not a subfolder) of `/home/sources/MetaProject`

## Disclaimer

**The author can not be held liable for any use of this code. No binaries are distributed.**
