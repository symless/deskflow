# Deskflow -- mouse and keyboard sharing utility
# Copyright (C) 2024 Chris Rizzitello <sithlord48@gmail.com>
# Copyright (C) 2012-2024 Symless Ltd.
# Copyright (C) 2009-2012 Nick Bolton
#
# This package is free software; you can redistribute it and/or
# modify it under the terms of the GNU General Public License
# found in the file LICENSE that should have accompanied this file.
#
# This package is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program.  If not, see <http://www.gnu.org/licenses/>.

set(target deskflow-client)

if(WIN32)
  set(PLATFORM_SOURCES
    deskflow-client.exe.manifest
    deskflow-client.rc
    MSWindowsClientTaskBarReceiver.cpp
    MSWindowsClientTaskBarReceiver.h
    resource.h
    ${PROJECT_SOURCE_DIR}/src/apps/res/deskflow.ico
    ${PROJECT_SOURCE_DIR}/src/apps/res/tb_error.ico
    ${PROJECT_SOURCE_DIR}/src/apps/res/tb_idle.ico
    ${PROJECT_SOURCE_DIR}/src/apps/res/tb_run.ico
    ${PROJECT_BINARY_DIR}/src/version.rc
  )
elseif(APPLE)
  set(PLATFORM_SOURCES
    OSXClientTaskBarReceiver.cpp
    OSXClientTaskBarReceiver.h
  )
elseif(UNIX)
  set(PLATFORM_SOURCES
    XWindowsClientTaskBarReceiver.cpp
    XWindowsClientTaskBarReceiver.h
  )
endif()

add_executable(${target} ${PLATFORM_SOURCES} deskflow-client.cpp)

target_link_libraries(
  ${target}
  arch
  base
  client
  io
  mt
  net
  ipc
  platform
  server
  app
  ${libs})

if(${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
  set_target_properties(${target} PROPERTIES RUNTIME_OUTPUT_DIRECTORY $<TARGET_BUNDLE_CONTENT_DIR:Deskflow>/MacOS)
elseif(${CMAKE_SYSTEM_NAME} MATCHES "Linux")
  install(TARGETS ${target} DESTINATION bin)
elseif(${CMAKE_SYSTEM_NAME} MATCHES "Windows")
  install(
    TARGETS ${target}
    RUNTIME_DEPENDENCY_SET clientDeps
    DESTINATION .
  )
  install(RUNTIME_DEPENDENCY_SET clientDeps
    PRE_EXCLUDE_REGEXES
      "api-ms-win-.*"
      "ext-ms-.*"
      "^hvsifiletrust\\.dll$"
    POST_EXCLUDE_REGEXES
      ".*system32.*"
    RUNTIME DESTINATION .
  )
endif()

