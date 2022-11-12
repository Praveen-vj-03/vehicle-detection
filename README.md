# vehicle-detection
'video_function_with_lanes.py' file. This second video runs into an interesting issue after running seamlessly to start, where the lane detection fails in the last 15 seconds or so, likely due to my current implementation running the lane detection after the vehicle detection (and the bounding boxes messing with the lane line detection).

#### Heatmaps

The 'heatmap_functions.py' file and Lines 134-162 of 'full_detection_pipeline.py' implement my heat maps.

In order to create the heat maps, for each box detected for an image/frame of video, these these box positions are recorded such that the areas within are given a certain amount of "heat", whereby more "heat" means there were more detections in that spot. After applying a threshold, only certain higher amounts of heat are left to eliminate some of the false positives. I then used 'scipy.ndimage.measurements.label()' to identify individual heat blobs in the heatmap.  From there, the individual heat blobs are assumed to be vehicles, and bounding boxes are made to cover the area of each of these heat blobs. As mentioned above, these heat maps get averaged across the last ten frames.

Here's an example result showing the heatmap from a series of images, the result of 'scipy.ndimage.measurements.label()' and the bounding boxes then overlaid onto the images:

#### Here are six frames and their corresponding heatmaps, after thresholding:

![Original Images](./images/test_images.PNG "Original Images")
![Heatmaps](./images/heatmaps.PNG "Image Heatmaps")
There are no longer any false positives in these example images.

#### Here the resulting bounding boxes are drawn onto the last frame in the series:
![Final product](./images/finished.PNG "Images with the final bounding boxes")

---

### Discussion

#### Issues and Potential Improvements

The first issue I ran into with this project was simply learning the best parameters to use, a common area of focus in machine learning. I initially played around mostly with the spatial dimensions and number of histogram bins, which had appeared to have more of an importance in lessons prior to the project but which I found to not have that great of an impact on the final classifier accuracy.

Along with this, although I was getting up near 97% accuracy with my classifier, I was quickly switching between implementations where I either had way too many false positives, or did the complete opposite and had trouble getting any vehicle detection once including my heat maps. A big discovery was seeing the impact the YCrCb color space had on the classifier - simply changing to that color space (one which admittedly is not very intuitive to me, such as RGB) improved the classifier accuracy to right near 99%. Although that does not sound like a huge difference, it made a clear difference in the output.

From there, implementing the additional scales took some re-tooling of my original pipeline. Once I did that, I was then detecting too many vehicles again, even with the improved classifier. Once I went back and raised my heatmap threshold, that problem was solved.

My model would have some interesting results if it was driving down a road with cars parked on either side or on a more open road (whereas the project video has a middle divider blocking the other side). Especially with a lot of parked cars, there could be a gigantic bounding box all along the side of the image.

As seen above, if I want to detect vehicles and lanes at the same time, I could probably retool my functions to either run more in parallel or otherwise not draw the vehicle detections onto the image until already pulling the lane line detection as well. This would prevent the issue late in the combined video where the lane line detection fails.

Another area of potential vast improvement is with regards to speed. My current implementation is much, much slower than real time (taking nearly 50 minutes to produce the less than one minute project video). 

Lastly, I think deep learning would be another approach (especially since the training data is of a sufficient size), or potentially using other classifier algorithms to see how they fare compare to a Linear SVC.

Overall though, I think my implementation for vehicle detection is a good start!
