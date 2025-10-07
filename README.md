## Project Background and Prior Work

This project (GLVision) is an improved and stable version of my earlier project OpenCV-Edge-Detector: [OpenCV-Edge-Detector](https://github.com/Nachiket1234/OpenCV-Edge-Detector) However, that **older project had numerous errors and didn't function** reliably—issues included:

- Failed live camera rendering from texture size mismatches.
- Problems with grayscale shader application not updating in real-time.
- Potential image corruption due to unhandled YUV padding in conversions.

Those bugs led me to **refactor and create this new repo for a more stable** and performant version that meets the requirements.
# GLVision: Real-Time Camera Grayscale Filter

GLVision is a high-performance Android application that demonstrates real-time video processing. The app captures the live camera feed and provides a toggle to apply a grayscale filter, processed efficiently using native C++ with the Android NDK and OpenCV. It also includes an FPS counter to monitor performance.

## GLVision Demo

<p align="center">
  <img src="https://raw.githubusercontent.com/Nachiket1234/GLVision/main/assets/glvision-demo.gif" alt="GLVision Demo" width="300"/>
</p>




## ✨ Features

  * **Live Camera Feed**: Displays a raw, real-time feed from the device's back camera.
  * **Real-Time Grayscale Filter**: Instantly apply or remove a grayscale effect on the live video stream.
  * **High-Performance Native Processing**: Image processing is offloaded to native C++ via JNI for maximum performance and minimal UI lag.
  * **OpenCV Integration**: Utilizes the power of OpenCV for efficient image manipulation on the native side.
  * **Live FPS Counter**: A real-time frames-per-second (FPS) counter to measure the performance of the video processing pipeline.
  * **Modern Android Stack**: Built with Android Jetpack components including CameraX for a robust and lifecycle-aware camera implementation.

-----

## 🛠️ Technology Stack & Libraries

  * **Platform**: Android
  * **Language**: Java & C++
  * **Core Libraries**:
      * **Android Jetpack**:
          * **CameraX**: For simplified and robust camera operations.
          * **AppCompat & Material Design**: For UI components and modern theming.
          * **Lifecycle**: For building lifecycle-aware components.
  * **Native Development**:
      * **Android NDK**: To compile C++ code for the Android platform.
      * **JNI (Java Native Interface)**: To bridge the gap between Java and C++ code.
      * **OpenCV for Android**: The core library used for image processing in the native layer.
  * **Build System**: Gradle & CMake

-----

## 🏛️ Architecture Overview

The application follows a simple but effective architecture to achieve real-time processing:

1.  **Camera Setup (Java)**

      * `MainActivity.java` uses the **CameraX** library to set up the camera.
      * Two use cases are bound to the camera's lifecycle:
        1.  **Preview**: Streams the camera output directly to a `PreviewView` for the "Raw Feed" mode.
        2.  **ImageAnalysis**: Provides a stream of camera frames as `ImageProxy` objects on a background thread. This is the source for our processing.

2.  **UI & State Management (Java)**

      * The UI consists of a `PreviewView` (for the raw feed) and an `ImageView` (for the processed grayscale feed).
      * A `Button` toggles the `applyFilter` boolean flag.
      * Based on this flag, the app shows either the `PreviewView` or the `ImageView`, providing a seamless switch between modes.

3.  **Frame Processing Pipeline (Java -\> C++)**

      * The `ImageAnalysis` analyzer receives frames in `RGBA_8888` format.
      * When the grayscale filter is enabled, the raw `byte[]` data from the image buffer is passed to a native C++ function (`processFrame`) via JNI.
      * `processFrame(byte[] frameData, ...)`: This is the native C++ function where the heavy lifting occurs. It wraps the Java byte array into an OpenCV `Mat` object, converts it to grayscale, and then writes the processed data back into the original byte array.
      * Back in Java, the modified byte array is used to create a `Bitmap`.
      * This `Bitmap` is then rotated to match the correct screen orientation and displayed in the `ImageView`.

4.  **Performance Monitoring**

      * A simple FPS counter calculates and displays the number of frames processed per second, updating the UI every second. This helps visualize the performance impact of the native processing.

-----

## 🚀 Setup and Build Instructions

### Prerequisites

  * Android Studio (latest stable version recommended)
  * Android NDK and CMake installed via the Android Studio SDK Manager.
      * Go to **Tools -\> SDK Manager -\> SDK Tools**.
      * Check **NDK (Side by side)** and **CMake**.
      * Click "Apply" to install them.

### Build Steps

1.  **Clone the repository:**
    ```bash
    git clone https://github.com/Nachiket1234/GLVision.git
    ```
2.  **Open in Android Studio:**
      * Open Android Studio.
      * Select **File -\> Open** and navigate to the cloned project directory.
3.  **Sync Gradle:**
      * Android Studio will automatically start a Gradle sync. This will download all the required dependencies, including CameraX and Material Components.
4.  **Build the Project:**
      * Once the sync is complete, build the project by clicking **Build -\> Make Project**. This will compile both the Java code and the native C++ code using CMake.
5.  **Run the App:**
      * Connect an Android device or start an emulator.
      * Click the **Run 'app'** button (the green play icon) in the toolbar. The app will be installed and launched on your device.

-----

## 📁 Project Structure

```
GLVision/
├── app/
│   ├── src/
│   │   ├── main/
│   │   │   ├── cpp/
│   │   │   │   ├── CMakeLists.txt      # CMake build script for native code
│   │   │   │   └── native-lib.cpp      # C++ source for JNI functions & OpenCV logic
│   │   │   ├── java/com/example/GLVision/
│   │   │   │   └── MainActivity.java   # Main application activity
│   │   │   ├── res/
│   │   │   │   └── layout/
│   │   │   │       └── activity_main.xml # UI layout for the main activity
│   │   │   └── AndroidManifest.xml   # App manifest file
│   └── build.gradle                  # App-level Gradle build script
└── build.gradle                      # Project-level Gradle build script
```

-----
