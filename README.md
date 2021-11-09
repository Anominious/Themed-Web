## Mitochondria Skeleton Length Calculator 

You can download the .m file [here](https://www.mathworks.com/matlabcentral/fileexchange/101579-mitochondria-s-skeleton-length-and-aspect-ratio-calculator) and install it as an app to access the function.
### Python Codes
Create a folder in the location of the .ipynb file to store the h5 file that is about to be created.
```python
script_dir = os.path.abspath('') 
results_dir = os.path.join(script_dir, 'mouse_test_h5_folder')
if not os.path.isdir(results_dir):
    os.makedirs(results_dir)
    print(results_dir + '--- did not exist but was created')
```
### Matlab Codes
Access the cvs file and extract index and bounding box coordinates, and convert them to 2 arrays.
```matlab
mito_meta = readtable(mito_meta_file_name);
mito_index = table2array(mito_meta(:,1));
bbox_array = table2array(mito_meta(:,19:24));
```
Use the bounding box coordinates to calculate 3 aspect ratios of each mitochondrion. Append the values in an array.
```matlab
x_true_dist = (floor((bbox_array(index,4)-bbox_array(index,1))/shrinked_ratio))*pixel_length;
y_true_dist = (floor((bbox_array(index,5)-bbox_array(index,2))/shrinked_ratio))*pixel_width;
z_true_dist = (bbox_array(index,6)-bbox_array(index,3))*page_thickness;
xy_ar = get_ratio(x_true_dist,y_true_dist);
xz_ar = get_ratio(x_true_dist,z_true_dist);
yz_ar = get_ratio(y_true_dist,z_true_dist);
aspect_ratios = [aspect_ratios;
                   [mito_index(index) xy_ar xz_ar yz_ar]];
```

