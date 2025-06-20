# NeRF 3D场景重建项目

本项目基于NeRF(Neural Radiance Fields)实现，使用PyTorch在自定义数据集上训练3D场景重建模型。

## 环境配置
- **Python 3.8+**
- 关键依赖库：
pip install torch torchvision scikit-image imageio matplotlib configargparse


## 数据集准备
1. 将自定义数据集放置在`data/nerf_llff_data/test/`目录下
2. 确保包含以下文件结构：
├── images/ # 场景图像（JPG/PNG格式）
├── images_8/
├── sparse/
└── poses_bounds.npy # 相机位姿与边界数据

## 训练与测试
### 1. 启动训练
python run_nerf.py --config configs/test.txt --spherify --no_ndc

### 2. 恢复中断训练
python run_nerf.py
--config configs/test.txt
--spherify
--no_ndc
--ft_path logs/test/[checkpoint].tar # 例如：logs/test/100000.tar

text

### 3. 测试渲染
python run_nerf.py --config configs/test.txt --spherify --no_ndc --render_only --render_test

text

## 关键参数说明
| 参数 | 说明 |
|------|------|
| `--config` | 配置文件路径 |
| `--spherify` | 球形场景优化（适用于360°场景） |
| `--no_ndc` | 禁用NDC坐标变换（推荐真实场景使用） |
| `--ft_path` | 恢复训练时的检查点路径 |
| `--render_only` | 仅渲染模式（不训练） |
| `--render_test` | 渲染测试集而非训练集 |

## 结果可视化
1. 渲染图像：在`logs/test/render_test/`中查看生成的PNG序列
2. 使用FFmpeg创建视频：
ffmpeg -framerate 30 -pattern_type glob -i '*.png' -c:v libx264 output.mp4

text

## 常见问题
**Q1: 训练中断后如何续训？**  
A: 使用`--ft_path`指向最新的`.tar`检查点文件（如`100000.tar`）

**Q2: 显存不足如何处理？**  
A: 在`configs/test.txt`中调整：
batch_size = 1024 # 减小批大小
netdepth_fine = 6 # 简化精细网络
N_samples = 64 # 减少采样点

text

**Q3: 如何生成新视角视频？**  
A: 创建相机路径文件，运行时添加：
--render_path --camera_path [path_to_poses.npy]

text

> 注：完整参数说明请查阅`run_nerf.py`中的`config_parser()`函数
