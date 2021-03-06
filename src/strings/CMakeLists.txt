get_filename_component(DIRNAME "${CMAKE_CURRENT_SOURCE_DIR}" NAME)
set(DLM_NAME mg_${DIRNAME})

if (TRE_INCLUDE_DIR AND TRE_LIBRARY)
  if (EXISTS ${TRE_INCLUDE_DIR} AND EXISTS ${TRE_LIBRARY})
    include_directories(${TRE_INCLUDE_DIR})

    configure_file("${DLM_NAME}.dlm.in" "${DLM_NAME}.dlm")
    add_library("${DLM_NAME}" SHARED "${DLM_NAME}.c")

    if (APPLE)
      set_target_properties("${DLM_NAME}"
        PROPERTIES
          SUFFIX ".${IDL_PLATFORM_EXT}.so"
      )
    endif ()

    set_target_properties("${DLM_NAME}"
      PROPERTIES
        PREFIX ""
    )

    target_link_libraries("${DLM_NAME}" ${IDL_LIBRARY} ${TRE_LIBRARY})

    install(TARGETS ${DLM_NAME}
      RUNTIME DESTINATION lib/${DIRNAME}
      LIBRARY DESTINATION lib/${DIRNAME}
    )
    install(FILES "${CMAKE_CURRENT_BINARY_DIR}/${DLM_NAME}.dlm" DESTINATION lib/${DIRNAME})
  else ()
    message(WARNING "Tre library specified, but does not exist")
  endif ()
endif ()

add_subdirectory(dictionary)

file(GLOB PRO_FILES "*.pro")
install(FILES ${PRO_FILES} DESTINATION lib/${DIRNAME})
install(FILES .idldoc DESTINATION lib/${DIRNAME})

