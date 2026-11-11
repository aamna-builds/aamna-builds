## Aamna Khan

Mechanisms, robotics and the hardware around them. B.Tech Mechanical Engineering, Ahmedabad University, class of 2028.

I treat a robot as one problem rather than separate parts: the linkage and where it loses control, the torque that has to come from somewhere, the frame it stands on, the firmware that moves it, and a clear note of what has run on real hardware and what has only run in simulation.

Each repository has the analysis behind the build as well as the build itself: the numbers that explained a problem, the decision it led to, and what is still open.

<br>

### Parallel mechanisms

**[5bar-chess-robot](https://github.com/aamna-builds/5bar-chess-robot)**: a chess robot with no gripper. A magnet under a 400 × 400 mm acrylic board, driven by a two-motor five-bar linkage, slides magnetic pieces from square to square. [Live simulator](https://aamna-builds.github.io/5bar-chess-robot/).

- **Built (Jan–Apr 2026):**
  - Hardware: an aluminium-extrusion frame, two NEMA 17 motors with 14:1 planetary gearboxes on TB6600 drivers, an Arduino Uno, and a permanent magnet flipped by a servo.
  - The torque budget forced a redesign mid-build: the board went from 440 to 400 mm and the links were shortened, instead of buying motors that were over budget.
  - It was demonstrated working, but it was jerky near the motors and less accurate at the board edges.
- **Explained:**
  - I derived the velocity Jacobian from the loop-closure constraints.
  - It shows the ranks nearest the motors sit 0.4° from a direct (type-II) singularity. There, one microstep moved the magnet up to 1.4 mm.
  - The two far corners were 2 mm out of reach.
- **Redesigned:**
  - A grid search over about 11,000 feasible layouts, with the motor spacing kept at least 100 mm and the total link length at most 580 mm, chose 265 / 315 mm links, 100 mm spacing and a board 57 mm further from the motors.
  - The worst-case singularity margin over every point a piece can travel through went from 0° to 21.8°.
  - Resolution is now at most 0.08 mm per microstep, and estimated peak torque is 0.5 N·m. On v1's near ranks it was over 4.4 N·m.
- **Software:**
  - A chess rules engine verified against standard perft counts, and a path planner that routes knights and blocked pieces along the lanes between squares and parks captures at the side.
  - Arduino firmware that solves the inverse kinematics on board and checks a whole move against the singularity limits before taking a step.
  - One JSON configuration feeds the MATLAB, Python, C++ and JavaScript versions, and CI checks they agree on every square.
- **Open:** the v2 links are not cut yet. The new firmware compiles and passes its tests on a PC, but has not run on the robot.

<br>

### Balance and control

**[Bo-Bot](https://github.com/aamna-builds/Bo-Bot)**: a self-balancing two-wheeled robot in a cat-shaped casing with an animated OLED face. Its controller is an ESP32 driving N20 motors with encoders, with an MPU-6050 for tilt. [Live simulator](https://aamna-builds.github.io/Bo-Bot/).

- **Model:** the plant is linearised from datasheet values. Its open-loop pole at +10.6 s⁻¹ means it falls over in about a tenth of a second.
- **Controller:**
  - Cascaded speed and angle loops run at 200 Hz.
  - The analysis showed that the filter lag on the speed estimate is what makes the loop stable: without it the poles are at +3.6 ± 71j.
  - With the 3.5 ms control delay included, the phase margin is 32° and the stable range of kp is 16.5 to 121.
- **Cross-checked:**
  - The Python and MATLAB analyses agree (MATLAB R2025b self-check).
  - The linear model is compared against a nonlinear browser simulator that runs the firmware's own control law.
- **Design:** a parametric Fusion 360 script drives both the CAD model and the A3 drawings, so the two cannot drift apart.
- **Open:** the firmware is written and tested against the simulator, but not yet run on hardware. The casing build is next.

<br>

### Arms

**[mearm-robotic-arm](https://github.com/aamna-builds/mearm-robotic-arm)**: a 4-DOF arm in laser-cut 4 mm acrylic, with Arduino PWM control of four servos and scripted pick-and-place.

- The elbow ran short of torque at full extension.
- The power supply and grounding decided how smooth the motion was.
- Next: inverse kinematics and smoother trajectories.

<br>

**How I work:**
- Geometry, units and limits are written once, in a single configuration file that every language reads.
- Each result is checked against a second implementation or a hand calculation.
- I keep the measured number, not the impression.
- Each project says plainly what has run on hardware and what has only run in simulation.
- Tests run in CI, so the work runs on someone else's machine too.

**Tools:** SolidWorks · Fusion 360 and its scripting API · MATLAB and Simulink · Python · C/C++ on Arduino and ESP32 · Raspberry Pi · laser cutting, 3D printing, aluminium-extrusion frames · Git and GitHub Actions

[LinkedIn](https://www.linkedin.com/in/aamnakhan17/) · [Email](mailto:ww.aamnakhan1709@gmail.com)
