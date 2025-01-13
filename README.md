# QR Code Decoder

This repository contains the implementation of a **QR Code Decoder** from scratch, developed as part of the **CSE483
Computer Vision Course**. The decoder uses advanced image processing techniques to read and extract information from QR
codes, including handling noise, applying error correction, and decoding the final message.

## **Table of Contents**

1. [Introduction](#introduction)
2. [Key Features](#key-features)
3. [Pipeline Overview](#pipeline-overview)
4. [Working Flow](#working-flow)
5. [Installation and Setup](#installation-and-setup)
6. [Usage Instructions](#usage-instructions)
7. [Testing and Results](#testing-and-results)
8. [Future Improvements](#future-improvements)

---

## **Introduction**

QR codes (Quick Response codes) are widely used for fast data encoding and scanning. This project implements a QR code
decoder using a series of computer vision techniques such as noise removal, warping, and format correction. The
objective is to decode both clean and noisy QR images accurately and efficiently.

---

## **Key Features**

This QR Code Decoder includes the following functionalities:

- **Color Correction:** Adjusts brightness and contrast for clearer QR code boundaries.
- **Noise Removal:** Eliminates high-frequency noise that may obscure the QR code.
- **QR Region Detection:** Detects the QR code region and aligns it to a standard format.
- **Quiet Zone Removal:** Removes the outer padding (quiet zone) of the QR code.
- **Locator Box Detection:** Identifies the position of the locator squares for orientation.
- **Error Correction:** Corrects errors within the QR code to recover valid data.
- **Mask Application:** Applies and removes masks for accurate final data extraction.
- **Numeric Data Conversion:** Converts the QR image into meaningful binary and numeric data.

---

## **Pipeline Overview**

### **1. Color Correction**

The input image undergoes histogram adjustments to improve the contrast, ensuring that QR code boundaries are more
distinct.

### **2. Noise Removal**

Periodic noise is filtered using frequency-based filtering to remove patterns that could interfere with QR detection.

### **3. QR Code Detection and Warping**

The algorithm locates the QR code region using corner and edge detection, followed by perspective transformation to
align the QR code for reading.

### **4. Preprocessing**

The detected region is binarized (converted to black and white), and the quiet zone around the QR code is removed.

### **5. Locator Box Identification**

The algorithm checks the QR corners for the presence of locator boxes (usually large black-and-white squares) to
determine the orientation.

### **6. QR Code Decoding**

The binary grid of the QR code is mapped into a data matrix representing the encoded information.

### **7. Error Correction**

Reed-Solomon error correction is applied to fix corrupted bits within the data matrix.

### **8. Data Masking**

Masks are removed to decode the underlying data, applying the final checks for format and integrity.

---

## **Working Flow**

The working flow of this project consists of multiple stages, each playing a crucial role in decoding the QR code
successfully. Below is a step-by-step detailed breakdown of each process.

---

### **Step 1: Image Input and Preprocessing**

**Description:**

- The user provides an image of a QR code, which may contain distortions, noise, or different lighting conditions.
- The image is converted to **grayscale** to simplify operations, as QR codes are essentially binary (black and white).
- A **contrast enhancement** is applied using histogram equalization to improve the visibility of the QR code,
  especially in poorly lit images.

**Advantages:**

- Simplifies further processing by reducing the image from 3 channels (RGB) to 1 (grayscale).
- Improves the chance of accurate detection by normalizing brightness and contrast across the image.
- Enhances the dynamic range for better distinction between foreground (QR code) and background.

---

### **Step 2: Noise Removal**

**Description:**

- The image may contain periodic noise, which can interfere with the QR code structure.
- A **Fourier Transform** is applied to detect and filter out periodic noise by zeroing out high-frequency components.
- Additionally, spatial domain filtering is applied for minor smoothing to remove background textures without affecting
  QR edges.

**Advantages:**

- Filters out unwanted patterns such as vertical/horizontal lines and textures from complex backgrounds.
- Preserves the critical features of the QR code, ensuring that locator boxes and data regions remain intact.

---

### **Step 3: QR Code Detection and Warping**

**Description:**

- The corners of the QR code are detected using a **corner detection algorithm** (e.g., Shi-Tomasi or Harris Corner
  Detection).
- Once the corners are detected, a **perspective transformation (homography)** is applied to align the QR code region
  into a straightened square image.

**Details of the Transformation:**

- The detected corners form a quadrilateral (distorted QR code shape).
- A **homography matrix** is computed to map this quadrilateral into a perfect square.
- This process warps the QR code to the correct orientation and alignment.

**Advantages:**

- Corrects perspective distortions caused by camera angles.
- Ensures that the QR code grid is aligned for accurate decoding.

---

### **Step 4: Binary Thresholding and Quiet Zone Removal**

**Description:**

- The warped QR code region is converted into a **binary image** (black and white) using **adaptive thresholding**.
- The "quiet zone" (a white margin around the QR code) is detected and removed to focus only on the main data area.

**Key Technical Details:**

- Adaptive thresholding adjusts the threshold locally for different regions in the image, making it robust to lighting
  variations.
- The removal of the quiet zone helps in cropping the QR code to its exact dimensions without extra padding.

**Advantages:**

- Handles uneven lighting conditions and shadows.
- Ensures that only the essential QR data grid is processed for decoding.

---

### **Step 5: Locator Box Detection**

**Description:**

- Locator boxes (three large black-and-white squares at the corners) are identified to determine the orientation of the
  QR code.
- The system checks all four corners of the QR code and determines which corner lacks a locator box (the "alignment
  pattern").
- Based on the presence of locator boxes, the QR code is oriented correctly.

**Key Details:**

- The positions of the three locator boxes are compared to infer the correct orientation of the QR code.
- The remaining corner, which may contain a smaller square, is used as a reference for scaling.

**Advantages:**

- Correctly orients the QR code, even if it is rotated by 90°, 180°, or 270°.
- Robust against mirrored and flipped images.

---

### **Step 6: Numeric Conversion and Mask Detection**

**Description:**

- The QR code is converted into a **grid of binary values** (0s and 1s) based on the detected black and white modules.
- A **mask pattern** is applied to encode data in QR codes. The mask ensures that there is no large uniform region of
  black or white, making the code easier to scan.
- The system identifies the applied mask and removes it to retrieve the raw data.

**Key Details:**

- Mask patterns are pre-defined, and the system tries all known patterns to find the one that produces valid data.
- The mask is applied using **bitwise XOR** operations on the data grid.

**Advantages:**

- Prevents decoding errors due to uniform regions.
- Ensures accurate extraction of data even if the QR code has complex patterns.

---

### **Step 7: Error Correction and Format Information Extraction**

**Description:**

- QR codes use **Reed-Solomon error correction** to detect and correct errors in the encoded data.
- The system extracts the **format information** (e.g., version, encoding type, error correction level) from special
  regions within the QR code.
- The extracted information is used to interpret the QR code correctly.

**Key Details:**

- Reed-Solomon codes can correct up to a specific number of errors based on the error correction level (L, M, Q, H).
- The system identifies and corrects bit errors in the data string to reconstruct the original message.

**Advantages:**

- Recovers valid data even if the QR code is partially damaged or contains noise.
- Supports multiple error correction levels for improved robustness.

---

### **Step 8: Data Extraction**

**Description:**

- After error correction, the final data grid is interpreted according to the QR code standard.
- The data is converted into a human-readable format (e.g., plain text, URL, or contact details).

**Key Details:**

- The data format depends on the encoding mode (numeric, alphanumeric, binary, or kanji).
- The system assembles the decoded bits into the original string format.

**Advantages:**

- Supports various types of encoded data (text, numbers, URLs).
- Provides a robust final output even for QR codes that were initially noisy or misaligned.

---

### **Summary of Working Flow**

| **Step**            | **Purpose**                                | **Key Technique**                   | **Advantage**                                    |
|---------------------|--------------------------------------------|-------------------------------------|--------------------------------------------------|
| Image Preprocessing | Improve image contrast                     | Histogram equalization              | Handles uneven lighting and enhances QR edges    |
| Noise Removal       | Remove background noise                    | Fourier Transform & low-pass filter | Removes patterns without affecting QR content    |
| QR Detection        | Detect and align QR region                 | Perspective transformation          | Corrects perspective distortions                 |
| Thresholding        | Convert to binary image                    | Adaptive thresholding               | Robust to lighting variations                    |
| Locator Detection   | Detect QR code orientation                 | Corner box detection                | Handles rotated and mirrored QR codes            |
| Mask Removal        | Extract raw data                           | Bitwise XOR                         | Prevents large uniform regions                   |
| Error Correction    | Correct errors in data                     | Reed-Solomon error correction       | Recovers data from partially corrupted QR codes  |
| Data Extraction     | Convert binary data into a readable format | Encoding type interpretation        | Supports different data types (text, URLs, etc.) |

---

## **Installation and Setup**

To set up and run the project:

### **1. Clone the Repository**

Download the project files locally:

```bash
git clone <repository-link>
cd <repository-folder>
```

### **2. Install Dependencies**

Install the necessary Python packages:

```bash
pip install -r requirements.txt
```

### **3. Run in Colab (Optional)**

To use Google Colab:

- Open the notebook in Colab by clicking the Colab badge provided.
- Make sure the necessary libraries are installed using the setup cells.

---

## **Usage Instructions**

### **Running the Decoder**

1. Load the image of the QR code you want to decode.
2. Execute the notebook cells sequentially.
3. Use the main function to decode a QR image:
   ```python
   result = decode_qr_code("path/to/your/image.jpg")
   print("Decoded QR Data:", result)
   ```
4. The output will display the decoded information or indicate any errors encountered during processing.

### **Input Requirements**

- The input should be a clear image of a QR code, but the decoder can handle moderate noise and distortions.
- Supported formats: `.jpg`, `.png`, `.bmp`.

---

## **Testing and Results**

The project includes several test cases to verify the performance of the QR decoder. These tests cover:

- **Standard QR Codes:** Clean QR images with no distortions.
- **Noisy QR Codes:** Images with added noise and reduced clarity.
- **Misaligned QR Codes:** QR images with perspective distortions.

### **Sample Test Outcomes**

- **Passed Tests:** Majority of the QR codes were successfully decoded.
- **Failed Tests:** The following test cases encountered issues:
    - **Test Case 2:** QR code failed due to minor alignment errors.
    - **Test Case 10:** Failed due to severe noise affecting QR recognition.
    - **Test Case 13:** Data corruption resulted in an unresolvable QR.
    - **Test Case 14:** Locator box misalignment led to an incorrect orientation.

---

## **Future Improvements**

- **Error Resilience:** Enhance the error correction module for handling extreme noise and distortion.
- **Auto-Orientation:** Implement an automatic orientation correction to handle flipped or mirrored QR codes.
- **Support for Advanced QR Types:** Extend functionality to handle micro QR and large-scale QR versions.
- **Real-Time Detection:** Develop a real-time version of the QR decoder using live camera feeds.

---
