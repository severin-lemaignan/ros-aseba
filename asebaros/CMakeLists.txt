cmake_minimum_required(VERSION 2.8.3)
project(asebaros)

#Declare build dependencies
find_package(catkin REQUIRED
    COMPONENTS
    message_generation
    std_msgs
    dashel
    aseba )

# This driver uses Boost threads
find_package(Boost REQUIRED COMPONENTS thread)


#Add message files
add_message_files(DIRECTORY msg
    FILES
    AsebaAnonymousEvent.msg
    AsebaEvent.msg)

#Add service files
add_service_files(DIRECTORY srv
    FILES
    GetEventId.srv
    GetEventName.srv
    GetNodeId.srv
    GetNodeList.srv
    GetNodeName.srv
    GetVariableList.srv
    GetVariable.srv
    LoadScripts.srv
    SetVariable.srv )

#And now generate the messages
generate_messages(DEPENDENCIES std_msgs)

# Generate catkin/pkg-config import information
catkin_package( CATKIN_DEPENDS message_runtime std_msgs )

if (WIN32)
	set(EXTRA_LIBS ${EXTRA_LIBS} winspool ws2_32 setupapi)
else (WIN32)
	if (APPLE)
		find_library(COREFOUNDATION_LIBRARY CoreFoundation)
		find_library(IOKIT_LIBRARY IOKit)
		mark_as_advanced(COREFOUNDATION_LIBRARY)
		mark_as_advanced(IOKIT_LIBRARY)
		set(EXTRA_LIBS ${EXTRA_LIBS} ${COREFOUNDATION_LIBRARY} ${IOKIT_LIBRARY} ${EXTRA_LIBS})
	else (APPLE)
		find_path(UDEV_INCLUDE_DIR libudev.h)
		find_library(UDEV_LIBS udev)
		include_directories(${UDEV_INCLUDE_DIR})
		set(EXTRA_LIBS ${EXTRA_LIBS} ${UDEV_LIBS} ${EXTRA_LIBS})
	endif (APPLE)
endif (WIN32)

# libxml2
find_package(LibXml2 REQUIRED)

include_directories(
    ${catkin_INCLUDE_DIRS}
    ${Boost_INCLUDE_DIRS}
    ${LIBXML2_INCLUDE_DIR})

add_executable(asebaros src/asebaros.cpp)
target_link_libraries(asebaros
    asebacompiler
    asebacore
    ${catkin_LIBRARIES}
    ${Boost_LIBRARIES}
    ${LIBXML2_LIBRARIES}
    ${EXTRA_LIBS})

add_dependencies(asebaros ${PROJECT_NAME}_generate_messages_cpp)

install(TARGETS asebaros
        RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION} )

