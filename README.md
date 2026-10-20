# MATLAB Image Processing Notes & Learning Log

> **Author:** Đỗ Đức Thọ  
> **Repository:** Continuous learning log, core theories, MATLAB code snippets, and output visualizations for Image Processing.



### 1. Read the images in folder and display

```
% Read and display image
img = imread("hagiang.jpg")
imshow(img) 
```
<img width="1063" height="802" alt="{FFB4E3AA-FAA5-43AC-9431-E8112C4DB4D4}" src="https://github.com/user-attachments/assets/31150b1a-17e7-473d-a084-e8a23aec464e" />

### 2. Read Meta-Data using the `imfinfo` function 
Meta-Data is all of imformation of an image
Differential with imshow demonstrate the size and intensity, Meta-Data show timestamp, device setting, bytes, type of image (PNG, JPG, ...)
```
img = imread("hagiang.jpg");
metadata = imfinfo("hagiang.jpg")
```
<img width="885" height="441" alt="{A25CE876-4BE1-47FB-B567-EE1F86EC7B07}" src="https://github.com/user-attachments/assets/b05f9043-a2d3-4094-b8f0-cea22dbd6790" />

### 3. We use the dot nation (.) to extract only the specific information required for our algorithm or analysis
For example the FileModDate 
```
img = imread("hagiang.jpg");
metadata = imfinfo("hagiang.jpg");
imgDate = metadata.FileModDate
```
<img width="1332" height="122" alt="{4D38D0FA-7899-4952-AAA5-452CEAB089CE}" src="https://github.com/user-attachments/assets/3563c965-6793-4da4-b00c-40461afad04e" />

### 4. Differentiation between `metadata.DateTime` (or `metadata.FileModDate`) vs. `datetime()`
1. **`metadata.DateTime` / `metadata.FileModDate` (Metadata Fields):**
   * These are specific field names inside the `metadata` struct returned by `imfinfo()`[cite: 4].
   * **`DateTime`**: An optional field defined by EXIF standards representing when the photo was originally captured[cite: 3].
   * **`FileModDate`**: A standard system field representing the file's last modification timestamp on the disk[cite: 4].
   * Both hold raw text data (stored as a `char` array or `string`)[cite: 4].

2. **`datetime()` (MATLAB Data Type / Function):**
   * A built-in MATLAB function/constructor used to parse raw string representations into a native `datetime` data type.
   * Converting text into a `datetime` object enables time-series operations, such as calculating duration between timestamps, sorting by time, or filtering by dates.

3. **Role of `InputFormat`:**
   * Acts as a format specification template that informs MATLAB how to parse the incoming text string correctly:
     * EXIF `DateTime` format: `'yyyy:MM:dd HH:mm:ss'`
     * System `FileModDate` format: `'dd-MMM-yyyy HH:mm:ss'`[cite: 4]
   * The pattern, delimiters, and spacing in `InputFormat` must strictly match the structure of the source string.
```
img = imread('hagiang.jpg');
metadata = imfinfo('hagiang.jpg');
imgDate = metadata.FileModDate;

% Sửa InputFormat thành 'dd-MMM-yyyy HH:mm:ss'
imgDate2 = datetime(imgDate, 'InputFormat', 'dd-MMM-yyyy HH:mm:ss')
```
<img width="291" height="136" alt="{0046F9F3-4023-4761-BECD-BB3CBA747A9C}" src="https://github.com/user-attachments/assets/2a402092-eb34-4d4e-a453-ed3d9dd04b39" />

### 5. Visualizing High Dynamic Range Images (16-bit uint16 Data)
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


### 6. Find the minimum and maximum pixel values ​​in the entire MRI image
```
minPixel = min(brainMRI(:))
maxPixel = max(brainMRI(:))
```
<img width="793" height="294" alt="{3835D7AC-FAF3-46B7-961E-73A88DC636DE}" src="https://github.com/user-attachments/assets/bb4c8b0b-2b44-478d-826c-b2ac5890d5c6" />

