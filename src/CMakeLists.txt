cmake_minimum_required(VERSION 3.19)

configure_file(config.h.in config.h @ONLY)

add_executable(${PROJECT_NAME}
  gconsole.c
  gdisplay.c
  gfile.c
  ghelp.c
  ginstr.c
  goatdata.c
  goattrk2.c
  gorder.c
  gpattern.c
  gplay.c
  greloc.c
  gsid.cpp
  gsong.c
  gsound.c
  gtable.c
)
if(WIN32)
  target_sources(${PROJECT_NAME} PRIVATE goattrk2.rc)
endif()
target_include_directories(${PROJECT_NAME} PRIVATE ${CMAKE_CURRENT_BINARY_DIR})
target_link_libraries(${PROJECT_NAME} PRIVATE asm bme resid)

add_executable(gt2reloc
  gt2reloc.c
  gconsole.c
  gdisplay.c
  gfile.c
  ginstr.c
  goatdata.c
  gorder.c
  gpattern.c
  gplay.c
  gsid.cpp
  gsong.c
  gsound.c
  gtable.c
)
target_include_directories(gt2reloc PRIVATE ${CMAKE_CURRENT_BINARY_DIR})
target_link_libraries(gt2reloc PRIVATE asm bme resid)

add_executable(ins2snd2
  ins2snd2.c
)
target_link_libraries(ins2snd2 PRIVATE bme)

add_executable(mod2sng
  mod2sng.c
)
target_link_libraries(mod2sng PRIVATE bme)

add_executable(sngspli2
  sngspli2.c
)
target_link_libraries(sngspli2 PRIVATE bme)

set(datasrc
  player.s
  altplayer.s
  chargen.bin
  palette.bin
  cursor.bin
  bcursor.bin
  goattrk2.bmp
)
list(TRANSFORM datasrc PREPEND ${CMAKE_CURRENT_SOURCE_DIR}/)

add_custom_command(
  OUTPUT goattrk2.dat
  COMMAND echo ${datasrc} > goattrk2.seq
  COMMAND $<TARGET_FILE:datafile> goattrk2.dat goattrk2.seq
  DEPENDS datafile ${datasrc}
  VERBATIM)

add_custom_command(
  OUTPUT goatdata.c
  COMMAND $<TARGET_FILE:dat2inc> goattrk2.dat goatdata.c
  DEPENDS dat2inc goattrk2.dat
  VERBATIM)

install(TARGETS ${PROJECT_NAME} gt2reloc ins2snd2 mod2sng sngspli2
  EXPORT ${PROJECT_NAME}
)

add_subdirectory(asm)
add_subdirectory(bme)
add_subdirectory(resid)
