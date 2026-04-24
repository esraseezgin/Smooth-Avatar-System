# Smooth Avatar System (MediaPipe & OpenCV)

A real-time 2D digital avatar system developed using **MediaPipe** and **OpenCV**. This project tracks body pose and hand landmarks simultaneously, mapping them onto a stylized digital canvas.

## Key Features

* **Hybrid Tracking:** Integrates MediaPipe **Pose** and **Hands** APIs to track full-body skeletal structures and 21 individual hand joints in real-time.
* **Custom Smoothing Algorithm:** Implements an alpha-blending logic to eliminate sensor jitter. This ensures the avatar movements are fluid and stable compared to raw tracking data.
* **Minimalist Visualization:** Renders a clean, "puppet-like" skeletal view on a dark canvas, separating the human feed from the digital representation.
* **Side-by-Side Comparison:** Uses horizontal concatenation to show the live camera feed and the generated avatar simultaneously.

## Technical Specifications

This project was developed and tested using:
* **Python:** 3.10  
* **Environment:** Windows (optimized with `cv2.CAP_DSHOW`)  
* **Main Libraries:** OpenCV-Python, MediaPipe  

## Installation & Usage

1. **Clone the repository:**
    ```bash
    git clone https://github.com/yourusername/smooth-avatar-system.git
    cd smooth-avatar-system
    ```

2. **Install dependencies:**
    ```bash
    pip install -r requirements.txt
    ```

3. **Run the application:**
    ```bash
    python main.py
    ```

## Controls

* **Q Key:** Press `q` to safely release the camera and exit the application.  
* **Smoothing Adjustment:** You can modify the `alpha` parameter in the `smooth()` function to change the balance between responsiveness and smoothness.

## About the Project

Developed as part of an exploration into Computer Vision and Human-Computer Interaction (HCI). The goal was to create a stable tracking system that can serve as a foundation for gesture-controlled applications or virtual character puppetry.
