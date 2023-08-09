# CAN_3508
## 1��PWM���Ƶ��
### 1.1��PWM���Ƶ����ԭ��
PWM���Ƶ����ԭ����ͨ���ı�����ռ�ձ����ı�����ת�٣�ռ�ձ�Խ�󣬵��ת��Խ�죬ռ�ձ�ԽС�����ת��Խ����
### 1.2��PWM���Ƶ����ʵ��
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
