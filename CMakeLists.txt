cmake_minimum_required(VERSION 3.10.0)
project(FreeRTOSDev VERSION 0.1.0)

include(CTest)
enable_testing()

set(CPACK_PROJECT_NAME ${PROJECT_NAME})
set(CPACK_PROJECT_VERSION ${PROJECT_VERSION})
include(CPack)

# ====================================================
# Set a few project configuration variables
# ====================================================
add_subdirectory("lib/CommonTools/cmake/device/stm32l4")

# ====================================================
# Import and Configure the FreeRTOS Project
# ====================================================
add_subdirectory("FreeRTOS")

# FreeRTOS Config Options
add_library(freertos_cfg INTERFACE)
target_include_directories(freertos_cfg INTERFACE "cfg")
export(TARGETS freertos_cfg FILE "${PROJECT_BINARY_DIR}/ProjectConfig/freertos-cfg.cmake")

# FreeRTOS Port Selection
add_library(freertos_port INTERFACE)
target_link_libraries(freertos_port INTERFACE freertos_cm4f)
export(TARGETS freertos_port FILE "${PROJECT_BINARY_DIR}/ProjectConfig/freertos-port.cmake")

# FreeRTOS Heap Selection
add_library(freertos_heap INTERFACE)
target_link_libraries(freertos_heap INTERFACE freertos_heap4)
export(TARGETS freertos_heap FILE "${PROJECT_BINARY_DIR}/ProjectConfig/freertos-heap.cmake")


# ====================================================
# Project Primary Target
# ====================================================
add_library(FreeRTOSDev STATIC FreeRTOSDev.cpp)
target_link_libraries(FreeRTOSDev PRIVATE
  freertos_inc
  freertos_core
  freertos_cfg
  freertos_port
  freertos_heap
  prj_device_target
)

