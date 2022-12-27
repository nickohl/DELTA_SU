# DELTA_SU
Nicholas Kohl, Environmental Engineering Master Thesis. Comparison of Worldview, UAVSAR, and satellite images in detecting flood using NASA DELTA script for machine learning.

Pre Process
The images are downloaded by https://hddsexplorer.usgs.gov for worldview images, 9 images will be used for image training, 3 for validation and one for testing. Labels are created using ArcGIS Pro, using DEM elevation data, original image and watershed boundary to determine flood extent.

Train and validation
The train and validation are performed through a command called "delta". For more information, check https://github.com/nasa/delta. Different yaml file define how to resample the image, the tile size for the machine learning, and definition of each class.


Nicholas Kohl
CEE609
Comparison of three aerial images for flood detection
2. Data and Methods

2.1 Raw Data
Data for this study consists of three different types of satellite and aerial view, Worldview, UAVSAR, and Sentinel-1. Data were acquired on August 31, 2017, from NASA satellite missions and ESA after hurricane Harvey hit Houston, TX. UAVSAR images are available at https://uavsar.jpl.nasa.gov, to download them on the website the date needs to be specified, and they will appear on the map. Click on one and tap “view” will give the option to download. The filed consist of an ANN file and metadata file. Sentinel-1 Images can be retrieved at https://scihub.copernicus.eu/dhus/#/home, and their download has a similar process than UAVSAR. The area of interest needs to be specified, because there are much more images available on a single day basis. Lastly, Worldview Images are available at https://hddsexplorer.usgs.gov . 

2.2 Data Processing 
Worldview (WV) images are from Landsat8, a multispectral imaging satellite. Clouds and three canopies interfere with this type of satellite, therefore extra attention is needed when labelling this image. Worldview images consist of two different image type, the first one is the real image collected by the satellite, the second one is a label file that was pre-processed creating four different classes: water, land, trees, and clouds. Both were used for the pixel editing process. Each image was uploaded on ArcGIS Pro, where they were divided into subsets based on the watershed, they were in. It was determined to use watershed HUC-12(Hydrologic Unit Code), as they were the best size based on the map extent. The number of sub-maps for each WV image was between 5 and 16, where 5 was closer to the coast and 15 in the inland. For each submap, using a Digital Elevation Model (DEM) and the original two files, it was determined the highest elevation of flooded water. From there, the label map was created, with everything under that elevation as water and the rest as land. All sub-maps were then mosaicked together, and resampled to the WV image pixel size, before the pixel editing process. Using elevation as a parameter defined most of the flooded area, but not a perfect labelling of it. Pixel editing was used to correctly label the open area, changing the value if the WV images clearly showed no flooding. For forested area, more attention was taken in the labelling, with aid of the DEM and original images to determine if there was water under the tree canopies or not. The final labels are defined as WV_Label.
	UAVSAR images need multiple software for processing. After being downloaded online, the ANN file was used in ASF Map Ready to extract 9 bands from it. Of these 9, 6 bands were used for the study, HHHH, HVHV, VVV and the real part of HHHV, HHVV, and HVVV. The six different bands were than uploaded on ArcGIS, where using the composite bands tool were merged in a single raster file. Each image was then clipped to the same extent as the previously mentioned WV_Label, as a single UAVSAR image is much larger than all the others. WV_Label was also resampled to a UVSR_label file, as the spatial extent between the two images is different. 
Sentinel-1 images had an easier processing workflow, they were added to ArcGIS and clipped like UAVSAR data, and then a S1_Label was created to match the pixel size of it.

