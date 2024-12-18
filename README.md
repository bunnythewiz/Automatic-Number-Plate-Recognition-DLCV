# Automatic Number Plate Recognition

## Overview
This project implements an **Automatic Number Plate Recognition (ANPR)** system using state-of-the-art deep learning models. The system detects vehicles and extracts license plates from video footage, providing smooth and accurate results.

---

## **Data**
- **License Plate Detector:**
  - Trained on the dataset: [License Plate Recognition Dataset](https://universe.roboflow.com/roboflow-universe-projects/license-plate-recognition-rxg4e/dataset/4).
  - **Sample video for testing:**
    - [Download here](https://drive.google.com/file/d/1_UtYQmn-sQqtBM49zKbEDO2SAbejq0of/view?usp=sharing).

---

## **Model**

### Vehicle Detector
- **Model:** YOLOv8 pre-trained model (YOLOv8n).
- **Training Dataset:** COCO dataset.
- **Download the model:** [YOLOv8n Pre-trained Model](https://huggingface.co/Ultralytics/YOLOv8/blob/main/yolov8n.pt).
- **Focus Classes:** Detection of vehicles with number plates.

### License Plate Detector
- **Model Download:** [License Plate Detector Model](https://drive.google.com/file/d/1LAGCUu4qA9bdovxBlCwzZBgdhvhLQYSw/view?usp=sharing).
- Below is an example of the training progress and final evaluation of the license plate detection model:

  ![Training Progress](https://prod-files-secure.s3.us-west-2.amazonaws.com/54ae63eb-1ab8-4263-900b-c85c76ed9354/3b305ea4-1901-4dc8-bdf4-88d99f3e36e8/image.png)

---

## **Dependencies**
- **SORT:** A simple online and realtime tracking algorithm for 2D multiple object tracking in video sequences. Clone the repository using the following command:
  ```bash
  git clone https://github.com/abewley/sort
  ```

---

## **Project Setup**

1. **Create and Activate Environment:**
    ```bash
    conda create --prefix ./env python==3.10 -y
    conda activate ./env
    ```

2. **Install Dependencies:**
    ```bash
    pip install -r requirements.txt
    ```

3. **Run the Pipeline:**
    - **Step 1:** Process the video file to generate a `test.csv` file:
      ```bash
      python main.py
      ```

    - **Step 2:** Interpolate values for missing frames and smooth output:
      ```bash
      python add_missing_data.py
      ```

    - **Step 3:** Visualize the results by processing the interpolated CSV file:
      ```bash
      python visualize.py
      ```

---

## **Features**
- Real-time vehicle detection.
- Accurate license plate recognition.
- Smooth interpolation for missing frame values.
- Visualized results for better analysis.

---

## **Acknowledgments**
- **Models:** YOLOv8 by Ultralytics.
- **Datasets:** Roboflow License Plate Dataset.
- **Tracking Algorithm:** SORT by Abe W.

---

### License
This project is licensed under the MIT License. See the LICENSE file for details.
