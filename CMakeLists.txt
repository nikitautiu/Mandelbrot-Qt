PROJECT(Mandelbrot-Qt)


# Tested only with version 2.8.
CMAKE_MINIMUM_REQUIRED(VERSION 2.8)
IF(UNIX)
    SET(CUDA_HOST_COMPILER /usr/bin/gcc-6)
ENDIF()

SET(CMAKE_CXX_STANDARD 17)

# requires Qt4 and CUDA
# CUDA might not be part of CMake 2.6, either install 2.8 or install the CUDA modules yourself.
FIND_PACKAGE(Qt4 REQUIRED)
FIND_PACKAGE(CUDA REQUIRED)

# QT settings
# see http://www.cmake.org/cmake/help/cmake-2-8-docs.html#module:FindQt4
SET(QT_USE_QTOPENGL 1)
SET(QT_USE_QTXML 1)

IF(WIN32)
    SET (QT_USE_MOTIF 0)
ENDIF()

INCLUDE(${QT_USE_FILE})

# List of all the C++ source code files
SET(MANDELBROT_SRCS_CXX
        mainwidget.cpp
        optionswidget.cpp
        main.cpp
        )

# List of all Qt header files
SET(MANDELBROT_MOC_HDRS
        mainwidget.h
        optionswidget.h
        )

# List of Qt .ui files
#SET(MANDELBROT_UIS
#        )

# List of CUDA files
SET(MANDELBROT_CUDA_SRCS
        iterate.cu
        )

# for moc
QT4_WRAP_CPP(MANDELBROT_SRCS_CXX ${MANDELBROT_MOC_HDRS})


IF(UNIX)
    CUDA_ADD_EXECUTABLE(${CMAKE_PROJECT_NAME} ${MANDELBROT_CUDA_SRCS} ${MANDELBROT_SRCS_CXX})
ELSEIF(APPLE)
    CUDA_ADD_EXECUTABLE(${CMAKE_PROJECT_NAME} MACOSX_BUNDLE ${MANDELBROT_CUDA_SRCS} ${MANDELBROT_SRCS_CXX})
ELSEIF(WIN32)
    CUDA_ADD_EXECUTABLE(${CMAKE_PROJECT_NAME} WIN32 ${MANDELBROT_CUDA_SRCS} ${MANDELBROT_SRCS_CXX})
ENDIF()

set_target_properties(${CMAKE_PROJECT_NAME}
        PROPERTIES CUDA_SEPARABLE_COMPILATION ON)
TARGET_LINK_LIBRARIES(${CMAKE_PROJECT_NAME} ${QT_LIBRARIES})

INSTALL(TARGETS ${CMAKE_PROJECT_NAME} DESTINATION bin)