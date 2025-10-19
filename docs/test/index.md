# Air780EPM管脚说明

> 作者：陆相成

## 一，管脚分类；

![](../../../../../合宙通信/文档网站/luatos-doc-pool/docs/root/docs/air780epm/product/image/TZZAb6ljEo8C7XxJUqgcmrmFncg.png)

Air780EPM 共有 109 个管脚，大致可以分为如下几类：  

1. 对内供电输入管脚，VBAT；    

2. 对外供电输出管脚，VDD_EXT;  

3. 开机键管脚，PWRKEY；  

4. 复位键管脚，RESET；  

5. 下载使能管脚，USB_BOOT，也常被简称为 BOOT；    

6. 模数转换管脚，ADC;   
- ADC1；   

    - ADC2；    

    - ADC3；    

    - ADC4；    

    - 

7. USB 管脚；

8. VBUS  ； 
   1. USB_DP；        
      USB_DM ；          

9. SIM 卡管脚，包括 SIM1 和 SIM2；    

   - SIM1；   
     VDD_SIM1；  
     SIM1_DAT；  
     SIM1_CLK；  
     SIM1_RST；        

   - SIM2；  
     VDD_SIM2；  
     SIM2_DAT；  
     SIM2_CLK；  
     SIM2_RST；   

10. 天线管脚，LTE_ANT，4G 和 WiFi Scan 共用；

11. NC 管脚，因为各种原因，或者为了兼容之前，或者为了兼容其它型号，虽然存在但是并没有实际功能的管脚，这些管脚通常被标注为 NC、Reserved、悬空或不标注任何文字；

12. 数字 IO 管脚，比如 GPIO、PWM、UART、ONEWIRE、SPI、I2C、LCD、Camera、CAN 接口等；
    这类管脚，因为最常用的功能是 GPIO，所以也常被称作 GPIO 管脚，实际可以复用为多种功能；
    下表是 LuatOS 已经支持的 IO 管脚的复用关系，我们会在后面的章节里详细介绍。

![](../../../../../合宙通信/文档网站/luatos-doc-pool/docs/root/docs/air780epm/product/image/WjsVbubC4oFIcYxYEA9cZ0BanHc.png)

## 二，对内供电，VBAT；

![](../../../../../合宙通信/文档网站/luatos-doc-pool/docs/root/docs/air780epm/product/image/OVzebXWa3oWQNcxWawGcjUoMnzd.png)

1. 模组管脚：PIN42/43;   

2. 电源特性；    

   电压范围：3.3V-4.35V；  

   电流需求：1A以上，所以通常需要并联搭配220uF电解电容或者22uF钽电容以用于瞬时大电流的输出响应；

3. 电池供电；

   常规型锂电池的放电范围是一般是3.2V-4.2V，为了更好地保护锂电池，一般将放电截止电压规定为3.3V；

   高压型锂电池的放电范围是一般是3.2V-4.35V，一样的原因，为了更好地保护锂电池，一般将放电截止电压规定为3.3V；

   其它类型的电池，比如锂锰电池、锂亚电池，放电截止电压可能低至2.0V，这种类型的电池需要在系统中增加升压DCDC，使其在放电期间供给模组的电压保持在3.3V以上；
      用于这种场景下的升压型DCDC我们使用过的是ETA1161；
     [ETA1161](file/ETA1161 V1.0.pdf){:target="_blank"}

4. 电源供电；

   这里说的电源，指的是类似于充电器这样的、通过AC/DC转换器为系统供电的电源形式，后面我们统一称这种电源形式为直流电源；

   直流电源的供电电压，市场上常见的有5V/9V/12V/18V/24V等，不同的输入电压，选择不同的降压型DCDC；

   DCDC的输出电压，建议设置在3.8V，且需要特别注意的是：
      有些DCDC在上电初始时的输出电压有可能会上窜到超出设置的输出电压值很多，比如，设置输出电压为3.8V，上电初始时的输出电压高达6V甚至7V以上，非常大概率的将模组的芯片或射频PA打坏，务必特别注意！
       用于这种场景下的降压型DCDC我们使用过的是JW5357和JW5103；
     JW5357，用于输入电压低于 18V 以下的场景；  
     [JW5357](file/JW5357HFM_Datasheet_R0.8_EN_20240130.pdf){:target="_blank"} 

- JW5103，用于输入电压低于 36V 以下的场景；  
  [JW5103](JWH5103_Datasheet_R0.21_EN_20230920_for 合宙.pdf){:target="_blank"} 

5. 耐压特性；

   长时间的正常工作，不能高于4.5V，包括偶尔的尖峰电压；
    为了兼容电池和直流电源两种供电形式，电池电压一般低于4.35V，直流电源经过DCDC降压后一般设置为3.8V输出，因此，模组的正常工作电压范围一般规定为3.3V-4.35V；

   短时的浪涌尖峰电压，不能高于5V，否则有可能将模组损坏；

   VBAT处使用的TVS选型建议：
    TVS应保证VRWM略大于芯片最大工作电压(4.5V)，VBR典型值在5.3V左右，最大VBR值建议不超过6V，钳位电压VC尽量小，建议浪涌IPP大于160A，IPP=100A (8*20uS脉冲)的钳位电压约为7.5V；
    建议型号，芯禾微 XESD317D-4V5；  
     [XESD317D-4V5](file/XESD317D-4V5 Rev 0.1.pdf){:target="_blank"} 

6. 特别提醒！

   * 虽然模组正常工作建议的电压范围是3.3V-4.35V，但是模组实际可以开机运行的电压最低可以到2.1V，但是，请特别注意如下提示：

     * 当模组IO电平设置为1.8V和VBAT供电为2.1V且模组可以开机运行时，此时IO电平可以正常保持1.8V，但射频指标，包括发射功率和接收灵敏度，已经严重恶化，甚至无法正常驻网；

     * 当模组IO电平设置为3.3V和VBAT供电为2.1V且模组可以开机运行时，此时IO电平实际输出为2.0V，且射频指标，包括发射功率和接收灵敏度，已经严重恶化，甚至无法正常驻网；

     * 因此，再次强调，模组正常工作建议的电压范围是3.3V-4.35V，过低电压会导致射频指标恶化甚至无法正常驻网；

* 模组上电开机时，需确保VBAT上电时的起始电压小于0.5V，否则可能会因电压临界状态造成模组时序混乱而无法正常开机；关于这一点的详细介绍，会在“开机时序”章节进行详细介绍；

  	* 实际应用中最常见的问题是：
  	
  	掉电关机后短时间内再上电，但是在此期间系统上大电容的电未放完，导致VBAT电压停留在0.5V至2.0V中间，模组主芯片部分功能无法完全关闭，导致再上电时开机时序不符合要求而无法正常开机；
  	  
  	 * 记忆中有客户做的项目，大电容完全放电完毕用时需要将近30秒左右，非常容易导致陷入"为何无法开机、模组是不是坏了"的长时间分析中；
  			
  	 * 常见的余电快速放电电路有两种；
  			
  	第一种，优势是电路简单，缺点是功耗较高且会一直损耗，进而导致放电速度无法设置的太快(放电电阻R4不能使用阻值太小的电阻)；

     ![](../../../../../合宙通信/文档网站/luatos-doc-pool/docs/root/docs/air780epm/product/image/MZJebVOz0oZR0DxHarBc4zVSnHb.png)
     第二种，优势是只有在断电的情况下才会放电，不会产生额外的功耗，缺点是电路相对复杂，且由于单向导电电路(二极管 D1)的存在会导致工作端电压有损耗(二极管压降 0.3V)；
  ![](../../../../../合宙通信/文档网站/luatos-doc-pool/docs/root/docs/air780epm/product/image/B63Zb3McDo1l6Yx2K34ckZePn8g.png)

