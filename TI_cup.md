TI_cup
全国大学生电子设计竞赛
# 1.有源二分频音频放大电路
## 1.1题目分析
[题目](https://github.com/LiZi510524/TI_cup/blob/main/B%E9%A2%98-%E6%9C%89%E6%BA%90%E4%BA%8C%E5%88%86%E9%A2%91%E9%9F%B3%E9%A2%91%E6%94%BE%E5%A4%A7%E7%94%B5%E8%B7%AF.pdf)要求制作一个有源分频网络，要求实现音频信号和功率放大

 1. 输入信号频率范围：100Hz ~ 20kHz， 幅度范围：10 ~ 100mV
 2. 输入阻抗大于10K，最大增益不小于46dB
 3. 高通滤波器的-3dB截止频率2kHz，阻带衰减率12dB/倍频程，负载电阻2W
 4. 高通滤波器的-3dB截止频率2kHz，阻带衰减率12dB/倍频程，负载电阻4W
 5. 高（低）通滤波与功率放大电路不允许用成品模块，预处理电路允许使用成品模块
## 1.2方案选择
![](https://i-blog.csdnimg.cn/direct/6c917241f4354ae89cfd8e0abf2acbb3.jpeg#pic_center)
首先将输入信号采用同相比例放大（输入阻抗满足10K）20倍，然后分别接入VGA(AD603)自动增益模块和RMS（AD637)读有效值模块，然后通过STM32或FPGA的ADC读取其有效值，阅读AD603模块的使用说明得到采用程控增益的表达式，DAC输出控制自动增益模块使其始终输出有效值RMS=4V，这样以满足后面负载的要求，然后高低通滤波设计部分推荐几款好用的网站：
 1.[Analog Device](https://tools.analog.com/cn/filterwizard/)
2.[TI Design](https://webench.ti.com/filter-design-tool/filter-response)
设置好需要满足的性能要求,还有低噪声，低功耗等等个性化定制的选项，便可设计出原理图，然后再根据原理图画PCB（当然选择哪款肯定会主推自家的芯片）
最后的功率放大电路在淘宝找一家，便有了原理图画PCB，当然最好买一个实物也就几块钱，尽量看一看上面元件的参数，有些商家的原理图不太正确，需要自己甄别一下
（上面的AD603和AD637模块均采用tb凌智电子家的)
## 1.3实际操作
![高低通滤波+功率放大](https://i-blog.csdnimg.cn/direct/8258a0192ad54b04861c5253bd200255.png#pic_center)
[高低通滤波+功率放大  jic工程文件](https://github.com/LiZi510524/TI_cup/blob/main/1.%E6%9C%89%E6%BA%90%E4%BA%8C%E5%88%86%E9%A2%91%E9%9F%B3%E9%A2%91%E6%94%BE%E5%A4%A7%E7%94%B5%E8%B7%AF/tmp9244.png)
将两部分连在一起集成化程度更高，主要是看起来更好看，在每个点预留有SMA口以及排针，方便调试，当然在最后做出来后还需要反复调试，以实现高性能的高低通滤波，可能还涉及到需要更改电容等等，有必要甚至需要Multisim仿真来找到问题所在。
硬件部分准备好后就开始组装了，负责软件部分的同学也要早早的将程控部分做好工作，然后将自制的高低通滤波+功放模块连起来调试，最后一步步对着题目要求看是否已满足

**程控部分代码：**
```c
adcx=Get_Adc_Average(ADC_CHANNEL_1,10);                             //得到ADC_D转换值                       
temp=(float)adcx*(3.3/4096);			                            //得到ADC电压值
V_adc=temp;                    										//赋值给V_adc          
V_dac=(int)((1.53+0.5*log10(V_adc))/3.3 * 4096.0);					//通过函数拟合得到V_dac的值
HAL_DAC_SetValue(&DAC1_Handler,DAC_CHANNEL_1,DAC_ALIGN_12B_R,V_dac);//设置DAC输出值
```
采频率部分未完待续...

**最后成品**

## 1.4比赛测评
## 1.5经验总结


