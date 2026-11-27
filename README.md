
<img width="2252" height="1783" alt="KakaoTalk_20261127_153242257" src="https://github.com/user-attachments/assets/76bd3cca-2c24-4aca-a1b7-b6fadfe18d9c" />

# PMSM-BLDC_FOC_Field_Oriented_Control_driver
Project for FOC algorithm and hardware development


## 1. Overview

<img width="642" height="357" alt="KakaoTalk_20261127_153242257_03" src="https://github.com/user-attachments/assets/78b27d4b-4712-49ad-996d-a125fbc85bf9" />


This project was developed to study Field-Oriented Control (FOC), a core technology in the development of QDD (Quasi-Direct Drive) actuators.

FOC is an algorithm that controls a motor by decoupling the magnetic flux axis from the torque axis; it generates three-phase voltages such that the torque axis always leads the magnetic flux axis by 90º.

※ A commercially available FOC driver was used, and the project involved studying the underlying principles by following the hardware design.

To achieve precise FOC control, the following functions were implemented:

- Rotor position sensing using an encoder
- Generation of PWM waveforms for inverter operation
- VDC (DC-link voltage) sensing for PWM waveform generation
- Current sensing via shunt resistors using an ADC
- MOSFET temperature sensing

## 2. System Architecture

<img width="677" height="217" alt="KakaoTalk_20261127_153242257_04" src="https://github.com/user-attachments/assets/2d5c914d-9e3e-4f5e-8fde-fb704872bbf7" />

### 2.1 3-phase BLDC/PMSM FOC

<br>- This method employs Field-Oriented Control (FOC), a technique for controlling a motor by transforming its phase currents into d-axis and q-axis currents referenced to the rotor. The three-phase currents (Ia, Ib, Ic) measured from the motor are converted into d-q axis currents (Id, Iq) using the electrical angle (θ). Each current is compared against a reference value, and a PI current controller is used to generate voltage command values ​​(Vd, Vq). Subsequently, these d-q axis voltages are converted back into three-phase voltages, and Space Vector Pulse Width Modulation (SVPWM) is applied to generate the inverter switching signals required to drive the Permanent Magnet Synchronous Motor (PMSM).

<br>- Furthermore, this control scheme is designed with a sequence of position, velocity, and current loops—a configuration known as a triple-cascade structure.

1. The position controller generates a velocity command based on the difference between the target position and the current position.
2. The velocity controller uses this velocity command to generate current commands for the d- and q-axes.
3. The current controller converts these current commands into voltage commands, which are then applied to the inverse dq-transformation equations.

Following the inverse transformation, an SVPWM (Space Vector Pulse Width Modulation) process is used to generate the pole voltage commands to be applied to the inverter.

### 2.2 Coordinate transformation technology

<br>**- Clarke transform (abc → αβ)**

<img width="340" height="248" alt="image" src="https://github.com/user-attachments/assets/7abc9ae6-f618-4488-b7e1-ef70ff735a8f" />

<br>**- Park transform (αβ → dq)**

<img width="215" height="148" alt="image" src="https://github.com/user-attachments/assets/83e38aa3-f63d-4973-a679-5ecc1c60954b" />


<br>**- inverse transform (dq → αβ → abc)**

<img width="192" height="246" alt="image" src="https://github.com/user-attachments/assets/b94578e3-4c44-4bed-bd30-9f5c47b1a0ca" />

### 2.3 Space Vector Pulse Width Modulation (SVPWM)

The reasons for choosing SVPWM control are as follows:

1. Improved DC-link voltage utilization (approximately 15.47% higher gain in terms of line-to-line voltage compared to SPWM)
2. Reduced torque ripple, as well as lower noise and vibration

<br><img width="590" height="233" alt="image" src="https://github.com/user-attachments/assets/48814cdf-7906-41dc-b59e-e47a51ae5efb" />

## 3. Hardware

The control board was designed using KiCad, and the circuit incorporates the following features:

1. NTC
2. EEPROM
3. Rotor Encoder
4. UART Communication
5. DC-DC Converter
6. 3.3V and 5V Regulators
7. 3-Phase Inverter
8. Current Sensing

**<br>-3D_MODEL**
<img width="1099" height="777" alt="KakaoTalk_20261127_153242257_05" src="https://github.com/user-attachments/assets/a5834460-09ba-4758-8fae-c2c0de4e5afd" />

**<br>-Artwork**
<img width="773" height="625" alt="KakaoTalk_20261127_153242257_06" src="https://github.com/user-attachments/assets/72a7de1c-5803-4d62-a909-3ec4b2adf78a" />

**<br>-schematic**
<img width="816" height="560" alt="image" src="https://github.com/user-attachments/assets/3fb58d19-41f4-4eb4-9a33-21692131a041" />

