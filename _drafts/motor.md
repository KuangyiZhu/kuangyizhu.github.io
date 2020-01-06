Motor
http://www.e-mechatronics.com/school/motion/index.html

http://robo-navi.com/document/SIer_SkillStandard_Book_8.pdf

https://www.rs-online.com/designspark/motion-control-jp

http://www.db.hivertec.net/seminer/jisshu_200909.pdf

https://www.sssf.jp/event/benkyokai_1009.html



EN
https://www.sssf.jp/event/benkyokai_1009.html
http://www.tij.co.jp/jp/lit/an/spra327/spra327.pdf
https://www.thierry-lequeu.fr/data/BPRA043.pdf
https://www.digikey.com/en/product-highlight/t/texas-instruments/motor-control


我一查产品好多啊。。。，这块慢慢深挖一下应该可以挖出许多信息的
这种，AGV专用UNIT
http://www.nidec-shimpo.co.jp/csp/dl/docs/ctlog_018.pdf

有家叫Oriental Motor（欧利恩）的直接告诉你如何选用，以下是一些资料的大概
首先是电源，主流是DC24V或DC48V，AGV里面还要设置电压监视sensor以检测驱动马达侧的电压低下，左右轮的速度需要一致，因此，需要选择收到指令后速度差异小的马达，最后在外形上，小车一般做的比较compact，低矮，所以马达也要尽可能小，因此当社的Brushless motor BLH 和BLV 系列是首选
https://www.orientalmotor.co.jp/tech/teruyo/vol60/



结合昨天发的马达控制的一些基础（估计你应该知道了）
https://www.contec.com/jp/support/basic-knowledge/daq-control/motion-control/
马达控制就是运动控制板收到上位机的参数，生成脉冲信号传达给 motor driver unit，motor driver unit控制马达，结合下面的英语资料，motor driver unit根据脉冲输出相应的直流信号传递给马达中的coil，驱动马达转动。

如果要精确控制马达， 一般使用servo motor 或者stepper motor这些都是 用在机械臂上的，小车的马达就用dc motor应该就可以了（前两种都是能够精确控制角度的motor agv 不需要）

关于brushless motor， stepper motor， servo motor的说明（英语）
https://www.seeedstudio.com/blog/2019/04/01/choosing-the-right-motor-for-your-project-dc-vs-stepper-vs-servo-motors/
https://www.elprocus.com/difference-dc-motor-servo-motor-stepper-motor/

【多个商家比较】
Oriental Motor
昨天发的欧利恩brushless DC马达的资料，其实他们的马达看上去也是带servo的（最上面一个图）驱动部这里有个feedback到速度控制部，所以只是欧利恩公司对这款产品叫DC马达而已，可能本质上也是servo motor
https://www.orientalmotor.co.jp/om/knowledge/uroko_bl/bl02.html

Nidec
和控制器的接线例子买了马达后会提供，比如说像这样
http://www.nidec-shimpo.co.jp/csp/dl/docs/ctlog_018.pdf

元配件示例
http://www.ti.com/applications/industrial/motor-drives/overview.html

控制器选型
单片机STEM32系列（力生使用），agvba.com/6729.html
Stm32 motion control 教程
https://www.st.com/content/st_com/en/support/learning/stm32-education/stm32-for-motor-control-/bldc-motor---scalar-control-and-v-f-control.html

DSP
TI的solution
http://www.ti.com/general/docs/litabsmultiplefilelist.tsp?literatureNumber=spra345

DSP vs MCU(STM32)
https://www.microchip.com/forums/m115630.aspx
2) DSP are much faster for integer maths operations. many microcontrollers even dont have them in hardware this has to be achieved in software (multiplication and division)

3) DSP are hundreds of times faster for floating point operations. In microcontrollers this has to be done in software.

4) DSP are oriented to be an input/output device with 'fast calculating machine'. Microcontrollers are a multifeature device with several ways of interfacing the world but they are not the fastest in none of them.

5) DSP are not designed to be a 'robust' device. By 'not robust' I mean that they require a more well designed board to let the work correctly. Microcontrollers can work on a Test Board!


具体如何接线做板子可以找人问下


【马达如何评价，如何调试，Renasus有工具】
--网站的大致意思，在开发运动控制里面马达的控制中高效低成本的sensorless control功能中，大量的时间耗费在debug，tuning中，有了这个kit可以大幅降低在这个上面耗费的时间
https://www.renesas.com/jp/ja/products/microcontrollers-microprocessors/rx/softtools/rx23t-rx24t-rssk.html

具体调试的时候马达也有发热等问题，也需要有人去解决

【车轮结构也有学问】
http://www.fujihensokuki.co.jp/archives/doc/gearmotor/p_catalogue/ag-v_cat_no_0706-1.pdf

【其他用于AGV的motor】
http://www.wacogiken.co.jp/agv/index.html


德国 wittenstein
https://www.wittenstein-ternary.jp/itas/

这家是个很小的公司。。。（社长据说是workman里面马达的设计者）
https://www.ipros.jp/product/detail/2000196868/

这家公司可以提供从encoder 到motor的整体解决方案，不过他们的方案居然是基于stepper motor的，
https://tech-compass.jp/case/044/

Oriental Motor的网站上有好多马达相关的名词解释
https://www.orientalmotor.co.jp/tech/teruyo/


运动控制系统大体上的知识点就这些了，DSP或MCS里面的算法应该是PI控制，这块等所有的模块都搭起来了再深入看下。
相机模块到时候我也去问下我理光的同学，然后就是两个模块的协同问题

モーターコントロール
https://www.renesas.com/jp/ja/products/ics-for-motor-actuator-driver/ics-for-motor-control.html

https://www.fujiele.co.jp/semiconductor/ti/tecinfo/news201711220000/

https://www.robotshop.com/blog/ja/how-to-make-a-robot-lesson-3-actuators-2-3703


http://www.rmf.mi.uec.ac.jp/mouse/2011/step_ctrl.pdf
https://okwave.jp/qa/q184521.html
http://www.mekatoro.net/digianaecatalog/melco-j4/book/melco-j4-P0189.pdf
http://kns.cnki.net/kcms/detail/detail.aspx?filename=JXXB201623022&dbcode=CJFQ&dbname=CJFD2016&v=
https://blog.csdn.net/Renjiankun/article/details/80513666
https://www.nidec.com/ja-JP/technology/motor/basic/00013/
https://www.pulsemotor.com/feature/pcd4600.html
https://astamuse.com/ja/published/JP/No/1995325624
https://patents.google.com/patent/CN107070329A/zh



https://tajimarobotics.com/
https://www.g200kg.com/archives/2018/03/post-274.html
http://www.mycom-japan.co.jp/giken/200804/200804.htm


http://www.et.byu.edu/~ered/ME537/Notes/Ch5.pdf


https://www.pmdcorp.com/resources/type/articles/get/mathematics-of-motion-control-profiles-article


http://bascetta.deib.polimi.it/images/7/77/CONTAUT_MEC-Lect8.pdf
http://ttuadvancedrobotics.wikidot.com/trajectory-planning-for-point-to-point-motion

https://journals.sagepub.com/doi/full/10.5772/5652