2.3 Model Training
To train the model it will be used a process developed by NASA called DELTA (Deep Earth Learning, Tool, and Analysis). The original process is available at https://github.com/nasa/delta. The process consists of a Convolutional Neural Network (CNN), which is used for image recognition of features. In this case, the scripts used are trained to classify images between land, water, cloud, urban area, flooded vegetation and others. To go through this process two different files are needed, in our case the original image, which is the train file, and the label image, created with the pixel editing in ArcGIS as described before. Three different yaml file are used for the model training. l8_cloud_dataset specify which files are to be used, and in what directory are, and define the different classes that compose the raster data. l8_cloud_train_network contains the training network for the image training, and l8_cloud_train_parameters contain the parameters to use for the image classification. Both image and label file are used in this process. This step produced a trained model valuable for the validation. The image and label file are both used to determine what the values of reflectance from satellite images correspond, therefore a precise label will give a more precise trained model. For this study, which consists of 13 images, 9 will be used for model training, 1 will be used for testing the process, and 3 will be used for model validation.

2.4 Model Validation
	Model validation is the last step, and it is used to determine the accuracy of the CNN by “feeding” it a satellite or aerial image but no corresponding label. A new image containing labels will be created by the validation process which will determine the accuracy of it. Correction can be made to the scripts to label certain parameters based on what is of interest. The validation process will give information on the frequency of each value, and its overall precision in recognizing different parameters. The validation overall score is based on how many images are used and how variegate each image is. An image with a lot of well-defined figures will give a better training network, which will result in better labelling during the validation process.

3. Discussion
During the pre-process phase, there were many differences between the images that would suggest which one will result more suitable for the machine learning. The worldview image was the one with the smallest spatial extent, meaning that the mislabeling might be more likely during the ArcGIS steps. However, the size of the images was the best size, with sentinel-1 covering a smaller area and UAVSAR covering a much larger area. As the labels were created starting from worldview images, the prediction is the training will give a better result with this image, respect to the other two.
The problem with UAVSAR images was also that they were divided in multiple passages, and the technology creates interference and incorrect pixel values when analyzing the raw data. Mosaicking the 4 or 5 different parts of an image together would create a less accurate training model. The best option therefore was clipping the label to only one of the image and perform the training with only limited data. A similar study by Denbina et al of 2020, which used UAVSAR data for CNN from Hurricane Harvey as well, had problem with backscatter angles from open water, as well as urban area. They also compared to NOAA imagery, and their classification was around 80% accurate. Their suggestion was to consider the angles during classification or perform more training.
Sentinel-1 images had no issue with the pixel value, but as said before for UAVSAR images most of the labels created were cut out to match the extent of the image, losing useful data for the machine learning.

















