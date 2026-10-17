# 📚 MATLAB Image Processing Notes & Learning Log

> **Author:** Đỗ Đức Thọ  
> **Repository:** Continuous learning log, core theories, MATLAB code snippets, and output visualizations for Image Processing.

---

### 1. Basic Image Reading & Meta-data Extraction
* **Theory:** Images are stored as 2D/3D numerical arrays. Meta-data (EXIF) stores camera properties, timestamps, and spatial resolution.
* **Key Takeaway:** The `imfinfo` function requires the file name passed as a `char` array (using single quotes `' '`) in standard MATLAB versions.

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



