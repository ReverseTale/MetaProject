# MetaProject
MetaProject for CMake, clones and builds all (selected) projects

## Building

This project uses CMake as a project/make generator, all you need to do is:

* Clone the project https://github.com/ReverseTale/MetaProject.git
* Create a folder inside MetaProject: `MetaProject/Build`
* Open CMake and set: 
> * Where is the source code: Path to `MetaProject`
> * Where to build the binaries: Path to `MetaProject/Build`
* Press [Configure]
* Select what you want using the `ReverseTale_BUILD_*` options (ie. LoginServer + GameServer, or Bot)
* Press [Generate]
* Inside `MetaProject/Build` you will have the MSVC project (or Makefile if in UNIX)


Have fun!


## Disclaimer

**The author can not be held liable for any use of this code. No binaries are distributed.**
