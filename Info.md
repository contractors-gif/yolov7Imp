Implementation of Yolov7 following [Yolov7 for Drone Detection](https://github.com/doguilmak/Drone-Detection-YOLOv7/tree/main) and using this [Yolov7 Implementation](https://github.com/WongKinYiu/yolov7). It should be noted that most of the files such as the `README.md` is copied from this repo.
# Changes Made to Original Files
Issues were found with outdated Pytorch code, so a few of the `.py` files had to be changed by adding `weights_only = False` to any `torch.load()` statements. The following files were changed to reflect this:
- `detect.py,train.py,train_aux.py,experimental.py,datasets.py,hubconf.py,general.py` 
This could lead to security issues so other, more in depth changes may need to be made if security needs to be improved.

Another other change was in `requirements.txt` where `numpy>=1.18.5` was changed to `numpy>=1.18.5,<2.4.0` to accommodate for current Python version (Python 3.14.2).

To train the model on specific drone class, the `coco.yaml` needs to be changed for datasets and class names to just one class for drone.
- weights for drone specific training can be found in google drive in `yolov7_drone.zip` when unzipped, file will be `best.pt` 
- Since testing on laptops and desktops was done under `yolov7x.pt`, the `colo.yaml` file was not changed to reflect the drone class.
# Steps for Running
## Step 1 (Install Requirements)
If `pip` is recognized:
`pip install -r requirements.txt` 
If `pip` is not:
`python -m pip install -r requirements.txt` 
## Step 2 (Optional: Train)
Can train model to use specific drone class
- change `coco.yaml` to change data set and number of classes (to 1) and class name to `drone` 
	- testing used Kaggle data set
- Training can be done using either `yolov7x.pt` or `coco128.pt` weight files
- `yolov7x.pt` from [Yolov7](https://github.com/WongKinYiu/yolov7/releases/download/v0.1/yolov7x.pt) 
- `coco128.pt` from training on coco128 smaller data set

Use following command for training (make sure to update `.pt` file or file path)
`python train.py --device 0 --batch-size 16 --data data/coco.yaml --img 640 640 --epochs 32 --weights yolov7x.pt --hyp data/hyp.scratch.p5.yaml --name yolov7x`
- `device 0` for using GPU. If using CPU use `device cpu` instead
- if running on limited capacity decrease to `batch-size 8` 

You can now use the `best.pt` file from the training.
## Step 3 (Optional: Test)
Can test model using `drone_short.mp4` in videos folder
`python detect.py --weights best.pt --conf 0.25 --img-size 640 --source videos/drone_short.mp4 --name drone_short_run` 
- make sure to update file path to `best.pt` or whatever weight file being used
## Step 4 (Run on Camera)
Use the following to use GPU and run on device camera
`python detect.py --device 0 --weights ../../best.pt --source 0 --view-img` 
- `--view-img` tag streams live video to ensure correct camera usage. delete if not needed
- `--device 0` specifies GPU, delete tag if using CPU which is default

View all resulting videos in `runs` folder
# Attempts to Improve
Attempts were made to improve model using [SAHI Slicing](https://github.com/obss/sahi) which could slow frame rate but should improve detection on a noisy background.

By following the [Yolov7 with Slicing](https://github.com/kadirnar/Yolov7-SAHI) repo, first attempts were made in Colab. Issues were found getting the model to load properly. It might be an issue with numpy version although after attempts to change this, it could be something else.