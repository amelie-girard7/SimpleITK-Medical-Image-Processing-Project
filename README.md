# SimpleITK-Medical-Image-Processing-Project
This project structure gives you a comprehensive framework to learn all major aspects of SimpleITK, from basic operations to advanced registration and segmentation techniques. You can start with the notebooks and then move to the scripts as you become more comfortable.

## Project Structure

```
Medical_Imaging_SimpleITK/
│
├── data/
│   ├── input_images/
│   │   ├── brain_mri.nii.gz          # Sample MRI scan
│   │   ├── chest_ct.nii.gz           # Sample CT scan
│   │   └── prostate_us.mha          # Sample ultrasound
│   ├── output_images/                # Processed images will be saved here
│   └── synthetic_data/               # Generated test images
│       ├── sphere_phantom.nii.gz
│       └── cube_phantom.nii.gz
│
├── notebooks/
│   ├── 01_basic_operations.ipynb     # Basic image operations
│   ├── 02_filtering.ipynb            # Image filtering examples
│   ├── 03_registration.ipynb        # Image registration
│   ├── 04_segmentation.ipynb        # Image segmentation
│   └── 05_visualization.ipynb       # Visualization techniques
│
├── scripts/
│   ├── preprocess.py                 # Image preprocessing
│   ├── register.py                  # Registration script
│   ├── segment.py                   # Segmentation script
│   └── utilities.py                 # Helper functions
│
└── requirements.txt                 # Python dependencies
```

## Key Files with Content Examples

### 1. Basic Operations Notebook (`notebooks/01_basic_operations.ipynb`)
```python
import SimpleITK as sitk
import matplotlib.pyplot as plt

# Load an image
image = sitk.ReadImage("data/input_images/brain_mri.nii.gz")

# Basic image information
print(f"Size: {image.GetSize()}")
print(f"Spacing: {image.GetSpacing()}")
print(f"Origin: {image.GetOrigin()}")
print(f"Pixel type: {image.GetPixelIDTypeAsString()}")

# Display a slice
arr = sitk.GetArrayFromImage(image)
plt.imshow(arr[100,:,:], cmap='gray')
plt.show()
```

### 2. Filtering Notebook (`notebooks/02_filtering.ipynb`)
```python
import SimpleITK as sitk

# Load and cast to float
image = sitk.ReadImage("data/input_images/brain_mri.nii.gz")
image_float = sitk.Cast(image, sitk.sitkFloat32)

# Edge detection
gradient = sitk.GradientMagnitude(image_float)

# Smoothing
smooth = sitk.CurvatureFlow(image1=image_float, timeStep=0.125, numberOfIterations=5)

# Thresholding
threshold = sitk.BinaryThreshold(image_float, lowerThreshold=100, upperThreshold=400)

# Save results
sitk.WriteImage(gradient, "data/output_images/gradient_magnitude.nii.gz")
sitk.WriteImage(smooth, "data/output_images/smoothed_image.nii.gz")
```

### 3. Registration Script (`scripts/register.py`)
```python
import SimpleITK as sitk

def register_images(fixed_image_path, moving_image_path, output_path):
    fixed = sitk.ReadImage(fixed_image_path)
    moving = sitk.ReadImage(moving_image_path)
    
    # Initial alignment
    initial_transform = sitk.CenteredTransformInitializer(
        fixed, moving, sitk.Euler3DTransform())
    
    # Registration
    registration_method = sitk.ImageRegistrationMethod()
    registration_method.SetMetricAsMattesMutualInformation(numberOfHistogramBins=50)
    registration_method.SetOptimizerAsGradientDescent(learningRate=1.0, numberOfIterations=100)
    registration_method.SetInitialTransform(initial_transform)
    
    final_transform = registration_method.Execute(fixed, moving)
    
    # Apply transform
    resampled = sitk.Resample(moving, fixed, final_transform, sitk.sitkLinear, 0.0)
    
    sitk.WriteImage(resampled, output_path)
    return resampled, final_transform
```

### 4. Segmentation Notebook (`notebooks/04_segmentation.ipynb`)
```python
import SimpleITK as sitk

# Load image
image = sitk.ReadImage("data/input_images/chest_ct.nii.gz")

# Otsu thresholding
otsu_filter = sitk.OtsuThresholdImageFilter()
otsu_filter.SetInsideValue(0)
otsu_filter.SetOutsideValue(1)
segmented = otsu_filter.Execute(image)

# Connected components
connected_components = sitk.ConnectedComponent(segmented)
relabeled = sitk.RelabelComponent(connected_components, minimumObjectSize=100)

# Save result
sitk.WriteImage(relabeled, "data/output_images/lung_segmentation.nii.gz")
```

### 5. Requirements File (`requirements.txt`)
```
SimpleITK>=2.2.1
numpy>=1.21.0
matplotlib>=3.4.0
jupyter>=1.0.0
ipywidgets>=7.6.0
```

## Learning Resources to Include

1. **Sample Data**: You can find free medical images from:
   - The Cancer Imaging Archive (TCIA)
   - MIDAS dataset from Kitware
   - BrainWeb simulated MRI dataset






