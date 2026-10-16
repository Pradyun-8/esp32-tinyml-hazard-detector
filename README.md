# esp32-tinyml-hazard-detector

A small TinyML project built to practice the full machine learning pipeline on real hardware. An ESP32 reads a gas sensor and an ultrasonic distance sensor, classifies the situation using a decision tree trained in Google Colab, and shows the live readings and the result on an OLED display.

## Hardware

- ESP32 development board
- MQ-2 gas sensor module
- HC-SR04 ultrasonic distance sensor
- 0.96" I2C OLED display (SSD1306)
- Breadboard, jumper wires and resistors

## What it detects

The model classifies each reading pair (gas, distance) into 4 classes:

| Gas reading | Distance | Class |
|---|---|---|
| 400 or below | 20 cm or more | CLEAR |
| 400 or below | below 20 cm | OBSTACLE |
| above 400 | 20 cm or more | GAS |
| above 400 | below 20 cm | GAS+OBSTACLE (most severe) |

The OLED shows the raw gas value, the distance in cm, the predicted class, and a large alert banner.

## How it works

1. Live readings are collected from both sensors.
2. The readings are labeled using the limits above.
3. A decision tree (scikit-learn, max depth 3) is trained in Google Colab.
4. The trained tree is exported as plain C code (`classify(gas, dist)`).
5. The function runs on the ESP32, so classification happens on the device with no extra libraries.
6. The OLED displays the readings and the result.

## Files

- `tinyml.ipynb`: Colab notebook with data preparation, training, and C code export.

## Notes and limitations

- The labels come from simple thresholds, so the model's accuracy is very high by construction. This is a learning project that demonstrates the TinyML pipeline, not a claim that ML is necessary for this task.
- The real dataset contained only 2 GAS-only samples (gas above 400 with nothing nearby). 50 synthetic GAS-only samples were added in the notebook to balance the classes.
- Ultrasonic readings that are negative or above 200 cm are treated as "nothing nearby".

## Future improvements

- Add a buzzer or LED for alert states.
- Detect rising gas trends over time instead of single readings.
- Collect a larger, more balanced real dataset and evaluate on held-out data.
- Add more sensors.
