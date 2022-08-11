# LiDAR

<br>

## 1. Structure and operating principles of LiDAR

### LiDAR (Light Detection And Ranging)

A LiDAR senseor sends a laser pulse in any particular direction and then we measure the time until a laser pulse hits an external object and is reflected back to the LiDAR is measured.
This measured time is called Time of Flight(ToF). ToF is measured to calculate the distance to the external object.<br>
Laser pulses travel at the speed of light, TOF multiplied by the speed of light equals the round trip distance. If you divide the round trip distance in half, you will finally get the distance to the external object.

<img src="https://user-images.githubusercontent.com/89831708/184099225-0c75a1ed-fe0c-4008-8eea-52ef6981bfbe.png" width="600" height="400"/>

* Using Infrared Frequencies
Most of the LiDAR sensors that are commercialized these days use infrared frequencies.
The LiDAR emits an infrared laser with a frequency of 332THz (903nm) and 346THz (865nm). 

<br>

### LiDAR - Output Data

LiDAR emits a single laser pulse for a single channel and emits multiple lasers for multiple channels. <br>
Autonomous vehicles mainly use 32 multi-channel LiDARs. And when we install a LiDAR on the top of the car, a 128 channels, or even a 256-channel LiDAR is installed.

![image](https://user-images.githubusercontent.com/89831708/184110498-e5d4938d-dbf2-44ba-ac9d-98cc7d5dc02d.png)
[image source](https://www.mdpi.com/2072-4292/12/3/510/htm)

<br>

## 2. Preprocessing algorithms for Point Cloud data

<br>

### Filtering


### Clustering
