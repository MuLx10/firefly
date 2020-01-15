# firefly


## To build the package

```bash
$ cd firefly
$ make
```
Set `GPU` vars to `1` [here](https://github.com/MuLx10/firefly/blob/238fb2dae08429a6bcca4a8d8af2336ffbb98fe1/firefly/Makefile#L3) if using GPU.

## Download pre-trained weights in firefly/backup/
```bash
$ cd firefly && mkdir backup && cd backup
```
[[thermal]](https://drive.google.com/open?id=14qrmSopyqegqRHrmwrMG86L-yXfWzr-i)
[[rgb]](https://pjreddie.com/media/files/yolov3.weights)

## Predict
```bash
$ cd firefly
```
### To predict on images(thermal)
```bash
$ ./darknet detector test cfg/thermal.data cfg/yolov3-spp-custom.cfg backup/yolov3-spp-thermal.weights <img_path> -dont_show -out result.json 
# Predicted output saved as prediction.jpg
```

### To predict on images(RGB)

```bash
$ ./darknet detector test cfg/coco.data cfg/yolov3.cfg backup/yolov3.weights <img_path> -dont_show -out result.json 
# Predicted output saved as prediction.jpg
```

Instead of supplying an <img_path> on the command line, you can leave it blank to try multiple images in a row. Instead you will see a prompt when the config and weights are done loading:
```bash
$ ./darknet detector test cfg/thermal.data firefly/cfg/yolov3-spp-custom.cfg backup/yolov3-spp-thermal.weights -dont_show
layer     filters    size              input                output
    0 conv     32  3 x 3 / 1   416 x 416 x   3   ->   416 x 416 x  32  0.299 BFLOPs
    1 conv     64  3 x 3 / 2   416 x 416 x  32   ->   208 x 208 x  64  1.595 BFLOPs
    .......
  104 conv    256  3 x 3 / 1    52 x  52 x 128   ->    52 x  52 x 256  1.595 BFLOPs
  105 conv    255  1 x 1 / 1    52 x  52 x 256   ->    52 x  52 x 255  0.353 BFLOPs
  106 detection
Loading weights from yolov3-spp-thermal.weights...Done!
Enter Image Path:
```

### To predict on videos(thermal)
```bash
$ ./darknet detector demo cfg/thermal.data cfg/yolov3-spp-custom.cfg backup/yolov3-spp-thermal.weights <video_file_path>
```

### To predict on videos(RGB)

```bash
$ ./darknet detector demo cfg/coco.data cfg/yolov3.cfg backup/yolov3.weights <video_file_path> -out_filename <output_video_path.avi>
```


### Using Python

```python
from subprocess import Popen, PIPE
thermal = "./darknet detector test cfg/thermal.data cfg/yolov3-spp-custom.cfg backup/yolov3-spp-thermal.weights"
rgb = "./darknet detector test cfg/coco.data cfg/yolov3.cfg backup/yolov3.weights"

pthermal = Popen([thermal], shell=True, stdout=PIPE, stdin=PIPE)
prgb = Popen([rgb], shell=True, stdout=PIPE, stdin=PIPE)

img_type, img_path = raw_input("Enter image type(thermal/rgb) and image path").split(" ")
img_path = img_path + '\n'
#img_path = bytes(img_path, 'UTF-8')  # Needed in Python 3.

result = None
if img_type == "thermal":
    pthermal.stdin.write(img_path)
    pthermal.stdin.flush()
    result = pthermal.stdout.readline().strip()
if img_type == "rgb":
    prgb.stdin.write(img_path)
    prgb.stdin.flush()
    result = prgb.stdout.readline().strip()

print(result)
# Predicted output saved as prediction.jpg
```

## Results

[Results on video](https://drive.google.com/open?id=1tJpNzwDZ-NABUacuZGMHmkAk-GL84206).

![rgb](https://user-images.githubusercontent.com/23444642/72368100-67053900-3723-11ea-8e04-f34b5239a2e9.jpg)
![thermal](https://user-images.githubusercontent.com/23444642/72367294-fad60580-3721-11ea-9eab-d695d6dde416.jpg)
![groundtruth](https://user-images.githubusercontent.com/23444642/72367337-10e3c600-3722-11ea-86fb-9cd6c113ddad.jpeg)

