add_gdal_driver(
  TARGET gdal_GEOR
  SOURCES georaster_dataset.cpp georaster_priv.h georaster_rasterband.cpp georaster_wrapper.cpp oci_wrapper.cpp
          cpl_vsil_ocilob.cpp
  DEF FRMT_georaster PLUGIN_CAPABLE_IF "NOT GDAL_USE_ZLIB_INTERNAL;NOT GDAL_USE_JPEG_INTERNAL")
gdal_standard_includes(gdal_GEOR)
if (GDAL_USE_ZLIB_INTERNAL)
  target_include_directories(gdal_GEOR PRIVATE $<TARGET_PROPERTY:libz,SOURCE_DIR>)
endif ()

if (GDAL_ENABLE_DRIVER_JPEG AND GDAL_USE_JPEG_INTERNAL)
  target_include_directories(gdal_GEOR PRIVATE $<TARGET_PROPERTY:libjpeg,SOURCE_DIR>)
  target_compile_definitions(gdal_GEOR PRIVATE -DJPEG_SUPPORTED)
  if (RENAME_INTERNAL_JPEG_SYMBOLS)
    target_compile_definitions(gdal_GEOR PRIVATE -DRENAME_INTERNAL_LIBJPEG_SYMBOLS)
  endif ()
endif ()

gdal_target_link_libraries(gdal_GEOR PRIVATE Oracle::OCI)

if (GDAL_ENABLE_DRIVER_JPEG AND (NOT GDAL_USE_JPEG_INTERNAL))
  target_compile_definitions(gdal_GEOR PRIVATE -DJPEG_SUPPORTED)
  gdal_target_link_libraries(gdal_GEOR PRIVATE JPEG::JPEG)
endif ()

if (NOT GDAL_USE_ZLIB_INTERNAL)
  gdal_target_link_libraries(gdal_GEOR PRIVATE ZLIB::ZLIB)
endif ()

if (GDAL_ENABLE_DRIVER_JPEG AND GDAL_ENABLE_DRIVER_GEOR_PLUGIN)
  # A bit ugly to add this file from the JPEG driver !
  target_sources(gdal_GEOR PRIVATE ../jpeg/vsidataio.cpp)
endif ()
