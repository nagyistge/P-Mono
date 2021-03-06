set ( API_PATH ${P_Prt_Src_Path}/API/ )
set ( Core_PATH ${P_Prt_Src_Path}/Core/ )

if(Win32)
	set ( User_PATH ${P_Prt_Src_Path}/WinUser/ )
elseif(LINUX OR MACOSX)
	set ( User_PATH ${CMAKE_CURRENT_SOURCE_DIR}/LinuxUser/ )
endif()

set( PrtUser_Output_PATH ${PROJECT_SOURCE_DIR}/../Bld/Drops/PrtUser/)
set( LIBRARY_OUTPUT_PATH ${PrtUser_Output_PATH}/lib/ )
set( LIBRARY_OUTPUT_INCLUDE_PATH ${PrtUser_Output_PATH}/include/ )

file ( GLOB API_SRC ${API_PATH}/*.c ${API_PATH}/*.h )
file ( GLOB Core_SRC ${Core_PATH}/*.c ${Core_PATH}/*.h )
file ( GLOB User_SRC ${User_PATH}/*.c ${User_PATH}/*.h )

set ( PrtUserSrc ${API_SRC} ${Core_SRC} ${User_SRC} )

add_library(Prt OBJECT ${PrtUserSrc})
add_library(Prt_test OBJECT ${PrtUserSrc})
add_library(Prt_shared SHARED $<TARGET_OBJECTS:Prt>)
add_library(Prt_static STATIC $<TARGET_OBJECTS:Prt>)
target_compile_definitions( Prt_test PRIVATE -DPRT_TEST )

target_include_directories(Prt PUBLIC ${API_PATH})
target_include_directories(Prt PUBLIC ${Core_PATH})
target_include_directories(Prt PUBLIC ${User_PATH})
get_property(Prt_Published_Headers_PATHS TARGET Prt PROPERTY INTERFACE_INCLUDE_DIRECTORIES)
# need to manually set includes for static and shared libraries, from object library include path
target_include_directories(Prt_shared PUBLIC ${Prt_Published_Headers_PATHS})
target_include_directories(Prt_static PUBLIC ${Prt_Published_Headers_PATHS})
target_include_directories(Prt_test PUBLIC ${Prt_Published_Headers_PATHS})

Publish_Library_Header(Prt_shared)
