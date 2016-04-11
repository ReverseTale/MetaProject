if (NOT ${ReverseTale_DEV})
    include(ExternalProject)

    set(DEP_BASE ${CMAKE_BINARY_DIR}/src)
    SET_PROPERTY(DIRECTORY PROPERTY EP_BASE ${DEP_BASE})

    Add2Set("${CMAKE_PREFIX_PATH}" ${PROJECT_SOURCE_DIR} CMAKE_PREFIX_PATH)
    List2String("${CMAKE_PREFIX_PATH}" CMAKE_PREFIX_PATH_STRING)
    List2String("${CMAKE_MODULE_PATH}" CMAKE_MODULE_PATH_STRING)

    ExternalProject_Add(
        Library
        GIT_REPOSITORY https://github.com/ReverseTale/Library.git
    	GIT_SUBMODULES cmake-common
        SOURCE_DIR ${PROJECT_SOURCE_DIR}/src/Library
        INSTALL_DIR ${PROJECT_SOURCE_DIR}
        LOG_DOWNLOAD ON
        LOG_CONFIGURE ON
        LOG_BUILD ON
        LOG_INSTALL ON
        LOG_UPDATE ON
        CMAKE_ARGS
            -DBOOST_ROOT=${BOOST_ROOT}
            -DCMAKE_PREFIX_PATH=${CMAKE_PREFIX_PATH_STRING}
            -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE}
            -DCMAKE_INSTALL_PREFIX:PATH=${PROJECT_SOURCE_DIR} -DINCLUDE_INSTALL_DIR=.
    	DEPENDS EP_cppformat
    )

    if (${ReverseTale_BUILD_LOGINSERVER} OR ${ReverseTale_BUILD_GAMESERVER})
        ExternalProject_Add(
            CommonServer
            GIT_REPOSITORY https://github.com/ReverseTale/Common-Server.git
        	GIT_SUBMODULES cmake-common
            SOURCE_DIR ${PROJECT_SOURCE_DIR}/src/Common-Server
            INSTALL_DIR ${PROJECT_SOURCE_DIR}
            LOG_DOWNLOAD ON
            LOG_CONFIGURE ON
            LOG_BUILD ON
            LOG_INSTALL ON
            LOG_UPDATE ON
            CMAKE_ARGS
                -DBOOST_ROOT=${BOOST_ROOT}
                -DLIBBSON_DIR=${LIBBSON_DIR}
                -DLIBMONGOC_DIR=${LIBMONGOC_DIR}
                -DCMAKE_PREFIX_PATH=${CMAKE_PREFIX_PATH_STRING}
                -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE}
                -DCMAKE_MODULE_PATH=${CMAKE_MODULE_PATH_STRING}
                -DCMAKE_INSTALL_PREFIX:PATH=${PROJECT_SOURCE_DIR} -DINCLUDE_INSTALL_DIR=.
        	DEPENDS EP_mongo-cxx-driver EP_threadpool11 EP_inih
        )
    endif()

    if (${ReverseTale_BUILD_LOGINSERVER})
        ExternalProject_Add(
            LoginServer
            GIT_REPOSITORY https://github.com/ReverseTale/Login-Server.git
        	GIT_SUBMODULES cmake-common
            SOURCE_DIR ${PROJECT_SOURCE_DIR}/src/Login-Server
            INSTALL_DIR ${PROJECT_SOURCE_DIR}
            LOG_DOWNLOAD ON
            LOG_CONFIGURE ON
            LOG_BUILD ON
            LOG_INSTALL ON
            LOG_UPDATE ON
            CMAKE_ARGS
                -DCMAKE_PREFIX_PATH=${CMAKE_PREFIX_PATH_STRING}
                -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE}
                -DCMAKE_MODULE_PATH=${CMAKE_MODULE_PATH_STRING}
                -DCMAKE_INSTALL_PREFIX:PATH=${PROJECT_SOURCE_DIR} -DINCLUDE_INSTALL_DIR=.
        	DEPENDS CommonServer
        )
    endif()

    if (${ReverseTale_BUILD_GAMESERVER})
        ExternalProject_Add(
            GameServer
            GIT_REPOSITORY https://github.com/ReverseTale/Game-Server.git
        	GIT_SUBMODULES cmake-common
            SOURCE_DIR ${PROJECT_SOURCE_DIR}/src/Game-Server
            INSTALL_DIR ${PROJECT_SOURCE_DIR}
            LOG_DOWNLOAD ON
            LOG_CONFIGURE ON
            LOG_BUILD ON
            LOG_INSTALL ON
            LOG_UPDATE ON
            CMAKE_ARGS
                -DCMAKE_PREFIX_PATH=${CMAKE_PREFIX_PATH_STRING}
                -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE}
                -DCMAKE_MODULE_PATH=${CMAKE_MODULE_PATH_STRING}
                -DCMAKE_INSTALL_PREFIX:PATH=${PROJECT_SOURCE_DIR} -DINCLUDE_INSTALL_DIR=.
        	DEPENDS CommonServer
        )
    endif()

    if (${ReverseTale_BUILD_BOT})
        ExternalProject_Add(
            Bot
            GIT_REPOSITORY https://github.com/ReverseTale/Bot.git
        	GIT_SUBMODULES cmake-common
            SOURCE_DIR ${PROJECT_SOURCE_DIR}/src/Bot
            INSTALL_DIR ${PROJECT_SOURCE_DIR}
            LOG_DOWNLOAD ON
            LOG_CONFIGURE ON
            LOG_BUILD ON
            LOG_INSTALL ON
            LOG_UPDATE ON
            CMAKE_ARGS
                -DCMAKE_PREFIX_PATH=${CMAKE_PREFIX_PATH_STRING}
                -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE}
                -DCMAKE_MODULE_PATH=${CMAKE_MODULE_PATH_STRING}
                -DCMAKE_INSTALL_PREFIX:PATH=${PROJECT_SOURCE_DIR} -DINCLUDE_INSTALL_DIR=.
        	DEPENDS CommonServer
        )
    endif()
