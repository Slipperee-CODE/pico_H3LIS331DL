if(CMAKE_SOURCE_DIR STREQUAL CMAKE_CURRENT_SOURCE_DIR) #THINK ABOUT CHANGING THIS TO MATCH WHAT pico_dshot does by making this a library that can be included using project()
    # Generated Cmake Pico project file

    cmake_minimum_required(VERSION 3.13)

    set(CMAKE_C_STANDARD 11)
    set(CMAKE_CXX_STANDARD 17)
    set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

    # Initialise pico_sdk from installed location
    # (note this can come from environment, CMake cache etc)

    # == DO NOT EDIT THE FOLLOWING LINES for the Raspberry Pi Pico VS Code Extension to work ==
    if(WIN32)
        set(USERHOME $ENV{USERPROFILE})
    else()
        set(USERHOME $ENV{HOME})
    endif()
    set(sdkVersion 2.2.0)
    set(toolchainVersion 14_2_Rel1)
    set(picotoolVersion 2.2.0-a4)
    set(picoVscode ${USERHOME}/.pico-sdk/cmake/pico-vscode.cmake)
    if (EXISTS ${picoVscode})
        include(${picoVscode})
    endif()
    # ====================================================================================
    set(PICO_BOARD pico2 CACHE STRING "Board type")

    # Pull in Raspberry Pi Pico SDK (must be before project)
    include(pico_sdk_import.cmake) #THIS (ALONG WITH SOME OF THESE OTHER LINES MIGHT NEED TO HAPPEN ALL THE TIME SO ADJUST THAT
    #Noticed this possibility after looking at the dshot library here: https://github.com/cadouthat/pico-dshot/blob/main/CMakeLists.txt

    #Maybe create an if statement to check if the current cmake file is the root cmake file and if so add_subdirectory(tests) 
    #which contains another makefile which makes the executable using the library created in the parent cmake file?

    #GEMINI EXAMPLE:
    ## Define your library target first
    # add_library(pico_dshot src/dshot.c)

    # # If this project is the one being built directly (not a sub-dependency)
    # if(PROJECT_IS_TOP_LEVEL) <- PROJECT_IS_TOP_LEVEL checks to see if this is the top cmake file
    #     add_subdirectory(tests) <- should contain a cmake file that (SEE BELOW)
    # endif()

    # (RIGHT HERE) DOES THIS 
    # add_executable(run_tests test_main.c)

    # # Link the library from the parent. 
    # # Because we used PUBLIC above, run_tests automatically finds the headers in ../src
    # target_link_libraries(run_tests pico_dshot)

    #You do still need to add_subdirectory(library_directory_here) before linking the library in your main cmake file so that the library gets compiled by its own cmake file 
    #END OF GEMINI EXAMPLES


    project(H3LIS331DL C CXX ASM)

    # Initialise the Raspberry Pi Pico SDK
    pico_sdk_init()

    # Add executable. Default name is the project name, version 0.1

    add_executable(H3LIS331DL)

    pico_set_program_name(H3LIS331DL "H3LIS331DL")
    pico_set_program_version(H3LIS331DL "0.1")

    # Modify the below lines to enable/disable output over UART/USB
    pico_enable_stdio_uart(H3LIS331DL 0)
    pico_enable_stdio_usb(H3LIS331DL 1)

    # Add the standard include files to the build
    target_include_directories(H3LIS331DL PRIVATE
            ${CMAKE_CURRENT_LIST_DIR}
    )

    set(CURR_TARGET_NAME "H3LIS331DL")
    target_sources(${CURR_TARGET_NAME} PRIVATE tests/default_test.c)
else()
    set(CURR_TARGET_NAME ${PARENT_TARGET_NAME})
endif()

target_sources(${CURR_TARGET_NAME} PRIVATE src/H3LIS331DL.c)

target_include_directories(${CURR_TARGET_NAME} PUBLIC
        ${CMAKE_CURRENT_SOURCE_DIR}/include
)
            
# Add the standard library to the build + Add any user requested libraries
# CMake should automatically handle redundantly linked libraries
target_link_libraries(${CURR_TARGET_NAME} PUBLIC
        pico_stdlib
        hardware_i2c
)

#make sure this doesn't happen if we're just adding this subdirectory 
#It creates the .uf2 for flashing the code into pico which we only want 1 of
if(${CURR_TARGET_NAME} STREQUAL "H3LIS331DL") 
    pico_add_extra_outputs(${CURR_TARGET_NAME}) 
endif()