7. VBAT 电压可以检测，具体见 LuatOS 核心库"ADC"章节的介绍 [1 adc - 合宙模组资料中心](https://docs.openluat.com/osapi/core/adc/) ；
   **adc.CH_VBAT**

```
常量含义：VBAT供电电压的通道id；
数据类型：number；
常量取值：-2；
示例代码：adc.open(adc.CH_VBAT)；
```

常量含义：VBAT 供电电压的通道 id；
数据类型：number；
常量取值：-2；
示例代码：adc.open(adc.CH_VBAT)；

```
adc.open(adc.CH_VBAT)--打开adc.CH_VBAT通道
local data6 = adc.get(adc.CH_VBAT)--获取adc.CH_VBAT计算值，将获取到的值赋给data6
adc.close(adc.CH_VBAT)--关闭adc.CH_VBAT通道
log.info("VBAT", data6)--打印adc.CH_VBAT计算值
```

## 三，开机键，PWRKEY；

![](../../../../../合宙通信/文档网站/luatos-doc-pool/docs/root/docs/air780epm/product/image/WShBbXaeboKLLSxr61Vcy3uEnDD.png)

1. 模组管脚：PIN7；

2. 功能说明；

   模组工作电压范围 3.3V-4.35V，PWRKEY 为开机键， 内部上拉至 VBAT；

   如果 PWRKEY 接地，芯片上电即开机；
       

          1. PWRKEY 可以直接接地，也可以串联电阻接地；
          2. 串联电阻接地时，最大阻值不应该超过 10K，否则有可能无法正常开机；
          3. 相对于直接接地，串联电阻的作用是 PWRKEY 因内部上拉到 VBAT 而引起的漏电流可以进一步减小；

   PWRKEY 不接地时：
       

       1. 模组关机状态下，检测到 PWRKEY 下降沿，模组启动上电流程；
       2. 当系统在启动状态下检测到 PWRKEY 下降沿时触发中断，是否进入关机过程或其他动作由软件决定；
       3. 模组上电开机时，需确保 VBAT 上电时的起始电压小于 0.5V，否则可能会因低电压造成模组时序混乱而无法正常开机；关于这一点的详细介绍，会在“开机时序”章节进行详细介绍；

3. 关于 PWRKEY 的详细参数介绍，见下表； 

   <style> td {white-space:nowrap;border:0.5pt solid #dee0e3;font-size:10pt;font-style:normal;font-weight:normal;vertical-align:middle;word-break:normal;word-wrap:normal;}</style>

   | 参数                      | 描述                     | 规格 |      |      | 单位 |
   | ------------------------- | :----------------------- | ---- | ---- | ---- | ---- |
   |                           |                          | Min  | Typ. | Max  |      |
   | PWRKEY                    |                          |      |      |      |      |
   | 高电平                    | High-level input voltage | 1.7  |      | 3.6  | V    |
   | 低电平                    | Low-level input voltage  |      |      | 1.1  | V    |
   | 低功耗模式 pm.WORK_MODE,1 | LowPower Voltage         | 1.7  | 1.9  | 2.1  | V    |
   | PSM+模式 pm.WORK_MODE,3   | PSM+ Voltage             | 1.7  | 1.9  | 2.1  | V    |
   | 上拉电阻                  | Pull-up Resistance       | 130k | 180k | 230k | Ω    |

4. 当 PWRKEY 接实体按键进行拉低执行开机、关机或中断输入动作检测时，实体按键键帽与底座接触时极易产生静电，因此，需要增加 TVS 进行保护；
   推荐物料，芯禾微 XESD100N-3V3；
   [XESD100N-3V3](file/XESD100N-3V3 Rev. 0.1.pdf){:target="_blank"}

5. 开机前，PWRKEY 是开机键；开机后，PWRKEY 就是一个中断输入，执行什么动作取决于软件的设置；
   关于 PWRKEY 作为中断输入的 LuatOS 库函数介绍，详见：[21 gpio - 合宙模组资料中心](https://docs.openluat.com/osapi/core/gpio/)；

<style> td {white-space:nowrap;border:0.5pt solid #dee0e3;font-size:10pt;font-style:normal;font-weight:normal;vertical-align:middle;word-break:normal;word-wrap:normal;}</style>

| 常量                | 类型       | 常量取值 | 解释                                                         | 参数示例                                           | 适用产品型号                     |
| ------------------- | ---------- | -------- | ------------------------------------------------------------ | -------------------------------------------------- | -------------------------------- |
| gpio.LOW<br>        | number<br> | 0        | 低电平<br>                                                   | gpio.set(17, gpio.LOW)                             | 全支持<br><br>                   |
| gpio.HIGH           | number<br> | 1        | 高电平                                                       | gpio.set(17, gpio.HIGH)                            | 全支持                           |
| gpio.PULLUP         | number<br> | 1        | 上拉                                                         | gpio.setup(17, nil, gpio.PULLUP)                   | 全支持                           |
| gpio.PULLDOWN       | number<br> | 2        | 下拉<br>                                                     | gpio.setup(17, nil, gpio.PULLDOWN                  | 全支持                           |
| gpio.RISING<br>     | number<br> | 0        | 上升沿触发<br>                                               | gpio.setup(27, irqFunc, gpio.PULLUP, gpio.RISING)  | 全支持                           |
| gpio.FALLING        | number     | 1        | 下降沿触发                                                   | gpio.setup(27, irqFunc, gpio.PULLUP, gpio.FALLING) | 全支持                           |
| gpio.BOTH<br>       | number<br> | 2        | 双向触发<br>                                                 | gpio.setup(27, irqFunc, gpio.PULLUP, gpio.BOTH)    | 仅Air780EXXX系列/Air8000系列支持 |
| gpio.WAKEUP0        | number     | 39       | 休眠唤醒脚0，不支持输出                                      | gpio.setup(gpio.WAKEUP0, nil)                      | 仅Air780EXXX系列/Air8000系列支持 |
| gpio.WAKEUP1<br>    | number<br> | 40       | VBUS，USB唤醒脚，不支持输出<br>                              | gpio.setup(gpio.WAKEUP1, nil)                      | 仅Air780EXXX系列/Air8000系列支持 |
| gpio.WAKEUP2<br>    | number<br> | 41       | USIM热插拔脚，不支持输出<br>                                 | gpio.setup(gpio.WAKEUP2, nil)                      | 仅Air780EXXX系列/Air8000系列支持 |
| gpio.WAKEUP3<br>    | number<br> | 42       | 休眠唤醒脚3，与GPIO20是同一个引脚<br>                        | gpio.setup(gpio.WAKEUP3, nil)                      | 仅Air780EXXX系列/Air8000系列支持 |
| gpio.WAKEUP4<br>    | number<br> | 43       | 休眠唤醒脚4，与GPIO21是同一个引脚<br>                        | gpio.setup(gpio.WAKEUP4, nil)                      | 仅Air780EXXX系列/Air8000系列支持 |
| gpio.WAKEUP5<br>    | number<br> | 44       | 休眠唤醒脚5，与GPIO22是同一个引脚<br>                        | gpio.setup(gpio.WAKEUP5, nil)                      | 仅Air780EXXX系列/Air8000系列支持 |
| gpio.WAKEUP6<br>    | number<br> | 45       | 休眠唤醒脚6，不支持输出<br>                                  | gpio.setup(gpio.WAKEUP6, nil)                      | 仅Air8000系列支持<br>            |
| gpio.AUDIOPA_EN<br> | number<br> | 22       | 音频PA使能脚，仅Air780EHV特有的引脚<br>                      | gpio.setup(gpio.AUDIOPA_EN, 0)                     | 仅Air780EHV支持<br>              |
| gpio.PWR_KEY        | number     | 46       | 开机前：开机键，下降沿时触发；<br>开机后：双向触发中断，不支持输出； | gpio.setup(gpio.PWR_KEY, nil)                      | 仅Air780EXX系列/Air8000系列支持  |

## 四，复位键，RESET；

![](../../../../../合宙通信/文档网站/luatos-doc-pool/docs/root/docs/air780epm/product/image/ClUPbDkilouXQnxSOkMcQusrn8d.png)

1. 模组管脚：PIN15；

2. 功能说明；

       - 模组 Reset 管脚为系统硬复位，低有效；
       
       - 模组 Reset 高电平应大于 1.3V 且不大于 3.6V；
       
       - 如果 MCU GPIO 通过三极管连接到模块的 RESET，则要求三极管的截止漏电流小于 0.5uA。否则，由于 RESET 内部上拉微弱，大漏电可能会将 RESET 拉到 1.3V 以下进而造成模组无法正常工作；
       
       - 在三极管漏电流不能保证的情况下，可使用 MOS 管代替三极管， 大多数 MOS 漏电流相对较低；
       
       - 如果外部高电平可能将 RESET 拉至 1.3V 以下，则需要外加上拉电阻使其在合理范围内；
       
       - 模组 Reset 容易受到干扰而引起异常复位，如有必要可在 Reset 引脚上添加 RC 滤波电路；

     ![](../../../../../合宙通信/文档网站/luatos-doc-pool/docs/root/docs/air780epm/product/image/NIp9bhglboeTZRxRWBgcOI4FnPc.png)

3. 关于 RESET 的详细参数介绍，见下表；

   <style> td {white-space:nowrap;border:0.5pt solid #dee0e3;font-size:10pt;font-style:normal;font-weight:normal;vertical-align:middle;word-break:normal;word-wrap:normal;}</style>

   | 参数                      | 描述               | 规格 |      |      | 单位 |
   | ------------------------- | ------------------ | ---- | ---- | ---- | ---- |
   |                           |                    | Min  | Typ. | Max  |      |
   | RESET                     |                    |      |      |      |      |
   | 高电平                    | Input High Voltage | 1    |      | 3.6  | V    |
   | 低电平                    | Input Low Voltage  |      |      | 0.3  | V    |
   | 低功耗模式 pm.WORK_MODE,1 | LowPower Voltage   | 1    | 1.15 | 1.3  | V    |
   | PSM+模式 pm.WORK_MODE,3   | PSM+ Voltage       | 1    | 1.15 | 1.3  | V    |
   | 上拉电阻                  | Pull-up Resistance | 80k  | 120k | 160k | Ω    |

4. 当 RESET 接实体按键进行拉低执行开机、关机或中断输入动作检测时，实体按键键帽与底座接触时极易产生静电，因此，需要增加 TVS 进行保护；
   推荐物料，芯禾微 XESD100N-3V3；
   [XESD100N-3V3](file/XESD100N-3V3 Rev. 0.1.pdf){:target="_blank"} 

5. 注意！RESET 是硬件重启，不是软件重启，也不是关机；
   RESET 只接受外部输入，模组软件无法直接操作 RESET；

## 五，BOOT 键，USB_BOOT；

![](../../../../../合宙通信/文档网站/luatos-doc-pool/docs/root/docs/air780epm/product/image/D3JGbmitgoONPEx4fhOcGPgvnxy.png)

1. 模组管脚：PIN82；
 2. 功能说明； 
    - USB_BOOT，顾名思义通过操作该管脚从而使模组 Boot from USB，也就是进入 USB 下载模式；
    - USB_BOOT，软件初始化完成之前默认 I&PD 状态，即"输入 + 下拉"，因此，该管脚：
          悬空或外部下拉时，Boot from Flash；
          上拉至 VDD_EXT 时，Boot from USB；
     - USB_BOOT 管脚可以复用为 GPIO0，但需要注意：
       开机初始化完成之前，固定为 USB_BOOT 功能；
       开机初始化完成之后，可以复用为 GPIO0 功能；
       在设计硬件电路时，需要避免在开机初始化之前对该管脚形成事实性的上拉效果，否现会导致模组等待 Boot from USB 至少 30 秒后才可以启动 Boot from Flash 正常开机流程；
     - USB_BOOT 管脚的详细描述如下图所示；

![](../../../../../合宙通信/文档网站/luatos-doc-pool/docs/root/docs/air780epm/product/image/GZYHb2GANoZwBLxLgVKcXc1vnff.png)

 3.USB_BOOT 复用为 GPIO0 时的详细参数介绍，见下表；

<style> td {white-space:nowrap;border:0.5pt solid #dee0e3;font-size:10pt;font-style:normal;font-weight:normal;vertical-align:middle;word-break:normal;word-wrap:normal;}</style>

| 参数                      | 描述                         | 规格    |      |          | 单位 |
| ------------------------- | ---------------------------- | ------- | ---- | -------- | ---- |
|                           |                              | Min     | Typ  | MAX      |      |
| GPIO0，VDD_EXT=1.8V       |                              |         |      |          |      |
| VDD                       | IO Supply Voltage            |         | 1.8  |          | V    |
| 输入高电平                | High-level input voltage     | 0.7*VDD |      |          | V    |
| 输入低电平                | Low-level input voltage      |         |      | 0.2*VDD  | V    |
| 输出高电平                | High-level output voltage    | 0.8*VDD |      |          | V    |
| 输出低电平                | Low-level output voltage     |         |      | 0.15*VDD | V    |
| 低功耗模式 pm.WORK_MODE,1 | LowPower Voltage             |         | 0    |          | V    |
| PSM+模式 pm.WORK_MODE,3   | PSM+ Voltage                 |         | 0    |          | V    |
| 输出高电平时的驱动能力    | Digital High, Output Current | 4       | 6    | 9        | mA   |
| 输出低电平时的驱动能力    | Digital Low, Output Current  | 4       | 6    | 9        | mA   |
| 上拉电阻                  | Pull-up Resistance           | 110k    | 160k | 210k     | Ω    |
| 下拉电阻                  | Pull-down Resistance         | 160k    | 210k | 260k     | Ω    |
| GPIO0，VDD_EXT=2.8V       |                              |         |      |          |      |
| VDD                       | IO Supply Voltage            |         | 2.8  |          | V    |
| 输入高电平                | High-level input voltage     | 0.7*VDD |      |          | V    |
| 输入低电平                | Low-level input voltage      |         |      | 0.2*VDD  | V    |
| 输出高电平                | High-level output voltage    | 0.8*VDD |      |          | V    |
| 输出低电平                | Low-level output voltage     |         |      | 0.15*VDD | V    |
| 低功耗模式 pm.WORK_MODE,1 | LowPower Voltage             |         | 0    |          | V    |
| PSM+模式 pm.WORK_MODE,3   | PSM+ Voltage                 |         | 0    |          | V    |
| 输出高电平时的驱动能力    | Digital High, Output Current | 5.3     | 14   | 29.1     | mA   |
| 输出低电平时的驱动能力    | Digital Low, Output Current  | 4.2     | 18   | 25.4     | mA   |
| 上拉电阻                  | Pull-up Resistance           | 55k     | 85k  | 115k     | Ω    |
| 下拉电阻                  | Pull-down Resistance         | 70k     | 100k | 130k     | Ω    |
| GPIO0，VDD_EXT=3.3V       |                              |         |      |          |      |
| VDD                       | IO Supply Voltage            |         | 3.3  |          | V    |
| 输入高电平                | High-level input voltage     | 0.7*VDD |      |          | V    |
| 输入低电平                | Low-level input voltage      |         |      | 0.2*VDD  | V    |
| 输出高电平                | High-level output voltage    | 0.8*VDD |      |          | V    |
| 输出低电平                | Low-level output voltage     |         |      | 0.15*VDD | V    |
| 低功耗模式 pm.WORK_MODE,1 | LowPower Voltage             |         | 0    |          | V    |
| PSM+模式 pm.WORK_MODE,3   | PSM+ Voltage                 |         | 0    |          | V    |
| 输出高电平时的驱动能力    | Digital High, Output Current | 9.6     | 14.9 | 20.8     | mA   |
| 输出低电平时的驱动能力    | Digital Low, Output Current  | 11.6    | 22.4 | 36.3     | mA   |
| 上拉电阻                  | Pull-up Resistance           | 58k     | 77k  | 109k     | Ω    |
| 下拉电阻                  | Pull-down Resistance         | 49k     | 78k  | 138k     | Ω    |

4. 当 RUSB_BOOT 接实体按键进行拉低执行开机、关机或中断输入动作检测时，实体按键键帽与底座接触时极易产生静电，因此，需要增加 TVS 进行保护；
   推荐物料，芯禾微 XESD100N-3V3；
   [XESD100N-3V3](file/XESD100N-3V3 Rev. 0.1.pdf){:target="_blank"} 

5. 注意！USB_BOOT 只接受外部输入，模组软件无法直接操作 RESET；

## 六，USB 接口；

![](../../../../../合宙通信/文档网站/luatos-doc-pool/docs/root/docs/air780epm/product/image/YBmtbU8Gco4rlrxjPiocsSqOnDf.png)

1. 模组管脚；

       - VBUS，PIN61；
       
       - USB_DM，PIN60；
       
       - USB_DP，PIN59；

     说明：
     图中所示的 USB_DN 正确的写法应该是 USB_DM，历史原因一直将错误写法延续到了现在；
     USB_DM，USB Data Minus，USB 数据负信号；
     USB_DP，USB Data Positive，USB 数据正信号；

2. 功能说明；

   - 支持 USB Full speed，全速模式，最高支持 12Mbps，即 1.5MB/s；

   - 支持 USB High speed，高速模式，最高支持 480Mbps，即 40MB/s；

   - 支持 USB 下载软件，Boot from USB；

   - 支持 USB RNDIS，搭配 Windows/Linux 实现快速上网；

   - 仅支持 USB Slave，不支持 USB HOST;

3. 相关提示；

与 USB 相关的操作，重点关注如下几个方面：
    - 上电开机前将 USB_BOOT(GPIO0)上拉至 VDD_EXT，模组将进入 USB 下载模式(Boot from USB)；
    - 模组内部用于 USB 部分供电的电源是 LDO33USB(3.3V 输出)，该 LDO 只为 USB 功能提供电源，模组管脚未引出；
    - 低功耗模式 pm.WORK_MODE,1 和 PSM+ 模式 pm.WORK_MODE,3 下会将 LDO33USB 关闭；
    - 模组 VBAT 最低 2.3V 左右(实测最低可至 2.1V)时仍可工作，但因此时 LDO33USB 的输入(2.3V)已远低于输出(3.3V)所以 USB 已无法正常工作；
     - VBUS，在模组内部实际接的是 WAKEUP1，USB_VBUS 的 5V 经过分压后接到 WAKEUP1，用于 USB 插入检测；

4. 硬件参考设计；
   ![](../../../../../合宙通信/文档网站/luatos-doc-pool/docs/root/docs/air780epm/product/image/QlvNbHBWeoImx8xmFa1c7SfinCg.png)

- USB 接口的 PCB 设计中，差分信号线(USB_DP/USB_DM)的阻抗需控制在 90Ω±10%(即 81Ω～99Ω)，这是保证信号完整性的关键参数，阻抗不匹配将会导致信号反射、眼图闭合、误码率上升，甚至通信失败；

- 布线原则：

      1. 差分对等长匹配：长度差控制在 5mil 以内，避免时序偏差和共模干扰；
      2. 减少换层过孔：每次换层加一对回流地过孔，维持信号回流路径连续性，降低阻抗不连续风险；
      3. 保持完整参考平面：避免跨分割，优先内层布线，包地处理减少串扰；

- USB 接口容易引入静电干扰，因此需要在电路上增加 TVS 保护；
  USB High speed 差分数据线选择 TVS 的最关键参数是结电容容值，需要小于 1pF；
  我们验证使用过的是芯禾微 XESD124N-5V0 ;
  [XESD124N-5V0](file/XESD124N-5V0 Rev 0.1.pdf){:target="_blank"} 

- VBUS 电源上使用的 TVS 我们使用过的是 XESD307D-5V0 ;
  [XESD307D-5V0](file/XESD307D-5V0 Rev 0.1.pdf){:target="_blank"} 

  5. 与 USB 相关的 LuatOS API；

- pm 库 [52 pm - 合宙模组资料中心](https://docs.openluat.com/osapi/core/pm/) ；
  **pm.USB**

  ```lua
  常量含义：USB的供电使能；
  数据类型：number；
  常量取值：0；
  适用产品：Air780E系列、Air700E系列、Air8000系列；
  示例代码：--如下方所示，关闭USB的供电使能，即可关闭USB功能；
       pm.power(pm.USB ，false）
  ```

* mobile库 [43 mobile - 合宙模组资料中心](https://docs.openluat.com/osapi/core/mobile/) ；
  **mobile.CONF_USB_ETHERNET**

  ```
  常量含义：蜂窝网络模块的USB以太网卡控制；
  数据类型：number；
  常量取值：11；
  取值范围：0x01：开启RNDIS功能；
       0x03：开启RNDIS功能，使用NAT模式(基站分配ip)；
       0x05：开启ECM功能；
       0x07：开启ECM功能，使用NAT模式(基站分配ip)；
  注意事项：控制设备的USB以太网卡功能，通过位操作来控制不同的功能；
       仅在开启前可以修改；bit2：协议选择（1ECM协议，0RNDIS协议）；
       必须在飞行模式下设置才能生效；
       仅支持Air780EXXX系列和Air8000系列模块；
  示例代码：-- 进入飞行模式
       mobile.flymode(0, true)
       -- 设置开启RNDIS协议（bit0=1, bit1=1, bit2=0 → 0x03）
       mobile.config(mobile.CONF_USB_ETHERNET, 0x03)
       -- 退出飞行模式
       mobile.flymode(0, false)
  ```

- UART 库 [66 uart - 合宙模组资料中心](https://docs.openluat.com/osapi/core/uart/) ；
  **uart.VUART_0**

  ```lua
  常量含义：usb虚拟端口(适用于Air780EXX系列和Air8000系列模组)；
  数据类型：number；
  常量取值：32；
  示例代码: uart.setup(uart.VUART_0, 115200, 8, 1)；
  ```

## 七，上电/下载/复位/WakeUp时序；

上电时序、复位时序、下载时序，主要跟几个主要因素有关：

VBAT，供电；

时钟，系统运行的Clock信号；

PWRKEY，开机键；

RESET，复位键；

硬件BOOT，即USB_BOOT管脚的硬件操作，悬空或上拉；

软件BOOT，即软件开始执行BOOT动作，Boot from USB、UART or Flash；

说明：

虽然模组可以支持UART下载，本章节后面的内容也会简单说明UART下载，但建议请默认仅支持USB下载进行电路设计和实际操作；

1. 上电时序(PWRKEY不接地)；

![](../../../../../合宙通信/文档网站/luatos-doc-pool/docs/root/docs/air780epm/product/image/OU0TbVzomogByUxJMKYcJw9Onph.png)

2. 上电时序(PWRKEY接地);

![](../../../../../合宙通信/文档网站/luatos-doc-pool/docs/root/docs/air780epm/product/image/O02EbAnjEoIkGKxf5KGcH7P1nEc.png)

3. 上电下载时序(PWRKEY不接地)；

![](../../../../../合宙通信/文档网站/luatos-doc-pool/docs/root/docs/air780epm/product/image/LgdBbZz9zojLHVxWIJwc5OmXncf.png)

4. 上电下载时序(PWRKEY接地)；

![](../../../../../合宙通信/文档网站/luatos-doc-pool/docs/root/docs/air780epm/product/image/WEIGb1Yfro5CBHxJrqCcNO7jnJe.png)

5. 复位时序；

![](../../../../../合宙通信/文档网站/luatos-doc-pool/docs/root/docs/air780epm/product/image/H3jQbZUSdobYxHxKip5cJYutnre.png)

6.WakeUp时序;
 说明：

1. WAKEUP主要用于模组处于 低功耗模式pm.WORK_MODE,1 和 PSM+模式pm.WORK_MODE,3 下的休眠唤醒功能；

2. 关于WAKEUP的作用，在接下来的后续章节会详细说明，本处先仅作简单的时序介绍；

![](../../../../../合宙通信/文档网站/luatos-doc-pool/docs/root/docs/air780epm/product/image/CldPbp2cCoD4JpxiezlcNnURnNg.png)

## 八，双卡单待，SIM1/SIM2；

![](../../../../../合宙通信/文档网站/luatos-doc-pool/docs/root/docs/air780epm/product/image/FyZ6bTnaxojhuHxZb4McHjGXnWc.png)

1. 模组管脚；

   - SIM1；
     VDD_SIM1，PIN14；
     SIM1_DAT，PIN11；
     SIM1_RST，PIN12；
     SIM1_CLK，PIN13；

   - SIM2；
     VDD_SIM2，PIN65；
     SIM2_DAT，PIN64；
     SIM2_RST，PIN63；
     SIM2_CLK，PIN62；

2. 功能说明；

   - 模组支持双卡，但只能单待；

   - 模组SIM1为原生SIM卡接口，SIM2的数字信号为GPIO模拟，电源共用SIM1；

   - 复用为SIM2的GPIO，在硬件上同时被引到了不同的模组管脚上，比如：
     USIM2_RST，与CAM_BCLK(GPIO4)复用，二者不可同时使用；
     USIM2_CLK，与CAM_CS(GPIO5)复用，二者不可同时使用；
     USIM2_DAT，与CAM_RX0(GPIO6)复用，二者不可同时使用；
     这里的不可同时使用，是指在同一个硬件产品上，使用SIM2时就不能再使用GPIO4/5/6，包括这三个GPIO可以复用的其它功能，比如Camera、I2C、UART等，具体见GPIO复用表；
     ![](../../../../../合宙通信/文档网站/luatos-doc-pool/docs/root/docs/air780epm/product/image/MzJ1bdRnno02B1xoz6zcWeQSneh.png)

   - SIM1支持1.8V/2.8V两种IO电平的SIM卡，SIM2由于是GPIO4/5/6复用所以可以支持的SIM卡的电平与模组IO的电平保持一致，模组IO电平设置为1.8V时，SIM2则只支持1.8V电平的SIM卡，模组IO电平设置为2.8V/3.3V时，SIM2则只支持2.8V/3.3V电平的SIM卡(就IO电平而言，2.8V和3.3V可认为电平一致)；
     当前运营商提供的SIM卡基本默认兼容1.8V/2.8V电平，所以大家在实际应用中很少遇到因为IO电平不一致而导致不识卡的情况；

   - 也可以使用"SIM1+模拟开关"的方式实现双卡单待，我们验证调试过的SIM卡模拟开关为BCT4567；
     [BCT4567](file/SIM卡模拟开关-多路复用器_BCT4567EFE-TR_规格书.PDF){:target="_blank"} 

3. 参考设计；

   - 双卡设计时，推荐SIM1带插入检测功能，且配合SIM卡座的规格，保证在SIM卡插入后USIM_DET信号悬空而不是接地，可以进一步降低功耗；

   - 双卡设计且其中之一为贴片SIM卡时，推荐使用SIM2接贴片SIM卡，SIM1接SIM卡座；

   - SIM1注意事项；

     1. USIM_DET为SIM卡插入检测管脚，不需要时可以不接；

     2. USIM_DET推荐默认使用WAKEUP2，当然使用其它WAKEUP或GPIO中断也可以；

     3. WAKEUP2被命名为USIM_DET，目的是为了引导大家默认使用WAKEUP2用作USIM_DET，当产品设计中不需要USIM_DET时，该管脚WAKEUP2与其它WAKEUP信号没有区别，也可以用作它用；

     4. USIM_DET代码示例；

     ```lua
     --设置防抖，USM_DET脚的常量为gpio.WAKEUP2,直接填即可
     gpio.debounce(gpio.WAKEUP2,500)
     --设置中断触发，拔卡进入飞行模式，插卡进出飞行模式，val值为上升沿或者下降沿触发0/1
     gpio.setup(gpio.WAKEUP2,function(val)
     if val==0 then
     log.info("拔卡")
     mobile.flymode(0,true)
     else
     log.info("插卡")
     mobile.flymode(0,true)
     mobile.flymode(0,false)
     end
     end)
     
     ```

     5. USIM_DET需上拉至某一AGPIO，比如AGPIO3(GPIO23，软件可以控制一直高电平输出，此管脚在某些文档中也常被称为Vref)，不能使用VDD_EXT(低功耗模式pm.WORK_MODE,1 和 PSM+模式pm.WORK_MODE,3 下不能一直输出，后续的章节中会详细介绍)；
        ![](../../../../../合宙通信/文档网站/luatos-doc-pool/docs/root/docs/air780epm/product/image/RVa4b4omVoLjwNxTRozc1ES8nLo.png)

   - SIM2 注意事项；

     1. 请严格按照 SIM2 参考设计电路进行设计，SIM2 可以接 SIM 卡座，也可以接贴片 SIM 卡；
     2. USIM2，无论电源还是信号(CLK/DATA/RST)，均为复用模组的其它管脚；
        USIM2_VDD，与 USIM1_VDD 使用同一个电源 LDOSIM；
        USIM2_RST，与 CAM_BCLK(GPIO4)复用，二者不可同时使用；
        USIM2_CLK，与 CAM_CS(GPIO5)复用，二者不可同时使用；
        USIM2_DAT，与 CAM_RX0(GPIO6)复用，二者不可同时使用；
     3. 使用 SIM2 时，必须使用 API 函数 mobile.simid(1)进行切换，否则模组不会自动初始化 SIM2；
        关于 mobile.simid(2)的详细说明，见：[43 mobile - 合宙模组资料中心](https://docs.openluat.com/osapi/core/mobile/) ；
        ![](../../../../../合宙通信/文档网站/luatos-doc-pool/docs/root/docs/air780epm/product/image/JeG2bTHzioGxhHxxA9xcTrmGnfc.png)

     - SIM 卡插拔时极易产生静电，电路上需要添加 TVS 保护；
       推荐 TVS 型号为芯禾微 XESD100N-3V3 ；
       [XESD100N-3V3](file/XESD100N-3V3 Rev. 0.1.pdf){:target="_blank"} 

4. 特别提示；

   - SIM 卡不识卡，使用万用表测量 SIM 卡电源 VDD_SIM 没有电压，怎么办???

     1. 首先，需要明确的是，是因为模组没有识别到卡，所以 VDD_SIM 没有电压，并不是因为 VDD_SIM 没有电压所以识别不到 SIM 卡，因果关系不要反了，这几乎是所有新用户都会遇到的常识性的认知错误；
     2. 真实原因是：
        SIM 卡在初始化时，系统会尝试 4 次与 SIM 卡交互，此时 VDD_SIM 也会打开 4 次，分别在 1.8V 和 3.3V 交替检测，若检测不到 SIM 卡，VDD_SIM 卡就会关闭，因此在检测不到 SIM 卡的情况下你用万用表测量 USIM_VDD 总是低电平；
        下图为使用示波器测量出来的对应 SIM 卡未被识别到时的波形；
        ![](../../../../../合宙通信/文档网站/luatos-doc-pool/docs/root/docs/air780epm/product/image/Uw9ubvoWloP2m7xcWsJcjqqynCf.jpg)

   3. 一般来说，不识别卡的主要原因大概有如下几个；
      - SIM 卡封装做错；
      - SIM 卡接触不良；
      - SIM 卡信号线连错；
      - SIM 卡信号使用的 TVS 容值太大，导致 SIM 卡信号变形严重；
      - SIM 卡本身不良，一般做法是先使用个人手机 SIM 卡对比测试；
      - SIM2 不识别时，大概率是没有设置 mobile.simid(1) 导致的，详见 [43 mobile - 合宙模组资料中心](https://docs.openluat.com/osapi/core/mobile/#mobilesimidid-priority)；
      - SIM2 由 GPIO 复用而来，所以模组底层软件并不会主动轮询识别 SIM1 和 SIM2，只会默认初始化识别 SIM1，初始化 SIM2 需要主动设置 mobile.simid(1) ，再次强调！

5. 关于 LuatOS 中 SIM 相关的介绍；
   以下为两个常见的 API，更详细的请点击：[43 mobile - 合宙模组资料中心](https://docs.openluat.com/osapi/core/mobile/) ；
   **mobile.imsi(index)**
   **功能**
   获取 IMSI（国际移动用户识别码）
   **参数**
   _index_

```lua
参数含义：编号；
数据类型：number；
取值范围：0或1，不填默认0；
是否必选：可选传入此参数；
注意事项：必须等待sim卡准备就绪后，才能获取成功；
         在支持双卡的模块上才会出现0或1的情况；
参数示例：0 -- 表示获取第一张SIM卡的IMSI
```

**返回值**
  local result = mobile.imsi(index)
  _result_

```lua
含义说明：当前的IMSI值；
数据类型：string；
取值范围：IMSI字符串，若失败返回nil；
返回示例："460111234567890" -- IMSI字符串
```

**示例**

```lua
if mobile.simPin() then
    log.info("SIM卡准备就绪，开始获取imsi")
    log.info("imsi", mobile.imsi())
else
    log.info("SIM卡未准备就绪，可能未插入SIM卡或SIM卡有问题")
end
```

  **mobile.iccid(id)**

  **功能**

  获取或设置 ICCID（集成电路卡识别码）

  **参数**

  _id_

```
参数含义：SIM卡的编号；
数据类型：number；
取值范围：0或1,不填默认0；
是否必选：可选传入此参数；
注意事项：必须等待sim卡准备就绪后，才能获取成功；
参数示例：0 -- 表示获取第一张SIM卡的ICCID
```

  **返回值**

  local result = mobile.iccid(id)

  _result_

```
含义说明：ICCID值；
数据类型：string；
取值范围：ICCID字符串，若失败返回nil；
返回示例："89860855102480513111" -- 一个20位的ICCID字符串
```

  **示例**

```lua
 if mobile.simPin() then
    log.info("SIM卡准备就绪，开始获取iccid")
    log.info("iccid", mobile.iccid())
else
    log.info("SIM卡未准备就绪，可能未插入SIM卡或SIM卡有问题")
end
```

## 九，模数转换，ADC；

![](../../../../../合宙通信/文档网站/luatos-doc-pool/docs/root/docs/air780epm/product/image/VvNEbMIz1oImctxfO4Lcrtzxnps.png)

1. 模组管脚；

   - ADC0，PIN9；

   - ADC1，PIN96；

   - ADC2，PIN77；

   - ADC3，PIN76；

2. 功能说明；

   - 虽然我们习惯在沟通和交流中习惯称为 ADC，但更为准确的称呼应该是 AUXADC，AUXADC 是芯片内部的辅助 ADC 通道，主要用于温度监测、电池电量检测等；

   - 模组共有 4 个 AUXADC 通道，包含以下三个主要功能：

     1. 外接模拟电压信号检测通路；
        - 选择内部分压电路，适用于外接信号电压范围为 0~3.3V；
        - 选择直通 AUXADC 输入端的通路， 适用于电压范围 0-1.6V，或经外部分压后在 1.6V，分压后电压范围需控制在 0~1.6V；
     2. VBAT 电压检测通路；
        - VBAT 电压经过分压电路到达 AUXADC 输入口；
     3. 温度传感器检测通路；
        - 模组芯片内部温度检测：芯片温度发生变化时，片内 Thermal Sensor 的电压信号也会随之变化，将 THM_VBE 信号送至 AUXADC 测试；

   - ADC 内部框图；
     ![](../../../../../合宙通信/文档网站/luatos-doc-pool/docs/root/docs/air780epm/product/image/NRg0beVhBoaO1rxGcCOcksp9nTe.png)
     注意！

     1. 图中用 AIO 表示从外部输入到模组内部 AUXADC 的部分，可以理解为直接连接到模组 ADC 管脚的电平；

     2. 片内电阻绝对偏差 MAX= ±8.5%(-40~85 ℃)，片内电阻的相对偏差，阻值比误差 MAX=+/-0.15%(-40~85 ℃)；

     3. AUXADC 可以选择内部分压，也可以选择外部分压，不管选择外部分压还是内部分压，都需要调整合适的分压比，保证 AUXADC 输入端电压在 0~1.6V 范围；

     4. 当被测电压低于 1.6V 时，外部无需分压，内部也无需分压，LuatOS ADC 函数对应选择 adc.ADC_RANGE_MIN 常量；

     5. 当被测电压低于 3.3V 时，外部无需分压，内部需要分压至 1.6V 以内，LuatOS 已将该部分在底层做好，ADC 函数对应选择 adc.ADC_RANGE_MAX 常量即可；

     6. 当被测电压大于 3.3V 时，内部无需分压，外部需要分压至 1.6V 以内，LuatOS 已将该部分在底层做好，ADC 函数对应选择 adc.ADC_RANGE_MAX 常量即可；

     7. 读取模组芯片温度的常量为 adc.CH_CPU ，可检测温度范围为 -40 °C ~ 85 °C ，外部硬件电路上无需任何操作；

     8. 读取 VBAT 电压的常量为 adc.CH_VBAT ，电压范围为 2.2V-4.8V，在 VBAT 输入电压范围 3.3V-4.35V 之内，外部硬件电路上无需任何操作；

     9. 关于以上说明，汇总表格如下：

     <style> td {white-space:nowrap;border:0.5pt solid #dee0e3;font-size:10pt;font-style:normal;font-weight:normal;vertical-align:middle;word-break:normal;word-wrap:normal;}</style>

     | Channel | Application                                         | Input Range    | 外部分压           | 内部分压            | ADC函数常量       |
     | ------- | --------------------------------------------------- | -------------- | ------------------ | ------------------- | ----------------- |
     | AIO1~4  | Input signal come from the outside direct signal.   | 0V ~ 1.6V      | 不需要             | 不需要              | adc.ADC_RANGE_MIN |
     | AIO1~4  | Input signal comes from the outside divided signal. | 0V ~ 3.3V以上  | 需要分压至1.6V以内 | 不需要              | adc.ADC_RANGE_MIN |
     | AIO1~4  | Input signal come from the outside direct signal.   | 0V ~ 3.3V      | 不需要             | 需要,LuatOS底层操作 | adc.ADC_RANGE_MAX |
     | 温度    | Inner thermal sensor generate the signal            | -40 °C ~ 85 °C | 无需任何操作       | 无需任何操作        | adc.CH_CPU        |
     | VBAT    | VBAT voltage input                                  | 2.2V ~ 4.5V    | 不需要             | 需要,LuatOS底层操作 | adc.CH_VBAT       |

     10. 关于 LuatOS 中 ADC 的介绍，详见：[1 adc - 合宙模组资料中心](https://docs.openluat.com/osapi/core/adc/) ；

3.ADC 的详细参数介绍，如下表；
![](../../../../../合宙通信/文档网站/luatos-doc-pool/docs/root/docs/air780epm/product/image/S9fCbm9DTonp9sxjQfHcieIhnSe.png)

4. 特别注意事项；

   - AUXADC 的有效输入范围为 0.1V~1.5V，在 0~0.1V 和 1.5V~1.6V 范围可能存在较大误差，不建议使用；

   - 外部分压时，如果 AUXADC 输入电压无法满足低于 1.6V，以 NTC 电阻为例，可使用如下图右侧所示电路进行分压设计；
     ![](../../../../../合宙通信/文档网站/luatos-doc-pool/docs/root/docs/air780epm/product/image/DUTvb2jwpoAxnzx4CD0c0vbPnqf.png)

   - 如果不希望 ADC 变化太快，可以通过软件算法处理，过滤掉变化较大的数值，也可以在硬件电路上增加滤波电路；
     比如，外部分压时，可以增加滤波电容增加 ADC 输入稳定性，但缺点是 ADC 的细微变化会被过滤掉，请根据实际需要谨慎选择；
     说明：R300 可以增加 ESD 能力，阻值建议为 510Ω，不建议使用 K 级阻值的电阻；
     ![](../../../../../合宙通信/文档网站/luatos-doc-pool/docs/root/docs/air780epm/product/image/YZv0bfpB2oMMAXxIYUvcHseankd.png)5.关于 LuatOS 中 ADC 的介绍，详见：[1 adc - 合宙模组资料中心](https://docs.openluat.com/osapi/core/adc/) ；
     特别说明：

     1. 所有 ADC 共用一个通道，同时只能调用一路 ADC 采样，包括芯片温度、VBAT 电压；

     2. ADC 打开( adc.open() )后，会产生约 500uA 的功耗，如需低功耗控制，请将 ADC 关闭( adc.close() )；

```lua
local function adc_all_task()
    while 1 do
        sys.wait(1000)--延时1S           
        adc.setRange(adc.ADC_RANGE_MIN)--设置ADC引脚的测量范围0-1.5V，这种方式被测电压可以经过外部电阻分压后再挂在ADC上；
        adc.open(0)--打开ADC通道0
        local data0 = adc.get(0)--获取adc计算值，将获取到的值赋给data0
        adc.close(0)--关闭ADC通道0
        log.info("adc通道0", data0)--打印adc计算值

        adc.open(1)--打开ADC通道1
        local data1 = adc.get(1)--获取adc计算值，将获取到的值赋给data1
        adc.close(1)--关闭ADC通道1
        log.info("adc通道1", data1)--打印adc计算值

        adc.open(2)--打开ADC通道2
        local data2 = adc.get(2)--获取adc计算值，将获取到的值赋给data2
        adc.close(2)--关闭ADC通道2
        log.info("adc通道2", data2)--打印adc计算值

        adc.open(3)--打开ADC通道3
        local data3 = adc.get(3)--获取adc计算值，将获取到的值赋给data3
        adc.close(3)--关闭ADC通道3
        log.info("adc通道3", data3)--打印adc计算值

        adc.open(adc.CH_CPU)--打开adc.CH_CPU通道
        local data5 = adc.get(adc.CH_CPU)--获取adc.CH_CPU计算值，将获取到的值赋给data5
        adc.close(adc.CH_CPU)--关闭adc.CH_CPU通道
        log.info("CPU TEMP", data5)--打印adc.CH_CPU计算值

        adc.open(adc.CH_VBAT)--打开adc.CH_VBAT通道
        local data6 = adc.get(adc.CH_VBAT)--获取adc.CH_VBAT计算值，将获取到的值赋给data6
        adc.close(adc.CH_VBAT)--关闭adc.CH_VBAT通道
        log.info("VBAT", data6)--打印adc.CH_VBAT计算值
    end  
end

sys.taskInit(adc_all_task)
```

## 十，对外电源，VDD_EXT；

![loading-ag-2151](../../../../../合宙通信/文档网站/luatos-doc-pool/docs/root/docs/air780epm/product/image/2.png)

1. 模组管脚；

   * VDD_EXT，PIN24；

2. 功能说明；

   * VDD_EXT，是模组主芯片内部的一个LDO，其主要作用是为模组大部分IO(或者叫做GPIO)提供电源；

   * VDD_EXT，是模组主芯片内部的一个LDO，但并不是唯一一个LDO，也并不是模组所有的IO都由VDD_EXT负责供电；

   * 大家可以看到和可以使用的模组IO，共有内部的两个LDO负责供电，一个是VDD_EXT，一个是LDO_AON，其中，LDO_AON仅供内部使用，未在模组管脚引出；

   * VDD_EXT和LDO_AON分别负责哪些IO？二者有什么区别？见下表；
     ![](../../../../../合宙通信/文档网站/luatos-doc-pool/docs/root/docs/air780epm/product/image/1.png)

   * VDD_EXT和LDO_AON都是上电后自动开启，也都无法软件自定义关闭，不同的是：
     a. LDO_AON，开机后一直开启，低功耗模式pm.WORK_MODE,1 和 PSM+模式pm.WORK_MODE,3 不关闭，因此，其电压域下的IO，包括AGPIO、AGPIOWU、WAKEUP和Reset，在 低功耗模式pm.WORK_MODE,1 和 PSM+模式pm.WORK_MODE,3 下都可以保持工作；
     LDO_AON的详细状态，如下表所示；

     | VDD_EXT                   | 电压可调                 | 输出状态                                                     |
     | ------------------------- | ------------------------ | ------------------------------------------------------------ |
     | 常规模式pm.WORK_MODE,0    | 完全开启1.8V/2.8V/3.3V   | 1)上电开机后自动开启，初始电压为1.8V，之后调整至模组定义的IO默认电平3.3V；如需自定义调整，可通过pm.ioVol()配置；<br><br>2)电流输出上限200mA； |
     | 低功耗模式 pm.WORK_MODE,1 | 间歇性输出1.8V/2.8V/3.3V | 3)低功耗模式pm.WORK_MODE,1 状态下间歇性输出，频率和与通信协议要求的时间间隔相同，通常为0.64S/1.28S/2.56S中的一个； |
     | PSM+模式 pm.WORK_MODE,3   | 完全关闭                 | 4)PSM+模式(pm.WORK_MODE,3)下完全掉电;                        |

       b. VDD_EXT，开机后的状态比较复杂，如下表所示：

     | VDD_EXT                   | 电压可调                 | 输出状态                                                     |
     | ------------------------- | ------------------------ | ------------------------------------------------------------ |
     | 常规模式pm.WORK_MODE,0    | 完全开启1.8V/2.8V/3.3V   | 1)上电开机后自动开启，初始电压为1.8V，之后调整至模组定义的IO默认电平3.3V；如需自定义调整，可通过pm.ioVol()配置；<br><br>2)电流输出上限200mA； |
     | 低功耗模式 pm.WORK_MODE,1 | 间歇性输出1.8V/2.8V/3.3V | 3)低功耗模式pm.WORK_MODE,1 状态下间歇性输出，频率和与通信协议要求的时间间隔相同，通常为0.64S/1.28S/2.56S中的一个； |
     | PSM+模式 pm.WORK_MODE,3   | 完全关闭                 | 4)PSM+模式(pm.WORK_MODE,3)下完全掉电;                        |

     VDD_EXT，由于在 低功耗模式 pm.WORK_MODE,1 下状态为间线性输出，既不是完全打开，也不是完全关闭，因此，其电压域下的IO，GPIO0-19和GPOIO29-38，在低功耗模式 pm.WORK_MODE,1 下也会跟VDD_EXT一样，随着系统间歇性唤醒与基站交互而频繁产生高脉冲(重点是无法保持高电平或低电平)，间歇性唤醒频率和与通信协议要求的时间间隔相同，通常为0.64S/1.28S/2.56S中的一个；
       c.  VDD_EXT和LDO_AON都是通过函数 pm.ioVol() 设置输出电压，且 pm.ioVol() 对二者同时进行修改，所以大家看到的模组的所有IO，包括VDD_EXT电压域和LDO_AON电压域，电平都是同时修改且保持一致；

3. VDD_EXT详细参数；
   ![](../../../../../合宙通信/文档网站/luatos-doc-pool/docs/root/docs/air780epm/product/image/5.png)

4. 特别注意事项；

   * VDD_EXT标称输出电流最大至200mA，但是，VDD_EXT同时用于模组芯片内部和外部，为了保证内部该电压域负载的使用稳定性，请保证外部负载消耗电流不要超过50mA；

   * VDD_EXT在 低功耗模式pm.WORK_MODE,1 状态下间歇性输出，频率和与通信协议要求的时间间隔相同，通常为0.64S/1.28S/2.56S中的一个，因此，在可能影响低功耗模式pm.WORK_MODE,1 下中断唤醒的场合，要避免使用VDD_EXT做上拉电平用，比如UART1电平转换电路(UART1为LPUART，低功耗模式pm.WORK_MODE,1 下可被唤醒)；
     如下图所示，如果VREF使用VDD_EXT，那么低功耗模式pm.WORK_MODE,1 下模组的UART1_RX频繁的高低电平变化就会将模组唤醒，进而导致模组退出低功耗模式而功耗无法降低；
     ![](../../../../../合宙通信/文档网站/luatos-doc-pool/docs/root/docs/air780epm/product/image/3.png)

5. VDD_EXT相关LuatOS API；

   * VDD_EXT相关LuatOS API，请看pm库：[52 pm - 合宙模组资料中心](https://docs.openluat.com/osapi/core/pm/) ；

   * 配置IO电平，其实就是配置给IO供电的LDO，包括VDD_EXT和LDO_AON;
     相应的，配置VDD_EXT，就可以通过配置IO电平来实现；

* **pm.ioVol(id, val)**
  **功能**
    配置模块所有 IO 引脚的高电平电压
    **注意事项**
    可配置 IO 电平, 范围 1650 ~ 2000，2650~3400 , 单位毫伏, 步进 50mv，可以根据外围电路需求配置；
    实际应用中，经常配置的三个经典电压值为 1.8V/2.8V/3.3V；
    合宙支持二次开发的模组，绝大多数默认都为 3.3V，例外是：
    Air8000 系列模组，全系只支持 3.3V，不支持其它电平；
    Air780EHV，IO 电平固定为 3.3V；相对应的，Air780EHV-1.8V，IO 电平固定为 1.8V；
    **参数**
    id

  ```lua
  含义说明：需要配置高电平电压的对象；
  数据类型：number；
  取值范围：0；
  注意事项：对象id仅有pm.IOVOL_ALL_GPIO，如命名含义，指配置所有GPIO的高电压电平，调用该接口时模块会以该接口配置的高电压电平调整，无视固件的默认配置和硬件100脚IO_Volt_Set的拉高和拉低配置；
  参数示例：--如下方所示，第一个参数为**pm.IOVOL_ALL_GPIO**，所有GPIO高电平输出1.8V；
          pm.ioVol(pm.IOVOL_ALL_GPIO, 1800)
  ```

  val

```lua
含义说明：需要配置对象的电压值；
数据类型：number；
取值范围：1650 ~ 2000，2650~3400 , 单位毫伏, 步进50mv；
注意事项：模块会有1.8V和3.3V默认高电平电压的版本，也可调用该接口手动调整，以外围电路要求为主；
参数示例：--如下方所示，第二个参数为**3300**，所有GPIO高电平输出3.3V；
         pm.ioVol(pm.IOVOL_ALL_GPIO, 3300)
```

**返回值**

  local result = pm.ioVol(id, val)

  有一个返回值 result

result

```lua
含义说明：判断高电平电压调整是否正确执行；
数据类型：boolean；
取值范围：成功时返回true，失败时返回false；
注意事项：注意电压变化前后的外部电阻匹配；
参数示例：--如下方所示，第二个参数为3300，模块所有IO的高电平电压配置为为3.3V；
         --result返回true则模块电压转为3.3V；
         local result = pm.ioVol(pm.IOVOL_ALL_GPIO, 3300)
```

  **示例**

```lua
pm.ioVol(pm.IOVOL_ALL_GPIO, 3300)    -- 所有GPIO高电平配置为3.3V
pm.ioVol(pm.IOVOL_ALL_GPIO, 1800)    -- 所有GPIO高电平配置为1.8V
```

十一，GPIO/AGPIO/AGPIOWU/WAKEUP；
=============================

![](../../../../../合宙通信/文档网站/luatos-doc-pool/docs/root/docs/air780epm/product/image/6.png)





1. 模组管脚；

   * 上图虚线所示的管脚，全部可用作GPIO，只是管脚名只写了推荐的默认功能；

   * 这些IO管脚，不只是可用作GPIO，也可以用作其它数字信号，比如UART、SPI、I2C、OneWire、PWM、LCD接口、Camera接口、CAN接口、USB_BOOT使能等，详见下图；
     [复用表](file/Air780EPM&EHM&EHV&EGH&EGG&EGP&EHN&EHU_GPIO复用表_20251002.xlsx){:target="_blank"}
