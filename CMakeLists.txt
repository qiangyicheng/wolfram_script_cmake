# CMakeLists files in this project can
# refer to the root source directory of the project as ${PROJECT_SOURCE_DIR} and
# to the root binary directory of the project as ${PROJECT_BINARY_DIR}.

cmake_minimum_required(VERSION 3.14)
project (wolfram_script_cmake)

add_library(${PROJECT_NAME} INTERFACE)

set(ENABLE_WOLFRAM_SCRIPT False CACHE BOOL "whether WOLFRAM_SCRIPT is enabled")

if(ENABLE_WOLFRAM_SCRIPT)
    find_program(FOUND_WOLFRAM_SCRIPT wolframscript)
    if(FOUND_WOLFRAM_SCRIPT)
        message(STATUS "WolframScript found. WolframScript generators will be enabled if it is usable.")
        execute_process(
            COMMAND
                wolframscript -c GreaterEqual[$VersionNumber,11.0]
            OUTPUT_VARIABLE
                WOLFRAM_SCRIPT_TEST_VER
            OUTPUT_STRIP_TRAILING_WHITESPACE
        )
        if(WOLFRAM_SCRIPT_TEST_VER MATCHES "True")
            message(STATUS "Test passed, WolframScript generators are enabled")
        else()
            message(WARNING "Test failed, WolframScript generators are disabled")
            message(WARNING "To enable WolframScript generators, make sure command wolframscript is available and the wolfram kernel version is higher than 11.0")
            set(ENABLE_WOLFRAM_SCRIPT False CACHE BOOL "whether WOLFRAM_SCRIPT is enabled" FORCE)
        endif()
    else()
        message(STATUS "WolframScript enabled but not found. WolframScript generators will be disabled.")
        set(ENABLE_WOLFRAM_SCRIPT False CACHE BOOL "whether WOLFRAM_SCRIPT is enabled" FORCE)
    endif()
endif()


set(WOLFRAM_SCRIPT_TARGET_LIST   CACHE INTERNAL "Internal cache variable for list of WolframScript targets" FORCE)

macro(append_wolfram_script_target_list val)
set(WOLFRAM_SCRIPT_TARGET_LIST $CACHE{WOLFRAM_SCRIPT_TARGET_LIST} ${val} CACHE INTERNAL "Internal cache variable for list of WolframScript targets" FORCE)
message(STATUS "Adding target ${val} to the list of WolframScript targets")
message(STATUS "Current WOLFRAM_SCRIPT_TARGET_LIST is $CACHE{WOLFRAM_SCRIPT_TARGET_LIST}")
endmacro()