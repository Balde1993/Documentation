TODO: 

 - DARE UN ORGANIZZAZIONE ALLA GUIDA 
	-SPLITTARLA
	-RIORGANIZZARE LE SEZIONI/PARAGRAFI 

# Flashing OBU K1


This simple guide is meant to summarize basic steps and concepts related to K1 OBU flashing activities and shell interaction.
First paragraph illustrates usage of **KVM** in order to update both EC21 and Nordic firmwares. 
Second paragraph explains EC21 connection and shell interaction.


First paragraph shows two different ways to transfer files on the EC21 board. 
In the second and third paragraphs two different flashing procedures are explained.  

## Updating with KM tool
KM is a tool developed by **KMaster** that provides basic functionalities to transfer and install firmwares of both EC21 and Nordic systems. To use this tool user must be connected to the USB-C port of the OBU. 
### EC21
In order to update EC21's firmware user has to follow these steps: 

	1.	Specify absolute pathname of the file to transfer
	2.	Generates its MD5 by pushing the homonym button
	3.	Digits  "4"  in the type textbox (EC21)
	4. 	Push "Aggiornamento collaudo" (only if EC21 firmware is a testing release)
	5.	Push "Impostazione dati trasferimento" 
	6.	Push "Trasferimento File" (scrollbar will advance)
	7.	Push "Check" button

The following picture shows toll GUI status in a nominal condition

