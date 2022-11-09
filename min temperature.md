MIN	-7.9



[![Browsing HDFS](http://192.168.127.141:50070/explorer.html#/my_temperature/output)]





求最低气温:

[jian@HJ-hadoop ~]$ mkdir my_temperature

[jian@HJ-hadoop ~]$ cd my_temperature/

[jian@HJ-hadoop my_temperature]$ mkdir input

[jian@HJ-hadoop my_temperature]$ cd input

[jian@HJ-hadoop input]$ pwd

/home/jian/my_temperature/input

[jian@HJ-hadoop input]$ cd ..

[jian@HJ-hadoop my_temperature]$ ls

input  WeatherJob.java

[jian@HJ-hadoop my_temperature]$mkdir temperature_classes

[jian@HJ-hadoop my_temperature]$ cd temperature_classes/

[jian@HJ-hadoop my_temperature_classes]$ ll

total 0

[jian@HJ-hadoopmy_temperature_classes]$ cd ..

[jian@HJ-hadoop my_temperature]$

exportHADOOP_CLASSPATH=$(hadoop classpath)

[jian@HJ-hadoop	my_temperature]$echo $HADOOP_CLASSPATH

[jian@HJ-hadoop	my_temperature]$javac	-classpath ${HADOOP_CLASSPATH}	-d	temperature_classes      WeatherJob.java

[jian@HJ-hadoop my_temperature]$ cd temperature_classes/

[jian@HJ-hadoop temperature_classes]$ ll

total 12

-rw-rw-r-- 1 jian jian 2104 Nov  9 04:29 WeatherJob.class

-rw-rw-r-- 1 jian jian 1885 Nov  9 04:29 WeatherJob$WeatherMapper.class

-rw-rw-r-- 1 jian jian 2050 Nov  9 04:29 WeatherJob$WeatherReducer.class

[jian@HJ-hadoop temperature_classes]$ cd ..

[jian@HJ-hadoop my_temperature]$ jar -cvf WeatherJob.java -C temperature_classes/ .

added manifest

adding: WeatherJob$WeatherMapper.class(in = 1885) (out= 780)(deflated 58%)

adding: WeatherJob$WeatherReducer.class(in = 2050) (out= 898)(deflated 56%)

adding: WeatherJob.class(in = 2104) (out= 1049)(deflated 50%)

[jian@HJ-hadoop my_temperature]$ ll

total 8

drwxrwxr-x 2 jian jian  30 Nov  9 04:39 input

drwxrwxr-x 2 jian jian  107 Nov  9 04:29 temperature_classes

-rw-rw-r-- 1 jian jian 3495 Nov  9 04:42 WeatherJob.jar

-rw-rw-r-- 1 jian jian 3495 Nov  9 04:31 WeatherJob.java

[jian@HJ-hadoop	my_temperature]$hdfs	dfs	-mkdir	/my_temperature

2022-11-09 04:43:55,360 WARN util.NativeCodeLoader: Unable to load native-hadoop librar        y for your platform... using builtin-java classes where applicable

[jian@HJ-hadoop	my_temperature]$hdfs	dfs	-mkdir	/my_temperature/input

2022-11-09 04:48:04,054 WARN util.NativeCodeLoader: Unable to load native-hadoop librar        y for your platform... using builtin-java classes where applicable

[jian@HJ-hadoop my_temperature]$ cd input/

[jian@HJ-hadoop input]$ ll

total 80

-rw-rw-r-- 1 jian jian 79570 Nov  9 04:39 weather_data.txt

[jian@HJ-hadoop input]$ cd ..

[jian@HJ-hadoop my_temperature]$ ll

total 8

drwxrwxr-x 2 jian jian  30 Nov  9 04:39 input

drwxrwxr-x 2 jian jian  107 Nov  9 04:29 temperature_classes

-rw-rw-r-- 1 jian jian 3495 Nov  9 04:42 WeatherJob.jar

-rw-rw-r-- 1 jian jian 3495 Nov  9 04:31 WeatherJob.java

[jian@HJ-hadoop my_temperature]$ cd input/

[jian@HJ-hadoop input]$hdfs	dfs		-put	weather_data.txt /my_temperature/input

2022-11-09 05:46:01,095 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable

[jian@HJ-hadoop input]$ cd ..

[jian@HJ-hadoop	my_temperature]$hadoop	jar	WeatherJob.jar	WeatherJob	/my_temperature/input /my_temperature/output

 completed successfully

[jian@HJ-hadoop	my_temperature]$hdfs	dfs		-cat	/my_temperature/output/*

2022-11-09 06:00:52,249 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable

MIN   -7.9

[jian@HJ-hadoop	my_temperature]$hdfs	dfs		-get /my_temperature/output

2022-11-09 06:01:37,158 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable

 