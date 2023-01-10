[ONLYOFFICE site](https://www.onlyoffice.com/office-for-alfresco.aspx)

provides an installation video for linux/docker but this intallation process might sometimes fail.

A better installation process for onlyoffice-alfresco is also provided on the github project level. But proper parameters for  
this installation is missing(In regard to how to configure the Alfresco-ONLY-office Admin) page.

## Guide to Install onlyoffice for alfresco 7.3 ?

The best reference to the [git repo](https://github.com/onlyoffice/onlyoffice-alfresco/releases) which provides the recent release version **v6.0.1**  
is much compatible with recent versions of alfresco 7.2.x or 7.3.x.

##### OLDER ALFRESCO SDK compatibility with ONLYOFFICE 6.0.1 versions was not tested

Our goal is to get alfresco **7.2.x or 7.3.x** working with the **onlyoffice v6.0.1**.

### INSTALLATION PROCEDURE

[onlyoffice-alfresco](https://github.com/ONLYOFFICE/onlyoffice-alfresco) provides installation guide but in this case our main goal is to get only office working in alfresco with the minimum effort.

1.  Download the jars
    -   [REPO JAR](https://github.com/ONLYOFFICE/onlyoffice-alfresco/releases/download/v6.0.1/onlyoffice-integration-repo.jar)
    -   `- Copy the downloaded jar into {TOMCAT}/webapps/alfresco/WEB-INF/lib/`
    -   [SHARE JAR](https://github.com/ONLYOFFICE/onlyoffice-alfresco/releases/download/v6.0.1/onlyoffice-integration-share.jar)
    -   `- Copy the downloaded jar into {TOMCAT}/webapps/share/WEB-INF/lib/`
2.  Stop alfresco and restart
    -   NOTE: Does not matter how you stop your ?(running) alfresco.
    -   Enjoy the jars are existing in the lib directory after restart.
3.  Check if only office exist
    -   We can observe if the .jars executed properly with the alfresco
    -   From the alfresco admin page we can see the new config added ![](https://github.com/Fas96/T-images-repo/blob/main/alfresco-config.png?raw=true)
4.  IMPORTANT: Configure Alfresco to connect to ONLYOFFICE SERVER

6.  ![](https://github.com/Fas96/T-images-repo/blob/main/configer-details.png?raw=true)

##### It is very important to configure correctly

1.  The Document editing service address
2.  Secret key \[NOTE\] : **ONLY office uses JWT tokens to authenticate users** Access to the document SERVER.  
    This can be left empty if **NO JWT authentication** is enabled.
3.  The editing service is also important and its runs with the other document service like the converter service.
4.  Finally, the address for the internal request. which means the base url to the alfresco instance.  
    NOTE: Errors in this configuration would show an alert error.
    
    1.  Failure to connect to the server
    2.  Editing service connecting error
    3.  Authentication error if JWT secret is wrong
    
    ##### SUCCESS Connection inspection
    
    ![](https://github.com/Fas96/T-images-repo/blob/main/success-connect.png?raw=true)
    

If the connection to the document server was successful we expect to see the alert(success) after clicking save button.

If any configuration is wrong this might not connect to the server and an error explained above would be shown.

5.  Check onlyoffice Existing in ALFRESCO SHARE  
    In the DOCUMENT-LIBRARY of alfresco share we can see the new addition of ONLY OFFICE FEATURESIn the alfresco document library page we can see the new create features added  
    Also on a file we can see the new features of opening or create using ONLYOFFICE
6.  ![](https://github.com/Fas96/T-images-repo/blob/main/onlyoffice-doclib.png?raw=true)

![](https://github.com/Fas96/T-images-repo/blob/main/onlyoffice-doclib.png?raw=true)

If we decided to open it in onlyoffice we can see the webview like show below

![](https://github.com/Fas96/T-images-repo/blob/main/webview.png?raw=true)

6.  INSTALLING DOCUMENT SERVER  
    Notice none of the above feature works without a running instance of a document server.  
    In any OS or installation environment the installation and configuration ONLY has no much difference.

From the [OFFICIAL WEBSITE](https://www.onlyoffice.com/download-docs.aspx?from=officeforalfresco#docs-community) there are several options to get only office server running.  
In our case we would be using the [DOCKER community version](https://helpcenter.onlyoffice.com/installation/docs-community-install-docker-arm64.aspx?_ga=2.227019805.1228876411.1673223521-1074737998.1672112363).

NOTE: INSTALLATION WITHOUT SSL

As mentioned above we would be installing document server without SSL on a docker instance. But we need to ensure that our DOCKER is using the LOCAL COMPUTER DNS.

if the docker daemon.json is not configured directly we can still use the dns configuration in or command.

on my local pc I have the dns set as

![](https://github.com/Fas96/T-images-repo/blob/main/local-computer-dns.png?raw=true)

Well if my dns is set locally and then i can run the docker command to get my container running

```
sudo docker run -dit -p 8080:80   -e JWT_ENABLED='true' -e JWT_SECRET='secret' --dns=8.8.8.8 --restart=always \
    -v /volume/onlyoffice/DocumentServer/logs:/var/log/onlyoffice  \
    -v /volume/onlyoffice/DocumentServer/data:/var/www/onlyoffice/Data  \
    -v /volume/onlyoffice/DocumentServer/lib:/var/lib/onlyoffice \
    -v /volume/onlyoffice/DocumentServer/db:/var/lib/postgresql --name documentserver onlyoffice/documentserver
```

This command is to ensure I have JWT enabled with the secret key and also just keeping the Google IPV4 default DNS.  
Also, ensuring the volumes are created for our instance

Well obviously, if our instance is running we can access the [server welcome page](http://localhost:8080/welcome/) .  
Also, if we enabled the example page we can see the example page for testing our server.

### Important configuration for JWT TOKEN

When our instance is running and we can see the example page or the main page, we still need to check what the default secret is.

-   token: "secret"  
    Is what we used in our case. we need to check if that is correctly SET. If not then we set it to any secret we want.

### We need to get into the instance and make some changes on the running services

1.  Get into the bash

```
sudo docker exec -it {containerID} bash
```

![](https://github.com/Fas96/T-images-repo/blob/main/container%20Home.png?raw=true)

We want to get to where our app is installed /etc/--- for installed apps.

If we open the default.json configuration we can see the default secret keys and token ENABLED for JWT authentication.  
If any of this configuration is wrong then we get ERRORs in DOCUMENTS SERVER USAGE.

![](https://github.com/Fas96/T-images-repo/blob/main/default.png?raw=true)

Also we can see the configuration for local.json file.

![](https://github.com/Fas96/T-images-repo/blob/main/local.png?raw=true)

It is STATED that default changes in default.json are overridden by the configuration in the local.json folder

\--

![](https://github.com/Fas96/T-images-repo/blob/main/base-commands.png?raw=true)

\--  
If changes was made for the local.json or default.json in documentserver or documentserver-example and saved.  
Then we need to restart the services

![](https://github.com/Fas96/T-images-repo/blob/main/service-restart.png?raw=true)

If the DOCKER\_Instance is running properly and we connect to our local computer on the port specified in docker then we can use the example app and test if its working.

```
ifconfig|grep inet
```

and use the ip on port 8080 as specified to access the document server and also you can test the example.