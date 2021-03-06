find_package(OpenGL)
find_package(GLUT)

find_package(VTK)
include(${VTK_USE_FILE})

find_package(Qt4)
set(QT_USE_QTOPENGL TRUE)
include(${QT_USE_FILE})
add_definitions(${QT_DEFINITIONS})

include_directories(${OPENGL_INCLUDE_DIR})
include_directories(${GLUT_INCLUDE_DIR})
include_directories(${CMAKE_CURRENT_BINARY_DIR})
include_directories(${CMAKE_SOURCE_DIR}/demos)

if(VTK_USE_MPI)
  include_directories(${VTK_MPI_INCLUDE_DIR})
endif()

set (DATA_DIR "" CACHE PATH "Data directory")
set (INIT_BUFFER_SIZE 10000 CACHE INT "Initial VBO Size")

if (ENABLE_INTEROP)
  set(GPU_FLAGS "-DUSE_INTEROP")
endif ()

if (USE_CUDA)
  cuda_add_executable(contourGPU contour.cu isorender.cu ${CMAKE_SOURCE_DIR}/piston/util/quaternion.cpp OPTIONS "-DDATA_DIRECTORY=${DATA_DIR}" "-DBUFFER_SIZE=${INIT_BUFFER_SIZE}" ${GPU_FLAGS})
  target_link_libraries(contourGPU pthread vtkIO GLEW ${OPENGL_LIBRARIES} ${GLUT_LIBRARIES})
endif ()

add_executable(contourOMP contour.cpp isorender.cpp ${CMAKE_SOURCE_DIR}/piston/util/quaternion.cpp)
set_target_properties(contourOMP PROPERTIES COMPILE_FLAGS "-fopenmp -DDATA_DIRECTORY=${DATA_DIR} -DTHRUST_DEVICE_BACKEND=THRUST_DEVICE_BACKEND_OMP")
target_link_libraries(contourOMP pthread vtkIO GLEW gomp ${OPENGL_LIBRARIES} ${GLUT_LIBRARIES})


set(swdemo_SOURCES main.cpp glwindow.cpp isorender.cu ${CMAKE_SOURCE_DIR}/piston/util/quaternion.cpp)
set(swdemo_HEADERS glwindow.h)
set(swdemo_FORMS swdemo.ui)
QT4_WRAP_CPP(swdemo_HEADERS_MOC ${swdemo_HEADERS})
QT4_WRAP_UI(swdemo_FORMS_HEADERS ${swdemo_FORMS})

if (USE_CUDA)
  cuda_add_executable(swdemoGPU isorender.cu ${swdemo_SOURCES} ${swdemo_HEADERS_MOC} ${swdemo_FORMS_HEADERS} OPTIONS "-DDATA_DIRECTORY=${DATA_DIR}" "-DBUFFER_SIZE=${INIT_BUFFER_SIZE}" ${GPU_FLAGS})
  target_link_libraries(swdemoGPU GLEW pthread vtkIO ${OPENGL_LIBRARIES} ${GLUT_LIBRARIES} ${QT_LIBRARIES})
endif ()

add_executable(swdemoOMP isorender.cpp ${swdemo_SOURCES} ${swdemo_HEADERS_MOC} ${swdemo_FORMS_HEADERS}) 
set_target_properties(swdemoOMP PROPERTIES COMPILE_FLAGS "-fopenmp -DDATA_DIRECTORY=${DATA_DIR} -DTHRUST_DEVICE_BACKEND=THRUST_DEVICE_BACKEND_OMP")
target_link_libraries(swdemoOMP GLEW pthread vtkIO gomp ${OPENGL_LIBRARIES} ${GLUT_LIBRARIES} ${QT_LIBRARIES})


set(demo_SOURCES demo.cpp glwindow.cpp ${CMAKE_SOURCE_DIR}/piston/util/quaternion.cpp)
set(demo_HEADERS glwindow.h)
set(demo_FORMS demo.ui)
QT4_WRAP_CPP(demo_HEADERS_MOC ${demo_HEADERS})
QT4_WRAP_UI(demo_FORMS_HEADERS ${demo_FORMS})

if (USE_CUDA)
  cuda_add_executable(demoGPU isorender.cu ${demo_SOURCES} ${demo_HEADERS_MOC} ${demo_FORMS_HEADERS} OPTIONS "-DDATA_DIRECTORY=${DATA_DIR}" "-DBUFFER_SIZE=${INIT_BUFFER_SIZE}" ${GPU_FLAGS})
  target_link_libraries(demoGPU GLEW pthread vtkIO ${OPENGL_LIBRARIES} ${GLUT_LIBRARIES} ${QT_LIBRARIES})
endif ()

