add_gdal_driver(
  TARGET gdal_NITF
  SOURCES mgrs.c
          nitfdataset.cpp
          rpftocdataset.cpp
          nitfrasterband.cpp
          ecrgtocdataset.cpp
          nitffile.c
          nitfimage.c
          nitfaridpcm.cpp
          nitfbilevel.cpp
          rpftocfile.cpp
          nitfdes.c
          nitf_gcprpc.cpp
          nitfwritejpeg.cpp
          nitfwritejpeg_12.cpp
  BUILTIN)
gdal_standard_includes(gdal_NITF)
target_include_directories(gdal_NITF PRIVATE ${GDAL_RASTER_FORMAT_SOURCE_DIR}/gtiff
                                             ${GDAL_RASTER_FORMAT_SOURCE_DIR}/vrt)

if (CMAKE_VERSION VERSION_GREATER_EQUAL 3.16)
  set_property(SOURCE nitfimage.c nitffile.c nitfdes.c PROPERTY SKIP_UNITY_BUILD_INCLUSION ON)
endif ()
if (GDAL_USE_TIFF_INTERNAL)
  target_include_directories(gdal_NITF PRIVATE $<TARGET_PROPERTY:libtiff,SOURCE_DIR>)
  if (RENAME_INTERNAL_TIFF_SYMBOLS)
    target_compile_definitions(gdal_NITF PRIVATE -DRENAME_INTERNAL_LIBTIFF_SYMBOLS)
  endif ()
else ()
  gdal_target_link_libraries(gdal_NITF PRIVATE TIFF::TIFF)
endif ()
if (GDAL_USE_JPEG_INTERNAL)
  target_compile_definitions(gdal_NITF PRIVATE -DJPEG_SUPPORTED)
  if (RENAME_INTERNAL_JPEG_SYMBOLS)
    target_compile_definitions(gdal_NITF PRIVATE -DRENAME_INTERNAL_LIBJPEG_SYMBOLS)
  endif ()
  target_include_directories(gdal_NITF PRIVATE $<TARGET_PROPERTY:libjpeg,SOURCE_DIR>)
else ()
  if (GDAL_USE_JPEG)
    target_compile_definitions(gdal_NITF PRIVATE -DJPEG_SUPPORTED)
    gdal_target_link_libraries(gdal_NITF PRIVATE JPEG::JPEG)
  endif ()
endif ()
if (GDAL_USE_JPEG_INTERNAL OR GDAL_USE_JPEG)
  if (GDAL_USE_JPEG12_INTERNAL)
    target_compile_definitions(gdal_NITF PRIVATE -DJPEG_DUAL_MODE_8_12)
    target_include_directories(gdal_NITF PRIVATE ${CMAKE_CURRENT_BINARY_DIR})
  endif ()
endif ()
