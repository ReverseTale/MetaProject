cmake_minimum_required(VERSION 2.8.0)

# ---[ Latte project
project(MetaProject C CXX)

# Force prefix and third party
set(CMAKE_INSTALL_PREFIX ${CMAKE_SOURCE_DIR}/install CACHE INTERNAL "")
set(OVERRIDE_THIRD_PARTY ${CMAKE_SOURCE_DIR}/install CACHE INTERNAL "")

# ---[ Includes
set(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake-utils)

# -[ Macros and functions
include(Configure)
include(Macros)
include(External)
ResetAllTargets()

# -- [ Options
option(ReverseTale_BUILD_HEADLESS           "Build headless bot"        OFF)
option(ReverseTale_BUILD_BOT                "Build bot"                 OFF)
option(ReverseTale_BUILD_LOGINSERVER        "Build Login Server"        ON)
option(ReverseTale_BUILD_GAMESERVER         "Build Game Server"         ON)
option(ReverseTale_DEBUG                    "Debug library and tools"   ON)

if (ReverseTale_BUILD_BOT OR ReverseTale_BUILD_HEADLESS)
    message(FATAL_ERROR "Building bot (or headless bot) is disabled")
endif()

# --[ Build process
CreateTarget(TARGET MetaProject)

# -- [ Only add xHacking if building the bot
set(ReverseTale_BUILD_XHACKING ${ReverseTale_BUILD_BOT})

# ---[ ReversTale projects
add_subdirectory(dep)
