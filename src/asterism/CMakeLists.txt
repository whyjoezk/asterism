include_directories("../../3rdparty/libuv/include")
include_directories("../../3rdparty/http-parser")

aux_source_directory(. ASTERISM_SOURCE)
if(UNIT_TEST)
    aux_source_directory(./test ASTERISM_SOURCE)
    add_definitions(-DUNIT_TEST)
endif()

add_executable(asterism ${ASTERISM_SOURCE})

if(WIN32)
    list(APPEND LINK_LIB ws2_32 iphlpapi psapi userenv)
else()
    list(APPEND LINK_LIB pthread dl m)
endif()

if(ANDROID)
    list(REMOVE_ITEM LINK_LIB pthread)
endif()

list(APPEND LINK_LIB uv http_parser)

if(UNIX)
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -std=gnu99")
endif()

target_link_libraries(asterism ${LINK_LIB})
if(ANDROID)
    if (CMAKE_CXX_COMPILER_ID MATCHES Clang AND CMAKE_BUILD_TYPE STREQUAL Release)
        add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD COMMAND ${CMAKE_STRIP} ${CMAKE_PROJECT_NAME} COMMENT "Strip debug symbols done on final binary.")
    endif()
endif()
