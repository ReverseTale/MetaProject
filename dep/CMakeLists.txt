include(ExternalProject)

set(DEP_BASE ${CMAKE_BINARY_DIR}/dep)
SET_PROPERTY(DIRECTORY PROPERTY EP_BASE ${DEP_BASE})

if (${ReverseTale_DEV})
    if ("${ReverseTale_INSTALL_BASE}" STREQUAL "")
        set(ReverseTale_INSTALL_BASE "" CACHE PATH "Dependencies Install directory")
        message(FATAL_ERROR "ReverseTale_INSTALL_BASE Should be set")
    elseif ("${ReverseTale_INSTALL_BASE}" MATCHES "${PROJECT_SOURCE_DIR}/")
        message(FATAL_ERROR "ReverseTale_INSTALL_BASE Should be set outside of ${PROJECT_SOURCE_DIR}")
    endif()
elseif ("${ReverseTale_INSTALL_BASE}" STREQUAL "")
    set(ReverseTale_INSTALL_BASE    ${DEP_BASE})
endif()

set(LIBBSON_DIR         ${ReverseTale_INSTALL_BASE}/Dependencies/EP_libbson          CACHE INTERNAL "")
set(LIBMONGOC_DIR       ${ReverseTale_INSTALL_BASE}/Dependencies/EP_mongo-c-driver   CACHE INTERNAL "")
set(MONGOCXX_DIR        ${ReverseTale_INSTALL_BASE}/Dependencies/EP_mongo-cxx-driver)
set(CPPFORMAT_DIR       ${ReverseTale_INSTALL_BASE}/Dependencies/EP_cppformat)
set(INIH_DIR            ${ReverseTale_INSTALL_BASE}/Dependencies/EP_inih)
set(THREADPOOL11_DIR    ${ReverseTale_INSTALL_BASE}/Dependencies/EP_threadpool11)
set(XHACKING_DIR        ${ReverseTale_INSTALL_BASE}/Dependencies/EP_xHacking)

if (NOT ${USE_SYSTEM_LIBBSON})
    ExternalProject_Add(
        EP_libbson
        GIT_REPOSITORY https://github.com/mongodb/libbson.git
        GIT_TAG r1.3
        INSTALL_DIR ${LIBBSON_DIR}
        LOG_DOWNLOAD ON
        LOG_CONFIGURE ON
        LOG_BUILD ON
        LOG_INSTALL ON
        LOG_UPDATE ON
        UPDATE_COMMAND ""
        CMAKE_ARGS -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE} -DENABLE_TESTS=OFF -DCMAKE_INSTALL_PREFIX:PATH=${LIBBSON_DIR} -DINCLUDE_INSTALL_DIR=.
    )

    ExternalProject_Add_Step(
        EP_libbson
        EP_libbson-fix-libbson-alignment
        WORKING_DIRECTORY ${DEP_BASE}/Source/EP_libbson
        COMMAND git stash && git apply --ignore-whitespace ${CMAKE_SOURCE_DIR}/cmake/Patches/04-libbson.patch
        DEPENDEES download
    )

    Add2Set("${CMAKE_PREFIX_PATH}" ${LIBBSON_DIR} CMAKE_PREFIX_PATH)
else()
    add_custom_target(EP_libbson)
endif()

if (NOT ${USE_SYSTEM_MONGOC})
    ExternalProject_Add(
        EP_mongo-c-driver
        GIT_REPOSITORY https://github.com/mongodb/mongo-c-driver.git
        GIT_TAG r1.3
        INSTALL_DIR ${LIBMONGOC_DIR}
        LOG_DOWNLOAD ON
        LOG_CONFIGURE ON
        LOG_BUILD ON
        LOG_INSTALL ON
        LOG_UPDATE ON
        UPDATE_COMMAND ""
        CMAKE_ARGS -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE} -DBSON_ROOT_DIR=${LIBBSON_DIR} -DENABLE_TESTS=OFF -DENABLE_EXAMPLES=OFF -DCMAKE_INSTALL_PREFIX:PATH=${LIBMONGOC_DIR} -DINCLUDE_INSTALL_DIR=.
        DEPENDS EP_libbson
    )

    Add2Set("${CMAKE_PREFIX_PATH}" ${LIBMONGOC_DIR} CMAKE_PREFIX_PATH)
else()
    add_custom_target(EP_mongo-c-driver)
endif()

