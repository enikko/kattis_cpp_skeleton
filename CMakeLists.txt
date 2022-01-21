cmake_minimum_required(VERSION 3.10)

project(TDDD95)
enable_testing()

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
set(CMAKE_CPP_COMPILER g++)
set(CMAKE_CXX_STANDARD 17)
set(KATTIS_CXX_FLAGS "-g -o2 -static")
set(CXX_WARNING_FLAGS "-Wextra -Wshadow -Wnon-virtual-dtor -Wold-style-cast -Wcast-align -Wunused -Woverloaded-virtual -Wpedantic -Wconversion -Wsign-conversion -Wnull-dereference -Wdouble-promotion -Wformat=2 -Wduplicated-cond -Wduplicated-branches -Wlogical-op -Wuseless-cast")
set(CXX_SANITISER_FLAGS "-fno-omit-frame-pointer -fsanitize=signed-integer-overflow -fsanitize=undefined -fsanitize=bounds -fsanitize=float-divide-by-zero -fsanitize=integer-divide-by-zero -fsanitize=pointer-overflow")
set(CMAKE_CXX_FLAGS_DEBUG "${KATTIS_CXX_FLAGS} ${CXX_WARNING_FLAGS} ${CXX_SANITISER_FLAGS}")
set(CMAKE_CXX_FLAGS "${KATTIS_CXX_FLAGS} ${CXX_WARNING_FLAGS}")

include_directories(src/include)

function(add_lr_test PROBLEM)
  add_test(
    NAME ${PROBLEM}_local
    COMMAND ${CMAKE_SOURCE_DIR}/bin/run_tests ${CMAKE_BINARY_DIR}/${PROBLEM} ${CMAKE_SOURCE_DIR}/src/problems/${PROBLEM}/tests/ ${CMAKE_BINARY_DIR}/Testing/Passed ${PROBLEM}_local)
  add_test(
    NAME ${PROBLEM}_remote
    COMMAND ${CMAKE_SOURCE_DIR}/bin/make_delivery ${CMAKE_BINARY_DIR} ${CMAKE_SOURCE_DIR} ${CMAKE_BINARY_DIR}/Testing/Passed ${PROBLEM} ${PROBLEM}_remote)
  set_tests_properties(${PROBLEM}_local PROPERTIES FIXTURES_SETUP ${PROBLEM}_local_passed)
  set_tests_properties(${PROBLEM}_remote PROPERTIES FIXTURES_REQUIRED ${PROBLEM}_local_passed)
endfunction()

add_executable(hello src/problems/hello/hello.cpp)
add_lr_test(hello)
