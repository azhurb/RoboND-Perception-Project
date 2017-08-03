## Project: Perception Pick & Place

[confusion_matrix]: ./images/figure_2.png
[world_1]: ./images/world_1.png
[world_2]: ./images/world_2.png
[world_3]: ./images/world_3.png

### Creating the perception pipeline
#### 1. Pipeline for filtering and RANSAC plane fitting implemented.
* Implemented **Statistical Outlier Filtering** for removing outliers using _number of neighboring points_ equal to `10` and  _standard deviation multiplier threshold_ equal to `0.001`.
* Implemented **Voxel Grid Downsampling** using _leaf size_ `0.01`.
* Implemented **PassThrough Filter** for extracting working space along the axes _Z_ and _X_.
* Implemented **RANSAC Plane Segmentation** for extracting target objects from point cloud.

#### 2. Pipeline including clustering for segmentation implemented.
* Implemented **Euclidean Clustering** using _cluster tolerance_ equal to `0.05`, _minimum cluster size_ equal to `50` and _maximum cluster size_ equal to `20000`.

#### 3. Features extracted and SVM trained. Object recognition implemented.
* Extracted features using `capture_features.py`. I used HSV for compute color histograms. The number of spawns of each object is 50.
* Trained classifier using SVM with _rbf_ kernel. Resulted accuracy is `0.97`

![Normalized confusion matrix][confusion_matrix]

### Pick and Place Setup

#### For all three tabletop setups (`test*.world`), performed object recognition. Then read in respective pick list (`pick_list_*.yaml`). Next constructed the messages that would comprise a valid `PickPlace` request output them to `.yaml` format.

##### World 1
Correctly identified 100% of objects (**3/3**). Saved PickPlace requests in the `pr2_robot/scripts/output_1.yaml`.

![World 1][world_1]

##### World 2
Correctly identified 100% of objects (**5/5**). Saved PickPlace requests in the `pr2_robot/scripts/output_2.yaml`.

![World 2][world_2]

##### World 3
Correctly identified 87.5% of objects (**7/8**). The _glue_ was incorrectly recognized as _snacks_. I think it is because the _book_ in the foreground partially hides the _glue_. Saved PickPlace requests in the `pr2_robot/scripts/output_3.yaml`.

![World 3][world_3]

### Code discussion
I had some problems with Statistical Outlier Filtering. It was not possible to create an object.
```python
outlier_filter = cloud.make_statistical_outlier_filter()
```
I keep getting an error:  
`TypeError: __cinit__() takes exactly 1 positional argument (0 given)`

I tried to find a solution for many hours. The next day on the Slack channel I saw that it was a problem with `pcl`. I pulled out updated from the upstream git, reinstall pcl and problem was solved!

The next problem I got trying to save `yaml` file. I keep getting an error:  
`TypeError: data type not understood`
After some investigation it turned out that some elements of the dictionary had specific types - `numpy` types. For instance `object_name` element in the dictionary have `numpy.string_` type. Therefore, I had to perform type casting for each element of the dictionary using `int()`, `str()` and `float()` functions. I also published a solution of the problem on the Slack #udacity_perception channel.



