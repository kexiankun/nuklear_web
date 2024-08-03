cmake_minimum_required(VERSION 3.10)  # 设置CMake的最低版本要求

project(nuklear_web)  # 定义项目名称和语言

# 设置C标准
set(CMAKE_C_STANDARD 99)
set(CMAKE_C_STANDARD_REQUIRED True)

# 定义源文件和二进制文件
set(SRC main.c)
set(BIN nuklear_web)

# 定义编译标志
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -pedantic -O2 -DNKCD=NKC_GLFW")

# 平台特定的库
if(WIN32)
    set(BIN ${BIN}.exe)
    set(LIBS glfw3 opengl32 m GLU32 GLEW32)
elseif(APPLE)
    set(LIBS glfw3 "-framework OpenGL" m GLEW)
else()
    set(LIBS glfw GL m GLU GLEW)
endif()

# 添加可执行文件
add_executable(${BIN} ${SRC})

# 链接库
target_link_libraries(${BIN} ${LIBS})

# Emscripten构建
add_custom_target(web
    COMMAND emcc ${SRC} -DNKCD=NKC_GLFW -o index.html -s USE_GLFW=3 -Os --preload-file data
    DEPENDS ${SRC}
    COMMENT "使用Emscripten构建web目标"
)
