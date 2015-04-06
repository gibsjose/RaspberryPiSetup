#Raspberry Pi Programming Instructions
###Author: [gibsjose](www.gibsjose.com) – 3 April 2015

##Introduction
These notes describe programming with the GPIO pins.

##GPIO
The Raspberry Pi Model B+ has the following GPIO pinout:

![pins](http://ecuflashking.com/2012-12-06-RaspberryPi/Raspberry-Pi-GPIO-Layout-Revision-2-e1347664831557.png)

##PIGPIO
To interact with the GPIO pins using C, I used the PIGPIO library, found [here](http://abyz.co.uk/rpi/pigpio/).

###Installation
1. Obtain the `.zip`:
```bash
wget abyz.co.uk/rpi/pigpio/pigpio.zip
```

2. Unzip it and enter the directory:
```bash
unzip pigpio.zip
cd PIGPIO
```

3. Make and install:
```bash
make
make install
```

###Usage
Here is a sample program showing some of the capabilities of PIGPIO:

`pulse.c`:
```c
#include <stdio.h>
#include <pigpio.h>

int main(int argc, char * argv[]) {
    double start;

    if(gpioInitialise() < 0) {
        fprintf(stderr, "pigpio initialisation failed\n");
        return 1;
    }

    // set gpio modes
    gpioSetMode(4, PI_OUTPUT);
    gpioSetMode(17, PI_OUTPUT);
    gpioSetMode(18, PI_OUTPUT);
    gpioSetMode(23, PI_INPUT);
    gpioSetMode(24, PI_OUTPUT);

    // start 1500 us servo pulses on gpio4
    gpioServo(4, 1500);

    // start 75% dutycycle PWM on gpio17
    gpioPWM(17, 192); // 192/255 = 75%

    start = time_time();

    while ((time_time() - start) < 60.0)
    {
        gpioWrite(18, 1); // on

        time_sleep(0.5);

        gpioWrite(18, 0); // off

        time_sleep(0.5);

        // mirror gpio24 from gpio23
        gpioWrite(24, gpioRead(23));
    }

    // stop DMA, release resources
    gpioTerminate();

    return 0;
}
```

Compile and run with:
```bash
gcc -Wall -g -o pulse pulse.c -lpigpio -lrt -lpthread
sudo ./pulse
```
