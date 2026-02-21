🚀 DeepStream Human ROI Interaction Analytics
Pose-Based Multi-ROI Grasping Detection System

This project is a real-time human interaction analytics system built using the NVIDIA DeepStream SDK.

It detects people in a video stream, extracts pose keypoints, monitors wrist movement inside defined Regions of Interest (ROIs), and logs interaction analytics in structured JSON format.

The system is designed for practical deployment scenarios like retail monitoring, warehouse analytics, and smart interaction tracking.



🔍 What This Project Does

The pipeline performs the following in real time:

Detects persons using YOLO (via nvinfer)

Tracks each person using NvDCF tracker

Extracts 17 COCO pose keypoints

Monitors left and right wrist positions

Detects ROI-based grasping interactions

Maintains per-ROI state tracking

Logs structured per-frame JSON output

Displays live visualization with OSD overlays

🏗️ Pipeline Architecture
filesrc
   ↓
qtdemux
   ↓
h264parse
   ↓
nvv4l2decoder
   ↓
nvstreammux
   ↓
nvinfer (YOLO + Pose)
   ↓
nvtracker (NvDCF)
   ↓
nvvideoconvert
   ↓
nvdsosd
   ↓
nveglglessink
✋ ROI Grasping Logic

This system monitors:

Left Wrist → COCO Keypoint Index 9

Right Wrist → COCO Keypoint Index 10

Interaction States

GRASPING → Wrist is inside ROI

GRASPED → Wrist exited ROI after interaction

Configuration

Keypoint confidence threshold: 0.5

Debounce frames: 1

Multiple ROIs supported independently

Each ROI maintains its own state machine.

📂 Configuration (JSON)

Example configuration file:

{
  "input_video": "input.mp4",
  "log_file": "frame_log.json",
  "yolo_config": "config_infer_primary.txt",
  "roi": {
    "x": 500,
    "y": 300,
    "width": 300,
    "height": 200,
    "name": "Table"
  },
  "roi2": {
    "x": 1000,
    "y": 300,
    "width": 300,
    "height": 200
  }
}
📊 JSON Logging Format

Each frame produces structured metadata like:

{
  "frame": 125,
  "persons": [
    {
      "person_id": 2,
      "position": { "x": 820, "y": 410 },
      "keypoints": [...]
    }
  ]
}

This makes the system easy to integrate with:

Dashboards

Analytics engines

Cloud pipelines

Databases

🎨 Visualization
Bounding Box Colors
State	Color
Default	Red
Grasping	Green
Grasped	Yellow
ROI Behavior

Light Gray → No interaction

Green → Active interaction

HUD displays current frame information in real time.

🧠 Tracker Configuration

Tracker used: NvDCF (High Performance Mode)

Example tracker config:

[tracker]
tracker-width=640
tracker-height=384
gpu-id=0
ll-lib-file=/opt/nvidia/deepstream/deepstream/lib/libnvds_nvmultiobjecttracker.so
ll-config-file=config_tracker_NvDCF_perf.yml
📈 Analytics Collected

For each tracked person:

Global entry time

ROI entry time

ROI exit time

ROI interaction duration

🛠️ Build Instructions
Requirements

NVIDIA DeepStream SDK

CUDA

GStreamer

json-glib

NvDCF tracker

Compile
g++ main.cpp roi_tracker.cpp \
    -o human_roi_analytics \
    `pkg-config --cflags --libs gstreamer-1.0 json-glib-1.0` \
    -I/opt/nvidia/deepstream/deepstream/sources/includes \
    -L/opt/nvidia/deepstream/deepstream/lib \
    -lnvdsgst_meta -lnvds_meta -lnvds_infer
Run
./human_roi_analytics config.json
⚙️ Performance Notes

Streammux Resolution: 1920×1080

Batched Push Timeout: 40000 µs

Confidence filtering applied

Frame-level debouncing for stability

Optimized NvDCF tracker configuration

🧪 Real-World Use Cases

Retail shelf interaction monitoring

Warehouse human-object interaction tracking

Industrial safety monitoring

Gesture-based automation systems

Smart surveillance analytics

👨‍💻 About This Project

This project demonstrates how pose estimation can be combined with ROI-based logic inside a DeepStream pipeline to build meaningful real-time interaction analytics.

It focuses on clean tracking logic, modular ROI handling, structured logging, and deployable performance.
