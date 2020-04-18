# 1. Laboratorio HDFS
## EMR en el DCA

1. Descargar _dataset_ del repositorio y comprimirlo
```
git clone https://github.com/st0263eafit/bigdata.git 
cd bigdata/datasets && zip –r dataset * 
```
2. Copiar el _dataset_ al **DCA** y descomprimirlo en una carpeta
```
scp dataset.zip marami26@192.168.10.116:/home/marami26/dataset.zip 
ssh marami26@192.168.10.116 
mkdir datasets && unzip dataset.zip -d datasets 
```
3. Crear carpeta para almacenar los datasets en mi directorio de **HDFS**
```
hdfs dfs -mkdir /user/marami26/datasets 
```
4. Copiar el _dataset_ del **DCA** al directorio de **HDFS**
```
hdfs dfs –copyFromLocal datasets/* /user/marami26/datasets/ 
```

**Resultados**
* Terminal
![terminal](https://github.com/Mateo-RH/TopicosTelematica-BigDataLab/blob/master/imagenes/dca/terminal.png)

* Ambari
![ambari](https://github.com/Mateo-RH/TopicosTelematica-BigDataLab/blob/master/imagenes/dca/ambari.png)

## S3
Se crea un bucket en AWS S3 y se configura desbloqueando el acceso publico.
Tambien se debe de dar permisos de lectura publica a la carpeta _datasets_.

![s3](https://github.com/Mateo-RH/TopicosTelematica-BigDataLab/blob/master/imagenes/s3/bigdata-s3.JPG)

## AWS EMR

1. Creamos un cluster en EMR solicitando los siguientes componentes
 * **Hadoop:** framework de bigdata con almacenamiento en el emr-5.27.0
 * **Hue:** interfaz grafica de asistencia para interactuar con el cluster
 * **Zeppelin:** notebook basado en web
 * **Sqoop:** Herramienta para transferir datos voluminosos entre _Hadoop Apache_ y bases de datos estructurales como bases de datos relacionales.
 * **Oozie:** Es un _workflow scheduler_ para administrar jobs de Apache Hadoop
 * **Hive:** _Datawarehouse_ que facilita la lectura, escritura y manejo de grandes cantidades de datos.
 * **Livy:** Es un servicio que permite la facil comunicacion con un Cluster de _Spark_ a traves de una interfaz _REST_
 * **Tez:** Marco para aplicaciones de procesamiento de datos basadas en YARN en Hadoop
 
![creacionCluster.JPG](https://github.com/Mateo-RH/TopicosTelematica-BigDataLab/blob/master/imagenes/cluster/creacionCluster.JPG)

2. Habilitamos el acceso publico pero restringimos los puertos 8888 y 8890 que seran utilizados para _Hue_ y _Zeppelin_

![publicAccess.JPG](https://github.com/Mateo-RH/TopicosTelematica-BigDataLab/blob/master/imagenes/cluster/publicAccess.JPG)

3. Configuramos el grupo de seguridad del master para exponer los puertos 8888 y 8890

![secGroups.JPG](https://github.com/Mateo-RH/TopicosTelematica-BigDataLab/blob/master/imagenes/cluster/secGroups.JPG)

4. Para poder clonar este cluster damos click en la opcion _Exportación de la CLI de AWS_

![recreacion.JPG](https://github.com/Mateo-RH/TopicosTelematica-BigDataLab/blob/master/imagenes/cluster/recreacion.JPG)

5. Recreacion del cluster con AWS CLI
```
aws emr create-cluster --auto-scaling-role EMR_AutoScaling_DefaultRole --applications Name=Hadoop Name=Hive Name=Hue Name=Spark Name=Zeppelin Name=Tez Name=Sqoop Name=Oozie Name=Livy --ebs-root-volume-size 10 --ec2-attributes '{"KeyName":"cluster","InstanceProfile":"EMR_EC2_DefaultRole","SubnetId":"subnet-08c32029","EmrManagedSlaveSecurityGroup":"sg-00bebe09e6bbb50af","EmrManagedMasterSecurityGroup":"sg-08fdf4754b70c288a"}' --service-role EMR_DefaultRole --enable-debugging --release-label emr-5.27.0 --log-uri 's3n://aws-logs-242641527932-us-east-1/elasticmapreduce/' --name 'MiClusterEMR' --instance-groups '[{"InstanceCount":2,"EbsConfiguration":{"EbsBlockDeviceConfigs":[{"VolumeSpecification":{"SizeInGB":32,"VolumeType":"gp2"},"VolumesPerInstance":2}]},"InstanceGroupType":"CORE","InstanceType":"m4.xlarge","Name":"Principal - 2"},{"InstanceCount":1,"EbsConfiguration":{"EbsBlockDeviceConfigs":[{"VolumeSpecification":{"SizeInGB":32,"VolumeType":"gp2"},"VolumesPerInstance":2}]},"InstanceGroupType":"MASTER","InstanceType":"m4.xlarge","Name":"Maestro - 1"}]' --scale-down-behavior TERMINATE_AT_TASK_COMPLETION --region us-east-1
```

6. Eliminacion del cluster con AWS CLI
```
aws emr terminate-clusters --cluster-ids <clusterID>
```

**Resultados**

* **Hue**
![hue.JPG](https://github.com/Mateo-RH/TopicosTelematica-BigDataLab/blob/master/imagenes/cluster/hue.JPG)

* **Zeppelin**
![zeppelin.JPG](https://github.com/Mateo-RH/TopicosTelematica-BigDataLab/blob/master/imagenes/cluster/zeppelin.JPG)

* **SSH**
![ssh-conection.JPG](https://github.com/Mateo-RH/TopicosTelematica-BigDataLab/blob/master/imagenes/cluster/ssh-conection.JPG)

## Hue

#### HDFS

1. En el menu hamburguesa de arriba a la izquierda seleccionamos _Files_

![hdf1.JPG](https://github.com/Mateo-RH/TopicosTelematica-BigDataLab/blob/master/imagenes/hue/hdf1.JPG)

2. Ahora seleccionamos _Upload_ y subimos el _dataset_

![hdfs2.JPG](https://github.com/Mateo-RH/TopicosTelematica-BigDataLab/blob/master/imagenes/hue/hdfs2.JPG)

![hdfs3.JPG](https://github.com/Mateo-RH/TopicosTelematica-BigDataLab/blob/master/imagenes/hue/hdfs3.JPG)

#### S3

1. En el menu hamburguesa de arriba a la izquiera seleccionamos _S3_

![s31.JPG](https://github.com/Mateo-RH/TopicosTelematica-BigDataLab/blob/master/imagenes/s3/s31.JPG)

2. A continuacion veremos los _Buckets_ de S3 que poseemos en la cuenta

![s32.JPG](https://github.com/Mateo-RH/TopicosTelematica-BigDataLab/blob/master/imagenes/s3/s32.JPG)

3. Al seleccionar el Bucket creado previamente, tendremos acceso a el dataset que cargamos alli.

![s33.JPG](https://github.com/Mateo-RH/TopicosTelematica-BigDataLab/blob/master/imagenes/s3/s33.JPG)

# 2. Laboratorio Map/Reduce

## MyPC

Para ejecutar el [wordcount-local.py](https://github.com/st0263eafit/bigdata/blob/master/02-mapreduce/wordcount-local.py) y [wordcount-mr.py](https://github.com/st0263eafit/bigdata/blob/master/02-mapreduce/wordcount-mr.py) en mi computador primero instale Python, pip y mrjob.
1. [Instalacion de python](https://github.com/BurntSushi/nfldb/wiki/Python-&-pip-Windows-installation)
2. Despues de instalar python y pip, ejecutar la siguiente instruccion en la terminal: ``` pip install mrjob``` 

**Corriendo el wordcount**
![pc.JPG](https://github.com/Mateo-RH/TopicosTelematica-BigDataLab/blob/master/imagenes/labs/pc.JPG)

**Output Files**
* [local](https://github.com/Mateo-RH/TopicosTelematica-BigDataLab/blob/master/imagenes/labs/wordcount-local-output.txt)
* [mr](https://github.com/Mateo-RH/TopicosTelematica-BigDataLab/blob/master/imagenes/labs/wordcount-mr-output.txt)

## DCA/Jupyter

El DCA ya tenia las herramientas neesarias para realizar el ejercicio, por lo que solo fue necesario conectarme y ejecutar los scripts en una terminal de jupyter

**Corriendo el wordcount**
1. Sin Map Reduce

![sinMapReduce.JPG](https://github.com/Mateo-RH/TopicosTelematica-BigDataLab/blob/master/imagenes/local/sinMapReduce.JPG)

2. Con Map Reduce

![conMapReduce.JPG](https://github.com/Mateo-RH/TopicosTelematica-BigDataLab/blob/master/imagenes/local/conMapReduce.JPG)

## EMR

Para realizar el mismo ejercicio en el EMR de aws, primero fue necesario completar los siguientes pasos:
1. conectarme al nodo master del cluster: ```ssh -i "cluster.pem" hadoop@ec2-18-212-42-209.compute-1.amazonaws.com```
2. instalar git: ```sudo yum install -y git```
3. descargar el repositorio ``` git clone https://github.com/st0263eafit/bigdata.git ```
3. instalar mrjob: ```sudo pip install mrjob```

**Corriendo el wordcount**
1. Sin Map Reduce

![emr-local.JPG](https://github.com/Mateo-RH/TopicosTelematica-BigDataLab/blob/master/imagenes/labs/emr-local.JPG)

2. Con Map Reduce

![emr-mr.JPG](https://github.com/Mateo-RH/TopicosTelematica-BigDataLab/blob/master/imagenes/labs/emr-mr.JPG)

## Ejercicio 1

1. El salario promedio por Sector Económico (SE)

**codigo**

![punto1.JPG](https://github.com/Mateo-RH/TopicosTelematica-BigDataLab/blob/master/imagenes/labs/punto1.JPG)

**resultado**

![punto1.JPG](https://github.com/Mateo-RH/TopicosTelematica-BigDataLab/blob/master/imagenes/labs/punto1_res.JPG)

2. El salario promedio por Empleado

**codigo**

![punto1.JPG](https://github.com/Mateo-RH/TopicosTelematica-BigDataLab/blob/master/imagenes/labs/punto2.JPG)

**resultado**

![punto1.JPG](https://github.com/Mateo-RH/TopicosTelematica-BigDataLab/blob/master/imagenes/labs/punto2_res.JPG)

3. Número de SE por Empleado que ha tenido a lo largo de la estadística

**codigo**

![punto1.JPG](https://github.com/Mateo-RH/TopicosTelematica-BigDataLab/blob/master/imagenes/labs/punto3.JPG)

**resultado**

![punto1.JPG](https://github.com/Mateo-RH/TopicosTelematica-BigDataLab/blob/master/imagenes/labs/punto3_res.JPG)
