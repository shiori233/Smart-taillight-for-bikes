# 智能自行车尾灯
*本项目采用国产可视化仿真编程软件linkboy进行设计和编程。这个软件貌似使用的是基于python的语言，所以这里把代码贴出的同时po上项目截图。*
***
## 发射端代码
```
#include <system\predefine.txt>
#define $run$ run
#define $board$ NANO
#define $freq$ 16000000
#define $chip$ MEGA328
#define $cpu$ MEGA328
#define $voffset$ 0
#setcpu $chip$
#.$chip$ = #.COM_MCU;
#define $language$ chinese
public CON_S =
#include <Arduino\nanox\MEGA.B>
const uint16 CON_S_simsurport_ID = 0;
CON_S.driver.ID = CON_S_simsurport_ID;
左转.driver.D = CON_S.D5;
#define $language$ chinese
public 左转 =
#include <driver\Key_F\Pack.B>
const uint16 左转_simsurport_ID = 2;
左转.driver.ID = 左转_simsurport_ID;
Send.driver.CSN = CON_S.A5;
Send.driver.CE = CON_S.D2;
Send.driver.MOSI = CON_S.A4;
Send.driver.CLK = CON_S.D3;
Send.driver.IRQ = CON_S.A3;
Send.driver.MISO = CON_S.D4;
#define $language$ chinese
public Send =
#include <Arduino\NRF2401\Pack.B>
const uint16 Send_simsurport_ID = 3;
Send.driver.ID = Send_simsurport_ID;
右转.driver.D = CON_S.D6;
#define $language$ chinese
public 右转 =
#include <driver\Key_F\Pack.B>
const uint16 右转_simsurport_ID = 5;
右转.driver.ID = 右转_simsurport_ID;
#define $language$ chinese
public 延时器 =
#include <software\delayer\delayer.B>
const uint16 延时器_simsurport_ID = 11;
延时器.driver.ID = 延时器_simsurport_ID;

//*******************************************************
OS0 =
#include <system\os\os.txt>
const uint8 x0_OS_R0 = 4; OS0.RUN_NUMBER = x0_OS_R0;
const uint8 x0_OS_R1 = 0; OS0.RUN100us_NUMBER = x0_OS_R1;
const uint8 x0_OS_R2 = 8; OS0.TASK_NUMBER = x0_OS_R2;
#._start_ = main0;
#.OS = OS0;
void main0()
{
	OS_VarInit();
	OS0.OS_init();
	CON_S.OS_init();
	左转.OS_init();
	Send.OS_init();
	右转.OS_init();
	延时器.OS_init();
	OS0.CreateDriver( (#addr CON_S.OS_run), CON_S.OS_time );
	OS0.CreateDriver( (#addr 左转.OS_run), 左转.OS_time );
	OS0.CreateDriver( (#addr Send.OS_run), Send.OS_time );
	OS0.CreateDriver( (#addr 右转.OS_run), 右转.OS_time );
	x_TempAddEvent = OS0.CreateTask( #addr dispatch );
	OS0.Start();
	dispatch:
	forever {
		if( 左转.OS_EventFlag.0(bit) == 1 ) {
			左转.OS_EventFlag.0(bit) = 0;
			if( 左转_按键按下时_enable && x_左转_0_ResCount == 0 ) {
				OS0.AppTCB[OS0.CurrentTaskIndex].e_res_count -> x_左转_0_ResCount;
				x_TempAddEvent = OS0.CreateTask( #addr 左转_按键按下时 ); OS0.TrigEvent( x_TempAddEvent );
			}
		}
		if( 右转.OS_EventFlag.0(bit) == 1 ) {
			右转.OS_EventFlag.0(bit) = 0;
			if( 右转_按键按下时_enable && x_右转_0_ResCount == 0 ) {
				OS0.AppTCB[OS0.CurrentTaskIndex].e_res_count -> x_右转_0_ResCount;
				x_TempAddEvent = OS0.CreateTask( #addr 右转_按键按下时 ); OS0.TrigEvent( x_TempAddEvent );
			}
		}
		CON_S.OS_thread();
		OS0.Schedule();
		CON_S.OS_ClearWatchDog();
	}
}
uint8 x_SYS_PYVM_ResCount;
#param userspaceon
void 右转_按键按下时()
{
	Send.设置第_个数据为_((1s),('R')); //#12 
	Send.发送数据(); //#13 
	延时器.延时_秒((1s)); //#16 
	Send.设置第_个数据为_((1s),('S')); //#17 
	Send.发送数据(); //#18 
}
void 左转_按键按下时()
{
	Send.设置第_个数据为_((1s),('L')); //#7 
	Send.发送数据(); //#8 
	延时器.延时_秒((1s)); //#10 
	Send.设置第_个数据为_((1s),('S')); //#14 
	Send.发送数据(); //#15 
}
#param userspaceoff
uint8 x_TempAddEvent;
uint8 x_左转_0_ResCount;
bool 左转_按键按下时_enable;
uint8 x_右转_0_ResCount;
bool 右转_按键按下时_enable;

void OS_VarInit()
{
	#.SYS_Tick = 1;
	左转_按键按下时_enable = true;
	右转_按键按下时_enable = true;
}

```

