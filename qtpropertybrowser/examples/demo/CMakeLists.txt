
#
# Process demo resource file
#
IF(NOT USE_QT5)
   QT4_ADD_RESOURCES(resource_result demo.qrc)
ELSE() # assume qt5
   QT5_ADD_RESOURCES(resource_result demo.qrc)
ENDIF()


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

