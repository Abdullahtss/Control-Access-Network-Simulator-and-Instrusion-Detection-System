This file explains the internal logic, the "why" and "how" of the simulation.

Project Technical Details: CAN Simulator & IDS
1. Overview
Modern vehicles rely on the Controller Area Network (CAN) protocol for communication between Electronic Control Units (ECUs) like the engine, brakes, and airbags. However, CAN lacks built-in security (no encryption or authentication), making it vulnerable to hacking.

This project simulates a CAN network and implements an Anomaly-Based Intrusion Detection System (IDS). Unlike signature-based systems (which look for known bad patterns), this system learns what "Normal" looks like and flags anything that deviates from it.

2. The Simulation Logic
A. Normal Traffic Generation
The simulator creates a baseline by mimicking four specific ECUs. It uses numpy distributions to create realistic data noise:

Engine (ID 0x100): Sends RPM data (Normal distribution centered at 120).

Brake (ID 0x200): Sends status binary flags (Binomial distribution).

Steering (ID 0x300): Sends angle data (Normal distribution centered at 0).

Speed (ID 0x400): Sends speedometer data (Normal distribution centered at 60).

B. Attack Scenarios
We inject specific traffic patterns to test the IDS:

DoS Attack (Denial of Service):

Mechanism: Floods the bus with high-priority messages (ID 0x666) with near-zero time gaps.

Goal: To overwhelm the bus so legitimate messages cannot get through.

Fuzzing Attack:

Mechanism: Injects random CAN IDs (0x500 to 0x7FF) with random junk payloads.

Goal: To find vulnerabilities or crash ECUs by sending unexpected inputs.

Suspicious Pattern (Spoofing):

Mechanism: Uses a legitimate ID (Brake 0x200) but sends data values that physically don't make sense (e.g., values > 200).

Goal: To trick the car into thinking the brakes are applied.

3. Feature Engineering
Raw CAN data (ID, Data Bytes) isn't directly usable by most ML models. We transform the data into four key features:

delta_time: The time difference between the current message and the previous one.

Why? Helps detect DoS attacks (where time gaps become tiny).

can_id_normalized: Scaling the ID to a 0-1 range.

Why? Allows the model to recognize valid vs. invalid ID ranges (Fuzzing detection).

data_mean: Average value of the 8 data bytes.

data_std: Standard deviation of the 8 data bytes.

Why? These two statistical features help detect Payload attacks (where data values spike unexpectedly).

4. The Machine Learning Model: Isolation Forest
We use Isolation Forest, an unsupervised learning algorithm ideal for anomaly detection.

How it works: It builds random decision trees. Anomalies (attacks) are typically rare and different, so they are easier to "isolate" (they end up in shorter branches of the tree). Normal data requires more splits to isolate (deeper branches).

Training Strategy: We train the model only on Normal Data. This teaches the model "This is what safe driving looks like."

Testing: We then show the model mixed traffic. If the model sees a DoS attack or Fuzzing, it recognizes that the statistical properties (Time Delta, ID, Data) do not fit the "Normal" profile it learned, and assigns it a low anomaly score.

5. Live Dashboard
The final part of the project is a simulated real-time loop. It iterates through the test dataset row by row, feeding data to the model. If the Isolation Forest returns a score below a specific Threshold (calculated from the bottom 20% of scores), the system triggers an [ALERT], simulating a dashboard warning for the driver or security center.
