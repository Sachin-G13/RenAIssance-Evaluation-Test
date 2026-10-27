# RenAIssance: AI for Historical Document Analysis and Synthesis

Solutions to two evaluation tests for the **RenAIssance project (GSoC 2025)**, which applies machine learning to 17th-century Spanish printed documents:

1. **Test I:** extract the main text from scanned pages, ignoring page numbers, logos and margins.
2. **Test III:** generate synthetic Renaissance-style printed Spanish text with a conditional GAN (Keras), including realistic print imperfections.

## Key results

| Test | Method | Metric | Result |
|---|---|---|---|
| I: Layout filtering | PyMuPDF + Tesseract + margin filter | Matching accuracy | **52.33%** (101 / 193 valid OCR outputs) |
| III: Synthetic text | Conditional GAN (Keras), validated with EasyOCR | Character accuracy / CER | **~71% / 0.29** |
| III: Synthetic text | Same | Exact-word accuracy | **33.33%** |
| III: Synthetic text | Same | MSE / SSIM (avg. over 5 pages) | 0.1423 / 0.4811 |

**Main takeaways**
- Margin-based filtering plus OCR is enough to isolate main text on these pages, but accuracy is limited by OCR quality on historical typefaces.
- The generated words are mostly readable to an OCR engine (~71% of characters correct) but are often wrong at the whole-word level (33.33% exact), so errors accumulate across a word.
- Pixel-level metrics (MSE, SSIM) were only moderately informative here. OCR-based validation was a better check of whether the output reads as text.

---

## Test I: Layout Organization Recognition

**Task:** detect and extract the main text regions from scanned pages while ignoring embellishments (page numbers, logos, margins).

### Pipeline
1. **PDF to image:** the first 3 pages of each PDF are converted to `.png` with PyMuPDF.
2. **Word-level OCR:** Tesseract returns bounding boxes and recognized text.
3. **Margin-based filtering:** boxes outside the core text margins are discarded. Margin thresholds: **[FILL IN, e.g. fixed fractions of page width/height]**.
4. **Word cropping:** the remaining boxes are cropped into word-level images.
5. **Transcript alignment:** OCR-detected words are matched to the ground-truth transcript using Levenshtein distance (match threshold: **[FILL IN]**).
6. **Evaluation:** the cropped images are re-OCR'd and compared against the ground truth.

### Metric
**Matching accuracy** = matched predictions / valid OCR outputs × 100

This measures how well the pipeline isolates true main-text content. A match is decided by the Levenshtein threshold above, so it is a fuzzy match rather than a strict string equality. **[FILL IN: confirm whether the threshold allows any edit distance.]**

### Results
| Total word images | Valid OCR outputs | Correct matches | Accuracy |
|---|---|---|---|
| 209 | 193 | 101 | **52.33%** |

---

## Test III: Synthetic Renaissance Text Generation

**Task:** build a mid-scale generative model that produces synthetic Renaissance-style printed Spanish text with realistic imperfections such as ink bleed, smudging and fading.

### Approach
- **Dataset:** manually cropped character images (A–Z, a–z), augmented and labeled for conditional generation. Dataset size: **[FILL IN]** images.
- **Model (Keras):** conditional GAN (CGAN)
  - **Generator:** label-conditioned grayscale character generator.
  - **Discriminator:** classifies real vs. fake, conditioned on the character label.
  - Training details: **[FILL IN: epochs, image size, batch size, optimizer]**.
- **Text pipeline:**
  1. Read historical Spanish text from a `.docx` file.
  2. Generate each character image from its label.
  3. Assemble words with baseline alignment.
  4. Apply simulated degradation to imitate letterpress ink bleed (Gaussian blur plus **[FILL IN: any other effect, e.g. noise, erosion/dilation, intensity variation]**).

### Evaluation
| Metric | What it measures |
|---|---|
| **EasyOCR character accuracy / CER** | Whether OCR can read the generated words (~71% / 0.29) |
| **Exact-word accuracy** | Share of generated words read back exactly right (33.33%) |
| **MSE** | Pixel-wise error between generated and real images |
| **SSIM** | Structural similarity to real images |
| **Visual checks** | Side-by-side comparison with historical prints |

EasyOCR results were computed on **[FILL IN: number of generated words/pages]**. MSE and SSIM are global averages over 5 pages (MSE 0.1423, SSIM 0.4811).

---

## Limitations
- **Missing characters:** the character set covers A–Z and a–z only, so letters such as "ñ" and accented characters are not generated. Standard OCR engines also misread them, so they were missed in both tests.
- **OCR-dependent evaluation:** Tests I and III both rely on OCR output, so OCR errors are mixed into the reported accuracy. Tesseract and EasyOCR are not trained on this typeface.
- **Small evaluation sets:** 209 word images for Test I and 5 pages for the MSE/SSIM averages in Test III, so the numbers show feasibility rather than a robust benchmark.
- **Modest SSIM (0.48):** the generated characters match real ones in structure only partially. Visual checks and OCR accuracy were used alongside it for that reason.
- **Fuzzy matching in Test I:** matching accuracy uses a Levenshtein threshold, so it is more lenient than exact string match.

## Future work
- Train or fine-tune a custom OCR model on this typeface, including ñ and accented characters.
- Extend the character set and add stronger degradation models (paper texture, uneven ink).

---

## Repository structure
```
[FILL IN: list your notebooks/scripts, e.g.
 layout_organization.ipynb   # Test I
 synthetic_generation.ipynb  # Test III
 data/                       # cropped characters, transcripts
]
```

## Setup and usage
```bash
git clone https://github.com/Sachin-G13/<repo-name>.git
cd <repo-name>
pip install -r requirements.txt   # PyMuPDF, pytesseract, easyocr, tensorflow/keras, numpy, python-Levenshtein
```
Tesseract must be installed separately on your system. Then run **[FILL IN: notebook or script order for Test I and Test III]**.
