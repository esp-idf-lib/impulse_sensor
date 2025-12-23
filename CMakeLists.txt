# List of common ESP-IDF components required by this component
set(REQUIRED_COMPONENTS freertos esp_idf_lib_helpers esp_timer)

# Flag indicating availability of ESP driver headers (esp_driver_*.h)
# Note: Disabled for ESP-IDF v5.x where minor version < 3 due to API changes
set(HAS_ESP_DRIVERS TRUE)
if(${IDF_VERSION_MAJOR} STREQUAL 5 AND ${IDF_VERSION_MINOR} LESS 3)
    set(HAS_ESP_DRIVERS FALSE)
endif()

# Flag indicating availability of Pulse Counter (PCNT) driver
# Note: PCNT is not supported on esp32c2, esp32c3, and esp32c61 targets
set(HAS_PCNT TRUE)
set(NO_PCNT_SUPPORTED_TARGETS esp32c2 esp32c3 esp32c61)
if(IDF_TARGET IN_LIST NO_PCNT_SUPPORTED_TARGETS)
    set(HAS_PCNT FALSE)
endif()

if(HAS_ESP_DRIVERS)
    list(APPEND REQUIRED_COMPONENTS esp_driver_gpio)
    if(HAS_PCNT)
        list(APPEND REQUIRED_COMPONENTS esp_driver_pcnt)
    endif()
else()
    list(APPEND REQUIRED_COMPONENTS driver)
endif()

if(${IDF_TARGET} STREQUAL esp8266)
    set(REQUIRED_COMPONENTS esp8266 freertos esp_idf_lib_helpers esp_timer)
endif()

idf_component_register(
    SRCS impulse_sensor.c
    INCLUDE_DIRS .
    REQUIRES ${REQUIRED_COMPONENTS}
)

# include common cmake file for components
set(ESP_IDF_LIB_CMAKE ${CMAKE_CURRENT_LIST_DIR}/common/cmake/esp-idf-lib.cmake)
if(EXISTS ${ESP_IDF_LIB_CMAKE})
    include(${ESP_IDF_LIB_CMAKE})
else()
    message(WARNING "${ESP_IDF_LIB_CMAKE} not found")
endif()

# Export build configuration flags to the component library sources
if(HAS_PCNT)
    target_compile_definitions(${COMPONENT_LIB} PRIVATE HAS_PCNT)
endif()

if(HAS_ESP_DRIVERS)
    target_compile_definitions(${COMPONENT_LIB} PRIVATE HAS_ESP_DRIVERS)
endif()
