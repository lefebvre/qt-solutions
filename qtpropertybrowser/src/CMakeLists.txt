# Quick hack to re-implement QT4_WRAP_CPP
# so as to override cxx extension with cpp extension
#
MACRO(JQT4_WRAP_CPP outfiles)
   QT4_GET_MOC_FLAGS(moc_flags)
   QT4_EXTRACT_OPTIONS(moc_headers moc_options moc_target ${ARGN})
   FOREACH(it ${moc_headers})
      GET_FILENAME_COMPONENT(it ${it} ABSOLUTE)
      GET_FILENAME_COMPONENT(base_path ${it} PATH)
      GET_FILENAME_COMPONENT(name_ext ${it} EXT)
      GET_FILENAME_COMPONENT(base_name ${it} NAME_WE)
      SET(cpp_name "${base_path}/${base_name}.cpp")
      IF(name_ext MATCHES "cpp")
         QT4_MAKE_OUTPUT_FILE(${it} "" moc outfile)
      ELSE()
         QT4_MAKE_OUTPUT_FILE(${it} moc_ cpp outfile)
      ENDIF()
      QT4_CREATE_MOC_COMMAND(${it} ${outfile} "${moc_flags}" "${moc_options}" "${moc_target}")
      MACRO_ADD_FILE_DEPENDENCIES("${cpp_name}" "${outfile}")
   ENDFOREACH()
ENDMACRO()
MACRO(JQT5_WRAP_CPP outfiles)
   QT5_GET_MOC_FLAGS(moc_flags)
#   QT5_EXTRACT_OPTIONS(moc_headers moc_options moc_target ${ARGN})
   #FOREACH(it ${moc_headers})
   FOREACH(it ${ARGN})
      GET_FILENAME_COMPONENT(it ${it} ABSOLUTE)
      GET_FILENAME_COMPONENT(base_path ${it} PATH)
      GET_FILENAME_COMPONENT(name_ext ${it} EXT)
      GET_FILENAME_COMPONENT(base_name ${it} NAME_WE)
      SET(cpp_name "${base_path}/${base_name}.cpp")
      IF(name_ext MATCHES "cpp")
         QT5_MAKE_OUTPUT_FILE(${it} "" moc outfile)
      ELSE()
         QT5_MAKE_OUTPUT_FILE(${it} moc_ cpp outfile)
      ENDIF()
      QT5_CREATE_MOC_COMMAND(${it} ${outfile} "${moc_flags}" "${moc_options}" "${moc_target}")
      SET_PROPERTY(SOURCE "${cpp_name}" APPEND PROPERTY OBJECT_DEPENDS "${outfile}")
   ENDFOREACH()
ENDMACRO()
#
# Declare Source Files
#
SET(SOURCE
qtbuttonpropertybrowser.cpp
qteditorfactory.cpp
qtgroupboxpropertybrowser.cpp
qtpropertybrowser.cpp
qtpropertybrowserutils.cpp
moc_qtpropertybrowserutils_p.cpp # this guy gets generated below.
qtpropertymanager.cpp
qttreepropertybrowser.cpp
qtvariantproperty.cpp
)
#
# Include binary directory to find moc_...cpp generated files.
#
INCLUDE_DIRECTORIES("${CMAKE_CURRENT_BINARY_DIR}")
#
# Collect header files
#
SET(HEADERS
qtbuttonpropertybrowser.h
qteditorfactory.h
qtgroupboxpropertybrowser.h
qtpropertybrowser.h
qtpropertybrowserutils_p.h
qtpropertymanager.h
qttreepropertybrowser.h
qtvariantproperty.h
)
SET(MOC_LIST
qtbuttonpropertybrowser.h
qteditorfactory.h
qteditorfactory.cpp
qtgroupboxpropertybrowser.h
qtpropertybrowser.h
qtpropertybrowserutils_p.h
qttreepropertybrowser.h
qttreepropertybrowser.cpp
qtpropertymanager.h
qtpropertymanager.cpp
qtvariantproperty.h
)
#
# MOC the headers
# several cpp files required because of "PRIVATE" classes
IF(TPL_ENABLE_QT)
   JQT4_WRAP_CPP(out_files ${MOC_LIST})
   QT4_ADD_RESOURCES(resource_result  qtpropertybrowser.qrc)
ELSE() # assume Qt5
   JQT5_WRAP_CPP(out_files ${MOC_LIST})
   QT5_ADD_RESOURCES(resource_result  qtpropertybrowser.qrc)
ENDIF()
MESSAGE(STATUS "Added resource ${resource_result}")

#
# Add QtPropertyBrowser Library
#
TRIBITS_ADD_LIBRARY(QtPropertyBrowser
   SOURCES ${SOURCE} ${resource_result}
   NOINSTALLHEADERS ${HEADERS}
)


INSTALL(FILES ${HEADERS}
        DESTINATION "include/qt-solutions/qtpropertybrowser/src")