### 7. To know the class of image
```
% To know the class of image
whos brainMRI
```
<img width="757" height="101" alt="{F60A5C58-B8B4-4A26-870B-F79ED37CDB6A}" src="https://github.com/user-attachments/assets/b0b424e2-c68d-40c7-accd-da2f29ba8946" />

### 8. Image Averaging (Multi-frame Noise Reduction)
Image Averaging is a spatial domain enhancement technique used to reduce random camera sensor noise in static scenes. By taking multiple photos of the same stationary scene and calculating the mean intensity for each pixel across all frames, the random noise naturally cancels itself out while the actual structural details remain constant. This process significantly improves the Signal-to-Noise Ratio (SNR), resulting in a cleaner, sharper, and higher-quality image.

1. First case `uint8` Summing raw pixel values directly causes integer overflow saturation. When the cumulative pixel value exceeds 255, it gets capped at 255 before the division takes place, leading to severely blown-out white areas (clipping artifacts).
<img width="1480" height="372" alt="{F38C6959-5349-4DF5-81A8-5E396BC59753}" src="https://github.com/user-attachments/assets/8eb28744-a269-47bc-b8b3-8801eba7e6c8" />

```
img1 = imread("table1.jpg");
img2 = imread("table2.jpg");
img3 = imread("table3.jpg");
img4 = imread("table4.jpg");
img5 = imread("table5.jpg");
avgim = (img1 + img2 + img3 + img4 + img5)/5;
imshow(avgim)
title("Average Image")
```
<img width="1003" height="754" alt="{D9CA9FD9-933A-4B33-9667-6EF7F8A82383}" src="https://github.com/user-attachments/assets/1372118b-165c-4e76-9a88-5fc4550a3fbc" />

2. Converting images to floating-point representation scales pixel values to a range between 0 and 1. This prevents value capping during addition, ensuring accurate floating-point average calculation across all image
```
img1 = imread("table1.jpg");
img2 = imread("table2.jpg");
img3 = imread("table3.jpg");
img4 = imread("table4.jpg");
img5 = imread("table5.jpg");
avgim = (im2double(img1) + im2double(img2) + im2double(img3) + im2double(img4) + im2double(img5))/5;
imshow(avgim)
title("Average Image")
```
<img width="932" height="740" alt="{64E838DE-086B-4AA1-8C62-368DED1BA04F}" src="https://github.com/user-attachments/assets/1ff2b992-caae-4361-9d96-4addc6612339" />

3. Compare 2 immage before and after by using `montage` function. *note: you need to install `Image Processing Toolbox` on Add-On in MATLAB
```
img1 = imread("table1.jpg");
img2 = imread("table2.jpg");
img3 = imread("table3.jpg");
img4 = imread("table4.jpg");
img5 = imread("table5.jpg");
avgim = (im2double(img1) + im2double(img2) + im2double(img3) + im2double(img4) + im2double(img5))/5;

montage({img1, avgim})
title("some unfocus here because my hand unstable but the image show the true color and reduce noise")
```
<img width="928" height="391" alt="{FE9FCD9F-2795-4D33-8F49-0CAC2FE26344}" src="https://github.com/user-attachments/assets/24e150e0-43e9-4b9f-81e3-22b9bdbdb43e" />

### 8. Data Type Conversion & Grayscale Processing
Through the previous exercise, we learned how to perform conversions between fundamental data types (such as uint8 and double). In image processing, data type conversion is a standard workflow applied to images to enable precise mathematical operations and prevent computational issues like integer overflow.
To further optimize processing pipelines, images can also be converted to grayscale when color information is not required for the task.
```
img1 = imread("table1.jpg");
img1gray = im2gray(img1);
imshow(img1gray)
```
<img width="391" height="96" alt="{9DCA6391-2F92-4988-9D1F-00737F0CDACB}" src="https://github.com/user-attachments/assets/e490474b-3388-4363-9e48-23063e2e6497" />
<img width="984" height="695" alt="{80FD448A-6B3B-4B32-AF0C-E44E98264710}" src="https://github.com/user-attachments/assets/68b20961-af9f-4597-8731-6ee30f3e325f" />




