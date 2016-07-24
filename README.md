Apollo-11
=========

Original Apollo 11 guidance computer (AGC) source code, converted from their custom .agc files to .s files for syntax highlighting, for Command Module (Comanche055) and Lunar Module (Luminary099). Digitized by the folks at [Virtual AGC](http://www.ibiblio.org/apollo/) and [MIT Museum](http://web.mit.edu/museum/). The goal is to be a repo for the original Apollo 11 source code. As such, PRs are welcome for any issues identified between the transcriptions in this repository and the original source scans for [Luminary 099](http://www.ibiblio.org/apollo/ScansForConversion/Luminary099/) and [Comanche 055](http://www.ibiblio.org/apollo/ScansForConversion/Comanche055/), as well as any files I may have missed.

####Attribution

     Copyright: Public domain.
     Filename:  CONTRACT_AND_APPROVALS.agc
     Purpose:   Part of the source code for Colossus 2A, AKA Comanche 055.
                It is part of the source code for the Command Module's (CM)
                Apollo Guidance Computer (AGC), for Apollo 11.
     Assembler: yaYUL
     Contact:   Ron Burkey <info@sandroid.org>.
     Website:   www.ibiblio.org/apollo.
     Mod history:   2009-05-06 RSB  Transcribed from page images.
    
     This source code has been transcribed or otherwise adapted from digitized
     images of a hardcopy from the MIT Museum.  The digitization was performed
     by Paul Fjeld, and arranged for by Deborah Douglas of the Museum.  Many
     thanks to both.  The images (with suitable reduction in storage size and
     consequent reduction in image quality as well) are available online at
     www.ibiblio.org/apollo.  If for some reason you find that the images are
     illegible, contact me at info@sandroid.org about getting access to the 
     (much) higher-quality images which Paul actually created.
    
     Notations on the hardcopy document read, in part:
    
        Assemble revision 055 of AGC program Comanche by NASA
        2021113-051.  10:28 APR. 1, 1969  
    
     Page 1

    #************************************************************************
    #                                                                       *
    #       THIS AGC PROGRAM SHALL ALSO BE REFERRED TO AS:                  *
    #                                                                       *
    #                                                                       *
    #               COLOSSUS 2A                                             *
    #                                                                       *
    #                                                                       *
    #   THIS PROGRAM IS INTENDED FOR USE IN THE CM AS SPECIFIED             *
    #   IN REPORT R-577.  THIS PROGRAM WAS PREPARED UNDER DSR               *
    #   PROJECT 55-23870, SPONSORED BY THE MANNED SPACECRAFT                *
    #   CENTER OF THE NATIONAL AERONAUTICS AND SPACE                        *
    #   ADMINISTRATION THROUGH CONTRACT NAS 9-4065 WITH THE                 *
    #   INSTRUMENTATION LABORATORY, MASSACHUSETTS INSTITUTE OF              *
    #   TECHNOLOGY, CAMBRIDGE, MASS.                                        *
    #                                                                       *
    #************************************************************************


    SUBMITTED:  MARGARET H. HAMILTON        DATE:   28 MAR 69
        M.H.HAMILTON, COLOSSUS PROGRAMMING LEADER
        APOLLO GUIDANCE AND NAVIGATION

    APPROVED:   DANIEL J. LICKLY        DATE:   28 MAR 69
        D.J.LICKLY, DIRECTOR, MISSION PROGRAM DEVELOPMENT
        APOLLO GUIDANCE AND NAVIGATION PROGRAM

    APPROVED:   FRED H. MARTIN          DATE:   28 MAR 69
        FRED H. MARTIN, COLOSSUS PROJECT MANAGER
        APOLLO GUIDANCE AND NAVIGATION PROGRAM

    APPROVED:   NORMAN E. SEARS         DATE:   28 MAR 69
        N.E. SEARS, DIRECTOR, MISSION DEVELOPMENT
        APOLLO GUIDANCE AND NAVIGATION PROGRAM

    APPROVED:   RICHARD H. BATTIN       DATE:   28 MAR 69
        R.H. BATTIN, DIRECTOR, MISSION DEVELOPMENT
        APOLLO GUIDANCE AND NAVIGATION PROGRAM

    APPROVED:   DAVID G. HOAG           DATE:   28 MAR 69
        D.G. HOAG, DIRECTOR
        APOLLO GUIDANCE AND NAVIGATION PROGRAM

    APPROVED:   RALPH R. RAGAN          DATE:   28 MAR 69
        R.R. RAGAN, DEPUTY DIRECTOR
        INSTRUMENTATION LABORATORY


From: https://www.zhihu.com/question/48363995/answer/110825741?utm_campaign=weekly223&utm_source=weekly-digest&utm_medium=email
作者：WarMonkey
链接：https://www.zhihu.com/question/48363995/answer/110825741
来源：知乎
著作权归作者所有，转载请联系作者获得授权。

看懂这份代码需要大量的宇航学知识。需要对轨道动力学，飞船GNC（制导导航与控制），深空测控，热管理和电源管理等等，具有非常深入的了解。
整个飞船由一台计算机控制，其运算性能大致相当于ATmega128单片机。现在仍然有很多卫星是一个ATmega128管理所有功能的。
具体的控制原理并不复杂，很多操作是依赖硬件完成的。而不像现在的先进卫星/飞船大量依赖软件。现在的小卫星很多使用SDR作为测控收发机，暴力运算产生中频信号。60年代没人这么干，都是硬件编解码器。

多使用专用硬件，让硬件部分担工作量，是不需要很多计算的。缺点就是体积和重量会比较大。

apollo主控计算机大致有如下的功能：
任务管理，进程管理和内存分配：没错，这个古董计算机有操作系统！这是世界上第一个OS，有协作式调度功能，有动态内存分配。阿波罗11着陆的时候，因为同时开了着陆雷达和对接测速雷达，导致计算机产生了内存不足错误。系统抛弃了低优先级的任务，保证最重要的的姿态稳定和发动机推力调节能继续正常运行。虽然着陆雷达挂了，但飞船仍然是可控的。
主导航系统有平台式IMU（模拟稳定回路，机械相连的六分仪），计算机可以直接读取姿态角/速度/位置。备用导航系统使用欧拉角微分方程实现捷联惯性导航，测量器件精度极高并且飞船动态小，更新速率小于100Hz都够了。
位置修正的信息直接从六分仪和平台的角度传感器读出，代入公式计算即可得到修正量。
数据下行使用的是硬件编码器，计算机直接传送原始数据即可安排发送。其他的设备如主发动机、推力矢量机构、姿态控制喷口，以及一些热控组件。这些用到了pid控制或者简单的开关控制，每秒更新几次到几十次。
键盘和显示器：有一个CRT图形显示器，可以显示着陆月球/返回地球时的弹道信息。还有另外一个计算器数码管和键盘（居然是继电器驱动的）

地面设备因为不太受到体积和重量限制，做得就更加夸张了：主控大厅是eidophor光阀实现的巨幅投影机，一台电脑控制整个地面操作中心，生成所有的图像内容。
http://cdn.arstechnica.net/wp-content/uploads/2012/10/liebergot-eidophor1.jpg

总的来说，在数字计算机刚刚出现的年代，能造出这样的自动化设备是极为惊人的。当年有这样的技术能力，去做一条大型自动化生产线，或者控制整个化工厂，是完全没问题的。

现在的自动化控制程序，会用新一点的理论，比如惯性导航可以换四元数算法（但是德国mikrocopter仍然是欧拉角微分方程）。或者添加一些新的方法，例如嫦娥使用了激光雷达进行地形识别和导航。但是呢，基本的思路仍然和当年的程序接近。

换了C++语言和现代的x86处理器，这套程序的实现不会有根本的变化。一个自动化系统最核心的部分，仍然是这些物理定律、算法和工程参数，它决定了一套自动化系统的基本能力和技术特性。

原作代码因为是汇编写成的，确实很难懂。如果想学习其工作原理，不妨看一看相关领域的教材。直接啃生肉虽然来得快，但确实是太难以消化了。代码里面的东西现在的教材全都有，看了都能学到。
