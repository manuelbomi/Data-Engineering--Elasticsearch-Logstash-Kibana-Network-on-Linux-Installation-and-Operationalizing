# Data-Engineering---Elasticsearch-Logstash-Kibana-EKL-Network-on-Linux-Installation-Operationalizing

This tutorial highlights how an Elasticsearch-Logstash-Kibana (ELK) network can be installed and made operational on a Linux (Ubuntu 18.04.6 LTS (Bionic Beaver)) system. Without loss of generality, the approach that are highlighted in this tutorial could be used to install ELK networks on other Linux based systems. 
See here () for how the ELK network can be installed on via a Docker pull on Windows (WSL) platforms. 

## Background Discussion
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


![image](https://github.com/user-attachments/assets/e4e91204-6a24-47b5-a3db-f07ab7b5af1a)







