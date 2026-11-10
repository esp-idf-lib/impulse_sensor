list(APPEND CMAKE_MODULE_PATH ${CMAKE_CURRENT_LIST_DIR}/eil-cmake-utils/cmake)
include(eil_check_idf_features)
eil_check_idf_has_esp_drivers()

# List of common ESP-IDF components required by this component
set(REQUIRED_COMPONENTS freertos esp_idf_lib_helpers esp_timer)

# Flag indicating availability of Pulse Counter (PCNT) driver
# Note: PCNT is not supported on esp32c2, esp32c3, and esp32c61 targets
set(HAS_PCNT TRUE)
set(NO_PCNT_SUPPORTED_TARGETS esp32c2 esp32c3 esp32c61)
if(IDF_TARGET IN_LIST NO_PCNT_SUPPORTED_TARGETS)
    set(HAS_PCNT FALSE)
endif()

if(EIL_IDF_HAS_ESP_DRIVERS)
    list(APPEND REQUIRED_COMPONENTS esp_driver_gpio)
    if(HAS_PCNT)
        list(APPEND REQUIRED_COMPONENTS esp_driver_pcnt)
    endif()
else()
    list(APPEND REQUIRED_COMPONENTS driver)
endif()

idf_component_register(
    SRCS impulse_sensor.c
    INCLUDE_DIRS .
    REQUIRES ${REQUIRED_COMPONENTS}
)

include(eil_ci)

# Export build configuration flags to the component library sources
if(HAS_PCNT)
    target_compile_definitions(${COMPONENT_LIB} PRIVATE HAS_PCNT)
endif()

if(EIL_IDF_HAS_ESP_DRIVERS)
    target_compile_definitions(${COMPONENT_LIB} PRIVATE HAS_ESP_DRIVERS)
endif()
