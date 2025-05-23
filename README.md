# Hoverpod X  FPGA based Autonomous Hovering Camera Platform

** Hoverpod X ** is an open-source, FPGA-powered smart camera platform that replaces traditional tripods. Designed to track user movement and stabilize mobile phones like the iPhone during filming, Hoverpod X is ideal for vloggers, athletes and content creators seeking hands-free, smooth video capture. 

## Project Overview 
This project explores the use of FPGAs for real-time motor control, PWM signal generation and stabilization logic to build a self-balancing, hovering platform. It leverages BLDC motors fir veritical lift and servo motors for pan/tilt control of an iphone mount. 

---

## Key Features 
- FPGA based PWM signal genration (verilog)
- BLDC motor control via ESC (Electronic Speed Controller)
- Servo pan/tilt system for camera orientation
- Sensor integration for stabilization (IMU planned)
- Modular Verilog architecture with simulation support
- Open source and well documented for education use 

---

## System Architecture 
1. ** Payload Layer (Camera Mount & Stabilization)**
  - iPhone Mount on a servo driven gimbal
  - Controlled via FPGA generated PWM signals
  - Allows pan/tilt to keep subject centered
2. ** Control Layer (FPGA Core Logic) **
  This is the brain of the system. Implemented on the Basys 3 FPGA.
    Verilog Modules:
  - pwm_generator.v: Produces PWM signals for both BLDC motors via ESCs and servos
  - esc_controller.v: Interfaces with ESCs to control BLDC speed
  - servo_controller.v: Controls the pan/tilt gimbal via SG90/MG996R servos
  - imu_interface.v: Reads orientation from MPU6050 
  - pid_controller.v: Implements stabilization logic for flight balance
  - tracker_interface.v: (Future development): Takes in user position data from sensor tags for       tracking
3. ** Actuation Layer (Motors & Servo)**
  - 4x BLDC motors provide lift, controlled via ESCs
  - Servo motors adjust the iphone gimbal's angle
  - All motors receive PWM signals from the FPGA
4. ** Power & Regulation Layer**
  - 4S LiPo Battery (14.8 V)
    - Directly powers ESCs + BLDC motors
    - Passed through a buck converter (LM2596) to power:
      - FPGA (5V regulated) via barrel jack
      - Servos (5V) with adequate current
    - Shared Ground across all components to ensure reliable signal integrity
  - Allows pan/tilt to keep subject centered

---
---

## System Data Flow

The Hoverpod X platform follows a structured control flow centered around real-time motor management and stabilization:

1. **Sensor Feedback (IMU) → FPGA Control Logic**  
   - The **MPU6050 IMU** continuously measures orientation data (pitch, roll, yaw).
   - This data is sent to the FPGA over an I²C interface.
   - The FPGA processes this data using a PID control algorithm , generating corrective control signals.

2. **FPGA → PWM Generator → Actuators**
   - The control logic feeds target motor speeds to the PWM generator module.
   - The FPGA outputs PWM signals:
     - To ESCs for controlling BLDC motors (hover and lift).
     - To servo motors for adjusting the iPhone pan/tilt gimbal.

3. **User Tracking (Future Phase)**
   - A separate module (e.g., a Raspberry Pi with camera) will identify and track the user.
   - Target movement data will be sent to the FPGA via a UART or SPI interface.
   - The FPGA will translate this into position adjustments for the servos and motor balance.

4. **Power Distribution**
   - The **LiPo battery** provides primary power:
     - Directly to the ESCs and motors
     - Through a buck converter to power the FPGA and servos
   - All components share a common ground to ensure consistent signal referencing.

---

### 🔁 Summary of Flow

- **Sensors → FPGA**: Orientation and tracking inputs
- **FPGA → Actuators**: PWM control of BLDCs and servos
- **Battery → Power Rails**: Isolated and regulated power delivery
- **External Module (Future Dev)**: tag for user tracking

This architecture ensures responsive control, modular development, and reliable signal flow, closely emulating real-world embedded robotics systems.

