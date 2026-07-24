
set(gem_path ${CMAKE_CURRENT_LIST_DIR})
set(gem_json ${gem_path}/gem.json)

# Currently we are in the OpenXRTest/Code folder: ${CMAKE_CURRENT_LIST_DIR}
# Get the platform specific folder ${pal_dir} for the current folder: ${CMAKE_CURRENT_LIST_DIR}/Platform/${PAL_PLATFORM_NAME}
#       i.e. It could here : OpenXRTest/Code/Platform/<platform_name>  or
set(pal_dir ${CMAKE_CURRENT_LIST_DIR}/Platform/${PAL_PLATFORM_NAME})

# Now that we have the platform abstraction layer (PAL) folder for this folder, thats where we will find the
# traits for this platform. Traits for a platform are defines for things like whether or not something in this project
# is supported by this platform.
include(${pal_dir}/PAL_${PAL_PLATFORM_NAME_LOWERCASE}.cmake)

# Now that we have loaded our project traits for this platform, see if this project is even supported on this platform.
# If its not supported we just return after including the unsupported.
if(NOT PAL_TRAIT_OPENXRTEST_SUPPORTED)
    return()
endif()

# We are on a supported platform, so add the OpenXRTest target
# Note: We include the common files and the platform specific files which are set in openxrtest_files.cmake and
# in ${pal_dir}/openxrtest_${PAL_PLATFORM_NAME_LOWERCASE}_files.cmake
ly_add_target(
    NAME OpenXRTest.Static STATIC
    NAMESPACE Gem
    FILES_CMAKE
        openxrtest_files.cmake
        ${pal_dir}/openxrtest_${PAL_PLATFORM_NAME_LOWERCASE}_files.cmake
    INCLUDE_DIRECTORIES
        PUBLIC
            Include
    BUILD_DEPENDENCIES
        PRIVATE
            AZ::AzGameFramework
            Gem::Atom_AtomBridge.Static
)

ly_add_target(
    NAME OpenXRTest ${PAL_TRAIT_MONOLITHIC_DRIVEN_MODULE_TYPE}
    NAMESPACE Gem
    FILES_CMAKE
        openxrtest_shared_files.cmake
        ${pal_dir}/openxrtest_shared_${PAL_PLATFORM_NAME_LOWERCASE}_files.cmake
    INCLUDE_DIRECTORIES
        PUBLIC
            Include
    BUILD_DEPENDENCIES
        PRIVATE
            Gem::OpenXRTest.Static
            AZ::AzCore
)

# if enabled, OpenXRTest is used by all kinds of applications
ly_create_alias(NAME OpenXRTest.Builders NAMESPACE Gem TARGETS Gem::OpenXRTest)
ly_create_alias(NAME OpenXRTest.Tools    NAMESPACE Gem TARGETS Gem::OpenXRTest)
ly_create_alias(NAME OpenXRTest.Clients  NAMESPACE Gem TARGETS Gem::OpenXRTest)
ly_create_alias(NAME OpenXRTest.Servers  NAMESPACE Gem TARGETS Gem::OpenXRTest)
ly_create_alias(NAME OpenXRTest.Unified  NAMESPACE Gem TARGETS Gem::OpenXRTest)

################################################################################
# Gem dependencies
################################################################################

# Enable the specified list of gems from GEM_FILE or GEMS list for this specific project:
ly_enable_gems(PROJECT_NAME OpenXRTest GEM_FILE enabled_gems.cmake)
