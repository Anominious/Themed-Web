## Mitochondria Skeleton Length Calculator (Matlab)

You can download the .m file [here](https://www.mathworks.com/matlabcentral/fileexchange/101579-mitochondria-s-skeleton-length-and-aspect-ratio-calculator) and install it as an app to access the function.

### The Terms

#### Bounding Box

A bounding box of a section is decided by its largest and smallest values in x, y, and z direction. The length, width, and height of a bounding box can be obtained by doing subtractions bewteen each pair of x, y, and z values. 
Bounding Box Coordinates Examples:
<img src="img/bbox.png" width="300"/>

#### Aspect Ratio

The [aspect ratio](https://en.wikipedia.org/wiki/Aspect_ratio) the ratio between the length and width of a shape. Since the bounding box of a section is a rectangular prism, this project takes 3 aspect ratio of each section: xy, xz, yz. 

<img src="img/aspect_ratio.png" width="450"/>

#### Skeleton

The skeleton of sections is obtained using the [skeleton3d](https://www.mathworks.com/matlabcentral/fileexchange/43400-skeleton3d) function. The process of "skeletonization" is also known as the [Medial Axis Transform](https://homepages.inf.ed.ac.uk/rbf/HIPR2/skeleton.htm). Detailed examples of 3d skeletonization can be found [here](https://www.mathworks.com/help/images/ref/bwmorph3.html), an alternative to skeleton3d. However, neither of these two functions calculates the length of the generated skeletons. Therefore, calculating  the length of skeletons became the goal for this program.

#### Connectivity 
In order to calculate the length of the generated skeleton, this program calculate the distance between each adjacent voxel and add them up. In a 3d space, one [voxel](https://en.wikipedia.org/wiki/Voxel) can have 26 distinct adjacent voxels surrounding it. These 26 can be classified to 3 categories: connected via surface, via edge, and via point. The 3 categories each have different method to obtain the distance between the two voxels. 

Connected surface (6 total, devided to 3 kinds):

<img src="img/surface_connect.png" width="450"/>

Connected edge (12 total, devided to 3 kinds):

<img src="img/edge_connect.png" width="450"/>

Connected point (8 total):

<img src="img/point_connect.png" width="170"/>

### The Codes
#### How to use

The function takes 7 inputs: mito_meta_file_name, pixel_length, pixel_width, page_thickness, shrinked_ratio, sample_name, and full__h5_directory. Since the h5 file is exported as 2048/2048pixels resolution, the resolution can be different from the original image volume. The "shrinked_ratio" is simply origianl image volume length/2048. The rest of the inputs are pretty self explanitory. An example would be: 
```matlab
mito_skel_length_aspect_ratio_calculator('mouse_mito_meta',16,16,40,2,'mouse','C:\mito_project\mouse_h5_folder\mouse_mito_16nm.h5')
```

#### Getting Started 

First, access the cvs file and extract index and bounding box coordinates, and convert them to 2 arrays.
```matlab
mito_meta = readtable(mito_meta_file_name);
mito_index = table2array(mito_meta(:,1));
bbox_array = table2array(mito_meta(:,19:24));
```
Next, remove the categorical parent sections, which only serve as folders for other sections and are not actually drawn. The bounding box values for these sections are all -1.
```matlab
probelm = [-1 -1 -1 -1 -1 -1];
while ismember(probelm,bbox_array,'rows') == 1
    rowindex = find(ismember(bbox_array,probelm,'rows')>0);
    bbox_array(rowindex(1),:)=[];
    mito_index(rowindex(1),:)=[];
end
```
Also calculate the potential distancse in nanometers between two adjacent voxels, based on pixel length, pixel width, page thickness, and connectivity. 
```matlab
voxel_length = pixel_length*shrinked_ratio; 
voxel_width = pixel_width*shrinked_ratio; 
voxel_height = page_thickness; 
edge_dist_a = sqrt(voxel_length^2+voxel_width^2);
edge_dist_b = sqrt(voxel_length^2+voxel_height^2);
edge_dist_c = sqrt(voxel_width^2+voxel_height^2);
point_dist = sqrt(voxel_length^2+voxel_width^2+voxel_height^2);
```

#### Aspect Ratio 

##### Calculation Function:

Each ratio is calculated with the larger value devided by the smaller value. 
```matlab
  function d = get_ratio(a,b)
        if a>b
            d = a/b;
        else
            d = b/a;
        end
    end
```

##### Getting Aspect Ratios
This program calculates the aspect ratios of the bounding boxes and not the mitochondria themselves. And the lengths, widths, and heights of the bounding boxes can be mathmetically calculated with the data provided by `mito_meta`. 

Since the bounding box's side lengths are number of pixels, to calculate the true ratio of each mitochondria, the x length needed to be multiplied with `pixel_length`, as the y length with `pixel_width` and z length with `page_thickness`. After the calculation of each bounding box, the 3 aspect ratios are appended to an array as a vector. 
 
```matlab
x_true_dist = (bbox_array(index,4)-bbox_array(index,1))*pixel_length;
y_true_dist = (bbox_array(index,5)-bbox_array(index,2))*pixel_width;
z_true_dist = (bbox_array(index,6)-bbox_array(index,3))*page_thickness;
xy_ar = get_ratio(x_true_dist,y_true_dist);
xz_ar = get_ratio(x_true_dist,z_true_dist);
yz_ar = get_ratio(y_true_dist,z_true_dist);
aspect_ratios = [aspect_ratios;
                  [mito_index(index) xy_ar xz_ar yz_ar]];
```

#### Skeleton Length

##### Extracting the Boundary Box and Generating the Skeleton
Reading the full h5 file is way too much work. Therefore, for each mitochondrion, this program only read the part of the h5 defined by the mitochondrion's bounding box. Also due to the sheer sizes of h5 files, the image stacks are shrinked from their origin dimentions to 2048 pixel x 2048 pixel x original page number. Therefore, to find the start and distance in the h5 files, this program needs to devide the data from mito meta with the shrinked ratio, or the `h5read()` would have exceed limit errors. And since the h5 files are generated with a python program, whose index starts with 0, this program, a matlab program, whose index starts with 1, need to +1 to the values to eliminate 0 values.

The approximate bounding box, which is a 3d array, needs to be binarized according to the greyscale index of the mitochondrion in each loop, since skeletonization can only be used in binarized arrays/images. Afterwards, the skeleton of the mitochondrion can be generated with just a single line of code. 

```matlab
x_start = floor(bbox_array(index,1)/shrinked_ratio)+1;
y_start = floor(bbox_array(index,2)/shrinked_ratio)+1;
z_start = bbox_array(index,3)+1;
x_distance = floor((bbox_array(index,4)-bbox_array(index,1))/shrinked_ratio)+1;
y_distance = floor((bbox_array(index,5)-bbox_array(index,2))/shrinked_ratio)+1;
z_distance = bbox_array(index,6)-bbox_array(index,3)+1;
start = [x_start y_start z_start];
count =[x_distance y_distance z_distance];
bbox = h5read(full__h5_directory,'/images',start,count);
bi_bbox = bbox == mito_index(index);
skel = Skeleton3D(bi_bbox);
```



