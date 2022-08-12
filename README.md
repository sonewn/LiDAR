# LiDAR

<br>

## 1. Structure and operating principles of LiDAR

### LiDAR (Light Detection And Ranging)

A LiDAR senseor sends a laser pulse in any particular direction and then we measure the time until a laser pulse hits an external object and is reflected back to the LiDAR is measured.
This measured time is called Time of Flight(ToF). ToF is measured to calculate the distance to the external object.<br>

* **ToF (Time of Flight)** <br>
  * Laser pulses travel at the speed of light, TOF multiplied by the speed of light equals the round trip distance. If you divide the round trip distance in half, you will finally get the distance to the external object.
  * Most of the LiDAR sensors that are commercialized these days use infrared frequencies. The LiDAR emits an infrared laser with a frequency of 332THz (903nm) and 346THz (865nm). 

<img src="https://user-images.githubusercontent.com/89831708/184099225-0c75a1ed-fe0c-4008-8eea-52ef6981bfbe.png" width="600" height="400"/> [image source](https://en.wikipedia.org/wiki/Lidar)




<br>

### LiDAR - Output Data

LiDAR emits a single laser pulse for a single channel and emits multiple lasers for multiple channels. <br>
Autonomous vehicles mainly use 32 multi-channel LiDARs. And when we install a LiDAR on the top of the car, a 128 channels, or even a 256-channel LiDAR is installed.

![image](https://user-images.githubusercontent.com/89831708/184110498-e5d4938d-dbf2-44ba-ac9d-98cc7d5dc02d.png)
[image source](https://www.mdpi.com/2072-4292/12/3/510/htm)

<br>

The LiDAR sensor consist of a single laser pulse transmitter for each channel and a receiver, and we can say that one laser pulse represents a point channel.
Point channels are evenly distributed within the elevation range **from -25˚ to 15˚** (양각 범위) based on the horizon line. <br>

The LiDAR sensor, which has a number of point channels, rotates by θ˚ in the azimuth direction, and the point channels continuously measure the distance at every short cycle. When it rotates about the azimuth, we measure the distance at every 0.1˚ to 0.4˚. If the resolution is 0.1 degrees, we measure 3,600 times at 360 degrees, which means there are 3,600 channels.<br>
As the LiDAR rotates, it makes 10 to 20 measurements per second. So the rotational speed is 10 Hz to 20 Hz.




<br>

* Measurement of LiDAR (Multi channels)

  * the distance **R** to the object that is calculated from ToF, the Azimuth angle θ, and the elevation angle Φ. 
  * the direction of the laser signal from LiDAR is determined by θ and Φ. 
  * the three-dimensional position information is calculated by the direction and the distance **R**.
  
  * Each point data of the LiDAR contains information about the 3D position (X,Y,Z), Time when a measurement occurs, and the Intensity of the reflected signal(which measures how strong a reflected wave is received).

<br>
  
  > **_Intensity of the reflected signal_** <br>
  If the signal travels too far and come back, the intensity of the signal is low, but if it hits a highly reflective object that is far away from the LiDAR, the intensity increases. <br>
  For ex, when a laser is emitted to the ground, the intensity of the laser that is reflected on the asphalt is low, and the intensity of the laser reflected on the lane(which colored with white paint) is high.

<br>

> **_Data size of the LiDAR Point genrated per second_** <br>
(number of channels) x (number of samples per channel) x (Rotation Rate) <br>
= (e.g) 64 x 3600 x 8 ≈ 1.75M -> 1.75M x 20bytes = 35Mbytes/sec (this is not exact value of the commercially available LiDAR, but similar)
<br>

**[LiDAR channels example]**
|point|contents|
|:---:|:---:|
|point 1|x, y, z, Time, Intensity, Channel Index |
| ... |" |
|point N | x, y, z, Time, Intensity, Channel, Index |





## 2. Preprocessing algorithms for Point Cloud data

<br>

### Filtering
<br>

#### (1) Voxel Grid Filter 
We can represent three-dimensional space in three-dimensional cubes in terms of the `voxel`.
LiDAR is creating a point cloud with a lot of points in th three-dimensional domain, we divide that three-dimensional space in three-dimensional area into voxels, and then bundle up LiDAR points in each voxel to make centroid point a representative point. So we leave one centroid pointper voxel and remove the rest. <br><br>
`The Voxel Grid Filter` is a pre-processing method that allows noisiness to be seen separately and represents a point per voxel without necessarily using too much data, resulting in data with sufficient density. 

<br>

#### (2) Radius Outlier Removal Filter (ROR Filter)

<img src="https://user-images.githubusercontent.com/89831708/184300188-3722d437-639d-4480-8f8a-b9a63f8457b4.png" width="600" height="400"/> [이미지 출처](https://velog.io/@lottocomeon/Lidar)

If there are enough neighboring points within a given radius, we consider those points to be connected to other points.
If there aren't, we remove them because we consider them as points that are far away. (Through this simple operation, you can get rid of the noise measurement that occurs when it snows)
<br>

#### (3) Statistical Outlier Removal Filter (SOR Filter)

<img src="https://user-images.githubusercontent.com/89831708/184300544-2abd1565-cdb5-4c31-8fff-a9b3ebc3be80.png" width="600" height="400"/> [이미지 출처](https://velog.io/@lottocomeon/Lidar)

If you calculate the distance to the nearest K points for each point, you can calculate the average distance among them. Then you can calculate the standard deviation of the distance. If the distance between each point and the surrounding K-points is very different from certain distribution, these K-points are quite far from one point.

> For example, the total average of the distance is μt, the standard deviation of the total distances is σt, and the average of the distances to the nearest K points around a particular point is μi. If μi ≥ μt + α·σt where is larger than 1 such as 1.5 or 2, which means that this point is statistically far away from the points around it, it's considered an outlier and it will be deleted.


<br><br><br>

### Clustering

<br>

#### (1) Euclidean Cluster Extraction

![image](https://user-images.githubusercontent.com/89831708/184312660-4cdc78bc-d684-4a2d-b63e-8ea62052cd70.png)

A randomly selected point out of several points is called a Seed Point. There is a black circle around the Seed Point, an area that has a certain distance from the Seed Point. There are also blue points in the circle in addition to the red Seed Point. Those points are regarded as the same cluster. You set another point in the same cluster as Seed Point again and perform clustering. If points of another cluster overlap the original cluster, you can see them as the same cluster.

If you keep looking for clusters like this, some points don't have overlapping cluster points. In this case, you might think it's an outlier, or if you don't have enough points in the circle, you might think it's not the same cluster. This is an algorithm that simply determines whether it's the same class or not depending on the distance.

<br>

#### (2) K-Means clustering

This technique has the disadvantage of setting the number of clusters in the LiDAR Point Cloud in advance. 
The LiDAR points are gray dots, If you know that there are four clusters here, you specify four points. Ofcourse the first four points are randomly specified. Now gathering points for the explanation. So we'are going to specify these four points, and then aggregate the rest of the points in the nearest cluster of these four.

So we call this the Expectation Step. For example, the points in the upper left corner are the closest to the blue dot, to this is the blue cluster. The points in the lower right corner are the closest to the purple cluster in the center, so they are the same cluster as the purple one. After grouping the rest of the points, you will get the new average of the blue cluster, the green cluster, and so on. The average will be changed.

And the centroid of enw clusters is different from the four points we initially specified, and we regroup the clusters that are closest to these newly created averages. If you repeat the process of grouping and finding the new average a few times, you will get the clustering results like the one shown on the right side.

> 1\~2. We specify three points as K1, K2, and K3 and grouping by designated location <br>
3\~5. Initializing the position of the centroid to the average position calculated in step 4 in step 5. <br>
6. A new centroid is found again by clustering from the previous centroid.
<br>
If you continue to repeat steps 3~5, the centroid and the cluster will no longer change, which means hath we're finished with the clustering.




