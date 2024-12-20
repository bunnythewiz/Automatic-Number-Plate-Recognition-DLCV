# Automatic Number Plate Recognition

## Demo

https://github.com/Muhammad-Zeerak-Khan/Automatic-License-Plate-Recognition-using-YOLOv8/assets/79400407/1af57131-3ada-470a-b798-95fff00254e6

## Overview
This project showcases an **Automatic Number Plate Recognition (ANPR)** system that leverages Object Detection, Object Tracking, and Optical Character Recognition to accurately identify vehicles and extract license plate information from video footage, delivering seamless and precise results.

- A detailed report is attached as `ANPR_Report`.
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
The OCR process can produce multiple inconsistent readings of the same license plate due to Extra spaces, Special characters or Misread letters.

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

---

# Containerize using Docker
## 1. Clone the Repository
Create a directory for your project
```bash
mkdir my-project

cd my-project
```

Clone the repository
```bash
git clone https://github.com/bunnythewiz/Automatic-Number-Plate-Recognition-DLCV.git

cd Automatic-Number-Plate-Recognition-DLCV
```

## 2. Create Dockerfile
Create a new file named Dockerfile in your project root:

```bash
# Build stage
FROM python:3.10.12-slim AS builder
WORKDIR /app

# Copy requirements
COPY requirements.txt . 

# Install build dependencies and Python packages
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential gcc g++ libgl1-mesa-glx libglib2.0-0 \
    && pip install --no-cache-dir --prefer-binary -r requirements.txt \
    && rm -rf /var/lib/apt/lists/*

# Runtime stage
FROM python:3.10.12-slim
WORKDIR /app

# Environment variables
ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

# Install runtime dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
    libgl1-mesa-glx libglib2.0-0 \
    && rm -rf /var/lib/apt/lists/*

# Copy installed packages from builder
COPY --from=builder /usr/local/lib/python3.10/site-packages/ /usr/local/lib/python3.10/site-packages/

# Copy application files and models
COPY main.py util.py add_missing_data.py visualize.py ./ 
COPY ./Yolo_Models ./
COPY ./sample.mp4 ./

# Command to run the app
CMD ["python", "main.py"]
```

## 3. Build image

```bash
docker build -t anpr:latest .
```

