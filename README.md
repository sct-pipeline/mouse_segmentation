# mouse_segmentation
This tutorial explains how to segment spinal cords from small animals (e.g. mouse)

## Dependencies

SCT development version (master:d2c27fe61c51a4faa9e04b524df9f054a6edcf60). Next stable release: v3.2.3.

## Getting started

Below is an example of a mouse MRI from which you would like to segment the spinal cord:

<img src="https://github.com/sct-pipeline/mouse_segmentation/blob/master/doc/fig_data.png" width="600">

SCT algorithms don't work out-of-the-box because of the different scaling, and also the low cord/CSF contrast. Below are a series of commands that you can do to obtain acceptable segmentation results.

```bash
# crop image around the spinal cord (for faster processing). Find the cropping coordinates using a viewer, e.g. FSLeyes.
sct_crop_image -i data.nii.gz -dim 0,1,2 -start 90,200,40 -end 240,600,80 -o data_crop.nii.gz
```

<img src="https://github.com/sct-pipeline/mouse_segmentation/blob/master/doc/fig_data_crop.png" width="600">

```bash
# Manually label a few points along spinal cord centerline (see figure below)
# Tips: The flag -rescale 3 adjusts the dimension of the image so that the mouse spinal cord size approximately matches 
# that of human, on which PropSeg algorithm has been tuned and optimized.
sct_propseg -i data_crop.nii.gz -c t1 -init-centerline viewer -rescale 3
```

<img src="https://github.com/sct-pipeline/mouse_segmentation/blob/master/doc/fig_data_crop_labeling.png" width="600">

```bash
# Smoothing along spinal cord centerline to enhance contrast between cord and CSF
sct_smooth_spinalcord -i data_crop.nii.gz -s data_crop_centerline.nii.gz -smooth 5
```

<img src="https://github.com/sct-pipeline/mouse_segmentation/blob/master/doc/fig_data_crop_smooth.png" width="600">

```bash
# segment spinal cord (using pre-existing centerline as initialization)
sct_propseg -i data_crop_smooth.nii.gz -c t1 -init-centerline data_crop_centerline.nii.gz -rescale 3
```

<img src="https://github.com/sct-pipeline/mouse_segmentation/blob/master/doc/fig_seg_on_image.gif.gif" width="600">


## License

The MIT License (MIT)

Copyright (c) 2018 École Polytechnique, Université de Montréal

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

