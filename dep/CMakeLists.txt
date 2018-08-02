if (${ReverseTale_BUILD_GAMESERVER})
    RequireExternal(
        TARGET MetaProject
        MODULE "ReverseTale/Game-Server:master"
        ALWAYS_BUILD
        ENSURE_ORDER
    )
endif()

if (${ReverseTale_BUILD_LOGINSERVER})
    RequireExternal(
        TARGET MetaProject
        MODULE "ReverseTale/Login-Server:master"
        ALWAYS_BUILD
        ENSURE_ORDER
    )
endif()

ResolveExternal(TARGET MetaProject)
if (MetaProject_IS_RESOLVED)
    ExternalInstallDirectory(VARIABLE EXTERNAL_DIRECTORY)

    # Easy to use folders
    macro(makeLink src dest target)
        add_custom_target(${target} ALL DEPENDS ${src})
        add_custom_command(TARGET ${target} COMMAND ${CMAKE_COMMAND} -E create_symlink ${src} ${dest} DEPENDS ${dest} COMMENT "Linking ${src} -> ${dest}")
    endmacro()

    makeLink(${EXTERNAL_DIRECTORY}/src/ReverseTale_Library_master/ ${CMAKE_SOURCE_DIR}/project/Library LN_0)
    makeLink(${EXTERNAL_DIRECTORY}/src/ReverseTale_Common-Server_master/ ${CMAKE_SOURCE_DIR}/project/Common-Server LN_1)
    makeLink(${EXTERNAL_DIRECTORY}/src/ReverseTale_Login-Server_master/ ${CMAKE_SOURCE_DIR}/project/Login-Server LN_2)
    makeLink(${EXTERNAL_DIRECTORY}/src/ReverseTale_Game-Server_master/ ${CMAKE_SOURCE_DIR}/project/Game-Server LN_3)

    # Create a Visual Studio Code workspace
    get_filename_component(LIBRARY_PATH ${CMAKE_SOURCE_DIR}/project/Library ABSOLUTE)
    get_filename_component(COMMON_SERVER_PATH ${CMAKE_SOURCE_DIR}/project/Common-Server ABSOLUTE)
    get_filename_component(LOGIN_SERVER_PATH ${CMAKE_SOURCE_DIR}/project/Login-Server ABSOLUTE)
    get_filename_component(GAME_SERVER_PATH ${CMAKE_SOURCE_DIR}/project/Game-Server ABSOLUTE)
    configure_file(${CMAKE_SOURCE_DIR}/project.code-workspace.in ${CMAKE_SOURCE_DIR}/project.code-workspace @ONLY)
endif()
