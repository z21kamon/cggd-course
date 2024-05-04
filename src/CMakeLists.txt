file(GLOB SOURCE *.cpp ${BASE_HEADERS})
file(GLOB SHADERS "../data/shaders/*.vert" "../data/shaders/*.frag" "../data/shaders/*.comp" "../data/shaders/*.geom" "../data/shaders/*.tesc" "../data/shaders/*.tese")
file(GLOB SHADER_INCLUDES "../data/shaders/includes/*.glsl")
source_group("Shaders" FILES ${SHADERS})
source_group("Shaders\\Includes" FILES ${SHADER_INCLUDES})

file(GLOB BASE_SRC "*.cpp" "../external/imgui/*.cpp")
file(GLOB BASE_HEADERS "*.hpp" "*.h")

add_library(base STATIC ${BASE_SRC} ${BASE_HEADERS})
target_link_libraries(base ${Vulkan_LIBRARY} ${WINLIBS} ${DRACO_DECODER_LIBRARY})


add_executable("acg_in_gd_lab" WIN32 ${MAIN_CPP} ${SOURCE} ${SHADERS} ${SHADER_INCLUDES})
target_link_libraries("acg_in_gd_lab" base ${Vulkan_LIBRARY} ${WINLIBS})

if(RESOURCE_INSTALL_DIR)
	install(TARGETS "acg_in_gd_lab" DESTINATION ${CMAKE_INSTALL_BINDIR})
endif()