# ATD_challenge

---

## Introduction

This repository contains my attempt at a challenging medical image processing task for RSNA 2023 Abdominal Trauma Detection. The goal was to effectively utilize a combination of 3D segmentation and 2.5D LSTM + CNN models to achieve reliable results. Throughout the project, various methods and strategies were employed to preprocess, segment, and classify medical images.

## Methods Employed

1. **3D Segmentation with U-Net + ResNet18**: 
    - The initial stage involved preprocessing the given segmentation masks and volume data.
    - A 3D segmentation model using U-Net with a ResNet18 backbone was trained. This process is resource-intensive and requires significant GPU VRAM.
    - This trained model was then used to segment other series/patients in the dataset.

2. **Bounding Box Extraction**:
    - Post segmentation, bounding boxes were extracted from the segmentation masks to identify relevant slices.
    - Non-overlapping images were created using 3 adjacent slices. For instance, `image_0` is derived from slices 0, 1, and 2, while `image_1` is derived from slices 3, 4, and 5.

3. **Organ Detection**:
    - To determine missing organs in some series, a mapping was generated showing which organs were present in specific slices.
    - Linear regression models were trained for each organ to predict the presence of an organ in a given slice.
    - Combinations used for training:

```python
combinations = [
    (["liver", "bowel"], "kidneys"),
    (["liver", "spleen", "bowel"], "kidneys"),
    ...
    (["liver"], "spleen")
]
```

4. **Organ Extraction**:
    - Based on the predictions and the slices where organs were detected, the liver, kidneys, spleen, and bowel were extracted from their respective subregions.
    - These extracted subregions were preprocessed and resized to a uniform size of 10 x 320 x 320, primarily due to GPU VRAM limitations.

5. **2.5D LSTM + CNN (ConvNext-Nano) Training**:
    - The final model architecture utilized was a combination of LSTM and ConvNext-Nano, trained on the processed organ subregions. The model worked with an image size of 320x230.
    - Given the class imbalance in the dataset, there was an effort to weigh the underrepresented classes more. However, this aspect requires further fine-tuning.

## Future Work and Considerations

1. **Improved 3D Segmentation**: One key area of improvement is to further train and refine the 3D segmentation model to achieve better segmentation masks, especially for specific organs.
2. **Slice Selection Methodology**: A more sophisticated slice selection method can potentially yield better results.
3. **Utilizing Segmentation Masks**: In the training phase of the 2.5D LSTM + CNN model, incorporating the segmentation masks directly might offer improved performance.

---
