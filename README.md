# MetaProject
MetaProject for CMake, clones and builds all (selected) projects

## Building

This project uses CMake as a project/build generator. Proceed to your OS section

### Linux

If you are in Linux, the recommended build is by CMake + Ninja:
```
apt-get install git cmake ninja
```

Clone (recursively) the project

```
git clone --recursive https://github.com/ReverseTale/MetaProject.git
```
 
Create a folder inside MetaProject: `MetaProject/Build`

```
cd MetaProject && mkdir build
```

Now configure and compile with:

```
cmake ..
ninja
``` 

Proceed to `Workspace management` section.

### Windows

Install CMake and Visual Studio (Community Edition is free, minimum required is 2015).

Clone the project either using `git` or downloading the `zip` file from this page.

Using CMake GUI, set: 

> * Where is the source code: Path to `MetaProject`
> * Where to build the binaries: Path to `MetaProject/Build`
> * Press `[Configure]`
> * Press `[Generate]`
> * Inside `MetaProject/Build` you will have the MSVC project

Proceed to `Workspace management` section.

## Workspace management

Whenever you build the project, the resulting binaries can be found in the directory `MetaProject/install/bin`, specifically

```
MetaProject/install/bin/LoginServer
MetaProject/install/bin/GameServer
```

The recommended way to work is by using `Visual Studio Code`, either on Windows or Linux. To do so, **once the project is already built** (otherwise you will not see it) open the file `project.code-workspace` with `Visual Studio Code`. Once inside, you should already see 4 folders, one for each of the compomenents of this project.

To rebuild the project, once/if any modification has been done to any file, head back to your `build` directory and issue `ninja` again, ie:
```
cd MetaProject/build
ninja
```

Generated binaries will be updated on `MetaProject/install/bin/`.

Using Visual Studio Community Edition (or Pro versions) on Windows is, at the moment, work in project. There is no ETA and probably will be delayed for a long time.


Trobleshooting
--------------
##### Include could not find load file: `cmake-utils/*.cmake`

You did not clone with the `--recursive` option. Go to the `MetaProject` directory and run:

```
git submodule init
git submodule update --recursive
```

## Disclaimer

**The author can not be held liable for any use of this code. No binaries are distributed.**