<img width="809" height="662" alt="image" src="https://github.com/user-attachments/assets/daa5c751-cee6-4675-aad2-9439b7531a9c" />

<img width="805" height="473" alt="image" src="https://github.com/user-attachments/assets/a1c66394-8b99-47df-aa47-1e06cf47b229" />

## 4. Magnetic encoder
- Encoder circuit used<br>
<img width="647" height="487" alt="Image" src="https://github.com/user-attachments/assets/cc2dc32b-d313-4581-91da-5e522607cb5b" />

<br>- A magnetic encoder is a sensor that detects the position and angle of a rotor by measuring the magnetic field that changes as the magnet rotates.

<a href="https://www.unikeyic.com/media/datasheet/1a/ec/f21b/1a/a92c7469140eec31eee517c392211910.pdf" target="_blank">
  <img width="465" height="565" alt="Image" src="https://github.com/user-attachments/assets/64a9e644-da99-4426-9154-952752612587" /> 
</a> <br>
Click the image to go to the AS5047P datasheet page.


<br>- The on-axis configuration measures the angle by aligning the centers of the magnet and the sensor with the axis of rotation, allowing for relatively simple measurement of the rotor's position.

<a href="https://rozum.com/encoders-in-rdrive-servos/" target="_blank">
  <img width="800" height="500" alt="Image" src="https://github.com/user-attachments/assets/429b0628-dfb7-429d-a054-42781f0b411b" />
</a> <br>
Clicking the image will take you to the Rozum Robotics source page. <br>

<br> - In this setup, using a diametrically magnetized magnet causes the direction of the magnetic field detected by the sensor to rotate along with the magnet, enabling the calculation of the rotation angle.

※ Operating Principle of a Diametrically Magnetized Magnet
  1. Assume that the magnet's North (N) and South (S) poles are arranged along the diameter.
  2. Before the magnet rotates, the magnetic field is oriented in a specific direction relative to the sensor.
  3. When the motor shaft rotates, the magnet's N and S poles rotate along with it.
  4. Consequently, the direction of the magnetic field vector observed at the sensor's location changes.
  5. Magnetic sensing elements within the sensor, positioned orthogonally to each other, measure signals in two directions.

  ※ The sensor calculates the angle based on these two signals.
  - ```Bx​=Acosθm​```
  - ```By​=Asinθm​```
  
  Therefore, the angle can be calculated as follows.
  - ```θm​=atan2(By​,Bx​)```
  
  Here, Bₓ and By are the two orthogonal components of the magnetic field measured by the sensor, and A represents the magnitude of the magnetic field under ideal alignment conditions. <br>

- The measured mechanical angle is converted into an electrical angle—accounting for the motor's pole-pair count and electrical angle offset—and is subsequently used for coordinate transformation and current control in the FOC (Field-Oriented Control) of a PMSM.<br>
- ```θₑ​=wrap(pθm​+θ₀​)``` <br>
  - θm: Measured mechanical angle
  - p: Number of motor pole pairs
  - θ₀: Calibration value for sensor mounting position and electrical angle reference
  - θₑ: Electrical angle used in FOC
  - Wrap: A calculation (wrap-around) that causes a value to cycle back to the beginning or end when it goes outside a specified range.

## 5. Current Sensing

In CMOS circuits, current sensing can be performed when the low-side MOSFET is turned on.

To achieve this, the control system triggers an interrupt when the low-side PWM signal is active, allowing the current data to be read.

The principle behind this current sensing involves using an ADC to measure the voltage drop across a shunt resistor. 
The current value can then be calculated by dividing this voltage by the resistance of the shunt resistor.

<img width="372" height="371" alt="image" src="https://github.com/user-attachments/assets/95b28acc-4b4f-4052-a19e-679536e0de17" />

## 6. Demonstration Video

<br>- velocity & Position control
<br><img width="601" height="277" alt="image" src="https://github.com/user-attachments/assets/d3738429-aaad-44ba-ad3e-c9d3a8f30caf" />
<br>[Demo Video 1](https://www.instagram.com/reel/DfSBYZBP8zA/?utm_source=ig_web_copy_link&dmpr=MzRlODBiNWFlZA==&avpt=MzRlODBiNWFlZA==)

<br>- Position PID Tuning
<br><img width="616" height="277" alt="image" src="https://github.com/user-attachments/assets/99b3a961-27e0-4714-a176-e0e16d538ec4" />
<br>[Demo Video 2](https://www.instagram.com/reel/DfT5SeqP6IH/?utm_source=ig_web_copy_link&evlk=MzRlODBiNWFlZA==&avpt=MzRlODBiNWFlZA==)

## 7. License

## 8. References
  [1] https://www.unikeyic.com/media/datasheet/1a/ec/f21b/1a/a92c7469140eec31eee517c392211910.pdf
  
  [2] https://rozum.com/encoders-in-rdrive-servos/
