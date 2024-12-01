# Data-Engineering: Elasticsearch-Logstash-Kibana (EKL) Network-on-Linux: Installation and Operationalizing

This tutorial highlights how an Elasticsearch-Logstash-Kibana (ELK) network can be installed and made operational on a Linux (Ubuntu) system. Without loss of generality, the approach that are highlighted in this tutorial could be used to install ELK networks on other Linux based systems. 
See here https://github.com/manuelbomi/Data-Engineering---Elasticsearch-Logstash-Kibana-EKL-Network-on-Docker---A-Tutorial for how the ELK network can be installed via a Docker pull on Windows (WSL) operating systems. 

# Background Discussion
## Elasticsearch
Elasticsearch is a distributed search and analytics engine that's used for: 
  - Storing, searching, and analyzing data
Elasticsearch can store, search, and analyze large amounts of structured and unstructured data in near real-time
  - Enterprise search
Elasticsearch is a popular choice for enterprise search and discovery experiences. Enterprise search is a software technology that allows users within a company to search across various internal data sources like documents, emails, databases, and applications, effectively finding information from a single search bar, rather than navigating through individual storage systems; essentially acting as a company-wide search engine to access relevant information quickly and efficiently
  - Log analytics and management
Elasticsearch is commonly used for log analytics, which can provide operational insights. 
  - Security analytics
Elasticsearch can be used for security analytics, such as analyzing access logs and other system security logs. 
  - Business analytics
Elasticsearch can be used as a business analytics and in-sighting tool. 
  - Maps
Elasticsearch can be used to explore location data in real time. 
  - Cybersecurity Applications (SIEM)
Elasticsearch can be used for interactive investigation and automated threat detection. Elasticsearch is a popular tool for enabling security information and event management (SIEM). SIEM a security management system that combines information management and security and risk events management. SIEM helps organizations to detect and analyze security threats, respond to threats before they harm business operations, and to meet compliance requirements.
  - Endpoint security
Elasticsearch can be used for preventing, detecting, hunting for, and responding to threats from APIs that businesses use. It can also monitor system data logs in real time to detect security related issues. 
  - Spatial data
Elasticsearch can manage and integrate spatial data, which is useful for location-based search, dynamic mapping, and environmental monitoring.

Elasticsearch is built on Apache Lucene and is the heart of the Elastic Stack. It is optimized for speed and relevance on production-scale workloads. 

Elasticsearch has a RESTful API for interacting with the search engine and supports a wide range of query types. It also includes a variety of search and analysis features, such as: faceting, filtering, sorting, and highlighting

Elasticsearch can be combined with Logstash and Kibana to form the ELK network as shown below. 



