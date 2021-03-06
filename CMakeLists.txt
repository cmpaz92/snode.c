cmake_minimum_required(VERSION 3.0)

project(snode.c)

add_subdirectory(net)

add_subdirectory(http)

add_executable(snode.c main.cpp)
target_compile_options(snode.c PRIVATE --warn-conversion)
target_link_libraries(snode.c LINK_PUBLIC http)
install(TARGETS snode.c RUNTIME DESTINATION bin)

add_executable(posttest posttest.cpp)
target_compile_options(posttest PRIVATE --warn-conversion)
target_link_libraries(posttest LINK_PUBLIC http)
install(TARGETS posttest RUNTIME DESTINATION bin)

add_executable(simpleserver simpleserver.cpp)
target_compile_options(simpleserver PRIVATE --warn-conversion)
target_link_libraries(simpleserver LINK_PUBLIC http)
install(TARGETS simpleserver RUNTIME DESTINATION bin)


#find_program(iwyu_path NAMES include-what-you-use iwyu)
#if(NOT iwyu_path)
#  message(FATAL_ERROR "Could not find the program include-what-you-use")
#endif()
#set_property(TARGET snode.c PROPERTY CXX_INCLUDE_WHAT_YOU_USE ${iwyu_path})

find_package(Doxygen)
option(BUILD_DOCUMENTATION "Create and install the HTML based API documentation (requires Doxygen)" ${DOXYGEN_FOUND})

if(BUILD_DOCUMENTATION)
    if (DOXYGEN_FOUND)
        set(doxyfile_in ${CMAKE_CURRENT_SOURCE_DIR}/doc/Doxygen.in)
        set(doxyfile ${CMAKE_CURRENT_BINARY_DIR}/Doxyfile)

        configure_file(${doxyfile_in} ${doxyfile} @ONLY)

        message("Doxygen build started.")

        add_custom_target(doc
                        COMMAND ${DOXYGEN_EXECUTABLE} ${doxyfile_in}
                        WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
                        COMMENT "Generating API documentation with Doxygen"
                        VERBATIM)

        install(DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/html DESTINATION     share/doc)
    else (DOXYGEN_FOUND)
        message(FATAL_ERROR "Doxygen is needed to build the documentation.")
    endif (DOXYGEN_FOUND)
endif(BUILD_DOCUMENTATION)
