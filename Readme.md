## Detailed Design-
MapReduce is a programming model designed for processing large volumes of data in parallel by dividing the work into a set of independent tasks. The Master instance accepts the input from user and performs the user-provided operation. Mapper & reducer instances perform mapping & reducing tasks respectively. Key value store instance stores the intermediate & final data. There is a proper synchronization between each instance. Master is the backbone of the system, which co-ordinates with all other entities.
The system uses 4 auxiliary files:
i) output.txt
The output of the program (ie. Word Count/Inverted Index) is stored into this file. This file is created at KeyValueStore.
ii) log.txt
Log of each activity is stored into this file for debugging, error recovery and fault tolerance. This file is created at master instance.
iii) config.csv
Configuration file contains the pair of each environment variable along with its value. This is accessed each time the system starts & initializes the environment variable. This file is user-defined file which has all system configurations such as ports, IPs, number of mappers etc. This file is present at master-instance.
iv) data.csv
The output of the mapper is stored into this Key Value store. This is also called as a intermediate data which is stored in keyvaluestore-instance.

## Pseudo code
1. Start the master-instance. Provide proper operation to be performed along with the input file(s) on which the operation is performed.
2. Start the keyvaluestore-instance & Mapper instances (mapper-instance1, mapper-instance2, mapper-instance3). The startup script for executing each java jar files are executed once the instance is started.
3. Connect mapper & key value store instances
4. Read & split the input text data and distribute among different mappers using Round-Robin technique.
5. Mapper scans all tokens & stores the result into keyvalue store.
6. Control is returned back to Master. Master then fetches all mapper output from key value store.
7. Master clears the keyvalue store contents.
8. Hash values of each token is computed, and tokens are forwarded to reducer using the corresponding hash values.
9. Reducer maintains a map data structure to maintain the counts of each token.
10. At the end of processing, reducers set the result into key value store.
11. Master then commands the keyvalue store to export the data into output.txt file on Key value store instance.
The above code also logs each event along with proper timings. At the end of each phase, corresponding instances are terminated (Eg.- after mapping phase, Mappers are terminated).

## Program Flow
The program is executed by accessing the master instance. Following are the steps to execute on the google sdk console for accessing (SSH) and executing the jar file (ie. Main.java class file):
➢ gcloud compute ssh master-instance
➢ cd cloud_mapreduce
➢ java -jar cloudMapreduce-1.0-SNAPSHOT-jar-with-dependencies.jar

## VMs used-
The above instances are created using Machine type - n1-standard-1 (1 vCPU, 3.75 GB memory). 
Following are the instances configurations: 
i) Zone – us-central1-c 
ii) Firewalls – Allow HTTP traffic, Allow HTTPS traffic 
iii) OS - Debian 9 
iv) Size – 10GB 
v) On host maintenance – Migrate VM instances 
vi) Cloud API access scopes – Allow full access to all Cloud APIs 
vii) CPU platform – Intel Haswell 
viii) Disk Type – Persistent storage Startup scripts are also added. Eg. – For mapper instance, #! /bin/bash cd /home/sudip java -jar Mapper-1.0-SNAPSHOT-jar-with-dependencies.jar


## Performance numbers
i) Execution Time - The system performs the operation within 4 minutes based on the file input size of 10000 words.
ii) Memory Usage – Max. memory available is 10 GB. Memory reserved is 500 Mb for the OS and other system libraries. Out of this, 300 Mb is used for running the experiment and storing the data.
iii) CPU Usage – CPU usage goes as high as 51% of the total CPU capacity.
iv) Instances run at a time – 8 (1 master, 1 keyvaluestore, 3 mappers, 3 reducers)

## Weaknesses, Design & implementation improvements
The system has following weaknesses which can be improved in the later stage of developments:
1. The system does not support VM creation and destruction.
2. The system is not fault tolerant.
3. The system is run on non-preemptible VMs. Future developments can be added to make it run on transient VMs.
