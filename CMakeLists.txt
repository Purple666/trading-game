cmake_minimum_required (VERSION 2.6)

# main project
project (FXBattle)

find_package(Threads)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -std=c++17 -pedantic -Wextra")
find_package(Boost 1.52 COMPONENTS system thread REQUIRED)

# statsd client library
add_library(StatsDLibrary "${PROJECT_SOURCE_DIR}/statsd-c-client/statsd-client.c")

# includes
include_directories(".")
include_directories("exchange")
include_directories("fxbattle")
include_directories("statsd-c-client")

# test
add_executable(Test.exe 
  ${PROJECT_SOURCE_DIR}/test/exchange_account.test.cpp
  ${PROJECT_SOURCE_DIR}/test/exchange_arbitragedestroyer.test.cpp
  ${PROJECT_SOURCE_DIR}/test/exchange_brokerage.test.cpp
  ${PROJECT_SOURCE_DIR}/test/exchange_currency.test.cpp
  ${PROJECT_SOURCE_DIR}/test/exchange_fxconversions.test.cpp
  ${PROJECT_SOURCE_DIR}/test/exchange_fxconverter.test.cpp
  ${PROJECT_SOURCE_DIR}/test/exchange_main.test.cpp
  ${PROJECT_SOURCE_DIR}/test/exchange_market.test.cpp
  ${PROJECT_SOURCE_DIR}/test/exchange_randomtrader.test.cpp
)

# service
add_executable(FXBattle.exe 
  ${PROJECT_SOURCE_DIR}/fxbattle/fxbattle_configuration.cpp
  ${PROJECT_SOURCE_DIR}/fxbattle/fxbattle_metrics.cpp
  ${PROJECT_SOURCE_DIR}/fxbattle/fxbattle_main.cpp
)

target_link_libraries(FXBattle.exe ${Boost_LIBRARIES})
target_link_libraries(FXBattle.exe ${CMAKE_THREAD_LIBS_INIT})
target_link_libraries(FXBattle.exe StatsDLibrary)

# static files
add_custom_command(OUTPUT index.html
    COMMAND ${CMAKE_COMMAND} -E
    copy ${PROJECT_SOURCE_DIR}/templates/index.html ${CMAKE_CURRENT_BINARY_DIR}/templates/index.html
    DEPENDS ${PROJECT_SOURCE_DIR}/templates/index.html
)
add_custom_command(OUTPUT config.html
    COMMAND ${CMAKE_COMMAND} -E
    copy ${PROJECT_SOURCE_DIR}/templates/config.html ${CMAKE_CURRENT_BINARY_DIR}/templates/config.html
    DEPENDS ${PROJECT_SOURCE_DIR}/templates/config.html
)
add_custom_target(static ALL DEPENDS index.html config.html)
