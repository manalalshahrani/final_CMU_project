# 14848-project


Driver App: https://hub.docker.com/r/malshahr92/homedriver port 80:80
Spark: https://hub.docker.com/r/bitnami/spark/ port 7077:7077
Sonarqube and scanner: https://hub.docker.com/r/petronetto/sonarqube-alpine port 9000:9000
Jupiter: https://hub.docker.com/r/jupyter/scipy-notebook port 10000:10000
Hadoop namenode: https://hub.docker.com/r/bde2020/hadoop-namenode ports 9870:9870, 9000:9000
Hadoop datanode: https://hub.docker.com/r/bde2020/hadoop-datanode ports 9870:9870, 9000:9000



In order to get the application run on Kubernetes, the following steps in order:
1. Pull all the images and put them in the container registry except Driver App.
2. Deploy and expose all the containers using the ports listed above except hadoop namenode and datanode images
3. Deploy Hadoop namenode and make sure to add the following eviroment variable 
    . CLUSTER_NAME=test
    . CORE_CONF_fs_defaultFS=hdfs://namenode:9000
    . CORE_CONF_hadoop_http_staticuser_user=root
    . CORE_CONF_hadoop_proxyuser_hue_hosts=*
    . CORE_CONF_hadoop_proxyuser_hue_groups=*
    . CORE_CONF_io_compression_codecs=org.apache.hadoop.io.compress.SnappyCodec
    . HDFS_CONF_dfs_webhdfs_enabled=true
    . HDFS_CONF_dfs_permissions_enabled=false
    . HDFS_CONF_dfs_namenode_datanode_registration_ip___hostname___check= false   
I provided the namenode YAML file in this repository. 
4. expose the namenode service with the provided ports number following the same order. I offered the namenode service YAML file in this repository 
5. Deploy Hadoop datanode and make sure to add the following eviroment variable 
    . CLUSTER_NAME=test
    . CORE_CONF_fs_defaultFS=hdfs://namenode-service:9000
    . SERVICE_PRECONDITION=namenode-service:9000
    . CORE_CONF_hadoop_http_staticuser_user=root
    . CORE_CONF_hadoop_proxyuser_hue_hosts=*
    . CORE_CONF_hadoop_proxyuser_hue_groups=*
    . CORE_CONF_io_compression_codecs=org.apache.hadoop.io.compress.SnappyCodec
    . HDFS_CONF_dfs_webhdfs_enabled=true
    . HDFS_CONF_dfs_permissions_enabled=false
    . HDFS_CONF_dfs_namenode_datanode_registration_ip___hostname___check= false
I provided the datanode YAML file in this repository 
6. For the deployment YAML file for the Jupyter notebook, add these lines:
 containers:
      - args:
        - --NotebookApp.password='sha1:ec3d557855df:11d9dc731a0493f8bf9709f20653c8a170f5587c'
        command:
        - start-notebook.sh
Jupyter deployment YAML is available in this repository 
4. Take a note of all the exposed URLs for all the services 
5. Modify the HTML file by changing the URLs that used to be in there for the services
6. Build the HTML image and then push it to the docker hub.
7. Pull the Driver App you just pushed into the Google container registry.
8. Deploy and expose the driver image 
9. Navigate to the driver URL, and from there, you can use the hyperlinks to navigate to the microservices
but make sure to press the command key for Mac and control for windows when you click the hyperlinks to open in a new tap.

Jupyter password: "password"
Sonarqube and scanner username: "admin", password: "admin" 
