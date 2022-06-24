# 一、串口助手是什么？
通过电脑串口（包括USB口）收发数据并且显示的应用软件，一般用于电脑与嵌入式系统的通讯，借助于它来调试串口通讯或者系统的运行状态。也可以用于采集其他系统的数据，用于观察系统的运行情况。

简单的串口助手只能用来接收可发送串口数据，但是当你学会了如何开发串口助手，就可以根据**通信协议**开发**上位机**控制硬件和查看硬件的状态，先介绍如何开发一个简单的串口助手。

# 二、开发环境
>1. Visual Stdio
>2. C#

开发串口助手会用到Winform的相关知识，想要学习Winform的相关知识可以点击链接学习:[http://c.biancheng.net/csharp/winform/](http://c.biancheng.net/csharp/winform/)

# 三、开发步骤
## 1. 串口参数
要连接上串口让并实现串口通信我们首先要**搜索并选择**需要进行通信的串口号，然后**设置**好**波特率、数据位、停止位和检验位**，**串口号、波特率、数据位、停止位和检验位**相当于打开特定串口的简单的密码，设置好之后就能打开串口进行通信。
|名称 |作用 |常用数值 |
|--|--|--|
|[波特率](https://baike.baidu.com/item/%E6%B3%A2%E7%89%B9%E7%8E%87)|**串口通信的速率**（发送和接收各数据位的间隔时间）|4800<br>9600<br>14400 <br>19200<br> 38400 <br>56000<br> 57600 <br>**115200** <br>128000<br> 256000|
|[数据位](https://baike.baidu.com/item/%E6%95%B0%E6%8D%AE%E4%BD%8D)|表示**一组数据实际包含的数据位数**|5<br>6<br>7<br>8
|停止位|停止位在最后，用以标志一个字符传送的结束，表示**数据帧之间的间隔**|1<br>1.5<br>2<br>None
|[校验位](https://baike.baidu.com/item/%E6%A0%A1%E9%AA%8C%E4%BD%8D)|**用于数据验证**|Even<br>Odd <br>None
数据位概述图
![数据位概述图](https://img-blog.csdnimg.cn/1677f1ffd7b34f4b81d39e809897f657.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5Z2a5by655qE5aS05Y-R,size_19,color_FFFFFF,t_70,g_se,x_16)

## 2.UI设计
一个串口助手界面主要由**参数设置、接收框、发送框**三个部分以及一些**控制按钮**组成，参数设置使用**ComboBox**组件实现，接收和发送数据框使用**RichTextBox**组件实现，按钮使用**Button**组件实现，还有串口可以使用自带的**SerialPort**组件。
![在这里插入图片描述](https://img-blog.csdnimg.cn/f29ed39dabdd45f78bd2df71dca5db76.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5Z2a5by655qE5aS05Y-R,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

## 3.创建工程
（1）点击新建项目->选择Windows窗体应用->点击下一步->填写好项目名称和什么位置->点击创建
![在这里插入图片描述](https://img-blog.csdnimg.cn/cdac60f3d0824f47947512532be4288d.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5Z2a5by655qE5aS05Y-R,size_20,color_FFFFFF,t_70,g_se,x_16)
![在这里插入图片描述](https://img-blog.csdnimg.cn/c35953ca3c824f34801820c8b503bec3.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5Z2a5by655qE5aS05Y-R,size_20,color_FFFFFF,t_70,g_se,x_16)
（2）实现UI
创建好项目后左边是工具栏，可以直接拖拉组件，里面有我们要用到的**Label、ComboBox、RichTextBox、Button**组件，可以根据字母排序查找需要的组件。右边是用来设置组件参数，按F4可以出现该框，可以设置组件**名称、文件内容、颜色、大小、位置、字体、图标等参数**，点击**闪电标志**里面可以设置绑定事件。
![创建好项目](https://img-blog.csdnimg.cn/44edd1d55c5c4ef29cc9c7f54d1319e6.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5Z2a5by655qE5aS05Y-R,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
注意UI实现的过程中要设置好组件的Name，下面是我设置的一些Name参数
| 组件 | Name |
|--|--|
| **ComboBox** |comboBox_PortNames <br> comboBox_BaudRate<br> comboBox_DataBit<br>comboBox_StopBit <br>comboBox_Parity <br> |
|**RichTextBox**|richTextBox_Receive<br>richTextBox_Send|
|**Button**|button_Serach<br>button_OpenOrClose<br>button_CLEAR<br>button_Send|
![注意问题](https://img-blog.csdnimg.cn/3366c3c648354ed5bd08f9de57d61e24.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5Z2a5by655qE5aS05Y-R,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
串口波特率、数据位、停止位和校验位参数可以预先设置好，这里给出波特率参数设置，其他参数设置的方法一样
![在这里插入图片描述](https://img-blog.csdnimg.cn/70b584312bb840dd9a3c9368688efe43.gif)
SerialPort组件使用方法
![请添加图片描述](https://img-blog.csdnimg.cn/0a47353ae564431f872fedec21815ebf.gif)
基本实现好的UI（审美不在线），大家可以自行优化
![串口助手](https://img-blog.csdnimg.cn/94ebc7244f3f46cbba9d233e09436d74.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5Z2a5by655qE5aS05Y-R,size_20,color_FFFFFF,t_70,g_se,x_16)
点击启动的运行效果
![运行效果](https://img-blog.csdnimg.cn/fdd96169b7e94b53be87d22890224c95.gif)
（3）功能代码实现
代码要实现的功能主要有：
![在这里插入图片描述](https://img-blog.csdnimg.cn/c9ed46674758475d82a5048a1f3e16ec.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5Z2a5by655qE5aS05Y-R,size_20,color_FFFFFF,t_70,g_se,x_16)
- 1.初始化函数
编写初始化函数时要在Form1.Designer.cs文件的Windows 窗体设计器生成的代码里面添加`this.Load += new System.EventHandler(this.Form1_Load);`如下图所示
![在这里插入图片描述](https://img-blog.csdnimg.cn/48c22adfbab749658220c94760a91ce0.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5Z2a5by655qE5aS05Y-R,size_20,color_FFFFFF,t_70,g_se,x_16)
```csharp
		/**************************************************************************/
        #region 初始化窗体
        private void Form1_Load(object sender, EventArgs e) //加载界面程序
        {
            try
            {
                string[] str = SerialPort.GetPortNames();   //获取连接到电脑的串口号并存进数组
                comboBox_PortNames.Items.Clear();           //清除串口号下拉框的内容
                comboBox_PortNames.Items.AddRange(str);     //将串口号添加到下拉框

                if (str.Length > 0)
                {
                    comboBox_PortNames.SelectedIndex = 0;   //设置ComboBox框的初始值
                    comboBox_BaudRate.SelectedIndex = 7;
                    comboBox_DataBit.SelectedIndex = 3;
                    comboBox_StopBit.SelectedIndex = 1;
                    comboBox_Parity.SelectedIndex = 2;
                }
                else
                {
                    MessageBox.Show("当前无串口连接！");
                }
            }
            catch
            {
                MessageBox.Show("无串口设备!/r/n请检查是否连接设备!/r/n请检查设备驱动!");
            }
        }
        #endregion
        /**************************************************************************/
 ```
- 2.搜索串口	
```csharp
/**************************************************************************/
        #region 搜索串口（与初始化函数一样）
        private void button_Serach_Click(object sender, EventArgs e)
        {
            try
            {
                string[] str = SerialPort.GetPortNames();
                comboBox_PortNames.Items.Clear();
                comboBox_PortNames.Items.AddRange(str);

                if (str.Length > 0)
                {
                    comboBox_PortNames.SelectedIndex = 0;
                    comboBox_BaudRate.SelectedIndex = 7;
                    comboBox_DataBit.SelectedIndex = 3;
                    comboBox_StopBit.SelectedIndex = 1;
                    comboBox_Parity.SelectedIndex = 2;
                }
                else
                {
                    MessageBox.Show("当前无串口连接！");
                }
            }
            catch
            {
                MessageBox.Show("无串口设备!/r/n请检查是否连接设备!/r/n请检查设备驱动!");
            }
        }
        #endregion
        /**************************************************************************/
```
- 3.打开/关闭串口
```csharp
/**************************************************************************/
        #region 打开串口
        private void button_OpenOrClose_Click(object sender, EventArgs e)
        {
            if (!serialPort1.IsOpen)
            {
                if (comboBox_PortNames.SelectedItem == null)
                {
                    MessageBox.Show("请选择正确的串口", "提示");
                    return;
                }

                //设置串口参数
                serialPort1.PortName = comboBox_PortNames.Text.ToString();  //serialPort1是serialPort组件的Name 
                serialPort1.BaudRate = Convert.ToInt32(comboBox_BaudRate.SelectedItem.ToString());
                serialPort1.DataBits = Convert.ToInt32(comboBox_DataBit.SelectedItem.ToString());
                
                //设置停止位
                if (comboBox_StopBit.Text == "One")
                {
                    serialPort1.StopBits = StopBits.One;
                } else if (comboBox_StopBit.Text == "Two")
                {
                    serialPort1.StopBits = StopBits.Two;
                } else if (comboBox_StopBit.Text == "OnePointFive")
                {
                    serialPort1.StopBits = StopBits.OnePointFive;
                } else if (comboBox_StopBit.Text == "None")
                {
                    serialPort1.StopBits = StopBits.None;
                }
                
                //设置奇偶校验位
                if (comboBox_Parity.Text == "Odd")
                {
                    serialPort1.Parity = Parity.Odd;
                }
                else if (comboBox_Parity.Text == "Even")
                {
                    serialPort1.Parity = Parity.Even;
                }
                else if (comboBox_Parity.Text == "None")
                {
                    serialPort1.Parity = Parity.None;
                }

                try
                {
                    //禁止操作组件
                    comboBox_PortNames.Enabled = false;
                    comboBox_BaudRate.Enabled = false;
                    comboBox_DataBit.Enabled = false;
                    comboBox_StopBit.Enabled = false;
                    comboBox_Parity.Enabled = false;
                    button_Serach.Enabled = false;

                    serialPort1.Open(); //设置完参数后打开串口
                    button_OpenOrClose.Text = "Close";  //更改Open按钮文本内容
                }
                catch
                {
                    MessageBox.Show("串口打开失败！");
                }

                
                serialPort1.DataReceived += new SerialDataReceivedEventHandler(SerialDataReceive);  //打开串口后绑定数据接收
            }
            else if (button_OpenOrClose.Text == "Close") 
            {
                try
                {
                    //允许操作组件
                    comboBox_PortNames.Enabled = true;
                    comboBox_BaudRate.Enabled = true;
                    comboBox_DataBit.Enabled = true;
                    comboBox_StopBit.Enabled = true;
                    comboBox_Parity.Enabled = true;
                    button_Serach.Enabled = true;
                    
                    
                    serialPort1.DiscardInBuffer();  //清除缓冲区的数据
                    
                    serialPort1.Close();
                    button_OpenOrClose.Text = "Open";   //更改Close按钮文本内容
                }
                catch
                {
                    MessageBox.Show("串口打开失败！");
                }
            }
        }
        #endregion
        /**************************************************************************/
```
- 4.接收数据
```csharp
/**************************************************************************/
        #region 接收串口数据
        /*
        开辟缓存区 根据具体协议内容获取一帧数据进行接收中断处理
         */
        //变量
        List<byte> sp_buffer = new List<byte>(4096);    //串口缓存区
        int sp_buffer_max = 4096;   //串口缓存区最大缓存字节数

        private void SerialDataReceive(object sender, SerialDataReceivedEventArgs e)
        {
            if (serialPort1.IsOpen == false)
            {
                serialPort1.Close();
                return;
            }

            
            int Byte_len = serialPort1.BytesToRead; //读取缓存的数据长度
            byte[] Rc_byte = new byte[Byte_len];
            
            serialPort1.Read(Rc_byte,0,Byte_len);   //将缓存数据存储进字节数组里面

            if (sp_buffer.Count > sp_buffer_max)    //缓存超过字节数 先丢弃前面的字节  
                sp_buffer.RemoveRange(0,sp_buffer_max); //丢弃前面的字节0到sp_buffer_max

            sp_buffer.AddRange(Rc_byte);    //存入缓存区

            byte[] ruffer = new byte[9192]; //用来存放缓冲区的数据流

            //对数据流进行筛选，缓冲区每一组数据个数大于4则为我们想要的数据流
            if (sp_buffer.Count > 4)    
            {
                sp_buffer.CopyTo(0,ruffer,0,sp_buffer.Count);
                Task.Run(()=>printf_data(ruffer, sp_buffer.Count, 1));  //打印数据流
            }
        }
        #endregion
        /**************************************************************************/
```
- 5.打印数据流
```csharp
/**************************************************************************/
        #region 打印数据流
        void printf_data(byte[] Frame, int Length, int T_R)   //打印串口数据
        {
            Int16 i_len;
            StringBuilder s = new StringBuilder();

            if (T_R == 0)
                s.Append("发送：");
            else
                s.Append("接收：");

            for (i_len = 0; i_len < Length; i_len++)       //打印字符串
            {
                s.Append(Frame[i_len].ToString("X2"));
                s.Append(" ");
            }

            s.Append("[" + DateTime.Now.ToString("HH:mm:ss fff") + "]");
            s.Append("\r\n");

            string str_show = s.ToString();
            MethodInvoker mi = new MethodInvoker(() =>
            {
                if (richTextBox_ReceiveBox.Lines.Count() > 20)
                    richTextBox_ReceiveBox.Clear();
                richTextBox_ReceiveBox.AppendText(str_show);
            });
            BeginInvoke(mi);
            /*
            textBox_com_data.Focus(); //获取焦点
            textBox_com_data.Select(textBox_com_data.TextLength, 0);//光标
            textBox_com_data.ScrollToCaret(); //滚动条*/
        }
        #endregion
        /**************************************************************************/
```
- 6.发送数据
```csharp
/**************************************************************************/
        #region 发送数据
        private void button_Send_Click(object sender, EventArgs e)
        {
            if (serialPort1.IsOpen)
            {
                string[] sendbuff = richTextBox1.Text.Split();  //分割输入的字符串，判断有多少个字节需要发送
                int Buff_Len = sendbuff.Length;
                byte[] buff = new byte[Buff_Len];

                for (int i = 0; i < sendbuff.Length; i++)
                {
                    buff[i] = byte.Parse(sendbuff[i], System.Globalization.NumberStyles.HexNumber); //格式化字符串为十六进制数值
                }

                try
                {
                    serialPort1.Write(buff, 0, buff.Length);    //写数据
                    Task.Run(()=>printf_data(buff, Buff_Len, 0));
                }
                catch
                {
                    MessageBox.Show("发送失败！！");
                }
            }
            else 
            {
                MessageBox.Show("串口未打开！");
            }
            
        }
        #endregion
        /**************************************************************************/
```
- 7.清除接收框
```csharp
/**************************************************************************/
        #region 清除输出框
        private void Clear_Click(object sender, EventArgs e)
        {
            richTextBox_ReceiveBox.Clear();     //清楚数据接收框数据
        }
        #endregion
        /**************************************************************************/
```
功能函数写好后绑定按钮和函数
![在这里插入图片描述](https://img-blog.csdnimg.cn/6defe8152d2e4deda194bdee6b1ac740.gif)
（4）全部代码整合

```csharp
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;
using System.IO.Ports;
using System.IO;


namespace Serial
{
    public partial class Form1 : Form
    {

        public Form1()
        {
            InitializeComponent();

        }

        /**************************************************************************/
        #region 初始化窗体
        private void Form1_Load(object sender, EventArgs e) //加载界面程序
        {
            try
            {
                string[] str = SerialPort.GetPortNames();   //获取连接到电脑的串口号并存进数组
                comboBox_PortNames.Items.Clear();           //清除串口号下拉框的内容
                comboBox_PortNames.Items.AddRange(str);     //将串口号添加到下拉框

                if (str.Length > 0)
                {
                    comboBox_PortNames.SelectedIndex = 0;   //设置ComboBox框的初始值
                    comboBox_BaudRate.SelectedIndex = 7;
                    comboBox_DataBit.SelectedIndex = 3;
                    comboBox_StopBit.SelectedIndex = 0;
                    comboBox_Parity.SelectedIndex = 2;
                }
                else
                {
                    MessageBox.Show("当前无串口连接！");
                }
            }
            catch
            {
                MessageBox.Show("无串口设备!/r/n请检查是否连接设备!/r/n请检查设备驱动!");
            }
        }
        #endregion
        /**************************************************************************/

        /**************************************************************************/
        #region 搜索串口（与初始化函数一样）
        private void button_Serach_Click(object sender, EventArgs e)
        {
            try
            {
                string[] str = SerialPort.GetPortNames();
                comboBox_PortNames.Items.Clear();
                comboBox_PortNames.Items.AddRange(str);

                if (str.Length > 0)
                {
                    comboBox_PortNames.SelectedIndex = 0;
                    comboBox_BaudRate.SelectedIndex = 7;
                    comboBox_DataBit.SelectedIndex = 3;
                    comboBox_StopBit.SelectedIndex = 0;
                    comboBox_Parity.SelectedIndex = 2;
                }
                else
                {
                    MessageBox.Show("当前无串口连接！");
                }
            }
            catch
            {
                MessageBox.Show("无串口设备!/r/n请检查是否连接设备!/r/n请检查设备驱动!");
            }
        }
        #endregion
        /**************************************************************************/

        /**************************************************************************/
        #region 打开串口
        private void button_OpenOrClose_Click(object sender, EventArgs e)
        {
            if (!serialPort1.IsOpen)
            {
                if (comboBox_PortNames.SelectedItem == null)
                {
                    MessageBox.Show("请选择正确的串口", "提示");
                    return;
                }

                //设置串口参数
                serialPort1.PortName = comboBox_PortNames.Text.ToString();  //serialPort1是serialPort组件的Name 
                serialPort1.BaudRate = Convert.ToInt32(comboBox_BaudRate.SelectedItem.ToString());
                serialPort1.DataBits = Convert.ToInt32(comboBox_DataBit.SelectedItem.ToString());

                //设置停止位
                if (comboBox_StopBit.Text == "One")
                {
                    serialPort1.StopBits = StopBits.One;
                }
                else if (comboBox_StopBit.Text == "Two")
                {
                    serialPort1.StopBits = StopBits.Two;
                }
                else if (comboBox_StopBit.Text == "OnePointFive")
                {
                    serialPort1.StopBits = StopBits.OnePointFive;
                }
                else if (comboBox_StopBit.Text == "None")
                {
                    serialPort1.StopBits = StopBits.None;
                }

                //设置奇偶校验位
                if (comboBox_Parity.Text == "Odd")
                {
                    serialPort1.Parity = Parity.Odd;
                }
                else if (comboBox_Parity.Text == "Even")
                {
                    serialPort1.Parity = Parity.Even;
                }
                else if (comboBox_Parity.Text == "None")
                {
                    serialPort1.Parity = Parity.None;
                }

                try
                {
                    //禁止操作组件
                    comboBox_PortNames.Enabled = false;
                    comboBox_BaudRate.Enabled = false;
                    comboBox_DataBit.Enabled = false;
                    comboBox_StopBit.Enabled = false;
                    comboBox_Parity.Enabled = false;
                    button_Serach.Enabled = false;

                    serialPort1.Open(); //设置完参数后打开串口
                    button_OpenOrClose.Text = "Close";  //更改Open按钮文本内容
                }
                catch
                {
                    MessageBox.Show("串口打开失败！");
                }


                serialPort1.DataReceived += new SerialDataReceivedEventHandler(SerialDataReceive);  //打开串口后绑定数据接收
            }
            else if (button_OpenOrClose.Text == "Close")
            {
                try
                {
                    //允许操作组件
                    comboBox_PortNames.Enabled = true;
                    comboBox_BaudRate.Enabled = true;
                    comboBox_DataBit.Enabled = true;
                    comboBox_StopBit.Enabled = true;
                    comboBox_Parity.Enabled = true;
                    button_Serach.Enabled = true;


                    serialPort1.DiscardInBuffer();  //清除缓冲区的数据

                    serialPort1.Close();
                    button_OpenOrClose.Text = "Open";   //更改Close按钮文本内容
                }
                catch
                {
                    MessageBox.Show("串口打开失败！");
                }
            }
        }
        #endregion
        /**************************************************************************/

        /**************************************************************************/
        #region 接收串口数据
        /*
        开辟缓存区 根据具体协议内容获取一帧数据进行接收中断处理
         */
        //变量
        List<byte> sp_buffer = new List<byte>(4096);    //串口缓存区
        int sp_buffer_max = 4096;   //串口缓存区最大缓存字节数

        private void SerialDataReceive(object sender, SerialDataReceivedEventArgs e)
        {
            if (serialPort1.IsOpen == false)
            {
                serialPort1.Close();
                return;
            }


            int Byte_len = serialPort1.BytesToRead; //读取缓存的数据长度
            byte[] Rc_byte = new byte[Byte_len];

            serialPort1.Read(Rc_byte, 0, Byte_len);   //将缓存数据存储进字节数组里面

            if (sp_buffer.Count > sp_buffer_max)    //缓存超过字节数 先丢弃前面的字节  
                sp_buffer.RemoveRange(0, sp_buffer_max); //丢弃前面的字节0到sp_buffer_max

            sp_buffer.AddRange(Rc_byte);    //存入缓存区

            byte[] ruffer = new byte[9192]; //用来存放缓冲区的数据流

            //对数据流进行筛选，缓冲区每一组数据个数大于4则为我们想要的数据流
            if (sp_buffer.Count > 4)
            {
                sp_buffer.CopyTo(0, ruffer, 0, sp_buffer.Count);
                Task.Run(() => printf_data(ruffer, sp_buffer.Count, 1));  //打印数据流
            }
        }
        #endregion
        /**************************************************************************/

        /**************************************************************************/
        #region 打印数据流
        void printf_data(byte[] Frame, int Length, int T_R)   //打印串口数据
        {
            Int16 i_len;
            StringBuilder s = new StringBuilder();

            if (T_R == 0)
                s.Append("发送：");
            else
                s.Append("接收：");

            for (i_len = 0; i_len < Length; i_len++)       //打印字符串
            {
                s.Append(Frame[i_len].ToString("X2"));
                s.Append(" ");
            }

            s.Append("[" + DateTime.Now.ToString("HH:mm:ss fff") + "]");
            s.Append("\r\n");

            string str_show = s.ToString();
            MethodInvoker mi = new MethodInvoker(() =>
            {
                if (richTextBox_ReceiveBox.Lines.Count() > 20)
                    richTextBox_ReceiveBox.Clear();
                richTextBox_ReceiveBox.AppendText(str_show);
            });
            BeginInvoke(mi);
            /*
            textBox_com_data.Focus(); //获取焦点
            textBox_com_data.Select(textBox_com_data.TextLength, 0);//光标
            textBox_com_data.ScrollToCaret(); //滚动条*/
        }
        #endregion
        /**************************************************************************/

        /**************************************************************************/
        #region 发送数据
        private void button_Send_Click(object sender, EventArgs e)
        {
            if (serialPort1.IsOpen)
            {
                string[] sendbuff = richTextBox_Send.Text.Split();  //分割输入的字符串，判断有多少个字节需要发送
                int Buff_Len = sendbuff.Length;
                byte[] buff = new byte[Buff_Len];

                for (int i = 0; i < sendbuff.Length; i++)
                {
                    buff[i] = byte.Parse(sendbuff[i], System.Globalization.NumberStyles.HexNumber); //格式化字符串为十六进制数值
                }

                try
                {
                    serialPort1.Write(buff, 0, buff.Length);    //写数据
                    Task.Run(() => printf_data(buff, Buff_Len, 0));
                }
                catch
                {
                    MessageBox.Show("发送失败！！");
                }
            }
            else
            {
                MessageBox.Show("串口未打开！");
            }

        }
        #endregion
        /**************************************************************************/

        /**************************************************************************/
        #region 清除输出框
        private void Clear_Click(object sender, EventArgs e)
        {
            richTextBox_ReceiveBox.Clear();     //清楚数据接收框数据
        }
        #endregion
        /**************************************************************************/
    }
}
```
（5）实现效果
![在这里插入图片描述](https://img-blog.csdnimg.cn/7540ec93dcc14984a820b61badf79358.gif)
到这我们就完整地做一个简单的串口助手了，我们可以进行二次开发，添加按钮绑定特定的的指令，每次可以通过点击按钮控制硬件，或者对接收到的数据流进行解析并显示出来，这样就能够作出项目需要的上位机了。

最后，这是我第一次写博客，如果文件内容有什么错误的地方或者有什么不足的地方，希望各位大佬能帮忙指点一下。


