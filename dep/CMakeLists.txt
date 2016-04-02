include(ExternalProject)

set(DEP_BASE ${CMAKE_BINARY_DIR}/dep)
SET_PROPERTY(DIRECTORY PROPERTY EP_BASE ${DEP_BASE})

set(BSON_DIR ${DEP_BASE}/Install/EP_libbson)
set(MONGOC_DIR ${DEP_BASE}/Install/EP_mongo-c-driver)
set(MONGOCXX_DIR ${DEP_BASE}/Install/EP_mongo-cxx-driver)
set(CPPFORMAT_DIR ${DEP_BASE}/Install/EP_cppformat)
set(INIH_DIR ${DEP_BASE}/Install/EP_inih)
set(THREADPOOL11_DIR ${DEP_BASE}/Install/EP_threadpool11)
set(XHACKING_DIR ${DEP_BASE}/Install/EP_xHacking)

if (NOT ${USE_SYSTEM_LIBBSON})
    ExternalProject_Add(
        EP_libbson
        GIT_REPOSITORY https://github.com/mongodb/libbson.git
        GIT_TAG r1.3
        LOG_DOWNLOAD ON
        LOG_CONFIGURE ON
        LOG_BUILD ON
        LOG_INSTALL ON
        LOG_UPDATE ON
        UPDATE_COMMAND ""
        CMAKE_ARGS -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE} -DENABLE_TESTS=OFF -DCMAKE_INSTALL_PREFIX:PATH=${BSON_DIR} -DINCLUDE_INSTALL_DIR=.
    )

    ExternalProject_Add_Step(
        EP_libbson
        EP_libbson-fix-libbson-alignment
        WORKING_DIRECTORY ${DEP_BASE}/Source/EP_libbson
        COMMAND git stash && git apply --whitespace=fix ${CMAKE_SOURCE_DIR}/cmake/Patches/04-libbson.patch
        DEPENDEES download
    )

    set(CMAKE_PREFIX_PATH "${CMAKE_PREFIX_PATH};${BSON_DIR}" CACHE INTERNAL "")
else()
    add_custom_target(EP_libbson)
endif()

if (NOT ${USE_SYSTEM_MONGOC})
    ExternalProject_Add(
        EP_mongo-c-driver
        GIT_REPOSITORY https://github.com/mongodb/mongo-c-driver.git
        GIT_TAG r1.3
        LOG_DOWNLOAD ON
        LOG_CONFIGURE ON
        LOG_BUILD ON
        LOG_INSTALL ON
        LOG_UPDATE ON
        UPDATE_COMMAND ""
        CMAKE_ARGS -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE} -DBSON_ROOT_DIR=${BSON_DIR} -DBSON_INCLUDE_DIR=${BSON_DIR}/include -DENABLE_TESTS=OFF -DENABLE_EXAMPLES=OFF -DCMAKE_INSTALL_PREFIX:PATH=${MONGOC_DIR} -DINCLUDE_INSTALL_DIR=.
        DEPENDS EP_libbson
    )

    set(CMAKE_PREFIX_PATH "${CMAKE_PREFIX_PATH};${MONGOC_DIR}" CACHE INTERNAL "")
else()
    add_custom_target(EP_mongo-c-driver)
endif()

if (NOT ${USE_SYSTEM_MONGOCXX})
    ExternalProject_Add(
        EP_mongo-cxx-driver
        GIT_REPOSITORY https://github.com/mongodb/mongo-cxx-driver.git
        GIT_TAG r3.0.1
        LOG_DOWNLOAD ON
        LOG_CONFIGURE ON
        LOG_BUILD ON
        LOG_INSTALL ON
        LOG_UPDATE ON
        UPDATE_COMMAND ""
        CMAKE_ARGS -DBOOST_ROOT=${BOOST_ROOT} -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE} -DLIBBSON_DIR=${BSON_DIR} -DLIBMONGOC_DIR=${MONGOC_DIR} -DENABLE_TESTS=OFF -DENABLE_EXAMPLES=OFF -DCMAKE_INSTALL_PREFIX:PATH=${MONGOCXX_DIR} -DINCLUDE_INSTALL_DIR=.
        DEPENDS EP_mongo-c-driver
    )

    ExternalProject_Add_Step(
        EP_mongo-cxx-driver
        EP_mongo-cxx-driver-fix-find-bson
        WORKING_DIRECTORY ${DEP_BASE}/Source/EP_mongo-cxx-driver/cmake
        COMMAND git stash && git apply --whitespace=fix ${CMAKE_SOURCE_DIR}/cmake/Patches/03-mongocxx.patch
        DEPENDEES download
    )

    set(CMAKE_PREFIX_PATH "${CMAKE_PREFIX_PATH};${MONGOCXX_DIR}" CACHE INTERNAL "")
