# TSN-notebook
Notes for running temporal segment network.

## Getting docker image
```bash
docker pull bitxiong/tsn
```

## Run bash commands inside docker images
### Run images with bash enabled.
```bash
sudo docker run --gpus 1 -it bitxiong/tsn /bin/bash
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

### Start training
Once all necessities ready, we can start training TSN. For this, use the script `scripts/train_tsn.sh`. For example, the following command runs training on UCF101 with rgb input:
```bash
bash scripts/train_tsn.sh ucf101 rgb
```
**Note: mpirun error**

```
logging to logs/ucf101_rgb_split1.log
tee: logs/ucf101_rgb_split1.log: No such file or directory
--------------------------------------------------------------------------
mpirun has detected an attempt to run as root.
Running at root is *strongly* discouraged as any mistake (e.g., in
defining TMPDIR) or bug can result in catastrophic damage to the OS
file system, leaving your system in an unusable state.

You can override this protection by adding the --allow-run-as-root
option to your cmd line. However, we reiterate our strong advice
against doing so - please do so at your own risk.
--------------------------------------------------------------------------
```

Useful links to solve this problem
https://github.com/yjxiong/temporal-segment-networks/issues/222
https://github.com/AlanZhang1995/LeinaoPAI/blob/master/images/temporal-segment-networks2/Dockerfile

The problem can be solved by adding `--allow-run-as-root` to `scripts/train_tsn.sh` file. However, when I tried to run `bash scripts/train_tsn.sh ucf101 rgb`, the docker says:

```bash
F1021 14:42:04.707031  6612 common.cpp:190] Check failed: error == cudaSuccess (35 vs. 0)  CUDA driver version is insufficient for CUDA runtime version
*** Check failure stack trace: ***
    @     0x7f813ae2f5cd  google::LogMessage::Fail()
    @     0x7f813ae31433  google::LogMessage::SendToLog()
    @     0x7f813ae2f15b  google::LogMessage::Flush()
    @     0x7f813ae31e1e  google::LogMessageFatal::~LogMessageFatal()
    @     0x7f813b1b72d2  caffe::Caffe::SetDevice()
    @           0x40985f  train()
    @           0x406fc0  main
    @     0x7f8139d33830  __libc_start_main
    @           0x4074f9  _start
    @              (nil)  (unknown)
--------------------------------------------------------------------------
mpirun noticed that process rank 0 with PID 6612 on node 960c6b4db154 exited on signal 6 (Aborted).
--------------------------------------------------------------------------
```
Possible solution:
https://stackoverflow.com/questions/3253257/cuda-driver-version-is-insufficient-for-cuda-runtime-version

the training will run with default settings on 4 GPUs. Usually, it takes around 1 hours to train the rgb model and 4 hours for flow models, on 4 GTX Titan X GPUs.

The learned model weights will be saved in `models/`. The aforementioned testing process can be used to evaluate them.
