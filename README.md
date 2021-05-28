---
page_type: sample
languages:
- java
products:
- azure
description: "This guide walks you through the process of building, configuring, deploying, trouble shooting and scaling 
Java Web apps in App Service Linux."
urlFragment: java-app-service-sample
---

# End-to-end Java Experience in App Service Linux (Part 2)

Refer to [part 1](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux/tree/master/) for additional details.

This guide walks you through the process of building, 
configuring, deploying, trouble shooting and scaling 
Java Web apps in App Service Linux. 

## What you will build

You will build a Java Web app using 
[Spring Boot](https://spring.io/projects/spring-boot), 
[Spring Data for Cosmos DB](https://docs.microsoft.com/en-us/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db?view=azure-java-stable), 
[Azure Cosmos DB](https://docs.microsoft.com/en-us/azure/cosmos-db/sql-api-introduction)
and
[App Service Linux](https://docs.microsoft.com/en-us/azure/app-service/containers/app-service-linux-intro).

## What you will need

In order to deploy a Java Web app to cloud, you need 
an Azure subscription. If you do not already have an Azure 
subscription, you can activate your 
[MSDN subscriber benefits](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) 
or sign up for a 
[free Azure account](https://azure.microsoft.com/pricing/free-trial/).

In addition, you will need the following:

| [Azure CLI](http://docs.microsoft.com/cli/azure/overview) 
| [Java 8](https://www.azul.com/downloads/azure-only/zulu) 
| [Maven 3](http://maven.apache.org/) 
| [Git](https://github.com/) 
| 

## Getting Started

You can start from scratch and complete each step, or 
you can bypass basic setup steps that you are already 
familiar with. Either way, you will end up with working code.

When you are finished, you can check your results 
against YOUR code in 
[e2e-java-experience-in-app-service-linux-part-2/complete](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2/tree/master/complete).

You can start from [e2e-java-experience-in-app-service-linux-part-2/initial](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2/tree/master/initial).
Or, you can clone from [spring-todo-app](https://github.com/microsoft/spring-todo-app) 

If you are starting from scratch, you can scaffold a Web app using 
[Maven Web app archetype](https://maven.apache.org/archetypes/maven-archetype-webapp/)
or [start.spring.io](https://start.spring.io/).

#### Step ONE - Clone and Prep

```bash
git clone --recurse-submodules https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git
yes | cp -rf .prep/* .
```

## Create Azure Cosmos DB

Create Azure Cosmos DB 
using [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) 

### STEP A - LOGIN to Azure
Login your Azure CLI, and set your subscription 
    
```bash
az login
az account set -s <your-subscription-id>
```
### STEP B - Create Resource Group

Create an Azure Resource Group, and note down the resource group name

```bash
az group create -n <your-azure-group-name> \
    -l <your-resource-group-region>
```

### STEP C - Create COSMOS DB

Create Azure Cosmos DB with GlobalDocumentDB kind. 
The name of Cosmos DB must use only lower case letters. Note down the `documentEndpoint` field in the response

```bash
az cosmosdb create --kind GlobalDocumentDB \
    -g <your-azure-group-name> \
    -n <your-azure-COSMOS-DB-name-in-lower-case-letters>
```

### STEP D - Get COSMOS DB Key

Get your Azure Cosmos DB key, get the `primaryMasterKey`

```bash
az cosmosdb list-keys -g <your-azure-group-name> -n <your-azure-COSMOSDB-name>
```

## Build and Run Locally - Spring TODO App Powered Using Azure Cosmos DB

Open the [initial/spring-todo-app](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2/tree/master/initial)
sample app in your favorite IDE - IntelliJ | Eclipse | VS Code.

### STEP 1 - Change directory

```bash
cd initial/spring-todo-app
```  
    
### STEP 2 - Configure the app

Set environment variables using a script file. Start with 
the supplied template in the repo: 

```bash
cp set-env-variables-template.sh .scripts/set-env-variables.sh
```
 
Edit .scripts/set-env-variables.sh and supply Azure 
Cosmos DB connection info. Particularly:

```bash
export COSMOS_URI=<put-your-COSMOS-DB-documentEndpoint-URI-here>
export COSMOS_KEY=<put-your-COSMOS-DB-primaryMasterKey-here>
export COSMOS_DATABASE=<put-your-COSMOS-DATABASE-name-here>

// App Service Linux Configuration
export RESOURCEGROUP_NAME=<put-your-resource-group-name-here>
export WEBAPP_NAME=<put-your-Webapp-name-here>
export REGION=<put-your-REGION-here>
export SUBSCRIPTION_ID=<put-your-SUBSCRIPTION_ID-here>
```
   
Set environment variables:

```bash
source .scripts/set-env-variables.sh
```

### STEP 3 - Run Spring TODO App locally

```bash
mvn package spring-boot:run
```

   ```bash
bash-3.2$ mvn package spring-boot:run
[INFO] Scanning for projects...
[INFO]
[INFO] -------< com.azure.spring.samples:spring-todo-app >--------
[INFO] Building spring-todo-app 2.0-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
[INFO]
[INFO] --- maven-clean-plugin:3.1.0:clean (default-clean) @ spring-todo-app ---
[INFO] Deleting C:\idea\spring-todo-app\target
[INFO]
[INFO] --- maven-checkstyle-plugin:3.1.2:check (validate) @ spring-todo-app ---
...
...
2021-05-28 12:47:31.623  INFO 18020 --- [           main] c.a.c.i.d.RntbdTransportClient           : Using default Direct TCP options: azure.cosmos.directTcp.defaultOptions
2021-05-28 12:47:31.640  INFO 18020 --- [           main] Endpoint$RntbdEndpointMonitoringProvider : Starting RntbdClientChannelPoolMonitoringProvider ...
2021-05-28 12:47:31.665  INFO 18020 --- [ctor-http-nio-2] c.a.c.i.clientTelemetry.ClientTelemetry  : Client is not on azure vm
2021-05-28 12:47:33.727  INFO 18020 --- [           main] o.s.s.concurrent.ThreadPoolTaskExecutor  : Initializing ExecutorService 'applicationTaskExecutor'
2021-05-28 12:47:33.797  INFO 18020 --- [           main] o.s.b.a.w.s.WelcomePageHandlerMapping    : Adding welcome page: class path resource [static/index.html]
2021-05-28 12:47:33.907  INFO 18020 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
2021-05-28 12:47:33.916  INFO 18020 --- [           main] c.azure.spring.samples.TodoApplication   : Started TodoApplication in 8.386 seconds (JVM running for 9.372)
   ```

You can access Spring TODO App here: [http://localhost:8080/](http://localhost:8080/).
    
![](./media/spring-todo-app-running-locally.jpg)

## Deploy to App Service Linux 

### Step 4 - Configure for Cloud Deployment

Ensure that the [Maven Plugin for Azure App Service](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md) configuration is present and set to the latest version in the POM.xml file. 

```xml    
<plugins> 

    <!--*************************************************-->
    <!-- Deploy to Java SE in App Service Linux           -->
    <!--*************************************************-->
       
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.14.0</version>
        <configuration>
            <schemaVersion>v2</schemaVersion>
            <subscriptionId>${SUBSCRIPTION_ID}</subscriptionId>
            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>
            <pricingTier>P1v2</pricingTier>
            <runtime>
              <os>Linux</os>
              <javaVersion>Java 8</javaVersion>
              <webContainer>Java SE</webContainer>
            </runtime>
            <deployment>
              <resources>
                <resource>
                  <directory>${project.basedir}/target</directory>
                  <includes>
                    <include>*.jar</include>
                  </includes>
                </resource>
              </resources>
            </deployment>
            <appSettings>
              <property>
                <name>COSMOS_URI</name>
                <value>${COSMOS_URI}</value>
              </property>
              <property>
                <name>COSMOS_KEY</name>
                <value>${COSMOS_KEY}</value>
              </property>
              <property>
                <name>COSMOS_DATABASE</name>
                <value>${COSMOS_DATABASE}</value>
              </property>
              <property>
                <name>JAVA_OPTS</name>
                <value>-Dserver.port=80</value>
              </property>
            </appSettings>
       </configuration>
    </plugin>            
    ...
</plugins>
```

> [!NOTE]
> You can use the command `mvn azure-webapp:config` to modify the basic configuration.

### Step 5 - Deploy to Java SE in App Service Linux

```bash

// Deploy
bash-3.2$ mvn azure-webapp:deploy
[INFO] Scanning for projects...
[INFO]
[INFO] -------< com.azure.spring.samples:spring-todo-app >--------
[INFO] Building spring-todo-app 2.0-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
[INFO]
[INFO] --- azure-webapp-maven-plugin:1.14.0:deploy (default-cli) @ spring-todo-app ---
Auth type: AZURE_CLI
Default subscription: Consoto Subscription(subscription-id-xxx)
Username: user@contoso.com
[INFO] Subscription: Consoto Subscription(subscription-id-xxx)
[INFO] Creating app service plan...
[INFO] Successfully created app service plan asp-spring-todo-app.
[INFO] Creating web app spring-todo-app...
[INFO] Successfully created Web App spring-todo-app.
[INFO] Trying to deploy artifact to spring-todo-app...
[INFO] Successfully deployed the artifact to https://spring-todo-app.azurewebsites.net
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  02:05 min
[INFO] Finished at: 2021-05-28T09:43:19+08:00
[INFO] ------------------------------------------------------------------------
```

> [!NOTE]
> The Auth type here is **AZURE_CLI** because I have already logged in. If I have not logged in, this will be **OAUTH2**, and I will be prompted to log in to Azure first.

#### Open Spring Boot TODO App running on Java SE in App Service Linux

```bash
open https://spring-todo-app.azurewebsites.net
```
![](./media/spring-todo-app-running-in-app-service.jpg)


### Step 6 - Trouble Shoot Spring TODO App on Azure by Viewing Logs

Configure logs for the deployed Java Web 
app in App Service Linux:

```bash
az webapp log config --name ${WEBAPP_NAME} \
 --resource-group ${RESOURCEGROUP_NAME} \
  --web-server-logging filesystem
```

Open Java Web app remote log stream from a local machine:

```bash
az webapp log tail --name ${WEBAPP_NAME} \
 --resource-group ${RESOURCEGROUP_NAME}
 
bash-3.2$ az webapp log tail --name ${WEBAPP_NAME}  --resource-group ${RESOURCEGROUP_NAME}
2021-05-28T01:46:08.000655632Z   _____                               
2021-05-28T01:46:08.000701432Z   /  _  \ __________ _________   ____  
2021-05-28T01:46:08.000708133Z  /  /_\  \___   /  |  \_  __ \_/ __ \ 
2021-05-28T01:46:08.000711733Z /    |    \/    /|  |  /|  | \/\  ___/ 
2021-05-28T01:46:08.000714933Z \____|__  /_____ \____/ |__|    \___  >
2021-05-28T01:46:08.000718233Z         \/      \/                  \/ 
2021-05-28T01:46:08.000721333Z A P P   S E R V I C E   O N   L I N U X
2021-05-28T01:46:08.000724233Z Documentation: http://aka.ms/webapp-linux
...
...
2021-05-28T01:46:18.925044188Z   .   ____          _            __ _ _
2021-05-28T01:46:18.925481392Z  /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
2021-05-28T01:46:18.926004297Z ( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
2021-05-28T01:46:18.926587603Z  \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
2021-05-28T01:46:18.926599403Z   '  |____| .__|_| |_|_| |_\__, | / / / /
2021-05-28T01:46:18.926841806Z  =========|_|==============|___/=/_/_/_/
2021-05-28T01:46:18.931157849Z  :: Spring Boot ::                (v2.4.5)
...
...
2021-05-28T01:46:29.842553633Z 2021-05-28 01:46:29.842  INFO 124 --- [           main] c.azure.spring.samples.TodoApplication   : Started TodoApplication in 12.635 seconds (JVM running for 17.664)
2021-05-28T01:46:30.477951594Z 2021-05-28 01:46:30.477  INFO 124 --- [p-nio-80-exec-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring DispatcherServlet 'dispatcherServlet'
2021-05-28T01:46:30.483316162Z 2021-05-28 01:46:30.483  INFO 124 --- [p-nio-80-exec-1] o.s.web.servlet.DispatcherServlet        : Initializing Servlet 'dispatcherServlet'
2021-05-28T01:46:30.485411088Z 2021-05-28 01:46:30.484  INFO 124 --- [p-nio-80-exec-1] o.s.web.servlet.DispatcherServlet        : Completed initialization in 0 ms
2021-05-28T01:47:19.683003828Z 2021-05-28 01:47:19.682  INFO 124 --- [p-nio-80-exec-9] c.a.s.s.controller.TodoListController    : Fri May 28 01:47:19 GMT 2021 GET ======= /api/todolist =======
2021-05-28T01:47:26.069984388Z 2021-05-28 01:47:26.069  INFO 124 --- [-nio-80-exec-10] c.a.s.s.controller.TodoListController    : Fri May 28 01:47:26 GMT 2021 POST ======= /api/todolist ======= test
2021-05-28T01:47:26.649080678Z 2021-05-28 01:47:26.648  INFO 124 --- [p-nio-80-exec-1] c.a.s.s.controller.TodoListController    : Fri May 28 01:47:26 GMT 2021 GET ======= /api/todolist =======
```

When you are finished, you can check your results 
against YOUR code in 
[e2e-java-experience-in-app-service-linux-part-2/complete](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2/tree/master/complete).

## Scale out the Spring TODO App

Scale out Java Web app using Azure CLI:

```bash
az appservice plan update --number-of-workers 2 \
   --name ${WEBAPP_PLAN_NAME} \
   --resource-group ${RESOURCEGROUP_NAME}
```

## Congratulations!

Congratulations!! You built and scaled out a Java Web app using 
[Spring Boot](https://spring.io/projects/spring-boot), 
[Spring Data for Cosmos DB](https://docs.microsoft.com/en-us/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db?view=azure-java-stable), 
[Azure Cosmos DB](https://docs.microsoft.com/en-us/azure/cosmos-db/sql-api-introduction)
and
[App Service Linux](https://docs.microsoft.com/en-us/azure/app-service/containers/app-service-linux-intro).
.

## Resources

- [Java in App Service Linux dev guide](https://docs.microsoft.com/en-us/azure/app-service/containers/app-service-linux-java)
- [Azure for Java Developers](https://docs.microsoft.com/en-us/java/azure/)

---

This project has adopted 
the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/). 
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or 
contact [opencode@microsoft.com](mailto:opencode@microsoft.com) 
with any additional 
questions or comments.
