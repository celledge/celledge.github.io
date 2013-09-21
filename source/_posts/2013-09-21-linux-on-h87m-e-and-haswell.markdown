---
layout: post
title: "Linux on H87M-E &amp; Haswell"
date: 2013-09-21 09:55
comments: true
categories: Linux
---

I wanted to write down my notes for getting Ubuntu Linux fully
running on an Asus H87M-E with a Intel Haswell processor. I use this
on a media center, so I needed HDMI video and audio support.

* Ubuntu 13.04 is required for support of the Haswell GPU.
* Linux kernel 3.10 is required for HDMI audio support.
* I used Upubuntu to install 3.10.7 for my system.
* [Never use an older (1200/1500 series) Sandforce Controller SSD with Haswell Motherboards][SSD].
* The hardware controller is a NCT6791D. You will need a very recent
  version of the [nct6775 driver][nct]. It will probably be merged in
  Linux 3.12.
* If you use Western Digital Green Drives on Linux be sure to look at
  [idle3-tools][idle3].

[SSD]: http://www.ocztechnologyforum.com/forum/showthread.php?111964-Vertex-2-Agility-2-and-Haswell
[nct]: https://github.com/groeck/nct6775
[idle3]: http://idle3-tools.sourceforge.net/

Here is a script that I wrote to setup the fans in a way useful for my
system. One fan sits on my HDs, so I wanted to regulate it based on
their temperature.

<!-- more -->

{% codeblock H87M-E Fan Controller - fanctl.py %}
#!/usr/bin/python

import daemon
import time
from subprocess import check_output

fan_driver = '/sys/devices/platform/nct6775.656/'

fan_setup = [
    ('pwm1_enable',5),
    ('pwm1_temp_sel',2),
    ('pwm1_auto_point1_temp',39000),
    ('pwm1_auto_point1_pwm',0),
    ('pwm1_auto_point2_temp',41000),
    ('pwm1_auto_point2_pwm',110),
    ('pwm1_auto_point4_temp',75000),
    ('pwm1_auto_point4_pwm',255),
    ('pwm3_enable',1),
    ('pwm3',90),
]

for s in fan_setup:
    with open(fan_driver+s[0],'w') as fdf:
        fdf.write(str(s[1]))

def get_hdd_temp(hdd):
    temp_lines = check_output(['smartctl','-A', hdd]).strip().split("\n")
    temp_line = filter(lambda l: 'Temperature' in l, temp_lines)[0]
    temp = temp_line.split()[-1]
    return int(temp)

def get_hdd_max_temp():
    drives = ['/dev/sdb', '/dev/sdc']
    temps = map((lambda l: get_hdd_temp(l)),drives)
    return max(temps)

def hdd_temp_to_pwm(t):
    if t<=31:
        return 0
    elif t<=48:
        return 110+(t-34)*10
    else:
        return 255

def hdd_fan_loop():
    while True:
        t = get_hdd_max_temp()
        pwm = hdd_temp_to_pwm(t)
        with open(fan_driver+'pwm3','w') as fdf:
            fdf.write(str(pwm))
        time.sleep(3)

def main():
    try:
        hdd_fan_loop()
    finally:
        with open(fan_driver+'pwm3','w') as fdf:
            fdf.write('170')

with daemon.DaemonContext():
    main()
{% endcodeblock %}
