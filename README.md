# ADXL355 Data Acquisition System



## Overview

This project implements a high-speed data acquisition system for three ADXL355 accelerometers using a Raspberry Pi and a host computer. The system captures acceleration data at 1000 Hz from each accelerometer and streams it to a host computer for storage and analysis.

## System Architecture

1. **Raspberry Pi**: Acts as the data acquisition device, interfacing with three ADXL355 accelerometers via SPI and streaming data over TCP/IP.
2. **Host Computer**: Receives the streamed data, processes it, and stores it in binary files for further analysis.
3. **ADXL355 Accelerometers**: High-precision, low-noise 3-axis accelerometers.

## File Structure

### Raspberry Pi (192.168.0.23)

- `/home/bvex/accl_c/accl_tx.c`: C program for data acquisition and transmission
- `/home/bvex/accl_c/accl_tx`: Compiled executable of `accl_tx.c`
- `/home/bvex/accl_c/logs/`: Directory for log files

### Host Computer

- `accl_rx.c`: C program for receiving and storing data
- `accl_rx`: Compiled executable of `accl_rx.c`
- `run_accl.sh`: Bash script to automate compilation and execution
- `logs/`: Directory for log files

## Hardware Setup

### Pin Connections

# ADXL355 Data Acquisition System

[Previous content remains the same]

## Hardware Setup

### Pin Connections

| ADXL355 Pin | Raspberry Pi Pin | Accelerometer 1 (SPI0) | Accelerometer 2 (SPI0) | Accelerometer 3 (SPI1) |
|-------------|------------------|------------------------|------------------------|------------------------|
| MOSI        | PIN 19 (SPI0 MOSI) | Connected              | Connected              | -                      |
| MISO        | PIN 21 (SPI0 MISO) | Connected              | Connected              | -                      |
| SCLK        | PIN 23 (SPI0 SCLK) | Connected              | Connected              | -                      |
| CS          | PIN 24 (SPI0 CE0)  | Connected              | -                      | -                      |
| CS          | PIN 26 (SPI0 CE1)  | -                      | Connected              | -                      |
| MOSI        | PIN 38 (SPI1 MOSI) | -                      | -                      | Connected              |
| MISO        | PIN 25 (SPI1 MISO) | -                      | -                      | Connected              |
| SCLK        | PIN 40 (SPI1 SCLK) | -                      | -                      | Connected              |
| CS          | PIN 12 (SPI1 CE0)  | -                      | -                      | Connected              |
| VDD         | 3.3V               | Connected              | Connected              | Connected              |
| GND         | GND                | Connected              | Connected              | Connected              |

Note: Ensure that you connect the VDD (power) and GND (ground) pins of each accelerometer to the appropriate 3.3V and GND pins on the Raspberry Pi, respectively.

### NOTE: 

Raspberry pi 4 and above models have two SPI busses that can be used simultaneously. In this case since we are reading out three SPI sensors, we need to access the 2nd SPI bus on the pi called SPI1. To activate that add this line to "/boot/config.txt":

   ```bash
   dtoverlay=spi1-3cs
   ```

## Software Dependencies

### Raspberry Pi

- GCC compiler
- SPI kernel module enabled

To enable SPI:
1. Run `sudo raspi-config`
2. Navigate to "Interfacing Options"
3. Select "SPI"
4. Choose "Yes" to enable SPI
5. Reboot the Raspberry Pi

### Host Computer

- GCC compiler
- Bash shell

## Installation and Setup

1. Clone this repository on both the Raspberry Pi and the host computer.

2. On the Raspberry Pi:
   ```bash
   cd ~/accl_c
   gcc -o accl_tx accl_tx.c -lm
   ```

3. On the host computer:
   ```bash
   gcc -o accl_rx accl_rx.c
   ```

4. Ensure that the `run_accl.sh` script has execute permissions:
   ```bash
   chmod +x run_accl.sh
   ```

5. Update the `SSH_OPTIONS` in `run_accl.sh` with the correct path to your private key for SSH authentication.

## Usage

1. Connect the ADXL355 accelerometers to the Raspberry Pi according to the pin connection table above.

2. Ensure both the Raspberry Pi and host computer are on the same network.

3. On the host computer, run the `run_accl.sh` script:
   ```bash
   ./run_accl.sh
   ```

   This script will:
   - Compile the C programs on both the Raspberry Pi and host computer
   - Start the data acquisition program on the Raspberry Pi
   - Start the data reception program on the host computer
   - Display the elapsed time of the data acquisition process

4. To stop the data acquisition, press Ctrl+C in the terminal running `run_accl.sh`.

## Data Output

The received data is stored in binary files in the `outputs` directory on the host computer. Each accelerometer's data is saved in a separate subdirectory:

- `outputs/[timestamp]-accl-output/accl1/`
- `outputs/[timestamp]-accl-output/accl2/`
- `outputs/[timestamp]-accl-output/accl3/`

Each subdirectory contains binary files with chunks of data, named in the format: `[start_time]_chunk_[number].bin`

## Log Files

- Raspberry Pi: `/home/bvex/accl_c/logs/[timestamp]_accl_tx.log`
- Host Computer: `logs/[timestamp]_accl_rx.log`
- Script Log: `logs/run_accl_[timestamp].log`

These log files contain important information about the data acquisition process, including any errors or warnings.

## Customization

- To change the sampling rate, modify the `ADXL355_ODR_1000` definition in `accl_tx.c`.
- To adjust the acceleration range, modify the `ADXL355_RANGE_2G` definition in `accl_tx.c`.
- To change the data chunk duration, modify the `CHUNK_DURATION` definition in `accl_rx.c`.

## Data Visualization and analysis

Use the provided jupyter notebook called accl_data_analysis.ipynb to plot the data from the three accelerometers.

## Troubleshooting

1. **Connection Issues**: Ensure that the Raspberry Pi and host computer can communicate over the network. Check firewalls and network settings.

2. **SPI Errors**: Verify that SPI is enabled on the Raspberry Pi and that the ADXL355 accelerometers are correctly wired.

3. **Permission Errors**: Ensure that the user has the necessary permissions to access SPI devices and create log files.

4. **Data Reception Issues**: Check that the correct IP address and port are used in both `accl_tx.c` and `accl_rx.c`.

## Contributors

Mayukh Bagchi
