cmake_minimum_required(VERSION 3.0.2)
project(ProtobufTest)

set(CMAKE_BUILD_TYPE Release)

set(SRC_DIR src)
set(LIB_DIR lib)
set(BIN_DIR bin)
set(INCLUDE_DIR include)

set(TARGET_ADD_PERSON add_person)
set(TARGET_LIST_PEOPLE list_people)

set(PROTO_NAME addressbook)

set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${LIB_DIR})
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${BIN_DIR})

include_directories(${INCLUDE_DIR} "/export/devel/platform/boost/$ENV{BOOST_VERSION}/boost/include" "/usr/local/include")
link_directories( ${LIB_DIR} "/usr/local/lib")

set(APP_LINK_LIBS protobuf)

set(CMAKE_CXX_FLAGS "-fPIC -O3 -Wall -Wno-unused-function -std=c++20")

add_custom_command(
        OUTPUT ${SRC_DIR}/${PROTO_NAME}.pb.cc
        COMMAND cd ${SRC_DIR}
        COMMAND protoc --cpp_out=. ${PROTO_NAME}.proto
        COMMAND cd ..
        DEPENDS ${SRC_DIR}/${PROTO_NAME}.proto
        VERBATIM)
add_custom_target(generate_proto_src DEPENDS ${SRC_DIR}/${PROTO_NAME}.pb.cc)

set(SOURCES_ADD_PERSON ${SRC_DIR}/add_person.cpp ${SRC_DIR}/${PROTO_NAME}.pb.cc)
set(SOURCES_LIST_PEOPLE ${SRC_DIR}/list_people.cpp ${SRC_DIR}/${PROTO_NAME}.pb.cc)

add_executable(${TARGET_ADD_PERSON} ${SOURCES_ADD_PERSON})
add_executable(${TARGET_LIST_PEOPLE} ${SOURCES_LIST_PEOPLE})

target_link_libraries(${TARGET_ADD_PERSON} ${APP_LINK_LIBS})
target_link_libraries(${TARGET_LIST_PEOPLE} ${APP_LINK_LIBS})