#**********************************************************************
#
#  Copyright (c) 张晓东, 罗火灵. All rights reserved.
#  更多信息请访问: 
#    http://www.vtkchina.org (VTK中国)
#	  http://blog.csdn.net/www_doling_net (东灵工作室) 
#
#**********************************************************************

CMAKE_MINIMUM_REQUIRED( VERSION 2.8 )
PROJECT( vtkSDI )

#----------------------------------------------------------------------------------
# 查找并包含VTK工具包
FIND_PACKAGE( VTK )
IF (VTK_FOUND)
  INCLUDE (${VTK_USE_FILE})
ELSE (VTK_FOUND)
  MESSAGE (FATAL_ERROR "Cannot build without VTK. Please set VTK_DIR")
ENDIF( VTK_FOUND )

#----------------------------------------------------------------------------------
# 这里添加本工程的文件
# 主要分为两部分:
# 一是新建的单文档程序中的非工程文件
# 二是用户后续添加的类文件
SET( PROJECT_SRCS
    MainFrm.h
    MainFrm.cpp
    stdafx.h
    stdafx.cpp
    vtkSDI.h
    vtkSDI.cpp
    vtkSDIDoc.h
    vtkSDIDoc.cpp
    vtkSDIView.h
    vtkSDIView.cpp
    ResampleDialog.h
    ResampleDialog.cpp
    targetver.h
    Resource.h
    vtkSDI.rc  
    res/vtkSDI.rc2
    res/vtkSDI.ico
    res/vtkSDIDoc.ico
    res/Toolbar.bmp
    res/Toolbar256.bmp
    )

#----------------------------------------------------------------------------------
# 设置工程包含的vtk模块，这里根据需要加载对应的模块
INCLUDE("${VTK_DIR}/GUISupport/MFC/VTKMFCSettings.cmake")
SET( VTK_LIBS ${vtk_libraries} 
              vtkMFC
              vtkIO
              vtkRendering 
              vtkGraphics 
			  vtkHybrid 
              vtkFiltering 
	          vtkCommon 
	          vtkImaging 
   )

VTK_MFC_ADD_DELAYLOAD_FLAGS(CMAKE_EXE_LINKER_FLAGS
    vtkMFC.dll
    vtkIO.dll
    vtkRendering.dll
    vtkGraphics.dll
    vtkHybrid.dll
    vtkFiltering.dll
    vtkCommon.dll
    vtkImaging.dll
    )
    
#----------------------------------------------------------------------------------
ADD_EXECUTABLE( vtkSDI WIN32  ${PROJECT_SRCS} )
TARGET_LINK_LIBRARIES ( vtkSDI ${VTK_LIBS} )

#-----------------------------------------------------------------------------------
# Construct a list of paths containing runtime directories for project applications on Windows.
# Create two batch files which correctly set up the environment for the application and for Visual Studio.
#构建一个包含项目应用程序的运行时目录的路径列表。
#创建两个批处理文件，设置正确的环境下应用Visual Studio。
IF(WIN32)
  SET(PROJECT_RUNTIME_PATH "${VTK_DIR}/bin/@VS_BUILD_TYPE@")
  INCLUDE(CreateWindowsBatchScript.cmake)
  SET(VS_SOLUTION_FILE "${PROJECT_BINARY_DIR}/${PROJECT_NAME}.sln")
  FOREACH(VS_BUILD_TYPE Debug Release)
    CreateWindowsBatchScript("${CMAKE_SOURCE_DIR}/StartVS.bat.in"
      ${PROJECT_BINARY_DIR}/StartVS_${VS_BUILD_TYPE}.bat
      ${VS_BUILD_TYPE})
  ENDFOREACH()
ENDIF(WIN32)