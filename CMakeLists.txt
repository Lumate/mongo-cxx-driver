cmake_minimum_required (VERSION 2.8)

project (mongo-cxx-driver)

find_package(Boost 1.55 COMPONENTS thread system regex REQUIRED)

#enable c++ 11
add_compile_options(-std=c++11 -fPIC)

include_directories(src/
                    src/mongo/
                    ${Boost_INCLUDE_DIR}
                    )

add_definitions(-DMONGO_EXPOSE_MACROS)
add_definitions("-Wno-deprecated-declarations")

set (ALL_SRC  src/mongo/base/global_initializer.cpp
	      src/mongo/base/global_initializer_registerer.cpp
	      src/mongo/base/init.cpp
	      src/mongo/base/initializer_context.cpp
	      src/mongo/base/initializer.cpp
	      src/mongo/base/initializer_dependency_graph.cpp
	      src/mongo/base/make_string_vector.cpp
	      src/mongo/base/parse_number.cpp
	      src/mongo/base/status.cpp
	      src/mongo/base/string_data.cpp
	      src/mongo/bson/bson_validate.cpp
	      src/mongo/bson/oid.cpp
	      src/mongo/bson/optime.cpp
	      src/mongo/client/bulk_operation_builder.cpp
	      src/mongo/client/bulk_update_builder.cpp
	      src/mongo/client/bulk_upsert_builder.cpp
	      src/mongo/client/command_writer.cpp
	      src/mongo/client/connpool.cpp
	      src/mongo/client/dbclient.cpp
	      src/mongo/client/dbclientcursor.cpp
	      src/mongo/client/dbclientcursorshimarray.cpp
	      src/mongo/client/dbclientcursorshimcursorid.cpp
	      src/mongo/client/dbclient_rs.cpp
	      src/mongo/client/delete_write_operation.cpp
	      src/mongo/client/exceptions.cpp
	      src/mongo/client/gridfs.cpp
	      src/mongo/client/index_spec.cpp
	      src/mongo/client/init.cpp
	      src/mongo/client/insert_write_operation.cpp
	      src/mongo/client/options.cpp
	      src/mongo/client/replica_set_monitor.cpp
	      src/mongo/client/sasl_client_authenticate.cpp
	      src/mongo/client/sasl_sspi.cpp
	      src/mongo/client/update_write_operation.cpp
	      src/mongo/client/wire_protocol_writer.cpp
	      src/mongo/client/write_concern.cpp
	      src/mongo/client/write_operation_base.cpp
	      src/mongo/client/write_result.cpp
	      src/mongo/db/dbmessage.cpp
	      src/mongo/db/jsobj.cpp
	      src/mongo/db/json.cpp
	      src/mongo/logger/logger.cpp
	      src/mongo/logger/log_manager.cpp
	      src/mongo/logger/log_severity.cpp
	      src/mongo/logger/logstream_builder.cpp
	      src/mongo/logger/message_event_utf8_encoder.cpp
	      src/mongo/logger/message_log_domain.cpp
	      src/mongo/platform/process_id.cpp
	      src/mongo/platform/random.cpp
	      src/mongo/util/assert_util.cpp
	      src/mongo/util/background.cpp
	      src/mongo/util/base64.cpp
	      src/mongo/util/fail_point.cpp
	      src/mongo/util/fail_point_registry.cpp
	      src/mongo/util/fail_point_service.cpp
	      src/mongo/util/hex.cpp
	      src/mongo/util/log.cpp
	      src/mongo/util/md5.cpp
	      src/mongo/util/password_digest.cpp
	      src/mongo/util/stringutils.cpp
	      src/mongo/util/text.cpp
	      src/mongo/util/timer.cpp
	      src/mongo/util/time_support.cpp
	      src/mongo/util/util.cpp
	      src/mongo/util/net/ssl_manager.cpp
	      src/mongo/util/net/message_port.cpp
	      src/mongo/util/net/socket_poll.cpp
	      src/mongo/util/net/message.cpp
	      src/mongo/util/net/httpclient.cpp
	      src/mongo/util/net/sock.cpp
	      src/mongo/bson/util/bson_extract.cpp
	      src/mongo/util/concurrency/synchronization.cpp
	      src/mongo/util/concurrency/thread_name.cpp
	      src/third_party/murmurhash3/MurmurHash3.cpp
	      ${PROJECT_SOURCE_DIR}/src/mongo/base/error_codes.cpp
	    )
	    
add_library(mongoclient SHARED
	    ${ALL_SRC}
	    ${PROJECT_SOURCE_DIR}/src/mongo/config.h
	    ${PROJECT_SOURCE_DIR}/src/mongo/version.h
	    )
add_custom_command(OUTPUT ${PROJECT_SOURCE_DIR}/src/mongo/base/error_codes.cpp
		   COMMAND python generate_error_codes.py error_codes.err error_codes.h error_codes.cpp
		   WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}/src/mongo/base/)

set(MONGO_CLIENT_LIB "${CMAKE_SOURCE_DIR}/libmongoclient.so")

add_custom_command(OUTPUT ${PROJECT_SOURCE_DIR}/src/mongo/config.h
                   COMMAND cp config.h.in config.h
                   COMMAND sed -i 's/@mongoclient_ssl@/\\/\\/ \#undef MONGO_SSL/' config.h
                   COMMAND sed -i 's/@mongoclient_sasl@/\\/\\/ \#undef MONGO_SASL/' config.h
                   COMMAND sed -i 's/@mongoclient_have_header_unistd_h@/\#define MONGO_HAVE_HEADER_UNISTD_H 1/' config.h
                   COMMAND sed -i 's/@mongoclient_have_cxx11_atomics@/\#define MONGO_HAVE_CXX11_ATOMICS 1/' config.h
                   COMMAND sed -i 's/@mongoclient_have_gcc_atomic_builtins@/\\/\\/\#undef MONGO_HAVE_GCC_ATOMIC_BUILTINS 1/' config.h
                   COMMAND sed -i 's/@mongoclient_have_gcc_sync_builtins@/\\/\\/\#undef MONGO_HAVE_GCC_SYNC_BUILTINS 1/' config.h
                   WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}/src/mongo)

add_custom_command(OUTPUT ${PROJECT_SOURCE_DIR}/src/mongo/version.h
                   COMMAND cp version.h.in version.h
                   COMMAND sed -i 's/@mongoclient_version_major@/0/' version.h
                   COMMAND sed -i 's/@mongoclient_version_minor@/11/' version.h
                   COMMAND sed -i 's/@mongoclient_version_patch@/0/' version.h

                   WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}/src/mongo

)
