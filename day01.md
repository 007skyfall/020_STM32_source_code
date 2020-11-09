





#day01
##ARM体系结构
1、R13  SP指针的使用
2、要掌握 NVIC的概念，主要用于中断管理
3、M0 用的THumb指令
4、32个中断用于片内外设使用，还有一些不可屏蔽得异常，属于内核，RESET和systick
5、工作模式处理模式和线程模式
6、中断优先级可以配置
7、STM32产品命名规范，作为工程师来讲，重点关注内存大小！！
##开发环境搭建
1、搭建STM32CUBEMX
2、搭建keil开发环境
3、安装STLINK驱动
4、安装CH340G驱动
5、STM32启动流程
	- 5.1 获取SP指针，
	- 5.2 跳转到RESET，
	- 5.3 运行SystemInit初始化系统时钟
	- 5.4 运行C库__main初始化C运行环境
	- 5.5 最终执行main函数
##GPIO编程
###点亮LED
功能：完成跑马灯功能
1.new project
2.选择STM32F051K8U6
3.配置PB2 PB1 PB0 为输出模式
4.配置 SWD接口
5.配置工程名称为LED
6.添加点灯代码
```C
while（1）{
    HAL_GPIO_WritePin(GPIOB, LED4_Pin|LED3_Pin|LED2_Pin, GPIO_PIN_RESET);
    HAL_Delay(1000);
    HAL_GPIO_WritePin(GPIOB, LED4_Pin|LED3_Pin|LED2_Pin, GPIO_PIN_SET);
    HAL_Delay(1000);
}
```
###按键功能
功能：
	当按键按下时，LED2亮
	当按键松开时，LED2灭
1.配置PA8 为输入模式
2.添加按键检测代码
```c
while(1) {
	  //1、判断按键是否按下
	  if(HAL_GPIO_ReadPin(KEY_GPIO_Port,KEY_Pin) == GPIO_PIN_SET){
	  
	  //2、点亮led2
		  HAL_GPIO_WritePin(LED2_GPIO_Port,LED2_Pin,GPIO_PIN_RESET);
	  //3、等待松开
	while(HAL_GPIO_ReadPin(KEY_GPIO_Port,KEY_Pin) == GPIO_PIN_SET){}

}
```
##串口编程
###串口回显功能
功能：
	串口接收任意字节，回显任意字节
1.配置PA10 PA9 为串口功能 
2.使能串口为异步通信模式
3.配置波特率为115200
2.添加串口回显代码
```c
	while(!(huart1.Instance->ISR&(1<<5))){}
	huart1.Instance->TDR = huart1.Instance->RDR;
```
###printf重定向功能
```c
int fputc(int ch,FILE *f){
	while(__HAL_UART_GET_FLAG(&huart1,UART_FLAG_TXE) == RESET){}
	huart1.Instance->TDR = ch;
	return ch;
}
int fgetc(FILE *f){
	int ch;
	while(__HAL_UART_GET_FLAG(&huart1,UART_FLAG_RXNE) == RESET){}
	ch = huart1.Instance->RDR;
    	return fputc(ch,NULL);

}
```
###scanf功能验证
```c
	printf("plese input name:\r\n");
	scanf("%s",name);
	printf("plese input age:\r\n");
	scanf("%d",&age);
	printf("name is %s,age is %d\r\n",name,age);
```