References
Cheng, X., Pinto, N., Gong, J. (15 January 2013). Terrain Radiometric Calibration of airborne UAVSAR for forested area. Geo-Spatial Information Science. 15:4, 229-240. Retrieved at https://www.tandfonline.com/doi/full/10.1080/10095020.2012.745050
Denbina, M, Towfic, Z.J., Thill, M., Bue, B., Kasraee, N., Peacock, A., Lou, Y. (2020). Flood mapping using UAVSAR and convulational neural networks. 2020 IEEE International Geoscience and Remote Sensing Symposium. Retrieved at https://ieeexplore.ieee.org/abstract/document/9324379?casa_token=uT1GjwzQs-IAAAAA:sGmGyvvb0sO1xBbO4edHaKzEFgtsv4EKVwiO6AUXqiEv0HO8QoxtqRb9VCfB7NMc1_NPq_DBXw
Fore, A.G., Chapman, B.D., Hawkins, B.P., Hensley, S., Jones, C.E., Michel, T.R., Muellerschoen, R.J. (6 June 2015). UAVSAR Polarimetric Calibration. IEEE Transactions on Geoscience and Remote Sensing. 53. Retrieved at https://uavsar.jpl.nasa.gov/science/documents/UAVSAR_calibration.pdf
Gebremichael, E., Molthan, A.L., Bell, J.R., Schultz, L.A., Hain, C. (1 November 2020). Flood Hazard and Risk Assessment of Exterme Weather Events Using Synthetic Aperture Radar and Auxiliary Data: A case Study. Remote Sensing. 12(21):3588. Retrieved at https://www.mdpi.com/2072-4292/12/21/3588.
Hensley et al. (May 2008). The UAVSAR Instrument: Description and First Results. 2008 IEEE Radar Conference. Retrieved at https://ieeexplore.ieee.org/abstract/document/4720722?casa_token=c4STaHXWl_AAAAAA:xN2WvTeY2Si1A0gF2QEu9AYd8yjWcUQ0aGr9Uf8B-LGGdtUBRdifzUeVCBFP90qocXGJbnefrg
Hensley, S., Zebker, H., Jones, C., Michel, T., Muellerschoen, R., Chapman, B. (2009). First deformation results using the NASA/JPL UAVSAR instrument. 2009 2nd Asian-Pacific Conference on Synthetic Aperture Radar. 1051-1055. Retrieved at https://ieeexplore.ieee.org/abstract/document/5374246?casa_token=bvo7z_2Ybo8AAAAA:fMD_0Rx7X_B3Uj4CwPNZDTO0-1WCPPLtJhRQTBEg-DHYvGvoU7KlaY1h0NgQHIucn5973QipuA
Houstache, R. et al. (2 July 2018). Near-Real-Time Assimilation of SAR-Derived Flood Maps for Improving Flood Forecasts. Water Resource Research. 54(8). Retrived at https://agupubs.onlinelibrary.wiley.com/doi/full/10.1029/2017WR022205
Kundu, S., Lakshmi, V., Torres, R. (17 March 2022). Flood Depth Estimation during Hurricane Harvey using Sentinel-1 and UAVSAR data. Remote Sensing. Retrieved at https://www.mdpi.com/2072-4292/14/6/1450
Malenovsky, Z. et al. (15 May 2012). Sentinels for science: Potential of Sentinel-1, -2, and -3 missions for scientific observations of ocean, cryosphere, and land. Remote Sensing of Environment. 120, 91-101. Retrieved at https://www.sciencedirect.com/science/article/pii/S0034425712000648?casa_token=N4XgrN8DYmQAAAAA:Iphv8I_sChiCBAxnNWXcYGFqLXKbL95bagS4VKJR1Ei2yC4-oGV7M4bIaWxwasH60Cw43wjAsNo
Molthan, A., Bell, J. (10 December 2018). NASA Earth Science Activities supporting Analysis and Responses to the 2018 Hurricane Season. NASA. Retrieved at https://ntrs.nasa.gov/api/citations/20180008807/downloads/20180008807.pdf.
Palomba, G., Farasin, A., Rossi, C. (May 2020). Sentinel-1 Flood delineation with Supervised Machine Learning. Research Gate. Retrived at https://www.researchgate.net/publication/344391106_Sentinel-1_Flood_Delineation_with_Supervised_Machine_Learning. 
Rahnemoonfar, M. et al. (2021). Floodnet: A High Resolution Aerial Imagery Dataset for Post Flood Scene Understanding. IEEE Access. 9, 89644-89654. Retrieved at https://ieeexplore.ieee.org/abstract/document/9460988
Saksena, S., Merwade, V. (1 October 2015). Incorporating the effect of DEM resolution and accuracy for improved flood inundation mapping. Journal of Hydrology. Retrieved at https://www.sciencedirect.com/science/article/pii/S0022169415007520?casa_token=iU9KlNQEvr0AAAAA:YlAb5i9H3vd6PzftBdXI17kQUKRnTFwcsOAVN99lWSth8d0D8TvWka2Bx7RHMDPAARBL-rC5guY.
 Wang, C. et al. (14 Februart 2022). Flood extent mapping during hurricane Florence with repeat L-band UAVSAR images. Water Resource Research. Retrieved at https://agupubs.onlinelibrary.wiley.com/doi/full/10.1029/2021WR030606?casa_token=gAruDEyInSsAAAAA%3A7UINekEOJR7h3khP1jATrfeuFCCWJWQdOT977jW3GE8KlLEiw6HSqCN7MnG2INkm1VbfQ6C2Y3G7afE.

