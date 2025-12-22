# Drone Detection & Trajectory Analysis Pipeline

## Project Overview
This project implements a complete pipeline for **Drone Detection, Tracking, and Trajectory Analysis**. It starts from model selection and fine-tuning to real-time behavioral analysis of detected objects. The goal is to identify drones in video feeds and classify their behavior as **Normal, Suspect, or Hostile** based on their movement patterns.

---

## 🏗 Project Architecture

The project is organized into three main sequential steps:

### 1️⃣ Step 1: Detection & Fine-tuning
- **Objective**: Select and optimize the best detection model for multi-range drone identification.
- **Model Evolution & Fine-tuning History**:
  - **Initial Model (`runs/`)**: YOLOv8 trained on a dataset of close-up drone images (available in the `datasets/` folder).
  - **Phase 1 Fine-tuning (1st attempt) (`runs_finetunev1/`)**: 
    - Goal: Detect drones at a distance. 
    - Source: [Anti-UAV (Anti-UAV300)](https://github.com/ZhaoJ9014/Anti-UAV) dataset.
    - Processing: Footage converted to unique single frames to prevent overfitting.
    - Issue: The large imbalance in dataset size caused the model to lose its ability to detect close-up drones.
  - **Final Model  finetuning 2nd and last attempt ( which we used after)(`runs_finetune/`)**: 
    - Strategy: Combined dataset with a balanced ratio (~75% far-away and 25% close-up shots).
    - Result: Highly robust model that excels in both close-range and long-distance detection.

### 2️⃣ Step 2: Multi-Object Tracking (ByteTrack)
- **Objective**: Assign unique IDs to detections and track them across video frames.
- **Tech Stack**: YOLOv8 + ByteTrack.
- **Key Features**:
  - Real-time tracking of drones.
  - Generation of trajectory data exported to `tracking_data.csv`.
  - Column headers: `track_id`, `frame_id`, `x_center`, `y_center`.

### 3️⃣ Step 3: Trajectory Analysis & Behavioral Assessment
- **Objective**: Analyze the movement of tracked drones to detect threats.
- **Key Modules**:
  - **Kalman Filter**: Smooths noisy tracking data for stable trajectory prediction.
  - **Motion Feature Extractor**: Calculates velocity, acceleration, and direction changes.
  - **Trajectory Predictor**: Uses Polynomial Regression to forecast future positions.
  - **Behavioral Analyzer**: A rule-based system that classifies drones:
    - 🟢 **NORMAL**: Consistent speed and direction.
    - 🟠 **SUSPECT**: Sudden turns or loitering near restricted zones.
    - 🔴 **HOSTILE**: High acceleration, erratic patterns, or restricted zone violation.
  - **Visualizer**: Generates an annotated video with alerts and predicted paths.

---

## 📂 Folder Structure
```text
drone_detection_project/
├── drone_videos/           # Source video files
├── step1_detection/        # Training and model comparison scripts
├── step2_tracking/         # ByteTrack implementation and CSV output
├── step3_trajectory_analysis/ # Behavioral analysis and visualization
└── datasets/               # Training/Validation data
```

---

## 🚀 Getting Started

### Installation
Ensure you have Python 3.10+ installed. Install the necessary dependencies:

```bash
pip install ultralytics opencv-python lapx pandas scikit-learn matplotlib numpy
```

### Usage Guide

1. **Step 2: Tracking**
   Open `step2_tracking/bytetrack.ipynb`. Update the `MODEL_PATH` and `VIDEO_INPUT` variables, then run all cells. This will generate `tracking_data.csv`.

2. **Step 3: Analysis**
   Open `step3_trajectory_analysis/Trajectory_Analysis_Pipeline.ipynb`. Run all cells. The pipeline will read the CSV and video to produce an annotated output video (`output_notebook.mp4`) showing behavioral alerts.

---

## 📈 Key Technical Features
- **Kalman Smoothing**: Reduces "jitter" in bounding box coordinates.
- **Polynomial Prediction**: Anticipates where a drone will be in the next 15-30 frames.
- **Restricted Zone Monitoring**: Automatic alerts when a drone enters a user-defined polygon area.
- **Threat Scoring**: Dynamic threat levels based on a combination of speed, direction, and consistency.

---
*Created as part of the AI & TI Project.*
