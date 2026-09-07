# Computer-vision-polish-coins-segmentation

## About The Project
Counting loose change by hand can be tedious and repetitive. To solve this problem using Computer Vision, I created this project to automatically detect, classify, and sum Polish coins from a single photo.

The program combines circle detection with feature matching algorithms to recognize coin denominations (such as 2 zł, 50 gr, 20 gr, and 1 gr) from both sides (heads and tails). By utilizing a two-step classification method — size-based filtering followed by texture pattern matching — the system accurately calculates the total money value present in the image and visualizes the results.

## Built With
* **Language:** Python 3
* **Computer Vision & Image Processing:** OpenCV (`cv2`)
* **Numerical Operations:** NumPy
* **Environment:** Google Colab / Jupyter Notebook

## Project Workflow
1. **Coin Localization & Radius Calibration:** The input image is resized and converted to grayscale. After applying median blur noise reduction, the Hough Circle Transform (`cv2.HoughCircles`) detects circular shapes and measures their pixel radii to determine standard size thresholds for each denomination.
2. **Template Preprocessing & Feature Bank Creation:** Reference images for each denomination (both obverse "Orzeł" and reverse "Reszka") are loaded. Using Otsu thresholding and contour detection, the coin is isolated from the background. Contrast is enhanced with CLAHE (Contrast Limited Adaptive Histogram Equalization), and SIFT (Scale-Invariant Feature Transform) extracts keypoint descriptors stored in a reference dictionary.
3. **Region of Interest (ROI) Extraction:** Circles detected in the test image are cropped into individual coin patches and normalized to standard template dimensions with circular masks to eliminate border noise.
4. **Hybrid Two-Tier Classification:**
   * **Tier 1 (Size Heuristic):** Filters possible candidate denominations based on the measured radius (e.g., larger radii for 2 zł, smaller for 1 gr).
   * **Tier 2 (SIFT Matching):** Brute-Force Matcher (`cv2.BFMatcher`) evaluates candidate templates using Lowe's ratio test. If the number of good matches exceeds the confidence threshold, the coin is confirmed. Otherwise, a size-based fallback estimate is applied.
5. **Summation & Visual Annotation:** Values are added to a running balance. The image is annotated with bounding circles (green for confirmed matches, orange for fallback estimates), labels, and the total monetary sum rendered on screen.

## Instruction
1. Clone this repository to your local machine or open it in Google Colab.
2. Place your reference coin template images in the project directory:
   * `2Orzel.jpg`, `2Reszka.jpg`
   * `0.5Orzel.jpg`, `0.5Reszka.jpg`
   * `0.2Orzel.jpg`, `0.2Reszka.jpg`
   * `0.01Orzel.jpg`, `0.01Reszka.jpg`
3. Add your target photo containing coins.
4. Run the notebook `CoinRecog.ipynb`.
5. Check the annotated output image and the total counted sum displayed in the console and image header.

## Key Features & Results

### Coin Radius vs. Denomination Mapping
During calibration, coin pixel radii were categorized into ranges:

| Denomination | Expected Radius (px) | Fallback Decision |
|:-------------|:---------------------|:------------------|
| **2 zł**     | $r \ge 36$           | Assumed 2 zł      |
| **50 gr**    | $32 \le r < 36$      | Assumed 50 gr     |
| **20 gr**    | $29 \le r < 32$      | Assumed 20 gr     |
| **1 gr**     | $r < 29$             | Assumed 1 gr      |

<img width="600" height="337" alt="1" src="https://github.com/user-attachments/assets/ae9671cb-23ca-44ec-8ad3-b6eb80f422de" />


### Detection Visual Output
* **High Confidence (Green):** Verified by both Hough circle size and SIFT feature matching (points $\ge 8$).
* **Fallback Estimate (Orange with `?`):** Triggered when feature points are low, falling back to the most likely denomination by size.
* **Header Display:** The final total (e.g. `SUMA: 1.01 zl`) is drawn directly onto the top-left corner of the result image.

<img width="600" height="337" alt="2" src="https://github.com/user-attachments/assets/a29d79d9-4b27-4572-a386-5e0d9bdd3dbb" />


## Notes & Limitations
* Camera distance and angle should remain relatively consistent for the pixel radius thresholds to remain valid.
* Coins should not strongly overlap for the Hough Circle Transform to separate them accurately.