else()

    find_package(libmongocxx QUIET)

    if (libmongocxx_FOUND)
        if (NOT EXISTS ${PROJECT_SOURCE_DIR}/src/Library/CMakeLists.txt)
            execute_process(
            	COMMAND git clone https://github.com/ReverseTale/Library.git --recursive
            	WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}/src
            	OUTPUT_QUIET
            	ERROR_QUIET
            )
        endif()

        add_subdirectory(Library)

        if (${ReverseTale_BUILD_LOGINSERVER} OR ${ReverseTale_BUILD_GAMESERVER})
            if (NOT EXISTS ${PROJECT_SOURCE_DIR}/src/Common-Server/CMakeLists.txt)
                execute_process(
                	COMMAND git clone https://github.com/ReverseTale/Common-Server.git --recursive
                	WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}/src
                	OUTPUT_QUIET
                	ERROR_QUIET
                )
            endif()

            add_subdirectory(Common-Server)
        endif()

        if (${ReverseTale_BUILD_LOGINSERVER})
            if (NOT EXISTS ${PROJECT_SOURCE_DIR}/src/Login-Server/CMakeLists.txt)
                execute_process(
                	COMMAND git clone https://github.com/ReverseTale/Login-Server.git --recursive
                	WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}/src
                	OUTPUT_QUIET
                	ERROR_QUIET
                )
            endif()

            add_subdirectory(Login-Server)
        endif()

        if (${ReverseTale_BUILD_GAMESERVER})
            if (NOT EXISTS ${PROJECT_SOURCE_DIR}/src/Game-Server/CMakeLists.txt)
                execute_process(
                	COMMAND git clone https://github.com/ReverseTale/Game-Server.git --recursive
                	WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}/src
                	OUTPUT_QUIET
                	ERROR_QUIET
                )
            endif()

            add_subdirectory(Game-Server)
        endif()

        if (${ReverseTale_BUILD_BOT})
            if (NOT EXISTS ${PROJECT_SOURCE_DIR}/src/Bot/CMakeLists.txt)
                execute_process(
                	COMMAND git clone https://github.com/ReverseTale/Bot.git --recursive
                	WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}/src
                	OUTPUT_QUIET
                	ERROR_QUIET
                )
            endif()

            add_subdirectory(Bot)
        endif()

    endif()

endif()
