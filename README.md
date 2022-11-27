# DELTA_SU
Nicholas Kohl, Environmental Engineering Master Thesis. Comparison of Worldview, UAVSAR, and satellite images in detecting flood using NASA DELTA script for machine learning.

Pre Process
The images are downloaded by https://hddsexplorer.usgs.gov for worldview images, 9 images will be used for image training, 3 for validation and one for testing. Labels are created using ArcGIS Pro, using DEM elevation data, original image and watershed boundary to determine flood extent.

Train and validation
The train and validation are performed through a command called "delta". For more information, check https://github.com/nasa/delta. Different yaml file define how to resample the image, the tile size for the machine learning, and definition of each class.
