cmake_minimum_required(VERSION 3.0.0)

project(Amalgamate)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_EXTENSIONS OFF)

add_executable(amalgamate
	Amalgamate.cpp
)

if(${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
  target_sources(amalgamate PUBLIC juce_core_amalgam.mm)
else()
  target_sources(amalgamate PUBLIC juce_core_amalgam.cpp)
endif()


if(${CMAKE_SYSTEM_NAME} MATCHES "Linux")
  target_link_libraries(amalgamate dl pthread)
elseif(${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
  find_library(CORE_FOUNDATION CoreFoundation)
  find_library(APP_KIT AppKit)

  if (NOT CORE_FOUNDATION)
    message(FATAL_ERROR "missing framework CoreFoundation")
  elseif (NOT APP_KIT)
    message(FATAL_ERROR "missing framework AppKit")
  endif ()

  set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -lc++")
  target_link_libraries(amalgamate ${CORE_FOUNDATION} ${APP_KIT})
endif()

install(TARGETS amalgamate RUNTIME DESTINATION bin)
