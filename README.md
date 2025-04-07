# RenAIssance Project Tests – GSoC 2025  
*AI for Historical Document Analysis and Synthesis*

This repository contains solutions to two project tests submitted as part of the **RenAIssance Project Tests for Prospective GSoC 2025**. These tests focus on applying modern machine learning techniques to analyze and synthesize 17th-century Spanish Renaissance documents.

---

## Specific Test I: Layout Organization Recognition

**Task**: Build a model to **detect and extract main text regions** from scanned pages, while **ignoring embellishments** like page numbers, logos, and margins.

### Approach:
- **PDF to Image Conversion**: First 3 pages of each PDF are converted to `.png` using PyMuPDF.
- **Word-Level OCR**: Tesseract OCR used to extract bounding boxes and recognized text.
- **Margin-Based Filtering**: Bounding boxes outside core text margins are discarded.
- **Word Cropping**: Filtered boxes are used to create word-level image crops.
- **Transcript Alignment**: OCR-detected words matched to original transcripts using Levenshtein distance.
- **Final Evaluation**: Re-OCR on cropped word images and comparison against ground truth.

### Evaluation Metric:
- **Matching Accuracy** = (Matched Predictions / Valid OCR Outputs) × 100  
  Focuses on how accurately the model isolates true main text content.

### 📈 Results:
- Total word images: **209**  
- Valid OCR outputs: **193**  
- Correct matches: **101**  
- **Accuracy**: **52.33%**

>  Challenge: Standard OCR engines struggled with non-ASCII characters (e.g., "ñ"). Future improvements include training custom OCR models.

---

##  Specific Test III: Synthetic Renaissance Text Generation with Generative Models

**Task**: Design a **mid-scale generative model** (e.g., CGAN) to produce synthetic Renaissance-style printed Spanish text with **realistic imperfections** such as ink bleed, smudging, and fading.

###  Approach:
- **Dataset**: Manually cropped character images (A–Z, a–z), augmented and labeled for conditional generation.
- **Model**: Conditional GAN (CGAN)
  - **Generator**: Label-conditioned grayscale image generator.
  - **Discriminator**: Classifies real vs. fake conditioned on character identity.
- **Text Pipeline**:
  - Input historical Spanish text from `.docx`
  - Character-by-character image generation
  - Word assembly with typographic alignment and simulated degradation (e.g., Gaussian blur)

### Evaluation Metrics:
| Metric | Description |
|--------|-------------|
| **MSE** | Pixel-wise error between generated and real OCR outputs |
| **SSIM** | Measures structural similarity |
| **Visual Checks** | Side-by-side comparisons with historical prints |

### 📈 Results:
- **Global Averages over 5 pages**:  
  - **MSE**: 0.1423  
  - **SSIM**: 0.4811  

> ⚠️ Some characters like “ñ” were missed due to OCR limitations—highlighting the need for enhanced multilingual OCR tools or post-processing logic.

---

## Summary

| Test Name | Model Type | Focus | Key Metric |
|-----------|------------|-------|-------------|
| **Specific Test I** | OCR + Filtering | Main text detection | Matching Accuracy |
| **Specific Test III** | Conditional GAN | Synthetic degraded text generation | MSE, SSIM, Visual Fidelity |

