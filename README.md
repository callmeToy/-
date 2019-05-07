# -
定位综述 目前用于人脸定位的有两种主流方法，一种是基于级联形状回归模型，另一种就是基于深度学习的方法。级联形状回归方法就是使用回归模型，直接学习从人脸表征到人脸形状的映射函数，进而建立从表观到形状的对应关系。这种方法的学习依赖于训练集的选取，如果训练集中包含了复杂的姿态变化，学习到的函数测试性能就会比较好。现有很多基于回归的方法，其中比较突出的有颜水成的DCR（Deep Cascaded Regression）、ESR（Explicitly Shape Regression）方法、LBF（Local Binary Features）方法以及SDM（Supervised Decent Method）方法。但是，上述方法也存在一定的问题，例如，基于提取到的局部图像特征来定位时很难找到具有全局信息的关键点。基于点分布模型的方法和基于形状回归的方法都很依赖于初始值，其中，初始值通常由训练集的平均形状来给定，如果初始值或者设定的平均形状远远偏离目标位置，很难收敛到正确位置。比如，训练集中大部分都是正脸，那么对于测试集中大偏转角度人脸图像的定位就比较困难。 于是引入深度学习来解决较大面部偏转姿态的回归问题，深度学习的最大的优点是有强大的表达能力，可以自学习图像的特征，不需要人为的寻找特征。目前用的基本网络有（1）级联方式：由粗到精一级一级的优化前一步得到的形状，比如香港中文大学汤晓鸥老师的研究团队采用深度卷积神经网络的3个级联结构，逐步细化特征点位置。（2）沙漏网络：密集堆叠连接的U-Nets来进行人脸关键点定位，如CU-Nets通过卷积-反卷积，下采样与上采样网络，跨越不同U-Nets进行全局梯度传播，融合多尺度特征，不断迭代优化关键点坐标。但是深度学习方法的缺点是模型过于复杂，参数非常多，耗时比较长。 深度学习的国内外方法优化：（1）最早的有将人脸进行五官分区域定位回归，但很容易使最终结果陷入局部最优值而不是全局最优。（2）heatmap热图的方法，对数据标签进行处理生成高斯热图，从而更好的回归关键点位置。（3）风格聚合方法，对数据集进行处理生成不同风格的图片，以应对复杂的真实环境如光照强弱。（4）基于边缘感知的人脸关键点检测算法，首先通过消息传递并结合对抗学习得到高精度的边缘线检测结果，再将边缘线信息融合到关键点检测中，来提升算法在大侧脸、夸张表情、遮挡、模糊等极端情况下的鲁棒性。（5）3D人脸模型方法，一种方式是结合3D人脸姿态估计与投影来确定特征点初始位置，然后使用经典的回归树集成ERT方法来更好的进行位置回归；另一种是直接从单幅人脸图像中同时回归出3D人脸结构和密集对齐点；该类方法通过回归位置图，来获得3D几何以及语义信息。（6）人脸特征点检测与形状拟合方法，基于“特征提取”+“回归坐标”的方法，把形状拟合的坐标回归问题，转化为坐标PCA压缩后系数与形状整体仿射变换系数的回归问题（相当于将传统的SDM算法用于深度学习算法当中）。 目前定位方法的改进是： 1.基于深度学习的方法：改进如下： （1）受人体关键点定位 难例挖掘方法的启发，在全局的globalnet之后继续对损失较大的点继续进行微调。 （2）并行集成的方式同时训练两个网络globalnet、refinenet，测试时，第一个网络输出的l2 loss 取top k  loss作为refinenet的辅助损失继续微调回归关键点位置。（在300w 有缺失块的数据集上最终的测试结果，平均定位误差为5.9%）  300-W数据库： 68点定位库，包含4个数据集，比如AFW，LFPW，HELLE，还包含135张IBUG里面的图片，IBUG图片对于定位来说都是非常具有挑战性的，不管是旋转角度还是遮挡程度都是非常大的。我们用HELEN的2000张，LFPW的811张，AFW的337张，总共3148张图片作为训练集。用HELEN的554张，IBUG的135张图片，总共689张图片作为测试集。一般测试集分为3个标准：简单，有挑战，全集。简单的测试集就是前面554张图片，有挑战的就是后面那135张图片，全集即689张图片。对三个测试集分别测试，得出测试结果，并与其它方法进行比较。测试标准为定位的平均误差，是被双瞳孔距离归一化后的，平均误差越低代表这一算法越好。
