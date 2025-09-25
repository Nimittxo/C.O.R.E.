## Abstract 📝
C.O.R.E. (Cognitive Operations & Recovery Engine)

 is a desktop application designed to serve as a Digital Twin for real-time vehicle and aircraft diagnostics. This project demonstrates a complete Predictive Maintenance (PdM) pipeline, shifting from reactive to proactive maintenance strategies. By streaming and processing simulated multi-dimensional sensor data, C.O.R.E. utilizes deep learning models for unsupervised anomaly detection. The system provides an intuitive 3D visualization where a vehicle's subsystems are color-coded to reflect their operational health. Detected anomalies, such as production faults or system defects, are flagged in real-time. The platform also simulates a "self-healing" process, visually representing the recovery from a fault state to a healthy one. This MVP serves as a powerful proof-of-concept for reducing operational downtime, minimizing maintenance costs, and providing an interactive educational tool for understanding the principles of Industry 4.0 and predictive analytics.


## Role of Machine Learning 🧠
The machine learning component is the "cognitive" engine of C.O.R.E. Its primary role is 

unsupervised time-series anomaly detection (TSAD). Since real-world systems often lack labeled fault data, an unsupervised approach is the most flexible and realistic.


Learning Normal Behavior: The ML model will be trained exclusively on data representing normal operational behavior. It learns the intricate temporal (time-based) and inter-metric (correlation-based) dependencies between different sensors—for example, how engine RPM relates to temperature and oil pressure.


Detecting Deviations: Once trained, the model processes new, incoming data. It attempts to either 

forecast the next data point or reconstruct the current one. An anomaly is detected when there is a significant discrepancy between the model's output (the expected value) and the actual sensor reading. This discrepancy is calculated as an 



anomaly score. A score exceeding a predefined threshold signals a fault.


Model Agnosticism: The system is designed to be model-agnostic by using the ONNX (Open Neural Network Exchange) format. This allows us to train various models in Python (e.g., LSTMs, Autoencoders, Transformers) and seamlessly deploy them in the C++ application for high-performance inference.

## Dataset to be Used 📊
For the MVP, we will use publicly available, well-benchmarked time-series datasets that are standard in PdM research. This ensures our results are credible and comparable to existing work.

The most suitable datasets are 

multivariate time series (MTS) from industrial or aerospace domains, as they best represent the complexity of vehicle/aircraft systems.


Primary Choices:


SMD (Server Machine Dataset): A widely used dataset with data from 28 different server machines, containing 38 dimensions (features). It's excellent for testing the model's ability to handle high-dimensional data.


MSL (Mars Science Laboratory rover): Telemetry data from NASA's Curiosity rover, containing 55 dimensions. Its aerospace origin makes it highly relevant.


PSM (Pooled Server Metrics): Real-time anomaly detection data from eBay's server infrastructure, with 24 dimensions.



Why These Datasets? These datasets are commonly used to benchmark state-of-the-art (SOTA) TSAD models. They contain labeled anomalies, which is crucial for evaluating the performance of our unsupervised model using metrics like the 

F1-Score.

## Problems to Face During Building
Building C.O.R.E. involves overcoming several technical challenges that bridge the gap between data science and software engineering.

ML Model Integration: The most significant hurdle is linking the Python-trained ONNX model with the C++ application. This requires precise management of the ONNX Runtime dependency in CMake and ensuring tensor shape and data type consistency between the C++ data structures and the model's expected input.

3D Interaction (Picking): Translating a 2D mouse click on the screen to identify a specific 3D component of the car model is a non-trivial problem in computer graphics. For the MVP, we will simplify this by loading separate models for key subsystems (e.g., engine.obj, wheels.obj) and managing them as distinct entities.

Preprocessing Parity: Any data normalization or scaling (e.g., scaling values to a 0-1 range) done during Python model training must be identically replicated in C++ before feeding data to the model. Failure to do so will result in incorrect predictions.

UI Responsiveness: Running ML inference can be computationally intensive. To prevent the user interface from freezing, the ideal long-term solution is to run the anomaly detection process in a separate worker thread. For the MVP, we'll start with a single-threaded approach, assuming the model is small enough for near-real-time performance.

## Functions and Models
Model Architecture: Forecasting-Based LSTM
For the MVP, a forecasting-based Long Short-Term Memory (LSTM) network is an excellent choice. LSTMs are a type of Recurrent Neural Network (RNN) specifically designed to learn long-term temporal dependencies in sequential data, which is perfect for sensor readings.


How it works:

The model takes a "sliding window" of recent sensor data (e.g., the last 50 timestamps) as input.


It processes this sequence through its LSTM layers, which use internal "gates" (forget, input, output) to remember important patterns and forget irrelevant ones.

The final layer of the network predicts the sensor values for the very next timestamp.


The 

anomaly score is the difference (e.g., Euclidean distance or Mean Absolute Error) between the predicted values and the actual values that arrive at that timestamp.


Kernel/Loss Function

Loss Function for Training: During training, the model's weights will be optimized to minimize the Mean Absolute Error (MAE) between its predictions and the actual data in the training set. MAE is robust and a standard choice for regression-style forecasting problems.


"Function" for Anomaly Score: In the application, the anomaly score at runtime will be calculated using the Euclidean Distance between the predicted data vector and the actual data vector. This gives a straightforward measure of deviation.

## Relation to UN Sustainable Development Goals (SDGs) 🌍
The C.O.R.E. project, while a technical MVP, aligns with several key UN SDGs by demonstrating technology that promotes efficiency, resilience, and sustainability.

SDG 9: Industry, Innovation, and Infrastructure: The project directly supports this goal by creating a tool for building more resilient and reliable infrastructure (vehicles, aircraft, industrial machinery). Predictive maintenance is a key innovation in Industry 4.0 that minimizes failures and enhances operational safety and efficiency.

SDG 11: Sustainable Cities and Communities: By improving the reliability of transportation systems (both public and commercial), this technology contributes to safer and more efficient urban mobility. Fewer unexpected breakdowns of buses, trains, or delivery trucks lead to less congestion and more dependable services.

SDG 12: Responsible Consumption and Production: Predictive maintenance promotes resource efficiency. Instead of replacing parts on a fixed, often wasteful schedule, C.O.R.E. enables a "fix-on-demand" model. This extends the useful life of components, reduces the consumption of spare parts, and minimizes waste associated with premature replacements.