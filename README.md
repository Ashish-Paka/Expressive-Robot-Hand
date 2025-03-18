# Expressive Robotic Hand with Computer Vision, Servo Control, and 3D-Printed Design

## Introduction

The **Expressive Robotic Hand** project is a state-of-the-art system designed to replicate human-like dexterity and expressiveness while serving as an interactive personal assistant. By integrating:
- **3D-printed mechanical components**
- **Servo-based actuation**
- **Camera-driven hand tracking and gesture recognition**
- **Serial communication with an Arduino**

...the system can detect a user’s gestures in real time and adapt its own movements accordingly. This repository includes code for camera-based hand tracking (using MediaPipe and OpenCV), sensor data fusion, gesture mapping, and Arduino-based servo control. Future developments will integrate advanced interaction features such as voice commands and reinforcement learning.

---

## Table of Contents
1. [Mechanical Design and 3D Printing](#mechanical-design-and-3d-printing)
2. [Hardware and Electronics](#hardware-and-electronics)
3. [Software Architecture](#software-architecture)
4. [Hand Tracking and Pose Estimation](#hand-tracking-and-pose-estimation)
5. [Step-by-Step Working Explanation](#step-by-step-working-explanation)
6. [Future Work](#future-work)
7. [Code Organization](#code-organization)
8. [Including Images and Videos](#including-images-and-videos)
9. [Summary](#summary)

---

## 1. Mechanical Design and 3D Printing

### CAD Modeling & Design
- **CAD Tools:** The hand was designed using CAD software (e.g., SolidWorks or Fusion 360) to ensure precise geometry and robust joint configurations.
- **Design Objectives:**
  - **Lightweight:** Minimal material usage with structural strength.
  - **Modular:** Finger segments are designed for easy assembly and replacement.
  - **Cable-Driven:** Internal channels route cables to connect servo motors to the finger joints.

### 3D Printing Process
- **Printer & Settings:**
  - Example: Prusa i3 MK3 or similar FDM printer.
  - **Materials:** PLA or PETG for a balance between rigidity and durability.
  - **Layer Height:** ~0.2 mm for fine details.
  - **Infill:** 20–30% (using Grid or Gyroid infill) to ensure strength.
  - **Supports:** Enabled for overhangs, particularly in joint areas.
- **Example Image:**
  ![3D Print on Build Plate](docs/3d_print.jpeg)

### Assembly
- **Finger Segments:** 3D-printed parts are assembled using pins or screws.
- **Palm & Enclosure:** Houses servo motors or routes cables to an external servo box.
- **Servo Mounting:** Servos are attached securely; cables run through the finger segments to actuate joints.
- **Assembly Photo:**
  ![Assembled Robotic Hand](docs/hand.jpg)

---

## 2. Hardware and Electronics

### Components
- **Servo Motors:** 5 servo motors to mimic human finger movements.
- **Arduino/ESP32:** Microcontroller for receiving serial data and controlling servos.
- **Power Supply:** External power for servos (e.g., 5V, 2–3A) with a common ground.
- **Camera/Webcam:** For real-time hand tracking.
- **Miscellaneous:** Jumper wires, breadboard, and necessary connectors.

### Wiring & Setup
- **Servo Wiring:** Signal wires connected to PWM pins; power supplied externally with shared ground.
- **Microcontroller:** Receives control data via serial and drives the servos using the Arduino Servo library.
- **Camera Setup:** Connects via USB and is accessed by OpenCV for real-time image processing.

---

## 3. Software Architecture

### Computer Vision and Gesture Recognition
- **Camera-Based Tracking:**
  - Uses OpenCV to capture video and MediaPipe for hand landmark detection.
  - The `gesture_recognition.py` script processes the video feed, extracts hand landmarks, and calculates normalized distances (e.g., fingertip-to-palm).
  - An Exponential Moving Average (EMA) filter smooths these measurements.

- **Mapping to Servo Angles:**
  - Normalized distances are mapped to a range of 0–180° (rounded to the nearest 15°).
  - The calculated values are sent via serial communication to the Arduino.

### Arduino Servo Control
- **Serial Communication:**  
  The Arduino reads comma-separated values from the serial port.
- **Servo Actuation:**  
  Uses the Arduino Servo library to drive servos to the received angle positions.
- **Differential Drive Kinematics:**  
  Although primarily used in mobile robotics, differential drive principles are applied to coordinate paired motor actions if needed for additional degrees of movement.

---

## 4. Hand Tracking and Pose Estimation

- **Camera-Based Tracking:**  
  - The system leverages a webcam and MediaPipe to detect hand landmarks in real time.
  - Hand landmarks (e.g., fingertips, wrist, MCP joints) are used to calculate distances for gesture recognition.
  
- **Sensor Fusion:**  
  - While the current implementation relies on the camera, future iterations will incorporate data from flex sensors and IMUs to enhance tracking accuracy.
  - Sensor fusion techniques (like Kalman filtering) are planned to merge data streams for robust pose estimation.

- **Feedback Loop:**  
  - The processed hand pose information is used to update control loops in real time, ensuring that the robotic hand mirrors the user’s gestures accurately.
  - This information is critical for enabling personal assistant functions, such as interpreting commands or performing tasks based on recognized gestures.

---

## 5. Step-by-Step Working Explanation

### Step 1: System Initialization and Calibration
1. **Power Up and Setup:**
   - Connect servo motors to the Arduino.
   - Attach the external power supply and ensure common ground.
   - Connect the USB webcam.
2. **Arduino Initialization:**
   - Upload the Arduino code (e.g., `arduino_code.ino`) to the board.
   - The Arduino waits for serial input.
3. **Computer Vision Startup:**
   - Run `gesture_recognition.py`.
   - The webcam feed is captured using OpenCV.
   - MediaPipe initializes and begins detecting hand landmarks.

### Step 2: Calibration Phase
1. **Data Collection:**
   - A calibration phase (approx. 10 seconds) collects the minimum and maximum distances between key hand landmarks.
   - The system uses this data to normalize future measurements.
2. **User Interaction:**
   - The user moves their hand naturally during calibration to provide a range of motion.
   - Visual feedback is provided on-screen.

### Step 3: Real-Time Gesture Recognition and Mapping
1. **Image Processing:**
   - The script converts each frame to RGB.
   - MediaPipe processes the frame to detect hand landmarks.
2. **Distance Calculation:**
   - Distances between the wrist and fingertips (and thumb-to-pinky MCP) are computed.
   - These distances are normalized relative to a reference measurement.
3. **Smoothing and Mapping:**
   - An EMA filter smooths the raw distance values.
   - The smoothed values are mapped to servo angle ranges (0–180°), rounded to the nearest 15°.

### Step 4: Serial Communication and Actuation
1. **Data Transmission:**
   - Mapped servo angles are formatted as a comma-separated string.
   - The string is transmitted over serial to the Arduino.
2. **Servo Control:**
   - The Arduino reads the incoming data.
   - Each servo is commanded to move to the specified angle, replicating the user's hand pose.
3. **Feedback:**
   - The process repeats in real time, allowing continuous tracking and actuation.

### Step 5: Personal Assistant Interaction
1. **Gesture Recognition:**
   - Beyond simple mimicry, the system interprets specific gestures (e.g., a wave or a pinch) as commands.
2. **Adaptive Response:**
   - In future iterations, recognized gestures will trigger predefined assistant actions, such as greeting the user or executing simple tasks.
3. **Learning and Adaptation:**
   - Reinforcement learning modules are planned to refine the system's responses based on user interaction over time.

---

## 6. Future Work

### Reinforcement Learning Integration
- **Expanded Action Space:**  
  Enhance the control framework to include adaptive speed, dynamic goal switching, and nuanced interaction behaviors.
- **Reward Function:**  
  Develop a multi-objective reward function that balances gesture accuracy, collision avoidance, and energy efficiency.
- **RL Algorithm:**  
  Implement multi-agent RL (e.g., MAPPO) to train decentralized policies in simulation (Gazebo), then fine-tune for real-world deployment.
- **Policy Transfer:**  
  Use online fine-tuning and domain randomization to ensure robust transfer of learned policies to the physical system.

### Enhanced Physical Implementation and Personal Assistant Features
- **Hardware Upgrades:**  
  Integrate custom PCBs, robust motor drivers, and enhanced sensor suites for improved reliability.
- **Field Trials:**  
  Conduct comprehensive testing in varied environments (laboratory, industrial, outdoor) to validate performance metrics such as mapping accuracy and control responsiveness.
- **Human-Robot Interaction:**  
  Develop intuitive user interfaces and communication protocols to enable natural interactions, enhancing the hand’s role as a personal assistant.

---

## 7. Code Organization

- **src/**: Contains source code for sensor processing, control loops, and ROS2 node prototypes.
- **docs/**: Includes CAD models, FEA reports, calibration files, images, and videos.
- **config/**: Holds configuration files for sensor calibration, PID tuning, and system parameters.
- **scripts/**: Utility scripts for data logging, performance analysis, and simulation setup.
- **tests/**: Unit tests for individual modules.
- **README.md**: This file.

---

## 8. Including Images and Videos

To include images or videos from the `docs` folder, use relative paths:
  ![3D Print on Build Plate](docs/3d_print.jpeg)
  ![Assembled Robotic Hand](docs/hand.jpg)
![Watch the Demonstration Video](docs/Video.mp4)
