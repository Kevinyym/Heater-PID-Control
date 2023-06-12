# Heater-PID-Control

## 1 目标
- [ ] 实现PID控制加热片温度

## 2 步骤
### 2.1 实现加热片的PWM控制（参考江科协代码）
- [ ] 加热片(电机)的PWM驱动函数能输入PWM的值作为参数(在PID控制的实例中，实际用不到按键)

### 2.2 实现DMA+AD转换功能来进行测温（参考江科协代码）
- [x] 电位计DMA+AD转换驱动函数，通过电压转换能计算出热敏电阻的温度值。
- [x] 热敏电阻的计算公式：T=f(Voltage, Belta)
- [x] DMA

### 2.3 串口通讯（参考江科协代码）
- [x] 串口输出热敏电阻的温度值，连接上位机图形化显示 
```Serial_Printf("Rps: %.2f\n", Rps); //串口输出转速，换行打印```
- [ ] 待完成功能：Kp, Ki, Kd参数通过串口输入，而不用每次更改都编译和下载一遍

### 2.4 PID控制温度
- [ ] 简单粗暴的控制方式，不能让温度稳定在 xxx 度
```
if (Temp > 41) Speed--;
if (Temp < 39) Speed++;
```
- [ ] 热敏电阻PID控制加热片温度
```
float Err=0, LastErr=0, NextErr=0, Add=0, Kp=20, Ki=0.5, Kd=0, POut=0, IOut=0, DOut;
int8_t TotalOut=0;

float PID(float Rps, float Target)
{
	Err = Target - Rps; //计算实际值与目标值的偏差值
	POut = Kp * Err; //计算PID的比例值P的输出值
	IOut += Ki * Err; //计算PID的比例值I的输出值
	DOut = Err - LastErr;
	TotalOut = (int8_t)(POut + IOut + DOut);
	LastErr = Err;
	return TotalOut;
}
```
## 3 定时器表资源表：STM32F103C8T6定时器资源：TIM1,TIM2,TIM3,TIM4
```	
	*功能*	  *定时器*	 *类型*
	闲置	   TIM1	   高级定时器
	PWM	    TIM2    通用定时器	
	Encoder	    TIM3    通用定时器	
	Timer	    TIM4    通用定时器	
```
## 4 接线框图
- [ ] 电机驱动TB6612(确认加热片的额定DC和功率，已有的TB6612是否能用？)
- [ ] 加热棒（额定电压DC 24V, 额定功率65W。为了简化电路，直接从ST-Link取电5V。验证电流：R=U^2/P=24^2/65=8.86 ohm，那么在5V下 I=U/R=5/8.86=0.8A，小于电机驱动的额定1.2A)
- [ ] 热敏电阻（规格NTC 100k, B3950）

![image](https://github.com/Kevinyym/Heater-PID-Control/assets/101639215/5c6804c8-7df8-4b05-86c6-cd1f336e669f)
- [ ] 此处电容可以不接
- [ ] ![image](https://github.com/Kevinyym/Heater-PID-Control/assets/101639215/33b318a5-9fe2-4084-ae33-6b1ef5e0fe64)