add_executable(demoOMP isorender.cpp ${demo_SOURCES} ${demo_HEADERS_MOC} ${demo_FORMS_HEADERS})
set_target_properties(demoOMP PROPERTIES COMPILE_FLAGS "-fopenmp -DDATA_DIRECTORY=${DATA_DIR} -DTHRUST_DEVICE_BACKEND=THRUST_DEVICE_BACKEND_OMP")
target_link_libraries(demoOMP GLEW pthread vtkIO gomp ${OPENGL_LIBRARIES} ${GLUT_LIBRARIES} ${QT_LIBRARIES})

if (USE_CUDA)
  set(bigdemo_SOURCES bigdemo.cpp glwindow.cpp isorender.cu ${CMAKE_SOURCE_DIR}/piston/util/quaternion.cpp)
  set(bigdemo_HEADERS glwindow.h)
  set(bigdemo_FORMS bigdemo.ui)
  QT4_WRAP_CPP(bigdemo_HEADERS_MOC ${bigdemo_HEADERS})
  QT4_WRAP_UI(bigdemo_FORMS_HEADERS ${bigdemo_FORMS})

  cuda_add_executable(bigdemoGPU ${bigdemo_SOURCES} ${bigdemo_HEADERS_MOC} ${bigdemo_FORMS_HEADERS} OPTIONS "-DDATA_DIRECTORY=${DATA_DIR}" "-DBUFFER_SIZE=${INIT_BUFFER_SIZE}" ${GPU_FLAGS})
  target_link_libraries(bigdemoGPU GLEW pthread vtkIO ${OPENGL_LIBRARIES} ${GLUT_LIBRARIES} ${QT_LIBRARIES})
endif ()

#add_executable(contourVTK contourVTK.cpp)
#set(CMAKE_INSTALL_RPATH ${VTK_DIR}/bin)
#set_source_files_properties(contourVTK.cpp PROPERTIES COMPILE_FLAGS "-DDATA_DIRECTORY=${DATA_DIR}")
#target_link_libraries(contourVTK vtkRendering vtkFiltering vtkIO vtkCommon vtkGraphics)

#add_executable(contourPAR contourPAR.cpp)
#set_source_files_properties(contourPAR.cpp PROPERTIES COMPILE_FLAGS "-fopenmp -DDATA_DIRECTORY=${DATA_DIR}")
#target_link_libraries(contourPAR vtkRendering vtkFiltering vtkIO vtkCommon vtkGraphics vtkParallel)

if (USE_CUDA)
  cuda_add_executable(glyphGPU glyph.cu glyphrender.cu ${CMAKE_SOURCE_DIR}/piston/util/quaternion.cpp OPTIONS "-DDATA_DIRECTORY=${DATA_DIR}" "-DBUFFER_SIZE=${INIT_BUFFER_SIZE}" ${GPU_FLAGS})
  target_link_libraries(glyphGPU pthread vtkIO vtkRendering vtkFiltering vtkCommon vtkGraphics GLEW ${OPENGL_LIBRARIES} ${GLUT_LIBRARIES})
  
  cuda_add_executable(tetraGPU tetra.cu tetrarender.cu ${CMAKE_SOURCE_DIR}/piston/util/quaternion.cpp OPTIONS "-DDATA_DIRECTORY=${DATA_DIR}" "-DBUFFER_SIZE=${INIT_BUFFER_SIZE}" ${GPU_FLAGS})
  target_link_libraries(tetraGPU pthread vtkIO vtkRendering vtkFiltering vtkCommon vtkGraphics GLEW ${OPENGL_LIBRARIES} ${GLUT_LIBRARIES})
endif ()

if (USE_CUDA)
  cuda_add_executable(rendererGPU renderer.cu rendererrender.cu ${CMAKE_SOURCE_DIR}/piston/util/quaternion.cpp OPTIONS "-DDATA_DIRECTORY=${DATA_DIR}" "-DBUFFER_SIZE=${INIT_BUFFER_SIZE}" ${GPU_FLAGS})
  target_link_libraries(rendererGPU pthread vtkIO vtkRendering vtkFiltering vtkCommon vtkGraphics GLEW ${OPENGL_LIBRARIES} ${GLUT_LIBRARIES})
endif ()

add_executable(rendererOMP renderer.cpp rendererrender.cpp ${CMAKE_SOURCE_DIR}/piston/util/quaternion.cpp) 
set_target_properties(rendererOMP PROPERTIES COMPILE_FLAGS "-fopenmp -DDATA_DIRECTORY=${DATA_DIR} -DTHRUST_DEVICE_BACKEND=THRUST_DEVICE_BACKEND_OMP")
target_link_libraries(rendererOMP pthread vtkIO gomp ${OPENGL_LIBRARIES} ${GLUT_LIBRARIES})