![](https://github.com/Balde1993/K1OS-Documentation/blob/master/Flashing_OBU_K1_pictures/CHECK_KM.png)

Once check phase has finished, giving "completed" message user could click on "Esecuzione" to effectively run loaded firmware.
During firmware updating phase a blue led will blink,  this will stop blinking only when init_icc function of libicc.so is called. 

### Nordic
In order to update Nordic's user has to follow these steps:

	1.	Specify absolute pathname of the file to transfer
	2.	Generates its MD5 by pushing the homonym button
	3. 	Bootloader update:
		3a.	Digits  "1"  in the type textbox (bootloader)
		3b.	Push "Impostazione dati trasferimento" 
		3c.	Push "Trasferimento File" (scrollbar will advance)	3d.	Push "Check" button
	4. 	Application software update:
		4a. Digits  "1"  in the type textbox (bootloader)
		4b.	Push "Impostazione dati trasferimento" 
		4c.	Push "Trasferimento File" (scrollbar will advance)
		4d.	Push "Check" button

	5.	click "esecuzione"
  
Figure x: Nordic bootlader           |  Figure x: Nordic SW
:-------------------------:|:-------------------------:
![](https://github.com/Balde1993/K1OS-Documentation/blob/master/OBU_K1OS_pictures/CHECK__nordic_KM.png) | ![](https://github.com/Balde1993/K1OS-Documentation/blob/master/OBU_K1OS_pictures/CHECK__nordic_2_KM.png)



# Flashing using shell scripts

TODO in questa sezione spiegare come creare il tar e scompattarlo correttamente una volta trasferito sulla OBU


# EC21 interaction

## Shell login 
Once OBU is being connected by a Serial-to-USB wire and powered login is requested. The following are the default Quectel EC21 credentials: 

- User: **root**
- Password: **quectel123**

## Files transfer
File transfering can be achieved in two different ways at least:
    - By - Serial Connection 
    - By using ADB protocol exploting Micro-USB port (if present)


Every OBU has a Serial UART connection. As we can see in the following picture, three pins of the main board are configured natively tu support this type of communication.

### Serial Connection
Using a terminal Emulator like Teraterm we can easily connect to the OBU by setting up a serial connection with the following parameters:
-- parametri 

Here we can transfer a given file using **ZModem** functionality embedded into **Teraterm**, in order to do this user has only to browse manually the files he wants to trasfer on the OBU. 

### ADB Protocol 
A problem related to the previous connection is about the communication speed it can achieve. Using ADB Protocol, exploiting the microUSB port provided by the OBU we can achieve a higher communication speed. The adb command we have to use is **adb push src dest**, where: 

- src: is the path of the file we want to transfer 
- dest: is the path, starting from the root, of the destination file inside the OBU file system


# K1OS

An alternative to previous methods is to flash the OBU directly by the linux shell it provides. In particular, two scripts are used in order to accomplish this task. 

## K1OS scripts
*k1os* script provide a set of useful tools that can be used to manage and monitor *K1OS* running/stored on the OBU. Available commands are the following:

- start: starts 	k1os using *k1os_wrapper*
- stop:  stops current k1os running instance 
- restart: restarts current k1os running instance 
- status: Lists and shows current status of all k1os components
- checknetwork: Reports OBU connectivity status
- Version: Lists and shows current versions of all k1os components

Note: **k1** is an alias of **k1os** 

*K1OS_wrapper* script implements k1os application starting and stopping functions. In order to start k1os it executes **application-manager** which in turns should run every other components. 
Stopping function iterated over running processed killing them in reverse order with respect to the starting order. 

## K1 on File System

- **etc/k1**: contains applications configuration files (appname.cfg)
- **/var/lib/k1**: contains applications logging configuration files, specifying logging level related to the three channels (console, remote, file)
- **usrdata/log**: contains applications log files 
- **/usrdata/audio_resources/**: .opus files
- **/usr/bin/**: contains k1os application executable 
- **/usr/lib/**: Third party libraries (fvad, mosquitto, iccv etc..)
- **/usr/lib/**: Applications shared objects (e.g libobumanager.so )
- **/etc/dbus-1/system.d/**: DBus interfaces configuration file   

#### Development cycle basic steps
TODO: integrare in un'altra sezione 

1. Run make ARCH=armv7 (K10s folder level)
2. Run make ARCH=armv7 install 
3. adb shell 
4. k1 restart or k1 stop/start

## Country specific tolling
This section initially illustrates country specific tolling's configuration files and parameters. After that we can an overview on principal concepts related to application's integration on k1os system. These concepts only relate to both compile and run contests.

### Configuration files
Every country-specific's tolling application has three configuration files as every other applications: 
	
- service_name.cfg 
- service_name_log_conf.json
- tolling_configuration.json 

Service_name_log_conf contains logging configuration data. This data specifies logging levels (uint from 0 to 4) for each allowed logging channel: remote, file and console. 

In service_name.cfg file are speficied to types of information. The first is the name of the file where tolling configuration information are stored persistently. The second is the name of the MQTT topic related to that country specific tolling.



In tolling_configuration.json file we can find a set of parameters stricly related to tolling logic: 

- tr_id
- block_behaviour
- transmission_timeout
- max_packet
- filter_distance
- filter_time
- filter_logic
- filter_heading
- network_anomaly_timeout
- gps_anomaly_timeout
- progressive_trip_id
- data_id 


TODO: Documentare i singoli parametri una volta che li ho capiti 
eg : 
tr_id": "TRN_HU", "block_behaviour": 12, "transmission_timeout": 5, "max_packet": 4, "filter_distance": 15.345000000000001, "filter_time": 2, "filter_logic": 17, "filter_heading": 18.678000000000001, "network_anomaly_timeout": 30, "gps_anomaly_timeout": 50, "progressi

## K1OS Integration 

To integrate an application into k1os system developer has to include it into K1OS_APPLICATIONS variable defined in the main Makefile (at k1os level). This step is obviously performed in order to include the related project in the compiling procedure of the whole system. 

### Updating brand configurations

Second thing to do is to add tolling application to one or more k1os brands. There are three available brands: 

- basic 
- truck 
- next 

As we are talking about gnss tolling application what we need to do is to add this application to truck brand. In order to achieve this we need to update applications.db3, a sql database containing brands information accessed by application manager to understand which application it has to executes). This database is generated using script named fill_applications_db.sh. Let's see database tables we need to update in order to achieve this integration. 

  - applcation_inventory
  - default_application_environment(optional)
  - brand_application_inventory (truck configuration's brand_id is 20)

So, if we imagine we need to add tolling-hu application to truck brands we will have the followings: 

<code>

INSERT INTO APPLICATION_INVENTORY ( application_id, name) VALUES (18, 'tolling-hu');

 
INSERT INTO DEFAULT_APPLICATION_ENVIRONMENT ( application_id, variable ) VALUES (18, 'LD_LIBRARY_PATH=:/usr/lib/k1');

INSERT INTO BRAND_APPLICATION_INVENTORY( brand_id, application_id, startup_order, enabled ) VALUES (20, 18, 16, 1);

</code>

### Tolling service activation
In the operative environment active services are retrieved exploiting OBU network functionalities. In other words *k1-proxy* is responsibile to dinamically update OBU's active services. In the development phase we can configurate this information editing **var/lib/k1/tolling_activation.json*, considering once again the case of *tolling-hu* we will have to add something like: 

```json
{
 "service_999":{
    "trn_id": "TRN999",
    "service_name": "tolling_HU",
    "service_id": 999,
    "active": true
  }
}
```

TODO: espandare i vari items una volta che sono tutti chiari al 100%
 
### Define tolling domain polygon 

Every gnss tolling domain is stricly associated to a geographic polygon. This polygon defines where the tolling domain is located around the globe. In order to test k1os behavior with respect to a tolling application we have introduce a new polygon. Polygons for testing activities (l'ho capita cosi ma probabily non è cosi) are persistenly stored in the device file system on *./var/lib/k1/gnss_polygons_conf.json* file. Obviosly in this file polygons are reduced to simple bounding boxes to simplify testing activities. 
Considering once case the case of *tolling-hu* we will have to define polygon in this way: 

```json
   {
    "domain_id":999,
    "domain_name":"HU",
    "points":[
      {
        "latitude":46,
        "longitude":16
      },
      {
        "latitude":46,
        "longitude":22
      },
      {
        "latitude":48,
        "longitude":22
      },
      {
        "latitude":48,
        "longitude":16
   }]}
```

TODO: Questa sezione poi andrà espansa un po una volta che saranno chiari i concetti: 
	- da dove vengono presi i poligoni ufficiali? quando? 
Note: Finally will be useful to update also *k1os.sh* to let it takes into account also the integrate application.

### Positioning Service demo mode 
Positioning service's main responsibilities is to get position update by the GPS module e forward them on DBus interprocess communication system. In the development phase developer may need to command positions at will. For this purpose two main methods are being identified: 

- Use Spirent to directly mock SAT system 
- Exploit positioning service demo mode 

First method requires the presence of Spirent system while second method only require few modifications on configuration files. 
First thing to do is to edit *positioning_service.cfg*, in particular: 

- demo_mode_active=true
- demo_mode_data_file=positioning_service_demo_mode_data_file.json

*demo_mode_data_file* file is a json file where we can specify a list of position update the positioning service has to produce. In the following snippet we can see how this file is formatted: 

```json
    "position_updates": [
        {
            "delay_before_this_update": 10.0,
            "latitude": 45.445065,
            "longitude": 9.161755,
            "altitude": 120.0,
            "speed": 0.0
        },
                {
            "delay_before_this_update": 0.0,
            "latitude": 47.0,
            "longitude": 18.0,
            "altitude": 110.0,
            "speed": 56.97017922986315
        }]
```

This is the fastest method to implement a simple simulation with which the user can test tolling applications. 




