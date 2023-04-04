cmake_minimum_required(VERSION 3.15)

project(curl-test C)

find_package(CURL REQUIRED)
if(NOT CURL_FOUND)
    message(FATAL_ERROR "Could not find curl")
endif()

message(STATUS "CURL_VERSION_STRING: ${CURL_VERSION_STRING}")
message(STATUS "CURL_INCLUDE_DIRS: ${CURL_INCLUDE_DIRS}")
message(STATUS "CURL_LIBRARIES: ${CURL_LIBRARIES}")

add_executable(${PROJECT_NAME} main.c)

target_link_libraries(${PROJECT_NAME} PRIVATE CURL::libcurl)
