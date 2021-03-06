cmake_minimum_required(VERSION 2.8)
project(fastcgi CXX)

if(CMAKE_BUILD_TYPE)
	set(CMAKE_BUILD_TYPE "Debug")
endif()

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/build")

set(EXECUTABLE_OUTPUT_PATH "${CMAKE_SOURCE_DIR}/build/bin")
set(LIBRARY_OUTPUT_PATH "${CMAKE_SOURCE_DIR}/build/lib")

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -Wextra -Wno-deprecated")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++14")

set(CMAKE_C_FLAGS_DEBUG "${CMAKE_C_FLAGS} -g3")
set(CMAKE_C_FLAGS_RELEASE "${CMAKE_C_FLAGS} -O3")

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake/")

find_package(FCGI REQUIRED)
if(FCGI_FOUND)
    INCLUDE_DIRECTORIES(${FCGI_INCLUDE_DIRS})
endif()

find_package(DBUS REQUIRED)
if(DBUS_FOUND)
    INCLUDE_DIRECTORIES(${DBUS_INCLUDE_DIRS})
endif()

INCLUDE_DIRECTORIES(
	src/${PROJECT_NAME}/
	src/json/src/
)

add_library(
	${PROJECT_NAME}_lib

	STATIC

        src/${PROJECT_NAME}/fastcgi.cpp
	src/${PROJECT_NAME}/dispatcher.cpp
	src/${PROJECT_NAME}/networkconfig.cpp
)

target_link_libraries(
	${PROJECT_NAME}_lib

	fcgi
	dbus-1
	pthread
)

add_executable(
	${PROJECT_NAME}
	
	src/${PROJECT_NAME}/main.cpp
)

target_link_libraries(
	${PROJECT_NAME} 

	${PROJECT_NAME}_lib
)

add_subdirectory(src/gtest)
add_subdirectory(src/${PROJECT_NAME}/tests)

install(TARGETS ${PROJECT_NAME} DESTINATION "bin")