![EKL iamge](https://github.com/user-attachments/assets/d46e3a25-ac22-4b8c-bcb3-dcf768a3296a)





## Logstash
Logstash is a data processing pipeline that collects data from various sources in real time, transforms, and send the data. Logstash is often used to prepare data for analysis in Elasticsearch. 
Before sending data to Elasticsearch, Logstash can be used to dynamically transform data of various forms and complexities. It can also be used to anonymize personal identifiable information (PII) data.

## Kibana
Kibana is primarily used for data visualization and exploration. It allows users to create dashboards and interactive charts based on data stored in Elasticsearch. Kibana is thus a valuable tool for analyzing large volumes of logs, for monitoring applications, and for gaining operational insights through visual representations like line graphs, bar charts, heat maps, etc. 





## Elasticsearch Installation
#### Update your Ubuntu system
  - sudo apt update
  - sudo apt upgrade

#### Install Java 8 or Java 11 (Java 8 may no longer be easily available on JDK website)
  - sudo apt install openjdk-11-jdk

#### Download Elasticsearch
  - wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.14.0-amd64.deb
    
#### Navigate to the Elasticsearch directory, extract and install it
  - sudo dpkg -i elasticsearch-7.14.0-amd64.deb

#### Open the Elasticsearch yaml file and allow it to be accessible from Localhost. Remove the # from the #network.host line. 
  - sudo nano /etc/elasticsearch/elasticsearch.yml

#### Configure Elasticsearch security settings
Also, on the yaml file, remove the # in the line that contains # xpack.security.enabled: true to enable basic security features for Elasticsearch

#### Reboot your system. Alternatively, you can restart Elasticsearch for your configurations to take effect
  - sudo systemctl restart elasticsearch

#### After restarting, the command below will work for subsequent starting of Elasticsearch
  - sudo systemctl start elasticsearch
    
#### You can automate Elasticsearch to always be part of your system startup with the command below:
  - sudo systemctl enable elasticsearch

#### Verify (via your browser or the Linux command line) that Elasticsearch is working

#### On browser type: 
  - http://localhost:9200


#### Example of the output of the command is shown below:



![image](https://github.com/user-attachments/assets/e9c6cf4a-79dd-4208-bfba-6eace53e9d85)


#### Alternatively, on command terminal type:
  - curl http://localhost:9200

#### An example of the output of the command is shown below:

![image](https://github.com/user-attachments/assets/7af5bab9-8d38-483a-bf11-c6432c58ad1d)






## Logstash Installation
#### Update your system if you miss that step when installing Elasticsearch
  - sudo apt update
  - sudo apt upgrade

Instead of using sudo apt-get install logstash, it is best to navigate to the elastic.co homepage and search for Logsatsh binaries that have the same serial number as your Elasticsearch. The Elasticsearch in this tutorial is version ‘7.14.0’. It is best to download same version of Logstash, unzip it on your system and install it. The figure below shows the download page of Logstash 7.14.0

![image](https://github.com/user-attachments/assets/3883f6bd-2444-487a-aae9-a65acc4256d2)


#### Use nano (or vi or vim) to create the Logstash configuration file. Use the command below from your Ubuntu terminal:

  - nano logstash-apache.conf

#### Add the content below to your logstash-apache.conf file. The content is also downloadable from the attached logstache-apache.conf file on this Github page 

input {
  file {
    path => "/tmp/access_log"
    start_position => "beginning"
  }
}

filter {
  if [path] =~ "access" {
    mutate { replace => { "type" => "apache_access" } }
    grok {
      match => { "message" => "%{COMBINEDAPACHELOG}" }
    }
  }
  date {
    match => [ "timestamp" , "dd/MMM/yyyy:HH:mm:ss Z" ]
  }
}

output {
  elasticsearch {
    hosts => ["localhost:9200"]
  }
  stdout { codec => rubydebug }
}


#### Use nano to create the /tmp/access_log file
  - nano /tmp/access_log

Add the example log below. You can also use another logs or dataset to populate the access_log file. The example log (obtained from: https://medium.com/@swinarah/run-logstash-in-ubuntu-1fc3a4638ff6 )is also downloadable from the attached access_log file on this Github page:

71.141.244.242 - kurt [18/May/2011:01:48:10 -0700] "GET /admin HTTP/1.1" 301 566 "-" "Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.9.2.3) Gecko/20100401 Firefox/3.6.3"
134.39.72.245 - - [18/May/2011:12:40:18 -0700] "GET /favicon.ico HTTP/1.1" 200 1189 "-" "Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 5.1; Trident/4.0; .NET CLR 2.0.50727; .NET CLR 3.0.4506.2152; .NET CLR 3.5.30729; InfoPath.2; .NET4.0C; .NET4.0E)"
98.83.179.51 - - [18/May/2011:19:35:08 -0700] "GET /css/main.css HTTP/1.1" 200 1837 "http://www.safesand.com/information.htm" "Mozilla/5.0 (Windows NT 6.0; WOW64; rv:2.0.1) Gecko/20100101 Firefox/4.0.1"


#### Navigate to the bin directory of the Logstash file and pass in the configuration file to Logstash:

  - bin/logstash -f logstash-apache.conf

Example on an Ubuntu terminal is shown below:

![image](https://github.com/user-attachments/assets/ec68c062-e95f-4fc1-8990-b02c777f9506)

The content of the access_log file will then be rendered by Logstash on the Linux terminal as shown below:

![image](https://github.com/user-attachments/assets/fdc8fa3d-8f37-428a-9448-e4ad74e0aff2)




![image](https://github.com/user-attachments/assets/0b6fd306-8872-4c08-844f-c5be3d163381)



#### Logstash can also be accessed on the 9600 port on localhost. To do that, type the command below on a browser:

  - http://localhost:9600/

![image](https://github.com/user-attachments/assets/b24b4d4b-67a7-43f3-8a14-382809aaa01f)


#### Browsers can also be used to render the content of the Logstash’s access_log file. To do that, use the command below on the browser:

  - http://localhost:9600/logstash*/_search

![image](https://github.com/user-attachments/assets/c308d804-135a-45ec-b641-18b01abcb674)





## Kibana Installation
Download Kibana installation file from the elastic.co website. For compatibility, ensure that the serial number of the Kibana binaries is similar to the Elasticsearch and the Logstash serial number (‘7.14.0’)

  - wget https://artifacts.elastic.co/downloads/kibana/kibana-7.14.0-linux-x86_64.tar.gz

Unzip the downloaded file and navigate to the kibana-7.14.0/ folder

  - tar -xzf kibana-7.14.0-linux-x86_64.tar.gz
  - cd kibana-7.14.0/

To start the Kibana service from command line, type:

  ./bin/kibana

Now, open Kibana at:

#### http://localhost:5601

The Kibana localhost homepage should be displayed as shown below:

![image](https://github.com/user-attachments/assets/0b7268e6-4222-4250-b7d4-8b86f2dcf76b)


On Kibana, search for Elastic Dev tools. On the Dev tool, to observe all available indexes on the Elasticsearch database.  Type:

  - GET /_cat/indices?v 

Run the command. You will observe that some default indexes (tables) and the Logstash index (table) are displayed as your available indices. Example is shown below:

![image](https://github.com/user-attachments/assets/f00673a1-8792-41e3-91ae-57382f9ec41f)

Search specifically for Logstash index data

  - GET /logstash/_search

The Logstash log data as rendered via Kibana


![image](https://github.com/user-attachments/assets/a06537a3-4eaf-421e-9504-6e30e54bffb0)


To verify that the ELK network can be accessed from either Elasticsearch or Kibana.
Ensure that your ELK network is still on and running by checking the ELK components via their respective ports on the browser. After verifying that the ELK network is online, open an Ubuntu terminal and type:

  - curl http://localhost/9200/_aliases?pretty=true

#### Note that the curl ‘http://localhost/9200/_aliases?pretty=true’ specifically queries Elasticsearch for all its available network and indices. The Kibana and Logstash indices are shown by the Elasticsearch call as shown below: 

![image](https://github.com/user-attachments/assets/61e3ba12-8570-495e-8fc1-2b803c6af4c0)

Same query as the previous one without the ‘pretty’ argument (curl http://localhost/9200/_aliases) yields the output below:

![image](https://github.com/user-attachments/assets/1baa2633-c0fb-4f5e-9b95-677f1c475e41)

The command (curl http://localhost/9200/_aliases?pretty=true) can also be used via the browser instead of the terminal. Output is shown below:

![image](https://github.com/user-attachments/assets/a247267b-38dc-4ca9-8cbc-5ab93a319e0c)


From the terminal or the browser, the:


  - curl localhost:9200/_cat/indices?v 


command can be used to check the ELK network. Out as shown below


![image](https://github.com/user-attachments/assets/ac8d9b25-78a8-4ca8-9610-5412d7ff8145)






















