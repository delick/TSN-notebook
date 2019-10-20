# TSN-notebook
Notes for running temporal segment network.

## Getting docker image
```bash
docker pull bitxiong/tsn
```

## Run bash commands inside docker images
### Run images with bash enabled.
```bash
sudo docker run -it bitxiong/tsn /bin/bash
```

### Then run the building scripts to build the libraries.
```bash
bash build_all.sh
```

### Find `CONTAINER ID` of that image.
```bash
sudo docker ps
```
and returns:
```
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
5fca2dec30f6        bitxiong/tsn        "/bin/bash"         20 minutes ago      Up 20 minutes                           xenodochial_booth
```

### Data preprocessing
#### Copy data into docker image.
The container id is `5fca2dec30f6`, so now let's copy rar files into it. Navigate to the folder where `UCF101.rar` exists and open terminal there:
```bash
sudo docker cp UCF101.rar 5fca2dec30f6:/app/ucf101/ucf101.rar
```

#### Extract files from archive
```bash
# Install unrar package.
apt install unrar
# Extract files.
unrar x UCF101.rar
```

### Extract frames and Optical Flow Images
Decompose videos into frames to run the training and testing. Also the temporal stream networks need optical flow or warped optical flow images for input.
The script has three arguments

- `SRC_FOLDER` points to the folder where you put the video dataset
- `OUT_FOLDER` points to the root folder where the extracted frames and optical images will be put in
- `NUM_WORKER` specifies the number of GPU to use in parallel for flow extraction, must be larger than 1

In this case, type:
```bash
bash scripts/extract_optical_flow.sh /app/ucf101/UCF-101/ /app/ucf101/UCFCUT/ 1
```

### Construct file lists for training and validation
A list file looks like
```
video_frame_path 100 10
video_2_frame_path 150 31
...
```

To build the file lists for all 3 splits of the two benchmark dataset, use the following command:
```bash
bash scripts/build_file_list.sh ucf101 /app/ucf101/UCFCUT/
```
The generated list files will be put in `data/` with names like `ucf101_flow_val_split_2.txt`.
