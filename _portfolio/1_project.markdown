---
layout: post
title: Senior Design
description: Senior Design Project
img: /img/SD/SingleSlideDesc.png
---

My senior design project was to develop a system for a heart pump that gives patients an interface to their pump and provides remote monitoring capabilities. The pump, named Aortix<sup>TM</sup>, is the product of a venture-backed biotech company in Houston, <a href="http://www.procyrion.com/" target="_blank">Procyrion Inc</a>, and is undergoing FDA approval.

<a href="http://www.youtube.com/watch?feature=player_embedded&v=87zXfmAA4AI" target="_blank"> 
<img class="img_row_full" src="{{ site.baseurl }}/img/SD/flowtasticVid.png" alt="video describing our project" title="video describing our project"/>
</a>

Here is a single slide description of our project. Our team name was Flowtastic, and the system we developed consisted of 3 parts. Starting at the patient, our Arduino-based system interfaces with the Aortix<sup>TM</sup> pump's control box. The Arduino system talks to a smart phone app we developed, which talks to an electronic medical record (EMR) system on the doctor's side. We used an open-source EMR to simulate what the doctor would see.

<img class="img_row_full" src="{{ site.baseurl }}/img/SD/SingleSlideDesc.png" alt="single slide description of our senior design project" title="single slide description of our senior design project"/>

I worked mainly on the Arduino-based system. This system recieves data and sends commands to the Aortix<sup>TM</sup> control box via UART. It also communicates via Bluetooth to the smartphone. Because the smartphone is not always connected to the Arduino, there is an SD card on the Arduino for data to be sent. We wrote code for block-sized data storage and retrieval. The system is also capable of changing the pump speed based on the patient's weight fluctuation, or a Doctor's command, which could be sent from the EMR portal.

<img class="img_row_full" src="{{ site.baseurl }}/img/SD/flowtastic01.jpg" alt="our arduino system" title="our arduino system"/>
<div class="col three caption" style="float=left;">
	Left: Our team! Right:The Flowtastic Arduino module.
</div>
<br>

Check out an article about the project on Texas Medical Center <a href="http://www.tmcnews.org/2015/04/students-use-smarts-damaged-hearts/" target="_blank"> news </a>

