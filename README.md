# CAN_3508
## 1、PWM控制电机
### 1.1、PWM控制电机的原理
PWM控制电机的原理是通过改变电机的占空比来改变电机的转速，  
占空比越大，电机转速越快，占空比越小，电机转速越慢。
### 1.2、PWM控制电机的实现
通过修改函数```Motor_Control(int pwm1, int pwm2)```的值来控制电机的转速，pwm1和pwm2的范围是-10000~10000
```c
void Motor_Left(int pwm)
{
	if (pwm > 0) // forward
	{
		__HAL_TIM_SET_COMPARE(&htim2, TIM_CHANNEL_1, pwm);
		__HAL_TIM_SET_COMPARE(&htim2, TIM_CHANNEL_2, 0);
	}
	else if(pwm < 0) //back
	{
		__HAL_TIM_SET_COMPARE(&htim2, TIM_CHANNEL_1, 0);
		__HAL_TIM_SET_COMPARE(&htim2, TIM_CHANNEL_2, -pwm);
	}
	else // stop
	{
		__HAL_TIM_SET_COMPARE(&htim2, TIM_CHANNEL_1, 0);
		__HAL_TIM_SET_COMPARE(&htim2, TIM_CHANNEL_2, 0);
	}
}

void Motor_Right(int pwm)
{
	pwm = -pwm;	
	if (pwm > 0) // forward
	{
		__HAL_TIM_SET_COMPARE(&htim2, TIM_CHANNEL_3, pwm);
		__HAL_TIM_SET_COMPARE(&htim2, TIM_CHANNEL_4, 0);
	}
	else if(pwm < 0) //back
	{
		__HAL_TIM_SET_COMPARE(&htim2, TIM_CHANNEL_3, 0);
		__HAL_TIM_SET_COMPARE(&htim2, TIM_CHANNEL_4, -pwm);
	}
	else // stop
	{
		__HAL_TIM_SET_COMPARE(&htim2, TIM_CHANNEL_3, 0);
		__HAL_TIM_SET_COMPARE(&htim2, TIM_CHANNEL_4, 0);
	}
}

void Motor_Control(int pwm1, int pwm2)
{
	// if (pwm1 == pwm2 ) go straight
	// if (pwm1 > pwm2) turn right
	// if (pwm1 < pwm2) turn left
	Motor_Left(pwm1);
	Motor_Right(pwm2);
	
}
```
## 2、CAN通信
### 2.1、CAN通信的原理
CAN通信是一种串行通信协议，它是一种高可靠性的通信方式，它的通信速率高，抗干扰能力强，  
能够实现多机通信，因此在工业控制领域得到了广泛的应用。
### 2.2、CAN通信的实现
CAN通信的实现需要两个部分，一个是发送端，一个是接收端，发送端通过CAN总线发送数据，接收端通过CAN总线接收数据。
#### 2.2.1、发送端
发送端通过CAN总线发送数据，发送端的代码如下：
```c
uint8_t CAN1_Send_Msg(uint16_t std_id, uint8_t *buff)
{
	
    can1_tx_header_msg.DLC = 8;
    can1_tx_header_msg.StdId = std_id;
    can1_tx_header_msg.ExtId = 0;
    can1_tx_header_msg.IDE = CAN_ID_STD;
    can1_tx_header_msg.RTR = CAN_RTR_DATA;
 
    if (HAL_CAN_AddTxMessage(&hcan1, &can1_tx_header_msg, buff, (uint32_t *)CAN_TX_MAILBOX0) != HAL_OK)
    {
        return 0;
    }
    return 1;
}
```
控制3508电机的代码如下：
```c
void Motor_Control_CAN(uint16_t std_id, uint8_t *buff)
{
	for (int i = 0; i < 7; i += 2)
	{
		buff[i] = buff[i] >> 8;
	}
	CAN1_Send_Msg(std_id, buff);
}
```
可以根据std_id控制不同的电机，std_id的范围是0x200~0x205，buff的长度是8，
