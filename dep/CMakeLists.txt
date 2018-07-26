
# RequireExternal(
#     TARGET MetaProject
#     EXCLUDE
#     SKIP_BUILD
#     MODULE "g-truc/glm:0.9.8"
#     INC_PATH "."
# )

# set(DEP_BASE ${CMAKE_BINARY_DIR}/dep)
# SET_PROPERTY(DIRECTORY PROPERTY EP_BASE ${DEP_BASE})

# if (${ReverseTale_DEV})
#     if ("${ReverseTSKIP_BUILDale_INSTALL_BASE}" STREQUAL "")
#         set(ReverseTale_INSTALL_BASE "" CACHE PATH "Dependencies Install directory")
#         message(FATAL_ERROR "ReverseTale_INSTALL_BASE Should be set")
#     elseif ("${ReverseTale_INSTALL_BASE}" MATCHES "${PROJECT_SOURCE_DIR}/")
#         message(FATAL_ERROR "ReverseTale_INSTALL_BASE Should be set outside of ${PROJECT_SOURCE_DIR}")
#     endif()
# elseif ("${ReverseTale_INSTALL_BASE}" STREQUAL "")
#     set(ReverseTale_INSTALL_BASE    ${DEP_BASE})
# endif()

# set(LIBBSON_DIR         ${ReverseTale_INSTALL_BASE}/Dependencies/EP_libbson          CACHE INTERNAL "")
# set(LIBMONGOC_DIR       ${ReverseTale_INSTALL_BASE}/Dependencies/EP_mongo-c-driver   CACHE INTERNAL "")
# set(MONGOCXX_DIR        ${ReverseTale_INSTALL_BASE}/Dependencies/EP_mongo-cxx-driver)
# set(CPPFORMAT_DIR       ${ReverseTale_INSTALL_BASE}/Dependencies/EP_cppformat)
# set(INIH_DIR            ${ReverseTale_INSTALL_BASE}/Dependencies/EP_inih)
# set(THREADPOOL11_DIR    ${ReverseTale_INSTALL_BASE}/Dependencies/EP_threadpool11)
# set(XHACKING_DIR        ${ReverseTale_INSTALL_BASE}/Dependencies/EP_xHacking)

#######################
# LIBBSON

    set(LIBBSON_CONFIGURE
        "-DENABLE_TESTS=OFF"
    )

    RequireExternal(
        TARGET MetaProject
        MODULE "mongodb/libbson:r1.9"
        CONFIGURE_ARGUMENTS ${LIBBSON_CONFIGURE}
        INC_PATH "."
    )

#######################
# MONGOC 

    set(MONGOC_CONFIGURE
        "-DENABLE_SASL=OFF"     # TODO: Revisit this, do we want it?
        "-DENABLE_SSL=OFF"      # TODO: Revisit this, do we want it?
        "-DENABLE_TESTS=OFF"
        "-DENABLE_EXAMPLES=OFF"
    )

    RequireExternal(
        TARGET MetaProject
        ENSURE_ORDER
        MODULE "mongodb/mongo-c-driver:r1.9"
        CONFIGURE_ARGUMENTS ${MONGOC_CONFIGURE}
        INC_PATH "."
    )

#######################
# MONGOCXX

    set(MONGOCXX_CONFIGURE
        "-DENABLE_TESTS=OFF"     # TODO: No longer can be disabled
        "-DENABLE_EXAMPLES=OFF"  # TODO: No longer can be disabled
    )

    RequireExternal(
        TARGET MetaProject
        ENSURE_ORDER
        MODULE "mongodb/mongo-cxx-driver:r3.2.0"
        CONFIGURE_ARGUMENTS ${MONGOCXX_CONFIGURE}
    )

#######################
# FMT 

    set(FMT_CONFIGURE
        "-DFMT_DOC=OFF"
        "-DFMT_TEST=OFF"
    )

    RequireExternal(
        TARGET MetaProject
        MODULE "fmtlib/fmt:b64d13a357d3556e54ed92a959baf3a14e2f6078"
        CONFIGURE_ARGUMENTS ${FMT_CONFIGURE}
    )

#######################
# INIH
    set(INIH_PATCHES
        "git apply --whitespace=fix ${CMAKE_SOURCE_DIR}/cmake/Patches/01-inih-cmake.patch"
        "git apply --whitespace=fix ${CMAKE_SOURCE_DIR}/cmake/Patches/02-inih-has_key.patch"
    )

    RequireExternal(
        TARGET MetaProject
        MODULE "benhoyt/inih:0c3f8ea80ca2c8be8995a0b7b4e84af1641a4dd2"
        CONFIGURE_STEPS ${INIH_PATCHES}
    )

#######################
# THREADPOOL11

    RequireExternal(
        TARGET MetaProject
        MODULE "gpascualg/threadpool11:2.0"
    )
    

if (${ReverseTale_BUILD_XHACKING})
    RequireExternal(
        TARGET MetaProject
        MODULE "gpascualg/xHacking:master"
    )
endif()
