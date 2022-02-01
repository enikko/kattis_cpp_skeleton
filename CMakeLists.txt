# MIT License
#
# Copyright (c) 2022 Erik Nikko
#
# Permission is hereby granted, free of charge, to any person obtaining a copy
# of this software and associated documentation files (the "Software"), to deal
# in the Software without restriction, including without limitation the rights
# to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
# copies of the Software, and to permit persons to whom the Software is
# furnished to do so, subject to the following conditions:
#
# The above copyright notice and this permission notice shall be included in all
# copies or substantial portions of the Software.
#
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
# AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
# OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
# SOFTWARE.

cmake_minimum_required(VERSION 3.10)

project(TDDD95)
enable_testing()

# Force to specify standard so that compile commands are correct
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
set(CMAKE_CXX_COMPILER g++)
set(CMAKE_CXX_STANDARD 17)

set(KATTIS_CXX_FLAGS "-g -O2")
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

function(add_problem PROBLEM)
  add_executable(${PROBLEM} "src/problems/${PROBLEM}/${PROBLEM}.cpp")
  add_lr_test(${PROBLEM})
endfunction()
