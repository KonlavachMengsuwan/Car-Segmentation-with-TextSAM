# 🚗 Car Segmentation with TextSAM

<img width="1988" height="1126" alt="image" src="https://github.com/user-attachments/assets/daaeffc3-e851-486f-9ea4-05f9c430f335" />

### Introduction
This experiment evaluates the **TextSAM** (Text-guided Segment Anything Model) for detecting and segmenting **cars** from high-resolution UAV RGB orthomosaics.  
While TextSAM is primarily designed for general object segmentation from text prompts, this test investigates its capability to identify small objects (cars ~4–5 m length) at **10 cm spatial resolution**.

The goal is to understand how TextSAM responds to changes in **Padding** and **Box Threshold**, and whether it can effectively detect vehicles under UAV conditions (tight spacing, tree shadows, and high object density).

---

### Methods

**Dataset:**  
- UAV RGB orthomosaic (10 cm/pixel)  
- Urban area with parked cars and mixed illumination (shadowed and bright regions)

**Deep Learning Tool:**  
- ArcGIS Pro → *Detect Objects Using Deep Learning*  
- Model: **TextSAM.dlpk**  
- Text Prompt: `"car"`

**Fixed parameters:**
| Parameter | Value |
|------------|--------|
| Text Prompt | `car` |
| Batch Size | 4 |
| TTA Scales | 1 |
| Non Maximum Suppression | ✅ Enabled |
| Tile Size (Environment) | 1024 pixels |
| Resolution | 10 cm |

**Tested parameters:**

#### Padding
| Test | Padding | Observation |
|------|----------|--------------|
| A | 64 | Many false detections and missed cars; context too small. |
| B | 128 | Fewer false positives, but still several missed cars. |
| C | 256 | Optimal balance — cars clearly detected, minimal false positives. |
| D | 320 | Slightly more context, but false detections increased again. |

**→ Best Padding: 256**

---

#### Box Threshold (with Padding = 256)
| Test | Box Threshold | Observation |
|------|----------------|--------------|
| A | 0.20 | More precise pixel-level detection, but false positives increase (e.g., tree shadows). |
| B | 0.25 | Balanced precision and recall; detects most cars with stable segmentation quality. |

**→ Best Box Threshold: 0.25**

---

### Results

**Final configuration**
```
Text Prompt: car
Padding: 256
Batch Size: 4
Box Threshold: 0.25
Text Threshold: 0.50
NMS Overlap: 0.30
Resolution: 10 cm
```


**Performance summary:**
- **Precision:** High — few false positives in paved and shaded areas.  
- **Recall:** Moderate — most cars detected; some under heavy tree cover missed.  
- **Computation time:** Reasonable (~x1.5 vs Padding 64 due to larger context).  

**Qualitative comparison:**
- At smaller paddings (64, 128), TextSAM failed to fully perceive car context.  
- Padding = 256 allowed TextSAM to interpret car geometry and background cues.  
- Box Threshold = 0.25 produced stable results across lighting conditions.  
- Threshold = 0.20 improved mask quality but introduced more false positives.

---

### Discussion
This study shows that **TextSAM** can perform coarse car segmentation at 10 cm GSD, but requires generous spatial context (Padding ≥ 256) to maintain scene awareness.  
For small or partially occluded cars, dedicated object-detection models (e.g., *CarDetection_USA.dlpk* or YOLOv8 fine-tuned) remain superior.

However, TextSAM demonstrates strong generalization for larger vehicles and clear road scenes, making it a valuable zero-shot baseline when no specialized model is available.

---

### Example visualization
## Padding: 64
<img width="1999" height="1121" alt="image" src="https://github.com/user-attachments/assets/5511c394-946e-4cd9-b72a-0bb616d9ca01" />

## Padding: 128
<img width="1995" height="1119" alt="image" src="https://github.com/user-attachments/assets/9dcdd9e0-04d2-4158-b318-af47664ccc7f" />

## Padding: 256
<img width="1997" height="1115" alt="image" src="https://github.com/user-attachments/assets/d57c10f4-f491-44c4-a6aa-f7dff0a9e995" />

## Padding: 320
<img width="1988" height="1121" alt="image" src="https://github.com/user-attachments/assets/9e840d46-2e53-40f6-8a2e-3bfc1a150049" />

---

---

📘 **Author:** Konlavach Mengsuwan  
🔍 **Research focus:** GEOAI: AI × Remote Sensing × UAV Analysis  
