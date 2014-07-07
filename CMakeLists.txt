cmake_minimum_required (VERSION 2.8)

project (mongo-cxx-driver)

find_package(Boost 1.55 COMPONENTS thread system regex REQUIRED)

#enable c++ 11
SET(CMAKE_CXX_FLAGS "-std=c++11 -g ${CMAKE_CXX_FLAGS}")

include_directories(src/
                    src/mongo/
                    ${Boost_INCLUDE_DIR}
                    )

add_subdirectory(src/third_party/gtest-1.7.0)
include_directories(${gtest_SOURCE_DIR}/include)

add_definitions(-DMONGO_EXPOSE_MACROS)

aux_source_directory(src/mongo/util UTIL_SRC)
aux_source_directory(src/mongo/util/net NET_SRC)
aux_source_directory(src/mongo/util/concurrency/ CONC_SRC)
aux_source_directory(src/mongo/db DB_SRC)
aux_source_directory(src/mongo/client CLIENT_SRC)
aux_source_directory(src/mongo/bson BSON_SRC)

list(REMOVE_ITEM CLIENT_SRC src/mongo/client/sasl_client_authenticate_impl.cpp 
			    src/mongo/client/include_bsonh_test.cpp
			    src/mongo/client/sasl_client_session.cpp
			    src/mongo/client/include_dbclienth_test.cpp)


add_library(mongoclient SHARED
	    ${UTIL_SRC}
	    ${NET_SRC}
	    ${CONC_SRC}
	    ${DB_SRC}
	    ${CLIENT_SRC}
	    ${BSON_SRC}
	    )

set_target_properties(mongoclient PROPERTIES PROJECT_LABEL lib_mongoclient)