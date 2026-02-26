cmake_minimum_required(VERSION 3.16)

set(U8G2_INCLUDE_DIRS
	"include"
)

set(U8G2_PRIV_INCLUDE_DIRS
	"src/core"
)

file(GLOB U8G2_CORE_SOURCES
	"src/core/*.c"
)

file(GLOB U8G2_DRIVER_SOURCES
	"src/driver/*.c"
)

set(U8G2_SOURCES
	${U8G2_CORE_SOURCES}
	${U8G2_DRIVER_SOURCES}
)

# 可选 MUI 源目录（兼容 src/mui 与 src/mnui）
if(EXISTS "${CMAKE_CURRENT_LIST_DIR}/src/mui")
	file(GLOB U8G2_MUI_SOURCES "src/mui/*.c")
	list(APPEND U8G2_SOURCES ${U8G2_MUI_SOURCES})
	list(APPEND U8G2_PRIV_INCLUDE_DIRS "src/mui")
elseif(EXISTS "${CMAKE_CURRENT_LIST_DIR}/src/mnui")
	file(GLOB U8G2_MUI_SOURCES "src/mnui/*.c")
	list(APPEND U8G2_SOURCES ${U8G2_MUI_SOURCES})
	list(APPEND U8G2_PRIV_INCLUDE_DIRS "src/mnui")
endif()

# 可选端口层（若目录存在源文件则自动编入）
if(EXISTS "${CMAKE_CURRENT_LIST_DIR}/src/port")
	file(GLOB U8G2_PORT_SOURCES
		"src/port/*.c"
	)
	list(APPEND U8G2_SOURCES ${U8G2_PORT_SOURCES})
	list(APPEND U8G2_PRIV_INCLUDE_DIRS "src/port")
endif()

if(DEFINED ESP_PLATFORM AND COMMAND idf_component_register)
	idf_component_register(
		SRCS ${U8G2_SOURCES}
		INCLUDE_DIRS ${U8G2_INCLUDE_DIRS}
		PRIV_INCLUDE_DIRS ${U8G2_PRIV_INCLUDE_DIRS}
		REQUIRES driver esp_driver_i2c esp_driver_spi
	)
else()
	add_library(u8g2 STATIC ${U8G2_SOURCES})
	target_include_directories(u8g2
		PUBLIC ${U8G2_INCLUDE_DIRS}
		PRIVATE ${U8G2_PRIV_INCLUDE_DIRS}
	)
endif()
