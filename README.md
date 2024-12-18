# Automatic Number Plate Recognition

## Overview
This project showcases an **Automatic Number Plate Recognition (ANPR)** system that leverages Object Detection, Object Tracking, and Optical Character Recognition to accurately identify vehicles and extract license plate information from video footage, delivering seamless and precise results.

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
- We will only focus on detecting the classes with number plates:
  
  ![Screenshot 2024-12-18 151743](https://github.com/user-attachments/assets/aa3a860d-43ed-43af-ba95-e7b8689a4e55)


### License Plate Detector
![image](https://github.com/user-attachments/assets/2686f62b-57ae-486d-ba13-918a9c26f748)
- **Model Download:** [License Plate Detector Model](https://drive.google.com/file/d/1LAGCUu4qA9bdovxBlCwzZBgdhvhLQYSw/view?usp=sharing).
- Below is the training progress and final evaluation of the license plate detection model:

  ![Model Training and Evaluation](https://github.com/user-attachments/assets/288ccb65-ece4-47a8-a0e6-68880b690d5b)


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
# Addressing Ambiguities in CSV Data and OCR Results

## Issues Identified
![image](https://github.com/user-attachments/assets/cb386e04-8ee5-4c08-a90f-3e6ff0b5b005)


### 1. Car ID Inconsistencies
Bounding boxes and license plate information are inconsistently detected across video frames, leading to intermittent appearance and disappearance of vehicles during playback, making tracking more complex.

### 2. OCR Variability
The OCR process can produce multiple inconsistent readings of the same license plate due to:
- Extra spaces
- Special characters
- Misread letters

These inconsistencies result in unreliable license plate recognition.

## Proposed Solutions

### 1. Linear Interpolation for Missing Frames
**Problem:** Inconsistent car ID detection across frames.  
**Approach:** Apply linear interpolation to fill gaps in bounding box detections.  
**Outcome:** Ensures a continuous presence of bounding boxes for each car across its lifespan in the video.

### 2. Selecting the Best OCR Result
**Problem:** Variability in OCR results for license plates.  
**Approach:** Choose the license plate reading with the highest OCR confidence score across all frames.  
**Outcome:** Replaces inconsistent OCR results with the most reliable reading, ensuring consistent and accurate license plate numbers.

## Benefits
- Eliminates flickering of car detections during video playback, ensuring smoother tracking.
- Improves the accuracy and reliability of license plate recognition.
- Maintains consistent tracking of car IDs, even with partial or imperfect detections in some frames.



