# lilygo_epd47_hc08_mic_voice_txt

LilyGo-EPD47 麦克风语音记事留言

<b>一.程序功能</b> <br/>
     语音留言，利用到了百度语音转换文字的服务，识别出的文字显示到墨水屏,墨水屏断电后不影响继续显示能力<br/>
     每段录音最长10秒(可调长), 一次记事约40-50字上下,正好一屏,基本够用. 平均一段录音的文字识别时间约2-5秒，取决于网络速度<br/>
     这是简化DEMO版本, 高级版本可设计记录很多留言,按日期定位,通过按钮翻页显示等，视情况决定是否继续开发<br/>
<br/>     
<b>二.硬件</b> <br/>
   1.LILYGO® T5-4.7 inch E-paper ESP32 电子墨水屏<br/>
       资料位置:  https://github.com/Xinyuan-LilyGO/LilyGo-EPD47<br/>       
   2.上述电子墨水屏专用的 HC08+MIC 模块<br/>
         lilygo-epd47  PDM麦克风<br/>
             VCC3.3    VCC<br/>
             GND       GND<br/>
             12        MIC_DATA<br/>
             13        MIC_CLOCK<br/>
效果图<br/>
<img src= 'https://github.com/lixy123/lilygo_epd47_hc08_mic_voice_txt/blob/main/mic_1.jpg?raw=true' /><br/>
<img src= 'https://github.com/lixy123/lilygo_epd47_hc08_mic_voice_txt/blob/main/mic_2.jpg?raw=true' /><br/>
<br/>
<b>三.软件</b><br/>
软件: arduino 1.8.13<br/>
库文件:<br/>
https://github.com/espressif/arduino-esp32 版本:1.0.6<br/>
https://github.com/Xinyuan-LilyGO/TTGO_TWatch_Library 最新版本, 仅为了用到它定义的开发板<br/>
https://github.com/Xinyuan-LilyGO/LilyGo-EPD47 最新版本<br/>
https://github.com/bblanchon/ArduinoJson 版本: 6<br/>
https://github.com/ivanseidel/LinkedList 最新版本<br/>
开发板选择：TTGO-T-WATCH <br/>
烧录到墨水屏<br/>
编译大小约 3.3M <br/>

<br/>
<b>四.用法</b><br/>
 <b> 1.配置</b><br/>
  ESP32首次运行时会自动初始化内置参数,自动进入路由器模式,创建一个ESP32SETUP的路由器，电脑连接此路由输入http://192.168.4.1 进行配置<br/>
    A.esp32连接的路由器和密码<br/>
    B.百度语音的账号校验码<br/>
      baidu_key: 一个账号字串       (必须注册获得)<br/>
      baidu_secert: 一个账号校验码  (必须注册获得)<br/>
      这两个参数需要注册百度语音服务,在如下网址获取 https://ai.baidu.com/tech/speech     <br/>
      开通中文普通话短语音识别，单次语音最长识别60秒。新用户可免费用一段时间，再用必须开通收费，1000次3.4元左右价位，如果不限制使用，最多有可能每天调用5000-10000次，所以增加了休眠功能控制调用次数,防止欠费<br/>
    C.其它音量监测参数: 默认是在家里安静环境下,如果周围较吵,可以将值调高<br/>
      define_max1 每0.5秒声音峰值（感应人声判断）<br/>
      define_avg1 每0.5秒声音均值（感应人声判断）<br/>
      define_max2 每0.5秒声音峰值（录音中静音判断）<br/>
      define_avg3 每0.5秒声音均值（录音中静音判断）<br/>
      
 <b> 2.运行</b><br/>
    A.上电或按RESET按钮<br/>
    B.对着墨水屏说话<br/>
    C.墨水屏将语音识别出来的文字显示<br/>
    D.20分钟如果无新识别到文字, 自动进入休眠模式节能。<br/>
    E.墨水屏休眠时，按左数第5个按钮, RESET按钮唤醒墨水屏重新工作 <br/>
    F.墨水屏工作状态时，按左数第一按钮主动进入休眠<br/>
    G.墨水屏工作状态时，按左数第2按钮清空历史文字显示<br/>
        
<br/>
<b>五.软件算法原理:</b><br/>
  1.esp32上电后实时读取MIC声音信号，当检测到周围声音强度达到足够音量设定后进入录音模式<br/>
  2.录音模式中，如检测到连续3秒音量低于设定,录音结束，否则一直录音，至多10秒停止录音，<br/>
  3.声音数据增加wav文件头信息，按百度语音转换协议传到百度云服务<br/>
  4.百度云服务返回识别的文字，将文字显示墨水屏<br/>  
  有效识别距离 1-4米 ，最佳距离<1米内，>4米识别率难保证.<br/>
  
<br/>
<b>六.其它技巧</b><br/>
  1.wav采集的数字声音数据一般是正数，负数成对生成。有点像水波振动，以数字0为基线上下跳动,静音时数值为0.<br/>
  2.程序会预存2秒的声音，这2秒不仅用于检测声强，也会用于文字识别。保证监听小于2个字的短语不会丢失声音数据.<br/>
  3.最佳声音数据格式: 16khz 16位 wav数据, 此格式百度文字识别效果最合适，也不会造成声音数据量过大, 8khz 8位wav 格式识别效果很差<br/>
<br/>
<b>七.工作用电:</b><br/>
  5v 70-100ma电流