---

---

##  Development Timeline

| Phase                          | Tasks                                    |
|--------------------------------|------------------------------------------|
| ✅ Planning & Block Diagram     | System design and architecture draft     |
| 🔄 PWM Generation               | Verilog modules for BLDC/servo PWM       |
| 🔄 ESC Communication            | ESC signal interfacing for BLDCs         |
| 🔲 IMU Sensor Feedback          | Integrating MPU6050 for stability input  |
| 🔲 PID Stabilization Logic      | Implementing control loop for hovering   |
| 🔲 iPhone Gimbal Servo System   | Servo control for pan/tilt stabilization |
| 🔲 Human Movement Tracking      | Vision/sensor tracking (future phase)    |
| 🔲 Final Integration & Testing  | Assembly, debugging, tuning              |
| 🔲 Documentation & Release      | Demo, GitHub content, schematics         |


---

## 🛠 Tools & Technologies

- **FPGA Board**: Basys 3 (Xilinx Artix-7)
- **HDL**: Verilog
- **Simulation**: Vivado, ModelSim
- **Motors**: 4x BLDC with ESC (minimum 600g thrust per motor)
- **Servos**: SG90 or MG996R for pan/tilt
- **Sensors**: MPU6050 IMU (for stabilization feedback)
- **Power Supply**:
  - FPGA: 5V via barrel jack using LiPo + buck converter
  - Motors: 4S LiPo (14.8V) directly to ESCs

---
---

## 📦 Bill of Materials (BOM)

Here is a complete hardware list for building the Hoverpod X prototype. Components are selected to match the FPGA-based architecture and support stable hovering with an iPhone payload.

| Category            | Component                        | Specs / Notes                                                              | Qty | Est. Price (USD) |
|---------------------|----------------------------------|----------------------------------------------------------------------------|-----|------------------|
|  **FPGA Controller** | **Basys 3 Board**                | Xilinx  FPGA, 3.3V IO, micro-USB + barrel power input               | 1   | $150              |
|                    | Jumper Wires                     | Male-male and male-female for interfacing                                  | 1 set | $6               |
|  **Motors**        | **BLDC Motors**                  | 2206 or 2207 size, ~600–800g thrust on 3S or 4S battery                     | 4   | $15 ea ($60)     |
|                    | **ESCs**                          | 20A–30A brushless ESC with PWM signal input                                | 4   | $10 ea ($40)     |
|  **Stabilization** | **MPU6050 IMU**                  | 3-axis gyro + 3-axis accelerometer, I2C-compatible with FPGA               | 1   | $5               |
|  **Servos**        | **SG90** or **MG996R**           | For pan/tilt mount. MG996R is  powerful (~2.5–5kg·cm torque)           | 2   | $6–$10 ea        |
|  **Mount**         | iPhone Tripod Adapter            | Holds the iPhone; 3D printed bracket or clamp mount                        | 1   | $10              |
|  **Power**         | **3S LiPo Battery**              | 11.1V, 2200mAh, 25C–35C (power for ESCs + servos)                           | 1   | $25              |
|                    | **DC-DC Buck Converter**         | LM2596-based 11.1V → 5V for FPGA and servos                                | 2   | $6 ea ($12)      |
|                    | **Barrel Jack Adapter**           | 2.1mm center-positive, to plug into Basys 3 barrel port                    | 1   | $3               |
|                    | **Power Distribution Board**      | Optional for clean power wiring to ESCs/servos                             | 1   | $8               |
|  **Frame**         | Carbon or Aluminum Arms          | Light, durable material for mounting motors + central frame                | —   | ~$15             |
|                    | Screws, spacers, zip ties         | For clean mounting and wire organization                                   | —   | ~$5              |
|  **Testing**       | Breadboard + Headers             | For prototyping PWM + servo connections                                    | 1   | $10              |
|                    | Oscilloscope                     | To test PWM output (or use logic analyzer )                                | —   | —                |

###  Estimated Total Cost: **~$360–$400 USD**

---



