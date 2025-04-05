# Specific Test I. Layout Organization Recognition

## Project Overview  
This project aims to build a preprocessing pipeline to aid the development of a model (e.g., CNN-RNN, Transformer, or Self-Supervised architecture) for optical layout recognition in scanned documents. The primary objective is to detect and isolate main text regions from scanned pages while ignoring embellishments such as logos, page numbers, or decorative borders.

## Strategy

The pipeline is structured in several stages to prepare a reliable dataset for training and evaluation:

### 1. PDF Organization and Extraction
- All `.pdf` files are grouped into a central folder.
- Files are unzipped and organized for image extraction.

### 2. Image Conversion
- First 3 pages of each PDF are converted into `.png` images using `PyMuPDF`.
- Each PDF gets its own folder to maintain clean structure.

### 3. Word-Level OCR and Bounding Box Extraction
- Each image undergoes OCR (using `Tesseract`) to extract bounding box coordinates and words.
- Coordinates and recognized words are stored in separate `.txt` files.

### 4. Margin Filtering
- Bounding boxes outside estimated left/right text margins are discarded to filter out headers, footers, and side embellishments.

### 5. Cropped Word Image Generation
- The filtered bounding boxes are used to crop word-level images.
- Each cropped word image is saved using the OCR-detected word as the filename.

### 6. Transcript Alignment and Correction
- The ground truth transcript (from `.docx` files) is parsed and segmented by page.
- Word filenames are corrected using Levenshtein distance to match the closest transcript word, ensuring consistent naming and case handling.

### 7. Evaluation
- OCR is re-applied on each cropped word image.
- Predictions are normalized and compared with ground truth words.

## Evaluation Metrics

To assess the effectiveness of the word-level recognition pipeline, the following metrics are used:

### Matching Accuracy
- **Definition**: Percentage of OCR-predicted words that exactly match the ground truth transcript (case-insensitive, punctuation-agnostic).
- **Formula**:  
  Accuracy = (Number of Correct Matches / Total Valid OCR Predictions) × 100
- **Purpose**: Validates how clean and recognizable the cropped word images are.

### Warning Logs
- Mismatches in the number of bounding boxes and words.
- Skipped words or duplicates in the alignment stage.

## Next Steps

This pipeline lays the foundation for training a deep learning model on structured, aligned word-image pairs. The next phase involves:
- Training a CNN-RNN or Transformer-based layout recognition model.
- Labeling and learning to segment main text blocks from embellishments using spatial patterns and word context.
- Using self-supervised contrastive learning to distinguish meaningful layout structures.
