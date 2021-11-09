## Mitochondria Skeleton Length Calculator 

You can download the .m file [here](https://www.mathworks.com/matlabcentral/fileexchange/101579-mitochondria-s-skeleton-length-and-aspect-ratio-calculator) and install it as an app to access the function.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

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

For more details see [Basic writing and formatting syntax](https://docs.github.com/en/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/Anominious/Themed-Web/settings/pages). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://support.github.com/contact) and we’ll help you sort it out.
