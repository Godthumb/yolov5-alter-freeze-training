# 交替训练策略
## 策略来源
[ECV 2021 冠军方案解读：占道经营识别方案](https://mp.weixin.qq.com/s/e07eRbNAkoDVRs7Q-rV0TA)

## 策略具体步骤
![策略步骤](/assets/640.jpeg "策略步骤")
- 第一步：从训练数据中随机选取一半，进行yolov5常规训练，该过程中所有的参数是同时更新的。最后获得在a榜上最好模型model1。

- 第二步分布迭代1就是采用的冻结模块进行，数据是随机选取训练数据的一半，预训练模型是第一步常规训练的最好模型，按照冻结训练方式进行训练。这个流程循环多次，获得model2。

- 第三步分布迭代2同样采用的是冻结模块进行，数据是所有训练数据，由于参数已经学过，这时我们将学习率调小一个量级，同样也是按照冻结训练方式进行训练。这个流程只循环一次，获得最终的模型。

## 冻结模块
冻结模块的特点就是将backbone和head轮流冻结，每epoch只更新未冻结部分的参数。我们以5个epoch为一个阶段，第一个epoch为head冻结，只训练backbone；第2~5个epoch为backbone冻结，只训练head。这样轮流更新backbone和head的参数

## 实现方法
实现方法很简单，修改train.py和utils/dataloaders.py即可。
- train.py修改140行和276行以及499行，具体参考修改后代码
- dataloaders.py修改647行

## 训练方法
训练方法同yolov5, 增加参数freeze-alter, 打开该参数则可实现上述策略， 默认前100个epoch执行策略步骤1