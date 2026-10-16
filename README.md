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




