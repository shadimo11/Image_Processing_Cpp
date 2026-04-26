# 🖼️ Image Processing in C++

> A full implementation of core **Digital Image Processing** operations in **C++**, supporting grayscale conversion, image flipping, brightness & contrast adjustment, blur filtering, and 90-degree rotation — with no external dependencies.

![Language](https://img.shields.io/badge/Language-C%2B%2B-blue?style=flat-square&logo=cplusplus)
![Standard](https://img.shields.io/badge/Standard-C%2B%2B11-orange?style=flat-square)
![Type](https://img.shields.io/badge/Type-Image%20Processing-blueviolet?style=flat-square)
![Format](https://img.shields.io/badge/Format-PPM%20%28P3%29-lightgrey?style=flat-square)

---

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#-features)
- [How It Works](#-how-it-works)
- [Class Overview](#-class-overview)
- [Getting Started](#-getting-started)
- [Usage Examples](#-usage-examples)
- [API Reference](#-api-reference)
- [Test Cases](#-test-cases)

---

## Overview

This project implements a **custom image processing library in pure C++** with no external dependencies. Images are represented internally as 3D matrices (`[height][width][channel]`) and are read from and written to the **PPM (P3)** format — a simple, human-readable pixel format.

It is commonly used as a foundation for understanding how image operations work at the pixel level, making it ideal for educational purposes, embedded systems, or any environment where library dependencies must be minimized.

---

## ✨ Features

- ✅ **Grayscale Conversion** — Converts RGB images using the standard luminance formula
- ✅ **Horizontal Flip** — Mirrors the image left to right
- ✅ **Vertical Flip** — Mirrors the image top to bottom
- ✅ **Brightness Adjustment** — Adds a fixed offset to all pixel values with clamping
- ✅ **Contrast Adjustment** — Scales pixel intensities around the midpoint (128)
- ✅ **Blur Filter** — Smooths the image using a 3×3 average kernel
- ✅ **90° Clockwise Rotation** — Rotates the image with swapped dimensions
- ✅ **PPM File I/O** — Load and save images in the P3 PPM format
- ✅ **No external libraries** — Pure C++ with STL only
- ✅ Handles edge cases: border pixels in blur, clamping in brightness/contrast

---

## 🔧 How It Works

Each image is stored as a 3D `vector<vector<vector<int>>>` with dimensions `[height][width][channels]`. Operations are applied either **point-wise** (per pixel) or via **convolution** with a fixed kernel:

```
Original RGB Image (4×4)              3×3 Average Blur Kernel
┌──────────────────────────────┐      ┌───────────────────┐
│ (255,0,0)   (0,255,0)        │      │  1/9  1/9  1/9   │
│ (0,0,255)   (255,255,0)      │  *   │  1/9  1/9  1/9   │  →  Blurred Image
│ (255,0,255) (0,255,255)      │      │  1/9  1/9  1/9   │
│ (128,128,0) (0,0,0)          │      └───────────────────┘
└──────────────────────────────┘
```

**Grayscale conversion** applies the ITU-R BT.601 luminance formula per pixel:

```
gray = 0.299·R + 0.587·G + 0.114·B
```

**Blur** averages each pixel's 3×3 neighborhood. Border pixels are left unchanged since no padding is applied.

**Contrast adjustment** scales each pixel around the midpoint to expand or compress the intensity range:

```
newVal = factor × (pixel − 128) + 128   →   clamped to [0, 255]
```

---

## 🏗 Class Overview

### `Image`
| Member / Method                                    | Type / Return | Description                                          |
|----------------------------------------------------|---------------|------------------------------------------------------|
| `data[height][width][channel]`                     | `vector<...>` | Internal 3D pixel storage                            |
| `Image(int w, int h, int ch = 3)`                  | Constructor   | Creates a blank image of given dimensions            |
| `getWidth()` / `getHeight()` / `getChannels()`     | `int`         | Returns image dimensions and channel count           |
| `operator()(int y, int x, int channel)`            | `int&`        | Pixel read/write access by row, column, and channel  |
| `loadPPM(const string& filename)`                  | `bool`        | Loads a P3 PPM file from disk                        |
| `savePPM(const string& filename)`                  | `bool`        | Saves the image to a P3 PPM file on disk             |
| `print()`                                          | `void`        | Prints pixel data to the console (for small images)  |

### Free Functions
| Function                              | Description                                               |
|---------------------------------------|-----------------------------------------------------------|
| `convertToGrayscale(input)`           | Converts a 3-channel image to a single-channel grayscale  |
| `flipHorizontal(input)`               | Mirrors the image along the vertical axis                 |
| `flipVertical(input)`                 | Mirrors the image along the horizontal axis               |
| `adjustBrightness(input, value)`      | Adds `value` to all pixel intensities, clamped to [0,255] |
| `adjustContrast(input, factor)`       | Scales intensities around midpoint 128 by `factor`        |
| `applyBlur(input)`                    | Applies a 3×3 uniform averaging filter                    |
| `rotate90(input)`                     | Rotates the image 90 degrees clockwise                    |

---

## 🚀 Getting Started

### Prerequisites
- C++ compiler supporting **C++11** or later (e.g. `g++`, MSVC, Clang)
- **No external libraries required** — pure C++ STL only

### Compilation

```bash
g++ -o image_processor main.cpp -std=c++11
./image_processor
```

### Output Files

After running, the following PPM files will be generated in the working directory:

| File                     | Description                        |
|--------------------------|------------------------------------|
| `test_image.ppm`         | The original 4×4 test input image  |
| `gray_image.ppm`         | Grayscale output                   |
| `flipped_horizontal.ppm` | Horizontally flipped output        |
| `flipped_vertical.ppm`   | Vertically flipped output          |
| `bright_image.ppm`       | Brightness-adjusted output (+50)   |
| `contrast_image.ppm`     | Contrast-adjusted output (×1.5)    |
| `blurred_image.ppm`      | Blurred output (3×3 average)       |
| `rotated90_image.ppm`    | 90° clockwise rotated output       |

> 💡 Use an image viewer that supports PPM format, or convert to PNG/JPG using [GIMP](https://www.gimp.org/) or ImageMagick:
> ```bash
> convert output.ppm output.png
> ```

---

## 📖 Usage Examples

```cpp
// Load an image
Image input;
input.loadPPM("test_image.ppm");

// Grayscale conversion
Image gray = convertToGrayscale(input);
gray.savePPM("gray_image.ppm");

// Flip operations
Image flippedH = flipHorizontal(input);
Image flippedV = flipVertical(input);
flippedH.savePPM("flipped_horizontal.ppm");
flippedV.savePPM("flipped_vertical.ppm");

// Brightness and contrast
Image bright   = adjustBrightness(input, 50);    // +50 to all channels
Image contrast = adjustContrast(input, 1.5f);    // 1.5x contrast boost
bright.savePPM("bright_image.ppm");
contrast.savePPM("contrast_image.ppm");

// Blur and rotation
Image blur    = applyBlur(input);
Image rotated = rotate90(input);
blur.savePPM("blurred_image.ppm");
rotated.savePPM("rotated90_image.ppm");
```

---

## 📚 API Reference

### `Image convertToGrayscale(const Image& input)`
Converts each RGB pixel to a single intensity value using the ITU-R BT.601 luminance formula:
`gray = 0.299·R + 0.587·G + 0.114·B`. Returns a new single-channel image.

**Time Complexity:** O(W × H)

### `Image flipHorizontal(const Image& input)`
Mirrors the image along the vertical axis by mapping each pixel at `(y, x)` to `(y, width−1−x)`.

**Time Complexity:** O(W × H × C)

### `Image flipVertical(const Image& input)`
Mirrors the image along the horizontal axis by mapping each pixel at `(y, x)` to `(height−1−y, x)`.

**Time Complexity:** O(W × H × C)

### `Image adjustBrightness(const Image& input, int value)`
Adds a fixed integer `value` (positive to brighten, negative to darken) to every channel of every pixel, then clamps results to `[0, 255]`.

**Time Complexity:** O(W × H × C)

### `Image adjustContrast(const Image& input, float factor)`
Scales each pixel channel around midpoint 128: `newVal = factor × (pixel − 128) + 128`, clamped to `[0, 255]`. Values above `1.0` increase contrast; values below `1.0` reduce it.

**Time Complexity:** O(W × H × C)

### `Image applyBlur(const Image& input)`
Applies a 3×3 box blur by averaging each pixel's 3×3 neighborhood. Border pixels (first/last row and column) are left as `0` due to no border padding.

**Time Complexity:** O(W × H × C × 9) ≈ O(W × H × C)

### `Image rotate90(const Image& input)`
Rotates the image 90 degrees clockwise by mapping each pixel at `(y, x)` to `(x, height−1−y)` in a new image with swapped width and height dimensions.

**Time Complexity:** O(W × H × C)

---

## 🧪 Test Cases

The `main()` function runs a full pipeline on a programmatically generated 4×4 test image covering all implemented operations:

| Test | Description                                               |
|------|-----------------------------------------------------------|
| 1    | Test image creation — 4×4 pattern with 16 named colors    |
| 2    | Grayscale conversion — single-channel luminance output    |
| 3    | Horizontal flip — pixel position mirror validation        |
| 4    | Vertical flip — pixel position mirror validation          |
| 5    | Brightness adjustment — `+50` with overflow clamping      |
| 6    | Contrast adjustment — `×1.5` with underflow clamping      |
| 7    | Blur filter — 3×3 averaging on interior pixels            |
| 8    | 90° rotation — swapped dimensions and pixel remapping     |

---

## ⚠️ Notes

- Only the **P3 (ASCII) PPM** format is supported for file I/O
- Blur border pixels are set to `0` — no border padding or pixel replication is applied
- Grayscale images are saved as **3-channel PPM** by repeating the intensity across R, G, B for viewer compatibility
- `adjustContrast` uses `float` arithmetic internally before clamping to avoid integer precision loss
- All operations return a **new image** — the original input is never modified

---

## 👥 Contributors

| Name | GitHub |
|------|--------|
| Shady Mohamed | [@shadimo11](https://github.com/shadimo11) |
| Kerolos Safwat | [@KiroSafwat](https://github.com/KiroSafwat) |
| Shahira Abdallah | [@Shahira-Abdallah](https://github.com/Shahira-Abdallah) |
| Mazen Elkashlan | [@toshiba-chef-89](https://github.com/toshiba-chef-89) |
| Youssef Asaad | [@Usf132](https://github.com/Usf132) |

## 📌 Agile & Project Management

This project is managed using **Agile methodology** on **Jira**.

Sprints are organized around function implementation phases as defined in the SRS document, with tasks distributed across team members to minimize blocking dependencies.

---

## 📄 License

This project was developed as part of `Winter SWE Training` by `Fuzetek`.
