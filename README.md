# Data-Engineering---Elasticsearch-Logstash-Kibana-EKL-Network-on-Linux-Installation-Operationalizing

This tutorial highlights how an Elasticsearch-Logstash-Kibana (ELK) network can be installed and made operational on a Linux (Ubuntu 18.04.6 LTS (Bionic Beaver)) system. Without loss of generality, the approach that are highlighted in this tutorial could be used to install ELK networks on other Linux based systems. 
See here () for how the ELK network can be installed on via a Docker pull on Windows (WSL) platforms. 

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















