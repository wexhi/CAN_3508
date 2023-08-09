# CAN_3508
## 1��PWM���Ƶ��
### 1.1��PWM���Ƶ����ԭ��
PWM���Ƶ����ԭ����ͨ���ı�����ռ�ձ����ı�����ת�٣�  
ռ�ձ�Խ�󣬵��ת��Խ�죬ռ�ձ�ԽС�����ת��Խ����
### 1.2��PWM���Ƶ����ʵ��
ͨ���޸ĺ���```Motor_Control(int pwm1, int pwm2)```��ֵ�����Ƶ����ת�٣�pwm1��pwm2�ķ�Χ��-10000~10000
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
## 2��CANͨ��
### 2.1��CANͨ�ŵ�ԭ��
CANͨ����һ�ִ���ͨ��Э�飬����һ�ָ߿ɿ��Ե�ͨ�ŷ�ʽ������ͨ�����ʸߣ�����������ǿ��  
�ܹ�ʵ�ֶ��ͨ�ţ�����ڹ�ҵ��������õ��˹㷺��Ӧ�á�
### 2.2��CANͨ�ŵ�ʵ��
CANͨ�ŵ�ʵ����Ҫ�������֣�һ���Ƿ��Ͷˣ�һ���ǽ��նˣ����Ͷ�ͨ��CAN���߷������ݣ����ն�ͨ��CAN���߽������ݡ�
#### 2.2.1�����Ͷ�
���Ͷ�ͨ��CAN���߷������ݣ����Ͷ˵Ĵ������£�
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
����3508����Ĵ������£�
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
���Ը���std_id���Ʋ�ͬ�ĵ����std_id�ķ�Χ��0x200~0x205��buff�ĳ�����8��
