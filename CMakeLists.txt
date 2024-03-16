cmake_minimum_required (VERSION 3.22)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} --std=c++20 -g")

project(libpiradio VERSION 0.1.0 DESCRIPTION "Shared class for Pi Radio Daemons")

include(GNUInstallDirs)

include_directories(./include)

find_package(sdbus-c++ REQUIRED)

if(CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
  set(CMAKE_INSTALL_PREFIX "/usr" CACHE PATH "..." FORCE)
endif()


set(LIBPIRADIO_SOURCES
  src/frequency.cxx
  src/freq_module.cxx
  src/daemon.cxx
  src/grpc.cxx
  src/cmdline.cxx
  src/fpga.cxx)

set(LIBPIRADIO_HEADERS
  include/piradio/cmdline.hpp
  include/piradio/dbus.hpp
  include/piradio/freq_module.hpp
  include/piradio/nrange.hpp
  include/piradio/services.hpp
  include/piradio/daemon.hpp
  include/piradio/fpga.hpp
  include/piradio/frequency.hpp
  include/piradio/sdjournal.hpp
  )

add_library(piradio STATIC ${LIBPIRADIO_SOURCES})

set_target_properties(piradio PROPERTIES PUBLIC_HEADER "${LIBPIRADIO_HEADERS}")

target_link_libraries(piradio PUBLIC
  grpc++_reflection
  grpc++
  grpc
  protobuf
  SDBusCpp::sdbus-c++
  systemd)

install(TARGETS piradio PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/piradio/ COMPONENT LIBPIRADIO)

set(CPACK_SET_DESTDIR true)
set(CPACK_INSTALL_PREFIX /)
set(CPACK_GENERATOR DEB)
set(CPACK_PACKAGE_NAME "piradio")
set(CPACK_PACKAGE_VENDOR "Pi Radio Inc.")
set(CPACK_PACKAGE_CONTACT "Michael Zappe <zapman@pi-rad.io>")
set(CPACK_DEBIAN_PACKAGE_MAINTAINER "Michael Zappe <zapman@pi-rad.io>")
set(CPACK_DEBIAN_PACKAGE_VERSION 0.1.2)
set(CPACK_DEBIAN_PACKAGE_RELEASE 7)
set(CPACK_DEBIAN_PACKAGE_CONTROL_EXTRA "${CMAKE_CURRENT_SOURCE_DIR}/deb/postinst")
set(CPACK_COMPONENTS_GROUPING ONE_PER_GROUP)
set(CPACK_DEB_PACKAGE_COMPONENT ON)
set(CPACK_DEB_COMPONENT_INSTALL ON)
set(CPACK_DEBIAN_FILE_NAME "DEB-DEFAULT")
set(CPACK_DEBIAN_ENABLE_COMPONENT_DEPENDS ON)
set(CPACK_DEBIAN_DEBUGINFO_PACKAGE ON)

set(CPACK_DEBIAN_PACKAGE_DEPENDS "libsdbus-c++dev, libfmt8-dev")

set(CPACK_DEBIAN_PACKAGE_DEBUG ON)
  
set(CPACK_DEBIAN_PACKAGE_DESCRIPTION "Global Pi Radio Description")
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "Global Pi Radio Description Summary")

set(CPACK_COMPONENTS_ALL LIBPIRADIO)

set(CPACK_COMPONENT_LIBPIRADIO_DISPLAY_NAME "piradio-libpiradio")
set(CPACK_COMPONENT_LIBPIRADIO_DESCRIPTION "Base ultility library for Pi Radio software")

include(CPack)
