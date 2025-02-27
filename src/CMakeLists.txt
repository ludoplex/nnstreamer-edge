# NNStreamer-Edge library
SET(NNS_EDGE_SRCS
    ${NNS_EDGE_SRC_DIR}/nnstreamer-edge-metadata.c
    ${NNS_EDGE_SRC_DIR}/nnstreamer-edge-data.c
    ${NNS_EDGE_SRC_DIR}/nnstreamer-edge-event.c
    ${NNS_EDGE_SRC_DIR}/nnstreamer-edge-internal.c
    ${NNS_EDGE_SRC_DIR}/nnstreamer-edge-log.c
    ${NNS_EDGE_SRC_DIR}/nnstreamer-edge-util.c
    ${NNS_EDGE_SRC_DIR}/nnstreamer-edge-queue.c
)

IF(MQTT_SUPPORT)
    IF(MOSQUITTO_LIB)
        SET(NNS_EDGE_SRCS ${NNS_EDGE_SRCS} ${NNS_EDGE_SRC_DIR}/nnstreamer-edge-mqtt-mosquitto.c)
    ELSE()
        SET(NNS_EDGE_SRCS ${NNS_EDGE_SRCS} ${NNS_EDGE_SRC_DIR}/nnstreamer-edge-mqtt-paho.c)
    ENDIF()
ENDIF()

IF(AITT_SUPPORT)
    SET(NNS_EDGE_SRCS ${NNS_EDGE_SRCS} ${NNS_EDGE_SRC_DIR}/nnstreamer-edge-aitt.c)
ENDIF()

ADD_LIBRARY(${NNS_EDGE_LIB_NAME} SHARED ${NNS_EDGE_SRCS})
SET_TARGET_PROPERTIES(${NNS_EDGE_LIB_NAME} PROPERTIES VERSION ${SO_VERSION})
TARGET_INCLUDE_DIRECTORIES(${NNS_EDGE_LIB_NAME} PRIVATE ${INCLUDE_DIR} ${EDGE_REQUIRE_PKGS_INCLUDE_DIRS})
TARGET_LINK_LIBRARIES(${NNS_EDGE_LIB_NAME} ${EDGE_REQUIRE_PKGS_LDFLAGS})

IF(MQTT_SUPPORT)
    IF(PAHO_MQTT_LIB)
        TARGET_LINK_LIBRARIES(${NNS_EDGE_LIB_NAME} ${PAHO_MQTT_LIB})
    ELSE()
        TARGET_LINK_LIBRARIES(${NNS_EDGE_LIB_NAME} ${MOSQUITTO_LIB})
    ENDIF()
ENDIF()

IF(AITT_SUPPORT)
    TARGET_LINK_LIBRARIES(${NNS_EDGE_LIB_NAME} ${AITT_LIB})
    TARGET_INCLUDE_DIRECTORIES(${NNS_EDGE_LIB_NAME} PUBLIC ${AITT_INCLUDE_DIR})
ENDIF()

INSTALL (TARGETS ${NNS_EDGE_LIB_NAME} DESTINATION ${CMAKE_INSTALL_LIBDIR})
INSTALL (FILES ${INCLUDE_DIR}/nnstreamer-edge.h DESTINATION ${INCLUDE_INSTALL_DIR})
