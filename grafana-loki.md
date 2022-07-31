 Guide for Grafana Loki (Open-source Log Aggregation by Prometheus)
===

## Loki  
Loki is a horizontally scalable, highly available, multi-tenant log aggregation system inspired by Prometheus. It is designed to be very cost-effective and easy to operate. It does not index the contents of the logs, but rather a set of labels for each log stream.

Loki is built around the idea of only indexing labels for logs and leaving the original log message un-indexed. This means that Loki is cheaper to operate and can be orders of magnitude more efficient.  


> Benefits of loki  
> - A simplified deployment mode  
> - Grafana log panel improvements  
> - Configurable per-stream retention  
> - Very fast integration   
> - Opensource   
> - Indexing only metadata and not the full logs.  
> - Loki use LogQL  
> - Loki is quicker to set up and use  

---

## Promtail 
Promtail is an agent which ships the contents of local logs to a private Grafana Loki instance or Grafana Cloud. It is usually deployed to every machine that has applications needed to be monitored.

---

## Grafana 
A visualization tool which supports loki as a datasource.   


---

How Does Grafana Loki works
===

![1_Uo7pndzKnyolFYbuJ0Pcig](https://user-images.githubusercontent.com/101656525/182035533-6f2e6fea-55c1-4ceb-9bfd-286d541666f3.gif)



>1. Pull Logs with Promtail
Promtail is a log collector made just for Loki. It uses the same Prometheus service discovery and has similar features for tagging, converting, and filtering logs before ingestion into Loki.  


>2. Store Logs in Loki
The text of logs is not indexed by Loki. Instead, entries are categorized into streams and labeled. This not only saves money but also means that log lines can be queried within milliseconds of being received by Loki.  


>3. Use LogQL to Explore
To explore your logs, use Loki's advanced query language, LogQL. Run LogQL queries from within Grafana to see your logs alongside other data sources, or use LogCLI if you prefer the command line.  

  ---  


## Grafana Loki Installation Steps

## 01. Setting up grafana  
  
Download grafana - use this [link](https://grafana.com/grafana/download )
I prefer to use **Standalone** **Linux** **Binaries** commands to download grafana.

```bash
wget https://dl.grafana.com/enterprise/release/grafana-enterprise-8.5.1.linux-amd64.tar.gz
```

`** if you don't have wget installed on your machine, go ahead and install it.`

Unzip the tar file   
```bash
tar -zxvf grafana-enterprise-8.5.1.linux-amd64.tar.gz
```
After unzip, you can see the grafana-8.5.0 directory 


## 02. Start up the grafana server 

Go to ```grafana-8.5.0/bin``` folder and run grafana-server binary  
```bash 
./grafana-server
   ```

After running the binary, log into the grafana by using your local ip address and put theport as 3000

	Eg: 127.0.1.1:3000 or <ip address>:3000

![image](https://user-images.githubusercontent.com/101656525/182036002-08a8bc06-5523-434f-839b-65a4a9d9ee2b.png)


`** if your server is not loading, there should be some firewall issue. In some cases, run the below command to open a port in the firewall to access grafana.`

```bash
sudo firewall-cmd --permanent --zone=public --add-port=3000/tcp
```
 

After that reload the firewall using below command ,  
```bash
sudo firewall-cmd --reload
```
 

Then you can try  
- https://127.0.1.1:3000 or ip address:3000

>By default the username and password is   
>- Username - admin   
>- Password -  admin

Once you enter the password for the first time you have to change the password.  

## 03. Download Loki

You can download and unzip loki by using the below github url, And download the supported version (in my case loki-linux-amd64.zip ) 

https://github.com/grafana/loki/releases or  
```bash
wget https://github.com/grafana/loki/releases/download/v2.5.0/loki-linux-amd64.zip
``` 

Download the config file  
```bash
wget https://raw.githubusercontent.com/grafana/loki/master/cmd/loki/loki-local-config.yaml
``` 

Enable firewall port for loki  
```bash
sudo firewall-cmd --permanent --zone=public --add-port=3100/tcp
```  
Reload the firewall
```bash  
sudo firewall-cmd --reload   
```  

Start the loki server.  
Open up a new terminal and start the server by using the below command.

```bash
./loki-linux-amd64 -config.file=loki-local-config.yaml
```  
`** you have to start the server as root user`

`** you have to point config file to start the promtail server(-config.file=loki-local-config.yaml)`


## 04. Download Promtail
You can download promtail by using the below github url, Don't forget to download the supported version (in my case  promtail-linux-amd64.zip ). 

https://github.com/grafana/loki/releases  or  
```bash
wget https://github.com/grafana/loki/releases/download/v2.5.0/promtail-linux-amd64.zip 
```
Unzip the file.

Download the config file using below commands, 
```bash
wget https://github.com/grafana/loki/blob/main/clients/cmd/promtail/promtail-local-config.yaml 
```

Start the promtail server.  
Open a new terminal and start a new session by using below command   
```bash
./promtail-linux-amd64 -config.file=promtail-local-config.yaml  
```
`** you have to start the server as root user`  
`** you have to point config file to start the promtail server`  

If you get error message like `“Unable to parse config: promtail-local-config.yaml mapping values are not allowed in this context” `
Go to the github page and copy the context and paste it to the promtail-local-config.yaml file. https://github.com/grafana/loki/blob/main/clients/cmd/promtail/promtail-local-config.yaml 


## 05. Add the datasource to Grafana
>* Go to the Data Sources section 
>* Select loki
>* Set the variables   
>        - Name - Loki  
>        - URL - your machine ip address and port 3100  
>        - http://127.0.1.1:3100
>* Set each and everything as default

![grafana2](https://user-images.githubusercontent.com/101656525/182035535-bb13039a-fe17-4179-a2a7-0ccf0e765e85.png)


If you set up your datasource successfully, you can see the DATA SOURCE section set to blue

![grafana3](https://user-images.githubusercontent.com/101656525/182035538-af947484-de87-4417-a13a-3075882a7cd9.png) 

--- 
---

Everything is set up, Now go to Explore section in left side menu


![grafana4](https://user-images.githubusercontent.com/101656525/182035540-75c24b37-38af-474c-b635-72ee02021a90.png)  
In the top left corner you can see the Log browser where you can enter the loki query   

![grafana5](https://user-images.githubusercontent.com/101656525/182035541-39aa8c9e-2ea6-4c24-b823-aff1bfa0495d.png)

In the right top corner you can see the time range option and Run query option

![grafana6](https://user-images.githubusercontent.com/101656525/182035545-ca3b57a7-cd51-4d8a-95ee-78dcf98b5a67.png)



    Useful queries
    {job="varlogs"} |= "error"
    {job=”varlogs”} |~ “Invalid User”
    {job="varlogs"} |~ "error|info"

You can specify the query refresh time  

![grafana7](https://user-images.githubusercontent.com/101656525/182035547-d9a7ac27-a3ae-44cc-b3e3-3af764a6c9b4.png) 

You can see the logs after running the query.


![grafana8](https://user-images.githubusercontent.com/101656525/182035548-abde12a6-bb3c-41f2-b980-44826351ad80.png)


--- 
If you want to specify which log should send to send loki, you can set it in the promtail-local-config.yaml under the labels:  

```yaml
- job_name: system
    static_configs:
    - targets:
        - localhost
    lables:
        job: System Logs
        __path__: /var/log/syslog
```

--- 
## Add a graph using LogQL query

You can simply add a dashboard by using Scalar Vectors and Series of Scalar Vectors to the simple logql queries. 

```sql
count_over_time({job="varlogs"}[1m])
```

![grafana9](https://user-images.githubusercontent.com/101656525/182035549-f040e29c-b299-4f5b-bfb2-8603d1a5e940.png)
![grafana10](https://user-images.githubusercontent.com/101656525/182035551-b8712e8d-7e4f-4dd8-8f72-ee61c4030caa.png)


