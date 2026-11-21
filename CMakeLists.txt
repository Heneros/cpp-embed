cmake_minimum_required(VERSION 3.20) # Убрана запятая

project(cpp-embed C CXX ASM)

set(CMAKE_C_STANDARD 11)
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_C_STANDARD_REQUIRED ON) 
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(CMAKE_C_COMPILER arm-none-eabi-gcc)
set(CMAKE_CXX_COMPILER arm-none-eabi-g++)

set(CPU_OPTIONS -mthumb -mcpu=cortex-m4)

add_compile_options(
    ${CPU_OPTIONS}
    -g
    -fstrict-volatile-bitfields
    -ffunction-sections
    -fdata-sections
    $<$<CONFIG:Debug>:-O0;-DDEBUG;-ffreestanding;-ggdb>
)

set(SRCDIR src)

include_directories(
    ${SRCDIR}
    ${SRCDIR}/CMSIS/Include
)


file(GLOB_RECURSE SOURCE_FILES ${SRCDIR}/*.c ${SRCDIR}/*.cpp ${SRCDIR}/*.s)

set(TARGET main.elf)

set(LINKER_SCRIPT "${CMAKE_CURRENT_SOURCE_DIR}/linker.ld")

add_executable(${TARGET} ${SOURCE_FILES})

target_link_options(${TARGET} PRIVATE
    ${CPU_OPTIONS}
    -nostartfiles
    -T${LINKER_SCRIPT}
    -Wl,-Map=lab.map,--cref
    -Wl,--gc-sections
    -specs=nano.specs
)