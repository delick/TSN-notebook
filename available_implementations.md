

# [two stream action recognition](https://github.com/jeffreyyihuang/two-stream-action-recognition)

**本项目不支持 Python 3**，项目基于 Python 2.7

- 空间流（TSN）
- 时间流（two-stream CNN）

## 主要依赖项

- PyTorch (torch==0.4.1)
- pickle
- numpy
- CUDA

# [two stream PyTorch](https://github.com/bryanyzhu/two-stream-pytorch)

- OS: Ubuntu 16.04
- Python: 3.5 / 2.7
- CUDA: 8.0
- OpenCV3
- dense_flow (分支 opencv-3.1)

需要安装带有 opencv_contrib 的 OpenCV3.

# [mmaction](https://github.com/open-mmlab/mmaction)

## 依赖项

- Linux
- Python 3.5+
- PyTorch 1.0+
- CUDA 9.0+
- NVCC 2+
- GCC 4.9+
- ffmpeg 4.0+
- [mmcv](https://github.com/open-mmlab/mmcv).
  需要从 master 分支克隆到本地重新编译。
- [Decord](https://github.com/zhreshold/decord)
  可选，已包括在子模块中，用于视频文件的训练，比 OpenCV 快
- [dense_flow](https://github.com/yjxiong/dense_flow)
  可选，已包括在子模块中，用于计算视频光流，依赖 OpenCV

## 安装 MMAction
- 安装 Cython
```shell
pip install cython
```
- 编译 CUDA 扩展
```shell
./compile.sh
```
- 安装 mmaction
```shell
python setup.py develop
```

请参考 [DATASET.md](https://github.com/open-mmlab/mmaction/blob/master/DATASET.md) 熟悉数据准备， [GETTING_STARTED.md](https://github.com/open-mmlab/mmaction/blob/master/GETTING_STARTED.md) 使用 MMAction
