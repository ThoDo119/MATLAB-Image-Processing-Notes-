# 📚 MATLAB Image Processing Notes & Learning Log

> **Author:** Đỗ Đức Thọ  
> **Repository:** Continuous learning log, core theories, MATLAB code snippets, and output visualizations for Image Processing.

---
## 📌 Table of Contents
1. [Basic Image Reading & Meta-data Extraction](#1-basic-image-reading--meta-data-extraction)
   * [1.1 Reading & Displaying Images](#11-reading--displaying-images)
   * [1.2 Extracting Image Metadata (imfinfo)](#12-extracting-image-metadata-imfinfo)
   * [1.3 Accessing Specific Struct Fields (Dot Notation)](#13-accessing-specific-struct-fields-dot-notation)
   * [1.4 Converting EXIF Timestamp to datetime Object](#14-converting-exif-timestamp-to-datetime-object)
   * [1.5 Displaying Non-uint8 Images (16-Bit Medical Data)](#15-displaying-non-uint8-images-16-bit-medical-data)
   * [1.6 Inspecting Pixel Intensity Range (min/max)](#16-inspecting-pixel-intensity-range-minmax)
   * [1.7 Inspecting Data Types (whos)](#17-inspecting-data-types-whos)
2. [Image Arithmetic, Type Conversion, and Frame Averaging](#2-image-arithmetic-type-conversion-and-frame-averaging)
   * [2.1 Naive Frame Averaging & Integer Overflow](#21-naive-frame-averaging--integer-overflow)
   * [2.2 Correct Averaging via Floating-Point Conversion (im2double)](#22-correct-averaging-via-floating-point-conversion-im2double)

---

### 1. Basic Image Reading & Meta-data Extraction
* **Theory:** Images are stored as 2D/3D numerical arrays. Meta-data (EXIF) stores camera properties, timestamps, and spatial resolution.
* **Key Takeaway:** The `imfinfo` function requires the file name passed as a `char` array (using single quotes `' '`) in standard MATLAB versions.

<details>
<summary><b>🔍 Click to expand Code & Output</b></summary>
  
```
% Read and display image
img = imread('boston night.jpg');
imshow(img); 
```
<img width="1017" height="672" alt="{87246564-F8B7-4F0C-97A5-07266518946A}" src="https://github.com/user-attachments/assets/c302ed59-4614-42ca-b62a-e6bf9a9d98f0" />

```
% Read Meta-Data using the imfinfo function
% Differential with imshow demonstrate the size and intensity, Meta-Data show timestamp, device setting, bytes, type of image (PNG, JPG, ...)
img = imread('boston night.jpg');
metadata = imfinfo('boston night.jpg')
```
<img width="1281" height="785" alt="{99F5AA36-BEC1-4C88-BE7D-D273B6DF8F50}" src="https://github.com/user-attachments/assets/7b73af43-2a01-4936-982f-ab6ec6f5f717" />

```
% We use the dot notation (.) to extract only the specific information required for our algorithm or analysis
img = imread('boston night.jpg');
imgDate = metadata.DateTime
cameraInfo = metadata.DigitalCamera
```
<img width="756" height="91" alt="{DE0C2667-F539-4931-A909-EFD129A4E18D}" src="https://github.com/user-attachments/assets/33019392-3db8-4d6c-adde-a7d3c22f6b55" />
<img width="1048" height="711" alt="{6BC11520-FEBC-4EA4-B31C-0850BC58D942}" src="https://github.com/user-attachments/assets/c14733ba-9d38-437c-9d52-8bca11181ccc" />

```
%  metadata.DateTime vs. datetime():
%    - 'DateTime' (capital D & T) is a specific field name inside the struct,
%      defined by EXIF standards. MATLAB is case-sensitive.
%    - 'datetime()' (lowercase) is MATLAB's built-in function used to convert 
%      raw text into a functional, readable datetime object.
%
%  Why convert to a 'datetime' object?
%    Raw 'metadata.DateTime' is just a plain character array ('char'). Converting 
%    it into a 'datetime' object allows for time-series calculations (e.g., 
%    subtracting timestamps between two frames to measure intervals or speed).
%
% Purpose of 'InputFormat':
%    Acts as a parsing template ('yyyy:MM:dd HH:mm:ss') telling MATLAB how to 
%    interpret the raw text string correctly (Year, Month, Day, Hour, Minute, Second).
%    Note: Spacing in 'InputFormat' must match the raw string exactly.

img = imread('boston night.jpg');
metadata = imfinfo('boston night.jpg')
imgDate = metadata.DateTime
imgDate2 = datetime(imgDate, 'InputFormat', 'yyyy:MM:dd HH:mm:ss')
```
<img width="435" height="271" alt="{EC38B5E4-19CD-4B71-BDF0-662DF088C4D3}" src="https://github.com/user-attachments/assets/54edb9fa-9157-4cca-96bf-e75adb1a442e" />

```

% 1. Load sample 16-bit medical dataset
load brainMRI; 

% 2. Default display (Renders dark/black image)
% Reason: uint16 data ranges from 0 to 65535. Since actual MRI pixel values
% are far below 65535, standard imshow maps them to near-black levels.
figure;
imshow(brainMRI);
title('Default imshow (Dark Output due to uint16 Dynamic Range)');

% 3. Auto-scaling intensity display range using empty brackets []
% Explanation: Empty brackets [] force MATLAB to dynamically map the minimum 
% intensity in the image to BLACK and the maximum intensity to WHITE.
figure;
imshow(brainMRI, []);
colorbar; % Adds an intensity reference bar next to the image
title('Auto-scaled Display Range');

% 4. Manual intensity display range specification [low high]
% Explanation: Maps values <= 50 to BLACK and values >= 600 to WHITE.
% This enhances contrast for specific tissue regions of interest.
figure;
imshow(brainMRI, [50, 600]);
colorbar;
title('Manual Display Range');
```
<img width="1557" height="488" alt="{98203E80-F6E3-41AC-8BC5-B9EE3B7DE1E5}" src="https://github.com/user-attachments/assets/de379d27-a73e-4539-9c0e-98ec9c81ccbf" />

```
% Find the minimum and maximum pixel values ​​in the entire MRI image
minPixel = min(brainMRI(:))
maxPixel = max(brainMRI(:))
```
<img width="793" height="294" alt="{3835D7AC-FAF3-46B7-961E-73A88DC636DE}" src="https://github.com/user-attachments/assets/bb4c8b0b-2b44-478d-826c-b2ac5890d5c6" />

```
% To know the class of image
whos brainMRI
```
<img width="757" height="101" alt="{F60A5C58-B8B4-4A26-870B-F79ED37CDB6A}" src="https://github.com/user-attachments/assets/b0b424e2-c68d-40c7-accd-da2f29ba8946" />

## 2. Image Arithmetic, Type Conversion, and Geometric Transformations
```
% --- 1. Load sequence of Orion Nebula images ---
img1 = imread("OrionNebula1.png");
img2 = imread("OrionNebula2.png");
img3 = imread("OrionNebula3.png");
img4 = imread("OrionNebula4.png");
img5 = imread("OrionNebula5.png");

% --- 2. Incorrect Naive Averaging (Demonstrating uint8 Overflow) ---
% Direct addition saturates pixels > 255 to 255 first.
% Dividing by 5 maps max intensities down to ~51 (dim/dark output).
imgSumNaive = img1 + img2 + img3 + img4 + img5;
imgAvgNaive = imgSumNaive / 5;

% Display dark, saturated output
imshow(imgAvgNaive);
title('Naive Averaging (Integer Saturation / Dark Output)');
```
<img width="1408" height="904" alt="{9FFD5F92-13F7-4BAC-BE7B-F3E5A1D5E2FB}" src="https://github.com/user-attachments/assets/bb839c49-977a-4887-be28-050b78a7d3c2" />

```
% Correct Averaging using im2double ---
% Convert images to double-precision floating-point matrices before addition.
% Pixel accumulation is unlimited during addition, preventing integer clipping.
avgI = (im2double(img1) + im2double(img2) + im2double(img3) + im2double(img4) + im2double(img5)) / 5;

% Display normalized average output
imshow(avgI);
title('Correct Averaging using im2double (Preserved Intensity & Noise Reduction)');
```
<img width="1384" height="850" alt="{50E972E4-89BA-4BB5-810F-AF86625D9F36}" src="https://github.com/user-attachments/assets/a4e238d7-0d6f-4af6-a45b-f433e2ca177f" />


