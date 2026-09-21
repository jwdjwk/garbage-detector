
基于 **YOLOv8 + ByteTrack** 的实时垃圾分类检测与统计系统。上传图片或视频，自动检测垃圾物体并按中国国标四大类（可回收物、厨余垃圾、有害垃圾、其他垃圾）分类统计。

![Python](https://img.shields.io/badge/Python-3.10+-blue)
![PyTorch](https://img.shields.io/badge/PyTorch-2.5+-ee4c2c)
![YOLOv8](https://img.shields.io/badge/YOLOv8-8.4+-green)
![CUDA](https://img.shields.io/badge/CUDA-12.1-red)
![License](https://img.shields.io/badge/License-MIT-yellow)

## ✨ 功能特性

- 🎯 **多类别垃圾检测**：支持纸板、玻璃、金属、纸张、塑料、厨余、电池等 8 类常见垃圾
- 📊 **国标分类统计**：自动映射到可回收物 / 厨余垃圾 / 有害垃圾 / 其他垃圾四大类，输出饼图与柱状图
- 🎥 **视频追踪计数**：基于 ByteTrack 的多目标追踪，跨帧保持 ID，统计进出垃圾数量
- ⚡ **GPU 加速推理**：RTX 4050 上推理速度 4.8ms/帧（~200 FPS）
- 🌐 **Gradio Web Demo**：上传图片/视频即可在线体验，无需配置环境

## 📂 项目结构

```
garbage-detector/
├── README.md
├── requirements.txt
├── .gitignore
├── LICENSE
├── configs/
│   └── default.yaml          # 训练与推理配置
├── src/
│   ├── __init__.py
│   ├── train.py              # 模型训练入口
│   ├── detect.py             # 单图/批量检测
│   ├── track.py              # 视频多目标追踪
│   ├── counter.py            # 垃圾分类统计逻辑
│   └── export.py             # ONNX / TensorRT 导出
├── scripts/
│   ├── download_data.py      # 数据集下载脚本
│   ├── extract_and_prepare.py # 数据集解压整理脚本
│   └── auto_split.py          # 数据集自动划分脚本
├── app.py                    # Gradio Web Demo
├── docs/                     # 训练曲线、性能对比截图
└── demo/                     # 演示图片与 GIF
```

## 🚀 快速开始

### 环境要求

- Python 3.10+
- NVIDIA GPU（RTX 4050 实测可用）
- CUDA 12.1

### 安装

```bash
git clone https://github.com/yourname/garbage-detector.git
cd garbage-detector

# 创建虚拟环境
conda create -n garbage python=3.10 -y
conda activate garbage

# 安装 PyTorch (CUDA 12.1)
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu121

# 安装其他依赖
pip install -r requirements.txt
```

### 数据集

使用 ModelScope 上的公开垃圾分类数据集（~12000 张图）：

```bash
# 下载数据集
git clone https://www.modelscope.cn/datasets/YizheDev/trash_detection.git data/garbage

# 解压并整理
python scripts/extract_and_prepare.py

# 自动划分 train/val/test
python scripts/auto_split.py
```

### 训练模型

```bash
yolo train model=yolov8s.pt data=data/garbage/garbage.yaml epochs=100 imgsz=640 project=runs name=garbage
```

### 单图检测

```bash
yolo predict model=runs/garbage/weights/best.pt source=path/to/image.jpg
```

### 视频追踪与计数

```bash
yolo track model=runs/garbage/weights/best.pt source=path/to/video.mp4 tracker=bytetrack.yaml
```

### 启动 Web Demo

```bash
python app.py
```

## 📊 检测类别

| 检测类别 | 英文名 | 国标大类 | 颜色标识 |
|---------|--------|---------|---------|
| 纸板 | cardboard | 可回收物 | 🔵 蓝色 |
| 玻璃 | glass | 可回收物 | 🔵 蓝色 |
| 金属 | metal | 可回收物 | 🔵 蓝色 |
| 纸张 | paper | 可回收物 | 🔵 蓝色 |
| 塑料 | plastic | 可回收物 | 🔵 蓝色 |
| 厨余垃圾 | food_waste | 厨余垃圾 | 🟢 绿色 |
| 有害垃圾 | battery | 有害垃圾 | 🔴 红色 |
| 其他垃圾 | other | 其他垃圾 | ⚪ 灰色 |

## 📈 训练结果

### 数据集信息

| 划分 | 图片数量 |
|------|---------|
| 训练集 | 8,886 |
| 验证集 | 1,750 |
| 测试集 | 1,761 |
| **总计** | **12,397** |

### 模型性能（YOLOv8s, 100 epochs, RTX 4050）

| 指标 | 数值 |
|------|------|
| Precision | 0.98 |
| Recall | 0.96 |
| mAP@0.5 | **0.98** |
| mAP@0.5:0.95 | **0.93** |
| 推理速度 | 4.8ms/帧 |
| FPS | ~200 |

### 训练曲线

![训练曲线](docs/results.png)

## 🛠️ 技术栈

- [Ultralytics YOLOv8](https://github.com/ultralytics/ultralytics) - 目标检测框架
- [PyTorch](https://pytorch.org/) - 深度学习框架
- [ByteTrack](https://github.com/ifzhang/ByteTrack) - 多目标追踪
- [Gradio](https://www.gradio.app/) - Web Demo
- [OpenCV](https://opencv.org/) - 图像处理
- [ModelScope](https://www.modelscope.cn/) - 数据集平台

## 📝 TODO

- [ ] 添加 ONNX / TensorRT 导出与加速对比
- [ ] 添加更多垃圾细分类别（灯管、药品、烟蒂等）
- [ ] 部署到 HuggingFace Spaces
- [ ] 添加摄像头实时检测模式
- [ ] 输出分类统计 Excel 报表

## 📄 License

MIT License
