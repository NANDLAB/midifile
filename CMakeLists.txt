cmake_minimum_required(VERSION 3.5)

project(midifile LANGUAGES C CXX)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

include(CheckIncludeFiles)
include(GNUInstallDirs)

include_directories(include)

check_include_files(unistd.h HAVE_UNISTD_H)
check_include_files(humdrum.h HAVE_HUMDRUM_H)
check_include_files(sys/io.h HAVE_SYS_IO_H)

##############################
##
## Operating-system specific settings:
##

if(APPLE)
    set(CMAKE_OSX_DEPLOYMENT_TARGET "10.9" CACHE STRING "Minimum OS X deployment version")
endif()

if(MSVC)
    add_definitions(-D_CRT_SECURE_NO_WARNINGS)

    option(STATIC_CRT "Use static CRT libraries" OFF)

    # Rewrite command line flags to use /MT if necessary
    if(STATIC_CRT)
        foreach(flag_var
                CMAKE_CXX_FLAGS CMAKE_CXX_FLAGS_DEBUG CMAKE_CXX_FLAGS_RELEASE
                CMAKE_CXX_FLAGS_MINSIZEREL CMAKE_CXX_FLAGS_RELWITHDEBINFO)
            if(${flag_var} MATCHES "/MD")
                string(REGEX REPLACE "/MD" "/MT" ${flag_var} "${${flag_var}}")
            endif(${flag_var} MATCHES "/MD")
        endforeach(flag_var)
    endif()
endif()


##############################
##
## Library:
##

set(SRCS
    src/Options.cpp
    src/Binasc.cpp
    src/MidiEvent.cpp
    src/MidiEventList.cpp
    src/MidiFile.cpp
    src/MidiMessage.cpp
)

set(HDRS
    include/Binasc.h
    include/MidiEvent.h
    include/MidiEventList.h
    include/MidiFile.h
    include/MidiMessage.h
    include/Options.h
)

add_library(midifile STATIC ${SRCS} ${HDRS})
target_include_directories(midifile PUBLIC include)

add_custom_target(tools)
add_dependencies(tools
    80off asciimidi binasc createmidifile createmidifile2 drumtab durations
    mid2mat mid2mtb mid2svg midi2binasc midi2melody midi2notes midi2skini
    midi2text midicater midimixup miditime perfid retick shutak smfdur stretch
    sysextest text2midi textmidi toascii tobin tobinary todec tohex type0 vlv
)

##############################
##
## Programs:
##

add_executable(80off EXCLUDE_FROM_ALL tools/80off.cpp)
add_executable(asciimidi EXCLUDE_FROM_ALL tools/asciimidi.cpp)
add_executable(binasc EXCLUDE_FROM_ALL tools/binasc.cpp)
add_executable(createmidifile EXCLUDE_FROM_ALL tools/createmidifile.cpp)
add_executable(createmidifile2 EXCLUDE_FROM_ALL tools/createmidifile2.cpp)
add_executable(drumtab EXCLUDE_FROM_ALL tools/drumtab.cpp)
add_executable(durations EXCLUDE_FROM_ALL tools/durations.cpp)
add_executable(mid2mat EXCLUDE_FROM_ALL tools/mid2mat.cpp)
add_executable(mid2mtb EXCLUDE_FROM_ALL tools/mid2mtb.cpp)
add_executable(mid2svg EXCLUDE_FROM_ALL tools/mid2svg.cpp)
add_executable(midi2binasc EXCLUDE_FROM_ALL tools/midi2binasc.cpp)
add_executable(midi2melody EXCLUDE_FROM_ALL tools/midi2melody.cpp)
add_executable(midi2notes EXCLUDE_FROM_ALL tools/midi2notes.cpp)
add_executable(midi2skini EXCLUDE_FROM_ALL tools/midi2skini.cpp)
add_executable(midi2text EXCLUDE_FROM_ALL tools/midi2text.cpp)
add_executable(midicater EXCLUDE_FROM_ALL tools/midicater.cpp)
add_executable(midimixup EXCLUDE_FROM_ALL tools/midimixup.cpp)
add_executable(miditime EXCLUDE_FROM_ALL tools/miditime.cpp)
add_executable(perfid EXCLUDE_FROM_ALL tools/perfid.cpp)
add_executable(retick EXCLUDE_FROM_ALL tools/retick.cpp)
add_executable(shutak EXCLUDE_FROM_ALL tools/shutak.cpp)
add_executable(smfdur EXCLUDE_FROM_ALL tools/smfdur.cpp)
add_executable(stretch EXCLUDE_FROM_ALL tools/stretch.cpp)
add_executable(sysextest EXCLUDE_FROM_ALL tools/sysextest.cpp)
add_executable(text2midi EXCLUDE_FROM_ALL tools/text2midi.cpp)
add_executable(textmidi EXCLUDE_FROM_ALL tools/textmidi.cpp)
add_executable(toascii EXCLUDE_FROM_ALL tools/toascii.cpp)
add_executable(tobin EXCLUDE_FROM_ALL tools/tobin.cpp)
add_executable(tobinary EXCLUDE_FROM_ALL tools/tobinary.cpp)
add_executable(todec EXCLUDE_FROM_ALL tools/todec.cpp)
add_executable(tohex EXCLUDE_FROM_ALL tools/tohex.cpp)
add_executable(type0 EXCLUDE_FROM_ALL tools/type0.cpp)
add_executable(vlv EXCLUDE_FROM_ALL tools/vlv.cpp)

