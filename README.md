# Bluetooth-based-oscilloscope-
A low-cost wireless oscilloscope prototype using ESP32, LM358, and OLED display for analog signal acquisition and waveform monitoring.
Bluetooth-Based Oscilloscope

A Wireless Signal Monitoring System Using ESP32 and Bluetooth

1. About the Project

The Bluetooth-Based Oscilloscope is an electronics project designed to monitor and display electrical signals wirelessly. It uses an ESP32 microcontroller to acquire analog signals and transmit the collected data to a display interface.

The project aims to provide a simple, portable, and cost-effective alternative for basic waveform monitoring and analysis.

2. Objectives

- To develop a wireless signal monitoring system.
- To acquire analog signals using the ESP32 ADC.
- To display real-time waveforms.
- To monitor voltage variations.
- To explore Bluetooth communication in electronics instrumentation.
- To develop a low-cost and portable oscilloscope prototype.

3. Components Used

Component| Purpose
ESP32| Microcontroller and signal acquisition
LM358| Operational amplifier
1.3-inch OLED Display| Waveform visualization
Potentiometer| Input signal adjustment
Resistors| Voltage division and circuit protection
Diodes| Input protection
Capacitor| Signal testing
Breadboard| Circuit connections
Jumper Wires| Electrical connections

4. Working Principle

1. An analog electrical signal is applied to the input circuit.
2. The LM358 conditions the input signal.
3. The ESP32 reads the analog signal through its ADC pin.
4. The acquired data is processed by the microcontroller.
5. The waveform and voltage information are displayed on the OLED.
6. Bluetooth communication can be used to transmit signal data to a smartphone.

5. Features

- Wireless communication using Bluetooth.
- Analog signal acquisition using ESP32.
- Real-time waveform visualization.
- Voltage monitoring.
- Compact and portable design.
- OLED-based graphical display.
- Scope for future mobile application integration.

6. Applications

- Electronics laboratory experiments.
- Basic electrical signal monitoring.
- Educational demonstrations.
- Circuit testing and troubleshooting.
- Learning embedded systems and wireless communication.

7. Future Scope

- Integration with a dedicated Android application.
- Improved waveform accuracy through digital filtering.
- Multiple-channel signal monitoring.
- Waveform recording and data storage.
- Frequency and amplitude measurement.
- Development of a compact battery-powered oscilloscope.

8. Technologies Used

- Programming Language: C/C++
- Microcontroller: ESP32
- Development Environment: Arduino IDE
- Communication: Bluetooth
- Display: I2C OLED (SH1106)
- Signal Conditioning: LM358

9. Project Status

Development Stage: Prototype

The project is being developed to demonstrate analog signal acquisition, waveform visualization, and wireless data communication using an ESP32.

10. Contributors

Developed as an academic electronics project.

Project Domain: Electronics and Communication Engineering (ECE)

---

11. License

This project is intended for educational and learning purposes. You are welcome to explore, modify, and improve the implementation.
