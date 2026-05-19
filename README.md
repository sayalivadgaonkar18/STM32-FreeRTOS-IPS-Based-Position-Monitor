# STM32 FreeRTOS IPS based Position Monitor

RTOS-based real-time inductive position sensing system using STM32G474RE and FreeRTOS with timer-triggered ADC, DMA, semaphore synchronization, queue-based task communication, and mechanical angle reconstruction using `atan2()` signal processing.


# Overview

This project implements a real-time position monitoring system using:

- STM32G474RE Nucleo board
- Renesas Inductive Position Sensor (IPS)
- FreeRTOS
- ADC + DMA
- Timer-triggered sampling
- Multi-task synchronization

The IPS sensor generates differential sine and cosine signals:
SINP
SINN
COSP
COSN

These signals are sampled using 4 ADC channels and processed using:
theta = atan2(sin, cos)

The system reconstructs the full mechanical angle from repeating electrical cycles and transmits the filtered angle over UART in real time.


# Features

- FreeRTOS multi-task architecture
- Timer-triggered ADC sampling
- ADC DMA circular mode
- Interrupt-to-task synchronization using binary semaphore
- Queue-based inter-task communication
- Mutex-protected UART transmission
- Real-time angle calculation using atan2f()
- Electrical-to-mechanical angle conversion
- Low-pass filtering for stable angle output
- Heartbeat LED task


# System Architecture

                TIM6 Trigger
                      │
                      ▼
               ADC1 Scan Sequence
                      │
                      ▼
                 DMA Circular
                      │
                      ▼
           DMA Complete Interrupt
                      │
                      ▼
             Binary Semaphore
                      │
                      ▼
               Processing Task
                      │
               Angle Queue
                      │
                      ▼
                 Logger Task
                      │
                      ▼
                    UART


# Hardware Used

## Development Board
- STM32G474RE Nucleo Board

## Sensor
- Renesas Inductive Position Sensor (IPS)

## Interfaces Used
- ADC1
- DMA1
- TIM6
- USART2


# Software Used

- STM32CubeMX
- STM32CubeIDE
- FreeRTOS CMSIS V2
- Tera Term / PuTTY


# RTOS Components Used

| Component | Purpose |
|---|---|
| Tasks | Concurrent execution |
| Queue | Angle data transfer |
| Binary Semaphore | ISR-to-task synchronization |
| Mutex | UART protection |
| DMA | Non-blocking ADC transfers |


# ADC Configuration

| Parameter | Value |
|---|---|
| ADC Channels | 4 |
| Resolution | 12-bit |
| Scan Mode | Enabled |
| Trigger Source | TIM6 TRGO |
| DMA Mode | Circular |
| Continuous Conversion | Disabled |


# Timer Configuration

TIM6 is configured to generate periodic ADC trigger events.

| Parameter | Value |
|---|---|
| Prescaler | 169 |
| Period | 999 |
| Sampling Frequency | 1 kHz |


# Signal Processing

## Differential Signal Calculation

sin_diff = SINP - SINN;
cos_diff = COSP - COSN;

## Electrical Angle

theta_{elec} = atan2(sin, cos)


## Mechanical Angle Reconstruction

The IPS produces 4 electrical cycles per mechanical revolution.

Mechanical angle is reconstructed using:

theta_{mech} = frac{theta_{elec} + 360 \times cycle}{4}


# Low-Pass Filtering

A first-order low-pass filter is used to reduce noise and angle jitter.

filtered_angle = (0.9f * filtered_angle) + (0.1f * mechanical_angle);


# UART Output Example

Angle = 124.52
Angle = 125.01
Angle = 125.44
Angle = 126.10


# Output Demonstration

Real-time mechanical angle logging through UART terminal using Tera Term.
