cmake_minimum_required(VERSION 3.1)
project(CIRCUIT)

set(MAIN command/main.cpp)
set(SPEED_TEST command/speed.cpp)
set(HEADER_DIR header)
set(SOURCE_DIR source)
set(BDD_DIR subprojects/bdd)
set(DNF_DIR subprojects/dnf)
set(CIRCUIT_DIR ${SOURCE_DIR}/circuit)
set(GATE_DIR    ${SOURCE_DIR}/gate)
set(TIMER_DIR   ${SOURCE_DIR}/timer)
set(BDD_EXPORTER_DIR   ${SOURCE_DIR}/bddExporter)
set(DNF_EXPORTER_DIR   ${SOURCE_DIR}/dnfExporter)
set(ACCEPTABLE_FOO_BUILDER_DIR   ${SOURCE_DIR}/AcceptableFooBuilder)

add_subdirectory(${BDD_DIR})
add_subdirectory(${DNF_DIR})


aux_source_directory(${CIRCUIT_DIR} CIRCUIT_CXXS)
aux_source_directory(${GATE_DIR}    GATE_CXXS)
aux_source_directory(${TIMER_DIR}   TIMER_CXXS)
aux_source_directory(${BDD_EXPORTER_DIR}    BDD_EXPORTER_CXXS)
aux_source_directory(${DNF_EXPORTER_DIR}    DNF_EXPORTER_CXXS)
aux_source_directory(${ACCEPTABLE_FOO_BUILDER_DIR}    ACCEPTABLE_FOO_BUILDER_CXXS)



add_executable(MAIN_CIRCUIT ${MAIN})
add_executable(SPEED_TEST_EXEC ${SPEED_TEST})

add_library(CIRCUIT_LIB STATIC  ${CIRCUIT_CXXS})
add_library(GATE_LIB    STATIC  ${GATE_CXXS})
add_library(TIMER_LIB   STATIC  ${TIMER_CXXS})
add_library(BDD_EXPORTER_LIB   STATIC  ${BDD_EXPORTER_CXXS})
add_library(DNF_EXPORTER_LIB   STATIC  ${DNF_EXPORTER_CXXS})
add_library(ACCEPTABLE_FOO_BUILDER_LIB   STATIC  ${ACCEPTABLE_FOO_BUILDER_CXXS})


target_include_directories(CIRCUIT_LIB  PUBLIC ${HEADER_DIR})
target_include_directories(GATE_LIB     PUBLIC ${HEADER_DIR})
target_include_directories(TIMER_LIB    PUBLIC ${HEADER_DIR})
target_include_directories(BDD_EXPORTER_LIB    PUBLIC ${HEADER_DIR})
target_include_directories(DNF_EXPORTER_LIB    PUBLIC ${HEADER_DIR})
target_include_directories(ACCEPTABLE_FOO_BUILDER_LIB    PUBLIC ${HEADER_DIR})


target_link_libraries(CIRCUIT_LIB BDD_LIB GATE_LIB DNF_LIB BDD_EXPORTER_LIB DNF_EXPORTER_LIB)
target_link_libraries(BDD_EXPORTER_LIB BDD_LIB GATE_LIB)
target_link_libraries(ACCEPTABLE_FOO_BUILDER_LIB DNF_LIB)
target_link_libraries(DNF_EXPORTER_LIB BDD_EXPORTER_LIB BDD_LIB DNF_LIB )
target_link_libraries(MAIN_CIRCUIT CIRCUIT_LIB ACCEPTABLE_FOO_BUILDER_LIB)
target_link_libraries(SPEED_TEST_EXEC CIRCUIT_LIB TIMER_LIB ACCEPTABLE_FOO_BUILDER_LIB)


set_target_properties(SPEED_TEST_EXEC PROPERTIES CXX_STANDARD 17 CXX_EXTENSIONS OFF)


# Test section

set(TEST_DIR test)
set(timeout 5)

add_executable(BASIC ${TEST_DIR}/basic.cpp)
target_link_libraries(BASIC CIRCUIT_LIB)

enable_testing()
add_test(BASIC_27 BASIC S27.BEN)
set_tests_properties(BASIC_27 PROPERTIES TIMEOUT ${timeout})