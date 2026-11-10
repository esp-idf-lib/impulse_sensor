# Example for `impulse_counter` driver

## What it does

It shows anemometer - impulse wind speed sensor data in a loop.

## Wiring

Connect `DATA` pin to the following pins with appropriate pull-up
resistors.

| Name | Description | Defaults |
|------|-------------|----------|
| `CONFIG_EXAMPLE_DATA_GPIO` | GPIO number for `DATA` | "6" for `esp32c3`, "19" for `esp32`, `esp32s2`, and `esp32s3` |
