CMAKE_MINIMUM_REQUIRED(VERSION 2.8.1)

# -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- #

# Avoid CMake regeneration within generated project.
SET(CMAKE_SUPPRESS_REGENERATION "1")

# -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- #

# Project declaration.
PROJECT(KLabTest)

# -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- #

# Compiler configuration
IF(WIN32)
	# Compiler options definition.
	STRING(REPLACE "/MDd" "/MTd" CMAKE_CXX_FLAGS_DEBUG_INIT ${CMAKE_CXX_FLAGS_DEBUG_INIT})
    STRING(REPLACE "/MDd" "/MTd" CMAKE_C_FLAGS_DEBUG_INIT   ${CMAKE_C_FLAGS_DEBUG_INIT})
    STRING(REPLACE "/MD"  "/MT"  CMAKE_C_FLAGS_RELEASE      ${CMAKE_C_FLAGS_RELEASE})
    STRING(REPLACE "/MDd" "/MTd" CMAKE_C_FLAGS_DEBUG        ${CMAKE_C_FLAGS_DEBUG})
    STRING(REPLACE "/MD"  "/MT"  CMAKE_CXX_FLAGS_RELEASE    ${CMAKE_CXX_FLAGS_RELEASE})
    STRING(REPLACE "/MDd" "/MTd" CMAKE_CXX_FLAGS_DEBUG      ${CMAKE_CXX_FLAGS_DEBUG})
		
	# Output path.	
	SET(CMAKE_RUNTIME_OUTPUT_DIRECTORY ../../../../../../../bin/win/)	
	
	FOREACH(OUTPUTCONFIG ${CMAKE_CONFIGURATION_TYPES})	# For multi-config builds.
		STRING(TOUPPER ${OUTPUTCONFIG} OUTPUTCONFIG)
		SET(CMAKE_RUNTIME_OUTPUT_DIRECTORY_${OUTPUTCONFIG} ../../../../../../../bin/win/)
	ENDFOREACH(OUTPUTCONFIG CMAKE_CONFIGURATION_TYPES)
ENDIF(WIN32)

