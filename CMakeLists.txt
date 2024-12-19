# Set minimum required version of CMake
cmake_minimum_required(VERSION 3.12)

# Set the Pico SDK path if not already defined
if(NOT DEFINED ENV{PICO_SDK_PATH})
    set(ENV{PICO_SDK_PATH} "C:/VSARM/sdk/pico/pico-sdk")
endif()

# Include build functions from Pico SDK
include($ENV{PICO_SDK_PATH}/external/pico_sdk_import.cmake)

# Set name of project (as PROJECT_NAME) and C/C++ standards
project(blink C CXX ASM)
set(CMAKE_C_STANDARD 11)
set(CMAKE_CXX_STANDARD 17)

# Force fetch and build the correct picotool version
set(PICOTOOL_FORCE_FETCH_FROM_GIT ON CACHE BOOL "Force building picotool from git" FORCE)

# Initialize the Pico SDK
pico_sdk_init()

# Tell CMake where to find the executable source file
add_executable(${PROJECT_NAME} 
    main.c
    button.c
    ultrasonic_sensor.c
    buzzer.c
    pir.c
    led.c
    servo.c
    wifi.c
)

# Add include directories
target_include_directories(${PROJECT_NAME} PRIVATE
    ${CMAKE_CURRENT_LIST_DIR}
    $ENV{PICO_SDK_PATH}/src/rp2_common/pico_cyw43_arch/include
    $ENV{PICO_SDK_PATH}/src/rp2_common/hardware_gpio/include
    $ENV{PICO_SDK_PATH}/src/common/pico_stdlib/include
    $ENV{PICO_SDK_PATH}/lib/cyw43-driver/src
    $ENV{PICO_SDK_PATH}/src/rp2_common/hardware_base/include
    $ENV{PICO_SDK_PATH}/src/common/pico_base/include
    $ENV{PICO_SDK_PATH}/src/common/pico_stdlib_headers/include/pico
)

# Create map/bin/hex/uf2 files
pico_add_extra_outputs(${PROJECT_NAME})

# Link to pico_stdlib (gpio, time, etc. functions)
target_link_libraries(${PROJECT_NAME} 
    pico_stdlib
    pico_cyw43_arch_none
    hardware_adc
)

# Enable USB output, disable UART output
pico_enable_stdio_usb(${PROJECT_NAME} 1) 
pico_enable_stdio_uart(${PROJECT_NAME} 0)

# Add Wi-Fi support
pico_enable_stdio_uart(${PROJECT_NAME} 0)
pico_enable_stdio_usb(${PROJECT_NAME} 1)
