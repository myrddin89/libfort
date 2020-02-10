option(FORT_HAVE_WCHAR "Enable wchar support" ON)
option(FORT_HAVE_UTF8 "Enable UTF8 support" ON)

add_library(fort SHARED fort.c)

target_include_directories(fort
    PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
        $<INSTALL_INTERFACE:include>
)

set_target_properties(fort
    PROPERTIES VERSION ${libfort_VERSION}
    SOVERSION ${libfort_SOVERSION})

if(NOT FORT_HAVE_WCHAR)
    target_compile_definitions(fort
        PRIVATE
            -DFT_CONGIG_DISABLE_WCHAR
    )
endif()

if(NOT FORT_HAVE_UTF8)
    target_compile_definitions(fort
        PRIVATE
            -DFT_CONGIG_DISABLE_UTF8
    )
endif()

include(CMakePackageConfigHelpers)
include(GNUInstallDirs)

set(CMAKECONFIG_INSTALL_DIR ${CMAKE_INSTALL_LIBDIR}/cmake/
    CACHE PATH "Installation directory for CMake files")

# configure_file(libfort.pc.in libfort.pc @ONLY)
# install(FILES ${CMAKE_CURRENT_BINARY_DIR}/libfort.pc DESTINATION lib/pkgconfig)

configure_package_config_file(
    fort-config.cmake.in
    ${CMAKE_CURRENT_BINARY_DIR}/fort-config.cmake
    INSTALL_DESTINATION ${CMAKECONFIG_INSTALL_DIR}/${LIB_NAME}
    PATH_VARS CMAKE_INSTALL_INCLUDEDIR
)

write_basic_package_version_file(
    ${CMAKE_CURRENT_BINARY_DIR}/fort-config-version.cmake
    COMPATIBILITY SameMajorVersion
)

install(
    FILES
        ${CMAKE_CURRENT_BINARY_DIR}/fort-config.cmake
        ${CMAKE_CURRENT_BINARY_DIR}/fort-config-version.cmake
    DESTINATION ${CMAKECONFIG_INSTALL_DIR}/fort
)

install(
    TARGETS fort
    EXPORT fort-targets
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
    INCLUDES DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)

install(
    EXPORT fort-targets
    FILE fort-targets.cmake
    NAMESPACE fort::
    DESTINATION ${CMAKECONFIG_INSTALL_DIR}/fort
)

install(FILES fort.h fort.hpp DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/fort)
