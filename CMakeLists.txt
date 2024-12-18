cmake_minimum_required(VERSION 3.26)

project(ProtoFiles VERSION 0.1 LANGUAGES CXX)

find_package (protobuf CONFIG REQUIRED)
find_package (gRPC CONFIG REQUIRED)

file(GLOB_RECURSE PROTO_FILES RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} "*.proto")

add_library(${PROJECT_NAME} STATIC ${PROTO_FILES})
target_link_libraries(${PROJECT_NAME}
    PUBLIC
        protobuf::libprotobuf
        gRPC::grpc
        gRPC::grpc++
)
target_include_directories(${PROJECT_NAME} PUBLIC ${CMAKE_CURRENT_BINARY_DIR})

get_target_property(grpc_cpp_plugin_location gRPC::grpc_cpp_plugin LOCATION)
protobuf_generate(TARGET ${PROJECT_NAME} LANGUAGE cpp)
protobuf_generate(TARGET ${PROJECT_NAME} LANGUAGE grpc GENERATE_EXTENSIONS .grpc.pb.h .grpc.pb.cc PLUGIN "protoc-gen-grpc=${grpc_cpp_plugin_location}")