﻿本项目使用神经网络算法实现了判别图像中英文字母的功能。
使用方法：在Main函数中指定神经网络的输入层神经元个数、输出层神经元个数和隐藏层个数。然后提供字母A、B和C的位图文件进行训练。训练完成后使用TEST1和TEST2位图进行识别测试。最后可以由用户指定位图文件进行测试。
项目代码说明：
神经网络的核心运算部分（即神经元运算）都写在Neuron类的output的set中。
Brain类定义了[]操作符，主函数调取Brain类的[]方法（如NET[x]）时，实质上是调取_outputs数组中给定索引下标的值（如_outputs[x]）。_outputs数组中存储的是输出层神经元。_outputs[x]会调用该Neuron类实例的output的get方法，其会读取所有指向该神经元的上层神经元的output值，并计算它们的加权和和激励值，作为输出。
对于隐藏层的神经元，依此类推，均读取指向当前神经元的上层神经元的output值，并计算其激励值，作为输出。
对于输入层，由于在Brain类的SetInput方法中会对每个输入层的output进行直接赋值，因此只要SetInput方法被调用过，输入层神经元的Neuron类实例中ret不可能是正无穷大（即初始默认值），所以在读取时直接返回输入值。
在第一次调用SetInput方法前，输入层神经元的output都是0，所以输出都是0.5（考虑到激励值计算公式为(Math.Atan(invar) / Math.PI + 0.5) * this.Parent.Outputmax，而Outputmax为1）。
Neuron类的seed方法是给每个输入随机赋予[-4,4]之间的一个值作为权重；mutate方法则是以一定的概率换掉原有的权重。
Brain类中有random方法调用每个神经元的seed方法；mutate方法调用每个神经元的mutate方法。
Brain类中的Score和score保存神经网络总输出在Main函数中的“得分”（即面对实际问题的实际训练结果），如果“分数”大于之前的神经网络中“分数”最高的（或本次训练是前10次训练以内的，Score更新条件具体见Score的set部分），那么（以Genome类实例的形式）保存当前神经网络的结构到Brain类的answer数组中，同时调用random/mutate方法生成新的神经网络。如果“分数”没有比之前得到的神经网络所得到的好，那么则把神经网络的权重换为原来得到最优“分数”的神经网络，并调用mutate方法进行突变。这部分代码具体见Brain类Score的set部分。