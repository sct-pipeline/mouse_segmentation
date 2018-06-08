# mouse_segmentation
This tutorial explains how to segment spinal cords from small animals (e.g. mouse)

## Dependencies

SCT development version (master:51f9f79ffad0cbdb3b865273faa4aa605fced2df). Next stable release: v3.2.1.


## Getting started

Below is an example of a mouse MRI from which you would like to segment the spinal cord:

SCT algorithms don't work out-of-the-box because of the different scaling, and also the low cord/CSF contrast. Below are a series of commands that you can do to obtain acceptable segmentation results.

```bash
# sct_crop_image -i Pre_contrast.nii.gz -dim 0,1,2 -start 80,200,40 -end 230,620,80 -o Pre_contrast_crop.nii.gz
sct_crop_image -i Pre_contrast.nii.gz -dim 0,2 -start 20,40 -end 290,80 -o Pre_contrast_crop.nii.gz

# stretch segmentation to match physical dimensions of human spinal cord (required by segmentation algorithm)
isct_antsApplyTransforms -d 3 -i Pre_contrast_crop.nii.gz -o Pre_contrast_crop_stretched.nii.gz -t affine_stretch.txt -r Pre_contrast_crop.nii.gz

# manually label a few points across spinal cord centerline (see README)
sct_propseg -i Pre_contrast_crop_stretched.nii.gz -init-centerline viewer -c t1

# smooth spinal cord
sct_smooth_spinalcord -i Pre_contrast_crop_stretched.nii.gz -s Pre_contrast_crop_stretched_labels_viewer.nii.gz -smooth 5

sct_propseg -i Pre_contrast_crop_stretched_smooth.nii.gz -init-centerline Pre_contrast_crop_stretched_labels_viewer.nii.gz -c t1 -radius 2

# Generate compress transformation
# TODO

# bring segmentation back to original space (compress)
isct_antsApplyTransforms -d 3 -i Pre_contrast_crop_stretched_smooth_seg.nii.gz -o Pre_contrast_crop_stretched_smooth_seg_compressed.nii.gz -t affine_compress.txt -r Pre_contrast_crop_stretched.nii.gz

# Open segmentation overlaid on original volume
fsleyes Pre_contrast.nii.gz Pre_contrast_crop_stretched_smooth_seg_compressed.nii.gz -cm red &
```

## License

The MIT License (MIT)

Copyright (c) 2018 École Polytechnique, Université de Montréal

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