else()
    add_custom_target(EP_mongo-cxx-driver)
endif()

ExternalProject_Add(
    EP_cppformat
    GIT_REPOSITORY https://github.com/cppformat/cppformat.git
    LOG_DOWNLOAD ON
    LOG_CONFIGURE ON
    LOG_BUILD ON
    LOG_INSTALL ON
    LOG_UPDATE ON
    UPDATE_COMMAND ""
    CMAKE_ARGS -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE} -DFMT_DOC=OFF -DFMT_TEST=OFF -DCMAKE_INSTALL_PREFIX:PATH=${CPPFORMAT_DIR} -DINCLUDE_INSTALL_DIR=.
)
set(CMAKE_PREFIX_PATH "${CMAKE_PREFIX_PATH};${CPPFORMAT_DIR}" CACHE INTERNAL "")


ExternalProject_Add(
    EP_inih
    GIT_REPOSITORY https://github.com/benhoyt/inih.git
    LOG_DOWNLOAD ON
    LOG_CONFIGURE ON
    LOG_BUILD ON
    LOG_INSTALL ON
    LOG_UPDATE ON
    UPDATE_COMMAND ""
    CMAKE_ARGS -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE} -DCMAKE_INSTALL_PREFIX:PATH=${INIH_DIR} -DINCLUDE_INSTALL_DIR=.
)

ExternalProject_Add_Step(
    EP_inih
    EP_inih-patches
    WORKING_DIRECTORY ${DEP_BASE}/Source/EP_inih
    COMMAND git stash && git clean -fx && git apply --whitespace=fix ${CMAKE_SOURCE_DIR}/cmake/Patches/01-inih-cmake.patch
         && git apply --whitespace=fix ${CMAKE_SOURCE_DIR}/cmake/Patches/02-inih-has_key.patch
    DEPENDEES download
)
set(CMAKE_PREFIX_PATH "${CMAKE_PREFIX_PATH};${INIH_DIR}" CACHE INTERNAL "")


ExternalProject_Add(
    EP_threadpool11
    GIT_TAG 2.0-dev
    GIT_REPOSITORY https://github.com/tghosgor/threadpool11.git
    LOG_DOWNLOAD ON
    LOG_CONFIGURE ON
    LOG_BUILD ON
    LOG_INSTALL ON
    LOG_UPDATE ON
    UPDATE_COMMAND ""
    CMAKE_ARGS -DBOOST_ROOT=${BOOST_ROOT} -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE} -DCMAKE_INSTALL_PREFIX:PATH=${THREADPOOL11_DIR} -DINCLUDE_INSTALL_DIR=.
)
set(CMAKE_PREFIX_PATH "${CMAKE_PREFIX_PATH};${THREADPOOL11_DIR}" CACHE INTERNAL "")

ExternalProject_Add_Step(
    EP_threadpool11
    EP_threadpool11-cmake-install
    WORKING_DIRECTORY ${DEP_BASE}/Source/EP_threadpool11
    COMMAND git stash && git apply --whitespace=fix ${CMAKE_SOURCE_DIR}/cmake/Patches/06-threadpool11-cmake_install.patch
    DEPENDEES download
)
set(CMAKE_PREFIX_PATH "${CMAKE_PREFIX_PATH};${INIH_DIR}" CACHE INTERNAL "")


if (${ReverseTale_BUILD_XHACKING})
    ExternalProject_Add(
        EP_xHacking
        GIT_REPOSITORY https://github.com/gpascualg/xHacking.git
        LOG_DOWNLOAD ON
        LOG_CONFIGURE ON
        LOG_BUILD ON
        LOG_INSTALL ON
        LOG_UPDATE ON
        CMAKE_ARGS -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE} -DCMAKE_INSTALL_PREFIX:PATH=${XHACKING_DIR} -DINCLUDE_INSTALL_DIR=.
    )
    set(CMAKE_PREFIX_PATH "${CMAKE_PREFIX_PATH};${XHACKING_DIR}" CACHE INTERNAL "")
endif()
