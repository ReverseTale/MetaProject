cmake_minimum_required(VERSION 2.8.0)

# ---[ Latte project
project(MetaProject C CXX)

# ---[ Install path
if(CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
    set (CMAKE_INSTALL_PREFIX "${PROJECT_BINARY_DIR}/install" CACHE PATH "Default install path" FORCE)
endif()

# -- [ Options
option(ReverseTale_BUILD_HEADLESS           "Build headless bot"        ON)
option(ReverseTale_BUILD_BOT                "Build bot"                 ON)
option(ReverseTale_BUILD_LOGINSERVER        "Build NosTale Server"      ON)
option(ReverseTale_BUILD_GAMESERVER         "Build NosTale Server"      ON)
option(ReverseTale_DEBUG                    "Debug library and tools"   ON)

# -- [ System libs options
option(USE_SYSTEM_LIBBSON                   "Use system LibBSON"        OFF)
option(USE_SYSTEM_MONGOC                    "Use system MongoC"         OFF)
option(USE_SYSTEM_MONGOCXX                  "Use system MongoCXX"       OFF)
option(USE_SYSTEM_BOOST                     "Use system Boost"          ON)

# -- [ If using system Boost, assert it is there
if (${USE_SYSTEM_BOOST})
    find_package(Boost QUIET)
    if (NOT ${Boost_FOUND})
        set(BOOST_ROOT ${Boost_FOUND} CACHE PATH "System boost path")
        mark_as_advanced(Boost_DIR)
        message(FATAL_ERROR "Please set BOOST_ROOT to point your Boost installation")
    endif()
else()
    message(FATAL_ERROR "Building Boost is still not supported")
endif()

# -- [ Only add xHacking if building the bot
set(ReverseTale_BUILD_XHACKING ${BUILD_BOT})

# ---[ Dependencies external projects
add_subdirectory(dep)
