cmake_minimum_required (VERSION 3.28)
project("modules")

add_subdirectory(image)
add_subdirectory(ffmpeg)
add_subdirectory(oal)
add_subdirectory(decklink)
add_subdirectory(screen)
add_subdirectory(newtek)

# Artnet has boost::variant compatibility issues on macOS
if (NOT CMAKE_SYSTEM_NAME STREQUAL "Darwin")
	add_subdirectory(artnet)
endif()

if (ENABLE_HTML)
	add_subdirectory(html)
endif ()

if (MSVC)
	add_subdirectory(flash)
	add_subdirectory(bluefish)
endif()


# Out-of-tree modules: -DCASPARCG_EXTERNAL_MODULES="/path/to/module_a;/path/to/module_b"
# Each directory is a normal module project (casparcg_add_module_project) living outside this tree,
# e.g. proprietary or experimental modules kept in another repository. Pass an absolute HEADER_FILE
# to casparcg_add_module_project so the generated included_modules.h can find the module header.
set(CASPARCG_EXTERNAL_MODULES "" CACHE STRING "Semicolon separated list of out-of-tree module directories")
foreach(module_dir ${CASPARCG_EXTERNAL_MODULES})
	get_filename_component(module_name "${module_dir}" NAME)
	message(STATUS "External module: ${module_name} (${module_dir})")
	add_subdirectory("${module_dir}" "${CMAKE_BINARY_DIR}/external_modules/${module_name}")
endforeach()
