cmake_minimum_required (VERSION 3.8)

# Hotreload for msvc
if (POLICY CMP0141)
  cmake_policy(SET CMP0141 NEW)
  set(CMAKE_MSVC_DEBUG_INFORMATION_FORMAT "$<IF:$<AND:$<C_COMPILER_ID:MSVC>,$<CXX_COMPILER_ID:MSVC>>,$<$<CONFIG:Debug,RelWithDebInfo>:EditAndContinue>,$<$<CONFIG:Debug,RelWithDebInfo>:ProgramDatabase>>")
endif()

project ("TestLibCmake")

include_directories("${CMAKE_SOURCE_DIR}/libs/raylib-5.5/include")
link_directories("${CMAKE_SOURCE_DIR}/libs/raylib-5.5/lib")

add_executable(${PROJECT_NAME} "TestLibCmake.cpp" "TestLibCmake.h")
target_link_libraries(${PROJECT_NAME} "${CMAKE_SOURCE_DIR}/libs/raylib-5.5/lib/raylib.lib")

if(WIN32)
    target_link_libraries(${PROJECT_NAME} 
        winmm 
        gdi32 
        opengl32 
        kernel32 
        user32 
        shell32
    )
elseif(UNIX AND NOT APPLE)
    target_link_libraries(${PROJECT_NAME} 
        GL      # OpenGL
        m       # Math library
        pthread # Threads
        dl      # Dynamic linking
        rt      # Real-time clock
        X11     # X11 window system
    )
elseif(APPLE)
    target_link_libraries(${PROJECT_NAME} 
        "-framework OpenGL"
        "-framework Cocoa"
        "-framework IOKit"
        "-framework CoreVideo"
    )
endif()

add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_if_different
    "${CMAKE_SOURCE_DIR}/libs/raylib-5.5/lib/raylib.dll"
    $<TARGET_FILE_DIR:${PROJECT_NAME}>
)

if (CMAKE_VERSION VERSION_GREATER 3.12)
  set_property(TARGET ${PROJECT_NAME} PROPERTY CXX_STANDARD 20)
endif()