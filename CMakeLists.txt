cmake_minimum_required(VERSION 3.0)
project(simple-sound-fmod)

if (WIN32) # Includes win64 platforms

  find_library(GLFW NAMES glfw3 PATHS external/lib)

  set(INCLUDE_DIRS
     external/fmod/inc )

  set(LIBRARY_DIRS
    external/fmod/lib/x64)

  set(CMAKE_CXX_STANDARD 11)
  set(CMAKE_CXX_FLAGS 
     "/wd4018 /wd4244 /wd4305 
     /D_CRT_SECURE_NO_WARNINGS 
     /D_CRT_NONSTDC_NO_DEPRECATE 
     /D NOMINMAX
     /EHsc")

  set(EXECUTABLE_OUTPUT_PATH ${CMAKE_SOURCE_DIR}/bin)

elseif (APPLE)

  set(CMAKE_MACOSX_RPATH 1)
  set(CMAKE_CXX_FLAGS "-Wall -Wno-deprecated-declarations -g -stdlib=libc++ -std=c++17")
  set(LIBRARY_OUTPUT_PATH ${CMAKE_SOURCE_DIR}/lib)
  set(EXECUTABLE_OUTPUT_PATH ${CMAKE_SOURCE_DIR}/bin)
  add_definitions(-DAPPLE)

  set(INCLUDE_DIRS
    /System/Library/Frameworks
    /usr/include
    /usr/local/include)

elseif (UNIX)

  set(CMAKE_CXX_FLAGS "-Wall -g -std=c++17 -Wno-comment")
  set(LIBRARY_OUTPUT_PATH ${CMAKE_SOURCE_DIR}/lib)
  set(EXECUTABLE_OUTPUT_PATH ${CMAKE_SOURCE_DIR}/bin)
  set(OpenGL_GL_PREFERENCE GLVND)

  set(INCLUDE_DIRS . /usr/local/cclude /usr/include)
  set(LIBRARY_DIRS /usr/X11R6/lib /usr/local/lib)

  add_definitions(-DUNIX)

endif()

include_directories(${INCLUDE_DIRS})
link_directories(${LIBRARY_DIRS})

add_executable(simple-sound simple-sound.cpp)
target_link_libraries(simple-sound fmod)
if (WIN32)
  add_custom_command(TARGET simple-sound POST_BUILD        # Adds a post-build event to MyTest
    COMMAND ${CMAKE_COMMAND} -E copy_if_different  # which executes "cmake - E copy_if_different..."
        "${PROJECT_SOURCE_DIR}/external/fmod/lib/x64/fmod.dll"      # <--this is in-file
        $<TARGET_FILE_DIR:simple-sound>)  
  set_property(TARGET simple-sound 
    PROPERTY VS_DEBUGGER_WORKING_DIRECTORY "${CMAKE_SOURCE_DIR}/bin")
endif()

