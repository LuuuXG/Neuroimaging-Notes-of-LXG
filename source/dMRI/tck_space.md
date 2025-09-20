# 纤维束空间转换

问题：Mrtrix3追踪得到的.tck纤维束文件在DSI studio中打开无法对齐。适合需要用DSI studio可视化的情况。

解决方案：问题核心是DSI studio使用LPS空间，而且会修改原始的空间信息。

具体步骤：

1. Mrtrix3得到的.tck是按照空间位置（mm）存储的，应用图像的affine的inverse矩阵转换到体素坐标（voxel）

2. 将上一步得到的体素坐标乘上DSI studio得到图像的affine矩阵，再次转换到mm空间，并处理不是等体素的情况。