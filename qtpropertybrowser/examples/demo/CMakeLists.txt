
#
# Process demo resource file
#
QT4_ADD_RESOURCES(resource_result demo.qrc)


#
# Add source directory for includes
#
INCLUDE_DIRECTORIES("${CMAKE_CURRENT_SOURCE_DIR}/../../src")

#
#Add demo executable
#
TRIBITS_ADD_EXECUTABLE(qtpropertybrowser_demo
       NOEXEPREFIX
       SOURCES main.cpp ${resource_result})