if (NOT ${USE_SYSTEM_MONGOCXX})
    ExternalProject_Add(
        EP_mongo-cxx-driver
        GIT_REPOSITORY https://github.com/mongodb/mongo-cxx-driver.git
        GIT_TAG r3.0.1
        INSTALL_DIR ${MONGOCXX_DIR}
        LOG_DOWNLOAD ON
        LOG_CONFIGURE ON
        LOG_BUILD ON
        LOG_INSTALL ON
        LOG_UPDATE ON
        UPDATE_COMMAND ""
        CMAKE_ARGS -DBOOST_ROOT=${BOOST_ROOT} -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE} -DLIBBSON_DIR=${LIBBSON_DIR} -DLIBMONGOC_DIR=${LIBMONGOC_DIR} -DENABLE_TESTS=OFF -DENABLE_EXAMPLES=OFF -DCMAKE_INSTALL_PREFIX:PATH=${MONGOCXX_DIR} -DINCLUDE_INSTALL_DIR=.
        DEPENDS EP_mongo-c-driver
    )

    ExternalProject_Add_Step(
        EP_mongo-cxx-driver
        EP_mongo-cxx-driver-fix-find-bson
        WORKING_DIRECTORY ${DEP_BASE}/Source/EP_mongo-cxx-driver/cmake
        COMMAND git stash && git apply --ignore-whitespace ${CMAKE_SOURCE_DIR}/cmake/Patches/03-mongocxx.patch
        DEPENDEES download
    )

    Add2Set("${CMAKE_PREFIX_PATH}" ${MONGOCXX_DIR} CMAKE_PREFIX_PATH)
    Add2Set("${CMAKE_MODULE_PATH}" "${DEP_BASE}/Source/EP_mongo-cxx-driver/cmake" CMAKE_MODULE_PATH)
else()
    add_custom_target(EP_mongo-cxx-driver)
endif()

ExternalProject_Add(
    EP_cppformat
    GIT_REPOSITORY https://github.com/cppformat/cppformat.git
    INSTALL_DIR ${CPPFORMAT_DIR}
    LOG_DOWNLOAD ON
    LOG_CONFIGURE ON
    LOG_BUILD ON
    LOG_INSTALL ON
    LOG_UPDATE ON
    UPDATE_COMMAND ""
    CMAKE_ARGS -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE} -DFMT_DOC=OFF -DFMT_TEST=OFF -DCMAKE_INSTALL_PREFIX:PATH=${CPPFORMAT_DIR} -DINCLUDE_INSTALL_DIR=.
)
Add2Set("${CMAKE_PREFIX_PATH}" ${CPPFORMAT_DIR} CMAKE_PREFIX_PATH)


ExternalProject_Add(
    EP_inih
    GIT_REPOSITORY https://github.com/benhoyt/inih.git
    INSTALL_DIR ${INIH_DIR}
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
    COMMAND git stash && git clean -fx && git apply --ignore-whitespace ${CMAKE_SOURCE_DIR}/cmake/Patches/01-inih-cmake.patch
         && git apply --ignore-whitespace ${CMAKE_SOURCE_DIR}/cmake/Patches/02-inih-has_key.patch
    DEPENDEES download
)
Add2Set("${CMAKE_PREFIX_PATH}" ${INIH_DIR} CMAKE_PREFIX_PATH)


ExternalProject_Add(
    EP_threadpool11
    GIT_TAG 2.0-dev
    GIT_REPOSITORY https://github.com/tghosgor/threadpool11.git
    INSTALL_DIR ${THREADPOOL11_DIR}
    LOG_DOWNLOAD ON
    LOG_CONFIGURE ON
    LOG_BUILD ON
    LOG_INSTALL ON
    LOG_UPDATE ON
    UPDATE_COMMAND ""
    CMAKE_ARGS -DBOOST_ROOT=${BOOST_ROOT} -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE} -DCMAKE_INSTALL_PREFIX:PATH=${THREADPOOL11_DIR} -DINCLUDE_INSTALL_DIR=.
)

ExternalProject_Add_Step(
    EP_threadpool11
    EP_threadpool11-cmake-install
    WORKING_DIRECTORY ${DEP_BASE}/Source/EP_threadpool11
    COMMAND git stash && git apply --ignore-whitespace ${CMAKE_SOURCE_DIR}/cmake/Patches/06-threadpool11-cmake_install.patch
    DEPENDEES download
)
Add2Set("${CMAKE_PREFIX_PATH}" ${THREADPOOL11_DIR} CMAKE_PREFIX_PATH)


if (${ReverseTale_BUILD_XHACKING})
    ExternalProject_Add(
        EP_xHacking
        GIT_REPOSITORY https://github.com/gpascualg/xHacking.git
        INSTALL_DIR ${XHACKING_DIR}
        LOG_DOWNLOAD ON
        LOG_CONFIGURE ON
        LOG_BUILD ON
        LOG_INSTALL ON
        LOG_UPDATE ON
        CMAKE_ARGS -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE} -DCMAKE_INSTALL_PREFIX:PATH=${XHACKING_DIR} -DINCLUDE_INSTALL_DIR=.
    )
    Add2Set("${CMAKE_PREFIX_PATH}" ${XHACKING_DIR} CMAKE_PREFIX_PATH)
endif()

if (${ReverseTale_DEV})
    find_package(libmongocxx QUIET)

    if (NOT libmongocxx_FOUND)
        ExternalProject_Add_Step(
            EP_mongo-cxx-driver
            EP_mongo-cxx-driver-rerun-cmake
            WORKING_DIRECTORY ${PROJECT_BINARY_DIR}
            COMMAND ${CMAKE_COMMAND} ${PROJECT_SOURCE_DIR}
            DEPENDEES download
        )
    endif()
endif()