IF(UNIX)
	IF(CMAKE_CL_64 OR CMAKE_SIZEOF_VOID_P EQUAL 8)
		IF(${FORCE_32BITS_BUILD} EQUAL 1)
			# Only build 32-bit binaries
			ADD_DEFINITIONS(-m32)
			SET(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -m32")
			SET(CMAKE_SHARED_LIBRARY_C_FLAGS "${CMAKE_SHARED_LIBRARY_C_FLAGS} -m32")
			SET(CMAKE_SHARED_LIBRARY_CXX_FLAGS "${CMAKE_SHARED_LIBRARY_CXX_FLAGS} -m32")
			SET(CMAKE_SIZEOF_VOID_P 4)
		ENDIF(${FORCE_32BITS_BUILD} EQUAL 1)
	ENDIF(CMAKE_CL_64 OR CMAKE_SIZEOF_VOID_P EQUAL 8)
	
	# Output path.
	SET(CMAKE_RUNTIME_OUTPUT_DIRECTORY ../../../../../bin/unix/)
	
	FOREACH(OUTPUTCONFIG ${CMAKE_CONFIGURATION_TYPES})	# For multi-config builds.
		STRING(TOUPPER ${OUTPUTCONFIG} OUTPUTCONFIG)
		SET(CMAKE_RUNTIME_OUTPUT_DIRECTORY_${OUTPUTCONFIG} ../../../../../bin/unix/)
	ENDFOREACH(OUTPUTCONFIG CMAKE_CONFIGURATION_TYPES)
ENDIF(UNIX)

IF(APPLE)
	IF(CMAKE_CL_64 OR CMAKE_SIZEOF_VOID_P EQUAL 8)
		IF(${FORCE_32BITS_BUILD} EQUAL 1)
			# Only build 32-bit binaries
			ADD_DEFINITIONS(-m32)
			SET(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -m32")
			SET(CMAKE_SHARED_LIBRARY_C_FLAGS "${CMAKE_SHARED_LIBRARY_C_FLAGS} -m32")
			SET(CMAKE_SHARED_LIBRARY_CXX_FLAGS "${CMAKE_SHARED_LIBRARY_CXX_FLAGS} -m32")
			SET(CMAKE_SIZEOF_VOID_P 4)
		ENDIF(${FORCE_32BITS_BUILD} EQUAL 1)
	ENDIF(CMAKE_CL_64 OR CMAKE_SIZEOF_VOID_P EQUAL 8)
	
	# Output path.
	SET(CMAKE_RUNTIME_OUTPUT_DIRECTORY ../../../../../bin/macos/)
	
	FOREACH(OUTPUTCONFIG ${CMAKE_CONFIGURATION_TYPES})	# For multi-config builds.
		STRING(TOUPPER ${OUTPUTCONFIG} OUTPUTCONFIG)
		SET(CMAKE_RUNTIME_OUTPUT_DIRECTORY_${OUTPUTCONFIG} ../../../../../bin/macos/)
	ENDFOREACH(OUTPUTCONFIG CMAKE_CONFIGURATION_TYPES)
ENDIF(APPLE)

# -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- #

# Generation of the source files.
IF(WIN32)
	FILE(
		GLOB
		SourceFiles
		*.h
		*.cpp 
		win/*.h
		win/*.cpp      
		../include/*.h 
		../include/win/*.h 
		../src/*.cpp	
		../src/win/*.cpp
		../resources/win/KLabTest.rc
		../resources/win/resource.h
	)
ENDIF(WIN32)

IF(UNIX)
	FILE(
		GLOB
		SourceFiles
		*.h
		*.cpp 
		unix/*.h
		unix/*.cpp 
		../include/*.h 
		../include/unix/*.h 
		../src/*.cpp	
		../src/unix/*.cpp
	)
ENDIF(UNIX)

IF(APPLE)
	FILE(
		GLOB
		SourceFiles
		*.h
		*.cpp 
		macos/*.h
		macos/*.cpp 
		../include/*.h 
		../include/macos/*.h 
		../src/*.cpp	
		../src/macos/*.cpp
	)
ENDIF(APPLE)

# -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- #

# Project filters definition.
SOURCE_GROUP("UnitTest\\Headers" REGULAR_EXPRESSION .*[.]h)
SOURCE_GROUP("UnitTest\\Sources" REGULAR_EXPRESSION .*[.]cpp)

SOURCE_GROUP("Model\\Interfaces" FILES ../include/ILogData.h ../include/ILogOutput.h)
SOURCE_GROUP("Model\\Headers" REGULAR_EXPRESSION ../include/.*[.]h)
SOURCE_GROUP("Model\\Sources" REGULAR_EXPRESSION ../src/.*[.]cpp)

SOURCE_GROUP("Main\\Headers" FILES ../include/KLab.h)

IF(WIN32)
	SOURCE_GROUP("Main\\Resources" FILES ../resources/win/KLabTest.rc)
	SOURCE_GROUP("Main\\Resources" FILES ../resources/win/resource.h)
ENDIF(WIN32)

IF(WIN32)
	SOURCE_GROUP("Main\\Sources" FILES win/main.cpp)
	SOURCE_GROUP("WinModel\\Headers" REGULAR_EXPRESSION ../include/win/.*[.]h)
	SOURCE_GROUP("WinModel\\Sources" REGULAR_EXPRESSION ../src/win/.*[.]cpp)
ENDIF(WIN32)

IF(UNIX)
	SOURCE_GROUP("Main\\Sources" FILES unix/main.cpp)
	SOURCE_GROUP("UnixModel\\Headers" REGULAR_EXPRESSION ../include/unix/.*[.]h)
	SOURCE_GROUP("UnixModel\\Sources" REGULAR_EXPRESSION ../src/unix/.*[.]cpp)
ENDIF(UNIX)

IF(APPLE)
	SOURCE_GROUP("Main\\Sources" FILES macos/main.cpp)
	SOURCE_GROUP("MacOSModel\\Headers" REGULAR_EXPRESSION ../include/macos/.*[.]h)
	SOURCE_GROUP("MacOSModel\\Sources" REGULAR_EXPRESSION ../src/macos/.*[.]cpp)
ENDIF(APPLE)

# -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- #

# Declaration of the executable
IF(CMAKE_BUILD_TYPE MATCHES "Debug")	
	IF(CMAKE_SIZEOF_VOID_P EQUAL 4)						# 32 bits generation.
		ADD_EXECUTABLE(KLabTest_d ${SourceFiles})
		IF(WIN32)
			TARGET_LINK_LIBRARIES(KLabTest_d cpptest_d)
		ENDIF(WIN32)
		IF(UNIX)
			TARGET_LINK_LIBRARIES(KLabTest_d cpptest_d pthread dl)
		ENDIF(UNIX)
		IF(APPLE)
			TARGET_LINK_LIBRARIES(KLabTest_d cpptest_d pthread dl "-framework CoreServices")
		ENDIF(APPLE)
	ENDIF(CMAKE_SIZEOF_VOID_P EQUAL 4)		
	IF(CMAKE_CL_64 OR CMAKE_SIZEOF_VOID_P EQUAL 8)		# 64bits generation.
		ADD_EXECUTABLE(KLabTest64_d ${SourceFiles})
		IF(WIN32)
			TARGET_LINK_LIBRARIES(KLabTest64_d cpptest64_d)
		ENDIF(WIN32)
		IF(UNIX)
			TARGET_LINK_LIBRARIES(KLabTest64_d cpptest64_d pthread dl)
		ENDIF(UNIX)
		IF(APPLE)
			TARGET_LINK_LIBRARIES(KLabTest64_d cpptest64_d pthread dl "-framework CoreServices")
		ENDIF(APPLE)
	ENDIF(CMAKE_CL_64 OR CMAKE_SIZEOF_VOID_P EQUAL 8)
ENDIF(CMAKE_BUILD_TYPE MATCHES "Debug")	
IF(CMAKE_BUILD_TYPE MATCHES "Release")
	IF(CMAKE_SIZEOF_VOID_P EQUAL 4)						# 32 bits generation.
		ADD_EXECUTABLE(KLabTest ${SourceFiles})	
		IF(WIN32)
			TARGET_LINK_LIBRARIES(KLabTest cpptest)
		ENDIF(WIN32)
		IF(UNIX)
			TARGET_LINK_LIBRARIES(KLabTest cpptest pthread dl)
		ENDIF(UNIX)
		IF(APPLE)
			TARGET_LINK_LIBRARIES(KLabTest cpptest pthread dl "-framework CoreServices")
		ENDIF(APPLE)
	ENDIF(CMAKE_SIZEOF_VOID_P EQUAL 4)
	IF(CMAKE_CL_64 OR CMAKE_SIZEOF_VOID_P EQUAL 8)		# 64bits generation.
		ADD_EXECUTABLE(KLabTest64 ${SourceFiles})
		IF(WIN32)
			TARGET_LINK_LIBRARIES(KLabTest64 cpptest64)
		ENDIF(WIN32)
		IF(UNIX)
			TARGET_LINK_LIBRARIES(KLabTest64 cpptest64 pthread dl)
		ENDIF(UNIX)
		IF(APPLE)
			TARGET_LINK_LIBRARIES(KLabTest64 cpptest64 pthread dl "-framework CoreServices")
		ENDIF(APPLE)
	ENDIF(CMAKE_CL_64 OR CMAKE_SIZEOF_VOID_P EQUAL 8)
ENDIF(CMAKE_BUILD_TYPE MATCHES "Release")

# -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- #