***
## 接收端代码
```
#include <system\predefine.txt>
#define $run$ run
#define $board$ NANO
#define $freq$ 16000000
#define $chip$ MEGA328
#define $cpu$ MEGA328
#define $voffset$ 0
#setcpu $chip$
#.$chip$ = #.COM_MCU;
Receive.driver.CSN = CON_R.A2;
Receive.driver.CE = CON_R.D2;
Receive.driver.MOSI = CON_R.A1;
Receive.driver.CLK = CON_R.D3;
Receive.driver.IRQ = CON_R.A0;
Receive.driver.MISO = CON_R.D4;
#define $language$ chinese
public Receive =
#include <Arduino\NRF2401\Pack.B>
const uint16 Receive_simsurport_ID = 0;
Receive.driver.ID = Receive_simsurport_ID;
#define $language$ chinese
public 延时器 =
#include <software\delayer\delayer.B>
const uint16 延时器_simsurport_ID = 1;
延时器.driver.ID = 延时器_simsurport_ID;
屏幕.driver.DIN = CON_R.A5;
屏幕.driver.CS = CON_R.A4;
屏幕.driver.CLK = CON_R.A3;
#define $language$ chinese
public 屏幕 =
#include <common\MAX7219_DOT32X8\Pack.B>
const uint16 屏幕_simsurport_ID = 9;
屏幕.driver.ID = 屏幕_simsurport_ID;
图形显示器.map = 屏幕.map;
#define $language$ chinese
public 图形显示器 =
#include <engine\GUI\icon.B>
const uint16 图形显示器_simsurport_ID = 13;
图形显示器.driver.ID = 图形显示器_simsurport_ID;
#define $language$ chinese
public CON_R =
#include <Arduino\nanox\MEGA.B>
const uint16 CON_R_simsurport_ID = 20;
CON_R.driver.ID = CON_R_simsurport_ID;

//*******************************************************
OS0 =
#include <system\os\os.txt>
const uint8 x0_OS_R0 = 2; OS0.RUN_NUMBER = x0_OS_R0;
const uint8 x0_OS_R1 = 0; OS0.RUN100us_NUMBER = x0_OS_R1;
const uint8 x0_OS_R2 = 8; OS0.TASK_NUMBER = x0_OS_R2;
#._start_ = main0;
#.OS = OS0;
void main0()
{
	OS_VarInit();
	OS0.OS_init();
	CON_R.OS_init();
	Receive.OS_init();
	延时器.OS_init();
	屏幕.OS_init();
	图形显示器.OS_init();
	OS0.CreateDriver( (#addr Receive.OS_run), Receive.OS_time );
	OS0.CreateDriver( (#addr CON_R.OS_run), CON_R.OS_time );
	x_TempAddEvent = OS0.CreateTask( #addr dispatch );
	OS0.Start();
	dispatch:
	forever {
		if( CON_R.OS_EventFlag.1(bit) == 1 ) {
			CON_R.OS_EventFlag.1(bit) = 0;
			if( CON_R_反复执行_enable && x_CON_R_1_ResCount == 0 ) {
				OS0.AppTCB[OS0.CurrentTaskIndex].e_res_count -> x_CON_R_1_ResCount;
				x_TempAddEvent = OS0.CreateTask( #addr CON_R_反复执行 ); OS0.TrigEvent( x_TempAddEvent );
			}
		}
		屏幕.OS_thread();
		CON_R.OS_thread();
		OS0.Schedule();
		CON_R.OS_ClearWatchDog();
	}
}
uint8 x_SYS_PYVM_ResCount;
#param userspaceon
void CON_R_反复执行()
{
	图形显示器.清空(); //#21 
	图形显示器.绘制_到坐标__处((STAY_ON),(0s),(0s)); //#22 
	if( (Receive.读取第_个数据((1s)))==('L') ) { //#4 
		图形显示器.清空(); //#6 
		图形显示器.绘制_到坐标__处((LEFT),(0s),(0s)); //#23 
		延时器.延时_秒((2s)); //#30 
		loop( 4s ) { //#18 
			图形显示器.清空(); //#16 
			延时器.延时_秒((0.4)); //#15 
			图形显示器.绘制_到坐标__处((LEFT),(0s),(0s)); //#14 
			延时器.延时_秒((0.4)); //#17 
		}
	}
	if( (Receive.读取第_个数据((1s)))==('R') ) { //#7 
		图形显示器.清空(); //#2 
		图形显示器.绘制_到坐标__处((RIGHT),(0s),(0s)); //#29 
		延时器.延时_秒((2s)); //#31 
		loop( 4s ) { //#27 
			图形显示器.清空(); //#25 
			延时器.延时_秒((0.4)); //#24 
			图形显示器.绘制_到坐标__处((RIGHT),(0s),(0s)); //#3 
			延时器.延时_秒((0.4)); //#26 
		}
	}
}
#param userspaceoff
[#.code uint8*?] LEFT =
[
0,0,32,0,8,0,0,0,0,
0x00,0x18,0x18,0x3C,0x3C,0x7E,0x7E,0xDB,0xDB,0x99,0x18,0x18,0x18,0x18,0x18,0x18,0x18,0x18,0x18,0x18,0x18,0x18,0x18,0x18,0x18,0x18,0x18,0x18,0x18,0x18,0x00,0x00,
];
[#.code uint8*?] RIGHT =
[
0,0,32,0,8,0,0,0,0,
0x00,0x00,0x18,0x18,0x18,0x18,0x18,0x18,0x18,0x18,0x18,0x18,0x18,0x18,0x18,0x18,0x18,0x18,0x18,0x18,0x18,0x18,0x99,0xDB,0xDB,0x7E,0x7E,0x3C,0x3C,0x18,0x18,0x00,
];
[#.code uint8*?] STAY_ON =
[
0,0,32,0,8,0,0,0,0,
0x00,0xC6,0x89,0x89,0x91,0x62,0x00,0x00,0x81,0xFF,0x81,0x80,0x80,0xC0,0x00,0x7E,0x81,0x81,0x81,0x7E,0x00,0x00,0x0F,0xF0,0x1F,0xF0,0x0F,0x00,0x00,0xBF,0x00,0x00,
];
uint8 x_TempAddEvent;
uint8 x_CON_R_1_ResCount;
bool CON_R_反复执行_enable;

void OS_VarInit()
{
	#.SYS_Tick = 1;
	CON_R_反复执行_enable = true;
}

```