target_link_libraries(80off midifile)
target_link_libraries(asciimidi midifile)
target_link_libraries(binasc midifile)
target_link_libraries(createmidifile midifile)
target_link_libraries(createmidifile2 midifile)
target_link_libraries(drumtab midifile)
target_link_libraries(durations midifile)
target_link_libraries(mid2mat midifile)
target_link_libraries(mid2mtb midifile)
target_link_libraries(mid2svg midifile)
target_link_libraries(midi2binasc midifile)
target_link_libraries(midi2melody midifile)
target_link_libraries(midi2notes midifile)
target_link_libraries(midi2skini midifile)
target_link_libraries(midi2text midifile)
target_link_libraries(midicater midifile)
target_link_libraries(midimixup midifile)
target_link_libraries(miditime midifile)
target_link_libraries(perfid midifile)
target_link_libraries(retick midifile)
target_link_libraries(shutak midifile)
target_link_libraries(smfdur midifile)
target_link_libraries(stretch midifile)
target_link_libraries(sysextest midifile)
target_link_libraries(text2midi midifile)
target_link_libraries(textmidi midifile)
target_link_libraries(toascii midifile)
target_link_libraries(tobin midifile)
target_link_libraries(tobinary midifile)
target_link_libraries(todec midifile)
target_link_libraries(tohex midifile)
target_link_libraries(type0 midifile)
target_link_libraries(vlv midifile)

if(HAVE_UNISTD_H AND HAVE_SYS_IO_H)
    add_dependencies(tools midi2beep)

    add_executable(midi2beep EXCLUDE_FROM_ALL tools/midi2beep.cpp)

    target_link_libraries(midi2beep midifile)
endif()

# The following programs require headers from humextra repository.
# <https://github.com/humdrum-tools/humextra>
if(HAVE_HUMDRUM_H)
    add_dependencies(tools
        henonfile mid2hum midiescerpt peep2midi
    )

    add_executable(henonfile EXCLUDE_FROM_ALL tools/henonfile.cpp)
    add_executable(mid2hum EXCLUDE_FROM_ALL tools/mid2hum.cpp)
    add_executable(midiexcerpt EXCLUDE_FROM_ALL tools/midiexcerpt.cpp)
    add_executable(peep2midi EXCLUDE_FROM_ALL tools/peep2midi.cpp)

    target_link_libraries(henonfile midifile)
    target_link_libraries(mid2hum midifile)
    target_link_libraries(midiexcerpt midifile)
    target_link_libraries(peep2midi midifile)
endif()

install(TARGETS midifile
  ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
  LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
  RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
)

install(FILES ${HDRS} DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/midifile)
