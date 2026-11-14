cmake_minimum_required(VERSION 3.20)
project(TwinsPlayer LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Décodeur AC-3 : portable, testable en ligne de commande sur tout système
add_executable(ac3test src/ac3test.cpp src/ac3.cpp)
add_executable(surroundtest src/surroundtest.cpp src/surround.cpp)

if(WIN32)
  if(MSVC)
    # CRT statique : l'exécutable tourne sans installer le « Visual C++ Redistributable »
    set(CMAKE_MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>")
    add_compile_options(/W3 /utf-8)
  endif()
  add_executable(TwinsPlayer WIN32 src/main.cpp src/player.cpp src/ac3.cpp src/surround.cpp)
  target_compile_definitions(TwinsPlayer PRIVATE UNICODE _UNICODE)
  target_link_libraries(TwinsPlayer PRIVATE
    mfplat mfreadwrite mf mfuuid ole32 uuid user32 gdi32 comctl32 comdlg32 shell32 propsys winmm)
endif()
