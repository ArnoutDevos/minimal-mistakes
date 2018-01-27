---
mathjax: false
tags: project engineering KULeuven
title: Dragon self-balancing two-wheel robot RF link
---
In 2013, [Jona Beysens](https://www.kuleuven.be/wieiswie/en/person/00101844), [Andreas Van Barel](https://www.kuleuven.be/wieiswie/en/person/00104326), and I were concerned with constructing a radiofrequency (RF) link from scratch on a Xilinx FPGA, using MATLAB Simulink for our Bachelor's in EECS project. Our design progress can be seen in the following two posters:

<figure class="half">
    <a href="https://arnoutdevos.github.io/assets/pdf/JLT_EPFL_ArnoutDevos.pdf"><img src="https://raw.githubusercontent.com/ArnoutDevos/ArnoutDevos.github.io/master/assets/images/DragonPoster1.png" height="150"></a>
    <a href="https://arnoutdevos.github.io/assets/pdf/JLT_EPFL_ArnoutDevos.pdf"><img src="https://raw.githubusercontent.com/ArnoutDevos/ArnoutDevos.github.io/master/assets/images/DragonPoster2.png" height="150"></a>
    <figcaption><i>KU Stealth</i>'s mirror-finish design and in-action image.</figcaption>
</figure>

Our RF link was part of a bigger project with 20 students to build a two-wheel self-balancing robot (similar to a segway). The final robot is shown in the video below:

<iframe width="560" height="315" src="https://www.youtube.com/embed/RHjh0bwaSz8?rel=0&amp;showinfo=0" frameborder="0" gesture="media" allow="encrypted-media" allowfullscreen></iframe>

Next to our functional RF link, I created an HTML5 web app which used the gyroscope and accelerometer of any mobile phone (Android, iOS, ...) to steer the robot. It uses a PHP and Perl backend to send commands from the webserver through a USB cable to our basestation, which then transmitted the data wirelessly to the robot.