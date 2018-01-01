# cmake_minimum_required(VERSION 3.3)
project(obs-outputs)

# --- Find libwebrtc or fail early
find_package( libwebrtc REQUIRED )

# --- platform specific libraries to link against
# NOTE ALEX: check if it s not already in the installer
if(WIN32)
  set( obs-outputs_PLATFORM_DEPS
    ws2_32
    winmm
    Iphlpapi
  )
  if(MSVC)
    set( obs-outputs_PLATFORM_DEPS
      ${obs-outputs_PLATFORM_DEPS}
      w32-pthreads
    )
  endif()
endif()

# --- header and source files
set( obs-outputs_HEADERS
  AudioDeviceModuleWrapper.h
  net-if.h         # NOTE ALEX: CHECK
  obs-output-ver.h # NOTE ALEX: CHECK
  rtmp-helpers.h   # NOTE ALEX: CHECK
  rtmp-stream.h    # NOTE ALEX: CHECK
  VideoCapture.h
  VideoCapturer.h
  WebRTCStream.h
  WebsocketClient.h
  )
set( obs-outputs_SOURCES
  AudioDeviceModuleWrapper.cpp
  net-if.c        # NOTE ALEX: CHECK
  obs-outputs.c   # NOTE ALEX: CHECK
  rtmp-stream.cpp # NOTE ALEX: CHECK
  rtmp-windows.c  # NOTE ALEX: CHECK
  VideoCapturer.cpp
  WebRTCStream.cpp
  )

# NOTE ALEX: since libwebrtc is compiled as a
# static lib, we need to use this flag for the 
# obs pugin dll to avoid linking issues with MSVC.
if ( WIN32 )
  set( CMAKE_CXX_FLAGS_RELEASE ${CMAKE_CXX_FLAGS_RELEASE} "/MT"  )
  set( CMAKE_CXX_FLAGS_DEBUG   ${CMAKE_CXX_FLAGS_DEBUG}   "/MTd" )
endif()

# --- library target
add_library( obs-outputs MODULE
  ${obs-outputs_SOURCES}
  ${obs-outputs_HEADER}
)

target_link_libraries( obs-outputs
  libobs
  ${WEBRTC_LIBRARIES}
  ${obs-outputs_PLATFORM_DEPS}
  websocketclient
  )

install_obs_plugin_with_data(obs-outputs data)