![Screenshot 2024-12-20 145137](https://github.com/user-attachments/assets/55491266-a6fd-4c08-99e8-0488bfd4cd9b)


## 4. Run Docker Container

```bash
docker run -d --name anpr-run anpr:latest
```


---

# AWS Deployment Pipeline

![image](https://github.com/user-attachments/assets/c51641d2-5d14-474f-be55-32b85b4275a0)

## **Setting Up Producer**

1. **Create a Video Stream**  
   - Go to **Kinesis Video Streams** and create a video stream.

2. **Launch EC2 Instance**  
   - Navigate to EC2 and launch a t2.small instance with Ubuntu as the operating system.
   - Use a key pair to securely connect to your instance.
   - SSH into the EC2 instance using the following command:
   ```bash
   ssh -i /path/to/your/private-key.pem ubuntu@your-ec2-public-ip-address
   ```

3. **Install Dependencies and Setup Producer SDK**  
   Execute the following commands in the EC2 instance:

    ```bash
    sudo apt update

    git clone https://github.com/awslabs/amazon-kinesis-video-streams-producer-sdk-cpp.git

    mkdir -p amazon-kinesis-video-streams-producer-sdk-cpp/build

    cd amazon-kinesis-video-streams-producer-sdk-cpp/build

    sudo apt-get install libssl-dev libcurl4-openssl-dev liblog4cplus-dev libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev gstreamer1.0-plugins-base-apps gstreamer1.0-plugins-bad gstreamer1.0-plugins-good gstreamer1.0-plugins-ugly gstreamer1.0-tools

    sudo apt install cmake

    sudo apt-get install g++

    sudo apt-get install build-essential

    cmake .. -DBUILD_DEPENDENCIES=OFF -DBUILD_GSTREAMER_PLUGIN=ON

    make

    sudo make install

    cd ..

    export GST_PLUGIN_PATH=`pwd`/build

    export LD_LIBRARY_PATH=`pwd`/open-source/local/lib
    ```

4. **Download Video for Testing**

    ```bash
    cd ~
    wget https://raw.githubusercontent.com/computervisioneng/real-time-number-plate-recognition-anpr/main/sample_30fps_1440.mp4
    ```

5. **Create IAM User for Access**

   - Go to **IAM** and create a new user with **AmazonKinesisVideoStreamsFullAccess** permissions.
   - Select the IAM user you created, go to *Security credentials*, and create access keys.

6. **Stream the Video**

   - Replace stream-name, access-key, secret-key, and region-name with the appropriate values for your setup and execute the following command on the EC2 instance:

    ```bash
    gst-launch-1.0 -v filesrc location="./sample_30fps_1440.mp4" ! qtdemux name=demux ! queue ! h264parse ! video/x-h264,stream-format=avc,alignment=au ! kvssink name=sink stream-name="stream-name" access-key="access-key" secret-key="secret-key" aws-region="region-name" streaming-type=offline demux. ! queue ! aacparse ! sink.
    ```

    ![Screenshot 2024-12-18 131722](https://github.com/user-attachments/assets/3e8f4cff-e149-459d-a61b-ae8e6793925d)

## Setting up Consumer #1: Object Detection and Tracking

1. **Launch EC2 Instance**  
   - Go to **EC2** and launch a `t2.xlarge` instance with 30GB storage size and ubuntu as the operating system.
   - Use a key pair to securely connect to your instance.
   - SSH into the EC2 instance using the following command:
   ```bash
   ssh -i /path/to/your/private-key.pem ubuntu@your-ec2-public-ip-address
   ```

2. **Install Dependencies and Set Up Virtual Environment**  
   - Execute the following commands in the EC2 instance:

   ```bash
   sudo apt update
   
   sudo apt install python3-virtualenv
   
   virtualenv venv --python=python3
   
   source venv/bin/activate
   ```

3. **Clone Repositories and Install Required Packages**
    - Clone the required repositories:
    ```bash
    git clone https://github.com/computervisioneng/amazon-kinesis-video-streams-consumer-library-for-python.git
    
    cd amazon-kinesis-video-streams-consumer-library-for-python
    
    git clone https://github.com/abewley/sort.git
    ```
    
    - Install necessary Python dependencies:
    ```bash
    pip install -r requirements.txt
    
    pip install -r sort/requirements.txt
    
    pip install ultralytics
    ```
    - If you encounter any dependency issues use the consumer1_requirements.txt file, which is present in the repository:
    ```bash
    pip install -r consumer1_requirements.txt
    ```
     
    - Install system dependencies:
    ```bash
    sudo apt-get update && sudo apt-get install ffmpeg libsm6 libxext6  -y
    
    sudo apt-get install python3-tk
    ```
    - If you encounter an issue related to sort/sort.py, it may be due to the backend configuration for Matplotlib.
    - Replace 'TkAgg' with 'Agg'
4. **Set Up IAM Role for EC2**

    - Go to IAM and create an access role for the EC2 instance with the following policies:
      - AmazonKinesisVideoStreamsFullAccess
      - AmazonDynamoDBFullAccess
      - AmazonS3FullAccess
      - AmazonSQSFullAccess
    - Attach the IAM role to the EC2 instance.
5. **Download Models**

    - Transfer the both the models to your EC2 instance using the following command:
    ```bash
    scp -i /path/to/your/private-key.pem /path/to/your/model-file.pt ubuntu@<ec2-public-ip>:~/
    ```
6. **Set Up AWS Resources**

    - Go to S3 and create an S3 bucket.
    - Go to DynamoDB and create two tables (on-demand mode).
    - Go to SQS and create a FIFO queue.
    - Go to Lambda and create a new Lambda function with the files:
      - lambda_function.py
      - util.py
7. **Set Up IAM Role for Lambda**

    - Go to IAM and create an access role for the Lambda function with the following policies:
      - AmazonDynamoDBFullAccess
      - AmazonS3FullAccess
      - AmazonSQSFullAccess
      - TextractFullAccess
    - Attach the IAM role to the Lambda function.
    - Edit the Lambda function timeout to 1 minute.
    - Set asynchronous invocation Retry attempts to 0
8. **Set Up S3 Event Notification**

    - Go to the S3 bucket and create a new event notification to trigger the Lambda function.
9. **Edit Variable Names**

    - In the EC2 instance, go to amazon-kinesis-video-streams-consumer-library-for-python/kvs_consumer_library_example_object_detection_and_tracking.py and edit the region-name, stream-name, bucket-name, table-name and queue-url.
    - In the Lambda function, go to lambda_function.py and edit the region_name, TableName and QueueUrl.
10. **Run Object Detection and Tracking**

    - Execute the following commands in the EC2 instance:
    ```bash
    cd ~/amazon-kinesis-video-streams-consumer-library-for-python
    
    python kvs_consumer_library_example_object_detection_and_tracking.py
    ```
    ![Screenshot 2024-12-18 131502](https://github.com/user-attachments/assets/ff75a9b2-b1f8-43f9-afaf-0a34165cb985)

## Setting Up Consumer #2: Visualization
1. **Clone the Repository**
    - Clone the required repository:
    ```bash
    git clone https://github.com/computervisioneng/amazon-kinesis-video-streams-consumer-library-for-python.git
    ```
2. **Download Required Files and Directories**
    - Download the following files and directory into your working environment:
      - main_plot.py
      - process_queue.py
      - The directory loading_frames
3. **Set Up IAM User**
    - Go to IAM in the AWS Management Console.
    - Create a new user with the following permissions:
      - AmazonKinesisVideoStreamsFullAccess
      - AmazonDynamoDBFullAccess
      - AmazonSQSFullAccess
    - After creating the user, go to Security credentials and generate access keys.
    - purge if there are any messages in sqs queue
4. **Edit Variables in Code**
    - Open the following files and edit the necessary variables with your specific configuration:
      - main_plot.py
      - process_queue.py
      - amazon-kinesis-video-streams-consumer-library-for-python/kvs_consumer_library_example_visualization.py
5. **Create a Virtual Environment and Install Requirements**
    - Set up a Python virtual environment and install dependencies:
    ```bash
    python3 -m venv venv
    
    source venv/bin/activate
    
    pip install -r requirements.txt

    pip install pandas
    ```
    - If you encounter any dependency issues use the consumer1_requirements.txt file, which is present in the repository:
    ```bash
    pip install -r consumer2_requirements.txt
    ```
    
6. **Execute the Scripts**
    - Run the following scripts in order:
    
    - Start queue processing:
    
    ```bash
    python process_queue.py
    ```
    
    - Start the visualization script:
    ```bash
    python main_plot.py
    ```
    - Start the object detection and tracking:
    
    ```bash
    python amazon-kinesis-video-streams-consumer-library-for-python/kvs_consumer_library_python/kvs_consumer_library_example_object_detection_and_tracking.py.
    ```
