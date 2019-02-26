CMAKE_MINIMUM_REQUIRED( VERSION 3.3 )

PROJECT( ${CELER_PROTO_LIB_NAME} )

SET(GENERATED_SOURCE_FILES "")
SET(GENERATED_HEADER_FILES "")
SET(GENERATED_INCLUDE_DIRS "")

FUNCTION(GENERATE_PROTO PROTO_DIR)
   SET(IN_DIR ${BLOCK_SETTLE_ROOT}/Celer/${PROTO_DIR})
   SET(OUT_DIR ${PATH_TO_GENERATED}/${PROTO_DIR})
   
   FILE(MAKE_DIRECTORY ${OUT_DIR})

   FILE(GLOB PROTO_FILES ${IN_DIR}/*.proto)

   FOREACH(PROTO_FILE ${PROTO_FILES})
      GET_FILENAME_COMPONENT(PROTO_NAME "${PROTO_FILE}" NAME_WE)

      SET(PROTO_SOURCE_FILE "${OUT_DIR}/${PROTO_NAME}.pb.cc")
      SET(PROTO_HEADER_FILE "${OUT_DIR}/${PROTO_NAME}.pb.h")

      SET(PROTO_SOURCE_FILES ${PROTO_SOURCE_FILES} ${PROTO_SOURCE_FILE})
      SET(PROTO_HEADER_FILES ${PROTO_HEADER_FILES} ${PROTO_HEADER_FILE})

      ADD_CUSTOM_COMMAND(OUTPUT ${PROTO_SOURCE_FILE}
                         OUTPUT ${PROTO_HEADER_FILE}
                         DEPENDS ${PROTO_FILE}
                         COMMAND ${PROTOBUF_PROTOC_EXECUTABLE} ${PROTO_FILE} --cpp_out=${PATH_TO_GENERATED} --proto_path=${BLOCK_SETTLE_ROOT}/Celer
                         WORKING_DIRECTORY ${BLOCK_SETTLE_ROOT}/Celer
      )
   ENDFOREACH(PROTO_FILE)

   SET(GENERATED_SOURCE_FILES ${GENERATED_SOURCE_FILES} ${PROTO_SOURCE_FILES} PARENT_SCOPE)
   SET(GENERATED_HEADER_FILES ${GENERATED_HEADER_FILES} ${PROTO_HEADER_FILES} PARENT_SCOPE)
   SET(GENERATED_INCLUDE_DIRS ${GENERATED_INCLUDE_DIRS} ${OUT_DIR} PARENT_SCOPE)
ENDFUNCTION(GENERATE_PROTO)

# setup CELER proto files

GENERATE_PROTO(com/celertech/baseserver/communication/login)
GENERATE_PROTO(com/celertech/baseserver/communication/protobuf)
GENERATE_PROTO(com/celertech/baseserver/api/enums)
GENERATE_PROTO(com/celertech/baseserver/api/user)
GENERATE_PROTO(com/celertech/baseserver/api/session)
GENERATE_PROTO(com/celertech/baseserver/api/socket)
GENERATE_PROTO(com/celertech/baseserver/api/performance)

GENERATE_PROTO(com/celertech/marketmerchant/api/enums)
GENERATE_PROTO(com/celertech/marketdata/api/enums)
GENERATE_PROTO(com/celertech/marketdata/api/price)
GENERATE_PROTO(com/celertech/marketdata/api/marketstatistic)
GENERATE_PROTO(com/celertech/staticdata/api/user/property)

GENERATE_PROTO(com/blocksettle/enums)

# BS_TERMINAL_API depends on BS_COMMON_ENUMS
# GENERATE_PROTO(com/blocksettle/terminal)
GENERATE_PROTO(com/celertech/marketmerchant/api/quote)
GENERATE_PROTO(com/celertech/marketmerchant/api/order)
GENERATE_PROTO(com/celertech/marketmerchant/api/order/bitcoin)
GENERATE_PROTO(com/blocksettle/private_bridge/accounts)
GENERATE_PROTO(com/blocksettle/private_bridge/enums)
GENERATE_PROTO(com/blocksettle/private_bridge/spotfx)
GENERATE_PROTO(com/blocksettle/private_bridge/spotxbt)
GENERATE_PROTO(com/blocksettle/private_bridge/eod)
GENERATE_PROTO(com/celertech/staticdata/api/account)
GENERATE_PROTO(com/celertech/staticdata/api/user/account)
GENERATE_PROTO(com/celertech/piggybank/api/enums)
GENERATE_PROTO(com/celertech/piggybank/api/subledger)
GENERATE_PROTO(com/celertech/baseserver/api/exception)

GENERATE_PROTO(com/celertech/marketwarehouse/api/enums/)
GENERATE_PROTO(com/celertech/marketwarehouse/api/configuration)
GENERATE_PROTO(com/celertech/marketmerchant/api/securitydefinition)

GENERATE_PROTO(com/celertech/staticdata/api/enums)
GENERATE_PROTO(com/celertech/staticdata/api/security)
GENERATE_PROTO(com/celertech/staticdata/api/property)

ADD_LIBRARY(${CELER_PROTO_LIB_NAME}
   ${GENERATED_SOURCE_FILES}
   ${GENERATED_HEADER_FILES}
)

TARGET_INCLUDE_DIRECTORIES(${CELER_PROTO_LIB_NAME} PUBLIC ${PATH_TO_GENERATED})

TARGET_INCLUDE_DIRECTORIES(${CELER_PROTO_LIB_NAME} PUBLIC ${GENERATED_INCLUDE_DIRS})
