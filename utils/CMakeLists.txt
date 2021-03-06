#====
# Create the system library
#====
bcm_module_name(utils)

bcm_module_header_paths(PUBLIC .)
bcm_module_dependencies(PUBLIC os)
file(GLOB_RECURSE _UTILS_FILES LIST_DIRECTORIES false
     RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} ${CMAKE_CURRENT_SOURCE_DIR}/*.c)
bcm_module_srcs(${_UTILS_FILES})

if(NOT OPEN_SOURCE)
    # Create the library target, but don't reset the internal global variables. We are going to use
    # them for the next target.
    bcm_create_lib_target(NO_RESET)

    #====
    # Specifically reset the globals for the sources only.
    #====
    bcm_module_srcs(RESET)

    #====
    # Create the bootloader libary
    #====
    bcm_module_name(utils_boot)
    bcm_module_definitions(PUBLIC -D_BLOADER_)
    bcm_module_srcs(bcmolt_calc_crc32_table.c)
endif()

bcm_create_lib_target()

if(NOT OPEN_SOURCE)
    # Add additional subdirectories for this module
    bcm_add_subdirectory(linux)
endif()

# Add files to the GitHub part of the release tree.
bcm_github_install(bcmolt_utils.h
                   bcmolt_presence_mask.h
                   bcmolt_mgt_group.h
                   bcmolt_calc_crc32_table.h
                   bcmolt_string.h
                   bcmolt_buf.h
                   RELEASE github/include)
