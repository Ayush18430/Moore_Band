# MooreBand
AI enabled cattle health tracking IOT device


It's an AI bases cattle health tracking system which uses 2 Sensor stack in moore collar i.e. Accelerometer and temperature sensor. The data is sent to firebase where is processed over a ML based algorithm and the processed data is crunched into form of cattles activity.
Moore is a cattle monitoring solution that detects anomalies in cattle behaviour. We believe that if we can quickly detect changes 
in a cow's behaviour, we can give the dairy farmer the necessary information to observe, 
diagnose or separate the infected cow and reduce losses in milk production. The Moore 
Solution is made up of 4 significant components.
1. The Moore Collar
2. A Wi-Fi Router
3. Cloud Database (Firebase)
4. Moore Mobile App

This repository consists of - 
1. 3D model of IOT device casing
2. Android App Code
3. Esp Code
4. Circuit Diagram
5. ML Code
6. Prototype circuit


PROCESS
1. Acquiring the sensor data –
We have used the Esp32 development board in our prototype, which has an inbuilt 
Wi-Fi functionality. Esp32 can store and read the sensors' data to ascertain cattle's 
orientation and actions, which supports judging the cattle's health. In developing our 
prototype, we have used the MPU6050 Accelerometer-Gyroscope module DS18B20 
probe-type Temperature Sensor.
• MPU6050 is low power and low-cost 6-axis gyroscope and accelerometer 
used to define cattle's orientation and track its movement using 3-axis 
acceleration. Esp32 communicates with the sensor using the I2C 
communication protocol and then stores these acceleration values, which 
will define the cattle's orientation.
• DS18B20 is a probe-type low power temperature sensor that measures 
the temperature of a body in contact with the probe and sends the 
temperature readings in the form of an analog signal, which are read by 
the Esp32 microcontroller. This data this then sent to the server.
ESP 32 has two cores. Core 1 continuously monitors all the sensor data and stores 
them in a queue (queue store_data), implemented using a circular array of enough 
size. This process is uninterrupted, which ensures no loss of data. 
2. Pre-processing the data –
Core 0 occasionally wakes up to pre-process the available data in the store_data 
queue. It processes 16 data units (each consisting of accelerometer and gyroscopic 
data, temperature value) to obtain the average deviation, RMS value, Peak to peak 
value, vectorial dynamic body acceleration (VeDBA) and crest factor. The processed 
6 | P a g e
data is then stored in a second queue (queue processed_data) implemented using 
another circular array of larger size. This process is repeated at regular intervals.
3. Transmitting data to the server –
When there are enough data points to send (every 15 minutes), Core 0 will power up 
Wi-Fi on esp32, connect to a nearby Wi-Fi network, and establish a connection with 
the FireBase real-time database. The core then will start uploading the data to the 
database.
Every neck collar will have its designated identification code, and it will be linked to a 
particular cow ID. This information is also available on the database. The ESP first 
reads this cow ID and appends data correspondingly into the Cow's Database.
4. Using an ML model to predict cow's health
After the data is uploaded onto the cloud database, the data for each cow is passed 
through a Machine Learning model. The machine learning model was trained using 
similar data using a random decision forests algorithm to classify the different 16-
second samples into different behaviour categories of cattle: low activity behaviours 
(lying, standing, ruminating) and high activity behaviours (feeding, walking). This 
data is then summed to find the total time for different activities for every 15 
minutes time frame and stored; for every hour, an activity index is created, which 
gives the ratio of change in hourly activity levels compared to activity levels from the 
same time period from the previous two days. This is useful for identifying abnormal 
behaviours or checking for extreme deviation from the normal routine.
Along with the activity metrics, the cow's temperature is also compared to its values 
for the previous two days to identify any abrupt change in normal temperatures.
5. Making the processed data available to farmers through a mobile app
The user is given provisions to login to the app using the phone number and OTP 
using FireBase authentication. The user will have to initially set up the barns and cow 
details (cow name, belt ID, breed, History of diseases and vaccinations) in each barn. 
Then the user will have to link the belt to the cow using the belt ID. Then the user 
will get the information about cow's health which includes pregnancy cycle, 
temperature and unusual behaviour. The cows with such problems will be 
highlighted, and the barns containing unhealthy cows will also be highlighted. The 
user will have the option to add new barns and cows. The user will also have a 
dashboard to see barn status and total linked cows.

APP 
TECH STACK
1. Android Studio (JAVA, XML) 
2. Material UI
3. FireBase
• Authentication
• Realtime database
4. Views 
• Recycler view
• Search bar
• Alert dialogue box
• Pie chart for temperature data
• Fragment
• Bottom navigation bar
The app is implemented in Android Studio using JAVA and XML. Material 
UI is used for attractive and modern UI. DataBinding is used to bind UI 
components in the layouts to data sources in the app. Authentication of 
the user is done by Mobile number / OTP using firebase Authentication, 
auto-fill functionality of OTP is implemented, and a timer is displayed for 
the user to wait accordingly for the subsequent OTP request. Dashboard 
and Profile Fragment are navigated through the bottom navigation bar. 
Barn's and cow's data is fetched from the firebase real-time database and 
displayed in RecyclerView, including the status of Barn/Cow depicted by 
the colour of the RecyclerView item. Search Functionality is added using 
the textChangedListner function of TextWatcher class.
The temperature of the cow is displayed using a piechart UI object.
Realtime data is fetched for each cow, and if an unusual behaviour flag 
turns true, then an alert is sent to the user indicating the possibility of 
disease
