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
