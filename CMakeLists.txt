
cmake_minimum_required(VERSION 3.2)
if ($ENV{TRAVIS_BUILD_NUMBER})
    project(hw1 VERSION 0.0.$ENV{TRAVIS_BUILD_NUMBER})
else()
    project(hw1 VERSION 0.0.1)
endif()

find_package(Boost COMPONENTS unit_test_framework REQUIRED)
if (NOT Boost_FOUND)
    find_package(Boost 1.75.0 COMPONENTS unit_test_framework REQUIRED)
endif(NOT Boost_FOUND)


configure_file(version.h.in version.h)

add_executable(hw1_cli main.cpp)
add_library(hw1 lib.cpp)
add_executable(boost_test_version boost_test_version.cpp)

set_target_properties(hw1_cli hw1 boost_test_version PROPERTIES
    CXX_STANDARD 14
    CXX_STANDARD_REQUIRED ON
)
target_include_directories(hw1
    PRIVATE "${CMAKE_BINARY_DIR}"
)

set_target_properties(boost_test_version PROPERTIES
    COMPILE_DEFINITIONS BOOST_TEST_DYN_LINK
    INCLUDE_DIRECTORIES ${Boost_INCLUDE_DIR}
)

target_link_libraries(boost_test_version
    ${Boost_LIBRARIES}
    hw1
)

target_compile_options(hw1_cli PRIVATE
    -Wall -Wextra -pedantic -Werror
)
target_compile_options(hw1 PRIVATE
    -Wall -Wextra -pedantic -Werror
)
target_compile_options(boost_test_version PRIVATE
    -Wall -Wextra -pedantic -Werror
)

target_link_libraries(hw1_cli PRIVATE
    hw1
)

install(TARGETS hw1_cli RUNTIME DESTINATION bin)

set(CPACK_GENERATOR DEB)

set(CPACK_PACKAGE_VERSION_MAJOR "${PROJECT_VERSION_MAJOR}")
set(CPACK_PACKAGE_VERSION_MINOR "${PROJECT_VERSION_MINOR}")
set(CPACK_PACKAGE_VERSION_PATCH "${PROJECT_VERSION_PATCH}")

set(CPACK_PACKAGE_CONTACT neige.mvk@gmail.com)

include(CPack)

enable_testing()

add_test(boost_test_version boost_test_version)

