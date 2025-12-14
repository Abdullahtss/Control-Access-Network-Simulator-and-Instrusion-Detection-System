CAN Bus Intrusion Detection System (IDS) Simulator
This project is a Python-based simulator for Controller Area Network (CAN) traffic, designed to generate synthetic vehicle network data and detect cybersecurity attacks using Machine Learning.

It simulates normal ECU communication and injects common cyberattacks (DoS, Fuzzing, and Suspicious Patterns), then uses an Isolation Forest model to detect these intrusions in real-time based on statistical anomalies.

🚀 Features
Traffic Simulation: Generates realistic CAN bus traffic mimicking Engine, Brake, Steering, and Speed sensors.

Attack Injection: Simulates three specific types of attacks:

DoS (Denial of Service): High-frequency message flooding.

Fuzzing: Injection of random IDs and payloads.

Payload Attacks: Suspicious data patterns (e.g., impossible brake signals).

Feature Engineering: Converts raw CAN frames (ID, Timestamp, 8-byte payload) into statistical features suitable for ML.

Unsupervised Learning: Uses sklearn.ensemble.IsolationForest trained only on normal traffic to detect unknown anomalies (Zero-Day threat simulation).

Evaluation Metrics: Includes Confusion Matrix, F1-Score, and ROC-AUC analysis.

Live Dashboard: Simulates a real-time monitoring stream with visual plotting of anomaly scores and console alerts.

🛠️ Tech Stack
Python 3.x

Pandas & NumPy: Data manipulation and statistical calculations.

Scikit-Learn: Machine Learning (Isolation Forest, Metrics).

Matplotlib: Visualization of traffic and anomaly scores.

📊 How It Works
Data Generation: The script creates a baseline of "Normal" traffic based on typical ECU frequencies and values.

Attack Simulation: Malicious datasets are generated and injected into the timeline.

Processing: The system calculates time deltas between messages and statistical properties of the data payload (Mean, Std Dev).

Training: An Isolation Forest model is trained on the "Normal" portion of the data to learn what healthy traffic looks like.

Detection: The model scores the combined dataset. Data points that deviate significantly from the learned "Normal" distribution are flagged as attacks.

📈 Performance
The model typically achieves high accuracy (F1-Score > 0.85) in distinguishing between normal driving patterns and injected noise/flooding attacks.

💻 Usage
Clone the repository.

Install dependencies:

Bash

pip install numpy pandas matplotlib scikit-learn
Run the Jupyter Notebook:

Bash

jupyter notebook Control_Access_Network_Simulator.ipynb
Execute the cells sequentially to generate data, train the model, and view the live dashboard simulation.
