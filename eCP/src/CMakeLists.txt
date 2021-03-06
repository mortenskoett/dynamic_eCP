# - Handle compiler flags -
message("Input C++ compiler flags: ${CMAKE_CXX_FLAGS}")

if(CMAKE_CXX_COMPILER_ID MATCHES GNU)
  message("GNU compiler found.")
  list(APPEND cflags
    "-Wextra" "-Wall" "-O3" "-g" "-fPIC" )
  list(APPEND warnflags
    "-Wsuggest-final-types" "-Wsuggest-final-methods" "-Wsuggest-override" "-Wpedantic")
  list(APPEND unknownflags
    "-fno-rtti" "-fno-exceptions" "-Wno-unused")

else()
  message(FATAL_ERROR "Current compiler not supported. Only GNU/GCC. Compile options/flags will not be set.")
endif()

# -- Shared Library --
# Not super good and only for CMake 3.12 or later:
file(GLOB SHARED_SRC_LIST CONFIGURE_DEPENDS "${ECP_SOURCE_DIR}/src/eCP/index/shared/*.cpp")
file(GLOB SHARED_HDR_LIST CONFIGURE_DEPENDS "${ECP_SOURCE_DIR}/src/eCP/index/shared/*.hpp")

add_library(sharedLib
  SHARED
  ${SHARED_SRC_LIST}
  ${SHARED_HDR_LIST}
)

target_compile_options(sharedLib PUBLIC ${cflags})

target_include_directories(sharedLib
  PUBLIC
    ./
)

# -- eCP Library --
# Not super good and only for CMake 3.12 or later:
file(GLOB SOURCE_LIST CONFIGURE_DEPENDS "${ECP_SOURCE_DIR}/src/eCP/index/*.cpp")
file(GLOB HEADER_LIST CONFIGURE_DEPENDS "${ECP_SOURCE_DIR}/include/eCP/index/*.hpp")

add_library(eCPLib
  SHARED
  ${SOURCE_LIST}
  ${HEADER_LIST}
)

target_compile_options(eCPLib PUBLIC ${cflags})

target_include_directories(eCPLib
  PUBLIC
    ../include/
    ./
)

target_link_libraries(eCPLib
  sharedLib
)

# -- Utilities Library --
add_library(utilLib
  ./eCP/utilities/utilities.cpp
)

target_compile_options(utilLib PRIVATE ${cflags})

target_include_directories(utilLib
  PUBLIC
    ./
)

# -- Debugging Library --
add_library(debugLib
  ./eCP/debugging/debug_tools.cpp
)

target_compile_options(debugLib PRIVATE ${cflags})

target_include_directories(debugLib
  PUBLIC
    ./
)

target_link_libraries(debugLib
  sharedLib
)
