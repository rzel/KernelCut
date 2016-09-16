This is the codes for the paper:

	"Normalized Cut Meets MRF"
	Meng Tang, Dmitrii Marin, Ismail Ben Ayed, Yuri Boykov
	In European Conference on Computer Vision (ECCV), Amsterdam, the Netherlands, October, 2016
  
## Motion Segmentation using KernelCut ##
Input image frames: directory "motionsegmentation/ducks01/images"  
Initial Strokes for the first frame: directory "motionsegmentation/ducks01/seedsmulti"  

Build dependency libraries (maxflow and easybmp)  
```{r, engine='bash'}
cd libs
make all
```
Build main program
```{r, engine='bash'}
cd ../kernelcut
make main
cd ../
```
Download executable for optical flow
```{r, engine='bash'}
wget http://lmb.informatik.uni-freiburg.de/resources/binaries/pami2010Linux64.zip
unzip pami2010Linux64.zip -d libs/LDOF
```
Compute optical flow
```{r, engine='bash'}
chmod +x libs/LDOF/ldof
matlab -nojvm -nosplash -nodisplay -r "cd motionsegmentation/ducks01; computeopticalflow"
```
Compute KNN graph for joint LAB + XY + M space
```{r, engine='bash'}
matlab -nojvm -nosplash -nodisplay -r "cd motionsegmentation/ducks01; getsubpixelimages"
matlab -nojvm -nosplash -nodisplay -r "cd motionsegmentation/ducks01; computeknn"
```
Go to motionsegmentation/motion.sh, change codepath, and run script
```{r, engine='bash'}
chmod +x ./motionsegmentation/motion.sh
./motionsegmentation/motion.sh
```
Output segmentations are in the directory "motionsegmentation/ducks01/output".

## Image Clustering using Kernel Cut and Spectral Cut##
download images:  

    cd imageclustering
    wget http://people.csail.mit.edu/torralba/code/spatialenvelope/spatial_envelope_256x256_static_8outdoorcategories.zip
    unzip spatial_envelope_256x256_static_8outdoorcategories.zip -d images
    mv images/spatial_envelope_256x256_static_8outdoorcategories/* images/
    
download feature extractor:  

    http://vision.stanford.edu/projects/objectbank/MATLAB_release.zip
    unzip MATLAB_release.zip -d ./
    mv MATLAB_release object-bank
extract features and compute Gaussian kernel:  

    matlab -nojvm -nosplash -nodisplay -r "computegaussiankernel"
compute eigenvalues:  

    matlab -nojvm -nosplash -nodisplay -r "eigen_labelme"
prepare ground truth labels:  

    matlab -nojvm -nosplash -nodisplay -r "preparelabels"
spectral clustering:  

    matlab -nojvm -nosplash -nodisplay -r "sc_labelme"
kernel k-means:  

    matlab -nojvm -nosplash -nodisplay -r "kkm_labelme"
kernel cut:  

    matlab -nojvm -nosplash -nodisplay -r "kcut_labelme"

