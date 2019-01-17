---
title: Vytvoření webové aplikace v Javě v Linuxu – Azure App Service
description: Vytvořit, nasadit a škálovat Web v jazyce Java Spring Boot aplikací pomocí služby Azure App Service v Linuxu a Azure Cosmos DB.
author: rloutlaw
ms.author: routlaw
manager: angerobe
ms.service: app-service-web
ms.devlang: java
ms.topic: tutorial
ms.date: 12/10/2018
ms.custom: seodec18
ms.openlocfilehash: a0a07a78d36e4c0d11132d0c7d5ff947f7073029
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54353562"
---
# <a name="tutorial-build-a-java-web-app-using-spring-and-azure-cosmos-db"></a>Kurz: Vytvoření webové aplikace Java Spring a Azure Cosmos DB

Tento kurz vás provede procesem vytváření, konfiguraci, nasazování a škálování webových aplikací v Javě v Azure. Až budete hotovi, budete mít [Spring Boot](https://projects.spring.io/spring-boot/) ukládající data ve [služby Azure Cosmos DB](/azure/cosmos-db) systémem [Azure App Service v Linuxu](/azure/app-service/containers).

![Aplikace Java spuštěná ve službě Azure App Service](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-locally.jpg)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte databázi Cosmos DB.
> * Připojení k databázi ukázkovou aplikaci a otestovat ho místně
> * Nasazení ukázkové aplikace do Azure
> * Stream diagnostických protokolů ze služby App Service
> * Přidat další instance pro horizontální navýšení kapacity ukázkové aplikace

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* [Azure CLI](https://docs.microsoft.com/cli/azure/overview)nainstalovanou ve vašem počítači. 
* [Git](https://git-scm.com/)
* [Java JDK](https://aka.ms/azure-jdks)
* [Maven](https://maven.apache.org)

## <a name="clone-the-sample-todo-app-and-prepare-the-repo"></a>Naklonujte ukázkovou aplikaci seznamu úkolů a připravte úložiště

Tento kurz používá aplikaci seznamu úkolů ukázkové pomocí webového uživatelského rozhraní, která volá rozhraní API REST Spring se opírá o [Spring dat Azure Cosmos DB](https://github.com/Microsoft/spring-data-cosmosdb). Kód aplikace je k dispozici [na Githubu](https://github.com/Microsoft/spring-todo-app). Další informace o vytváření aplikací v Javě pomocí Spring a Cosmos DB najdete v tématu [aplikace Spring Boot Starter s kurzem rozhraní SQL API služby Azure Cosmos DB](https://docs.microsoft.com/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db ) a [Spring dat Azure Cosmos DB úvodní](https://github.com/Microsoft/spring-data-cosmosdb#quick-start).


Spusťte příkazy folllowing v terminálu naklonujte ukázkové úložiště a nastavení prostředí ukázkové aplikace.

```bash
git clone --recurse-submodules https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git
cd e2e-java-experience-in-app-service-linux-part-2
yes | cp -rf .prep/* .
```

## <a name="create-an-azure-cosmos-db"></a>Vytvořit databázi Azure Cosmos

Postupujte podle těchto kroků a vytvořte databázi Azure Cosmos DB v rámci vašeho předplatného. Aplikace seznamu úkolů se připojili k této databázi a ukládání dat při spuštění, zachování stavu aplikace bez ohledu na to, ve kterém aplikaci spouštíte.

1. Přihlašovací jméno Azure CLI a volitelně nastavit své předplatné, pokud máte více než jedním připojeným k své přihlašovací údaje.

    ```bash
    az login
    az account set -s <your-subscription-id>
    ```   

2. Vytvořte skupinu prostředků Azure, zobrazí název skupiny prostředků.

    ```bash
    az group create -n <your-azure-group-name> \
        -l <your-resource-group-region>
    ```

3. Vytvořit databázi Azure Cosmos DB s `GlobalDocumentDB` druhu. Název služby Cosmos DB musí používat jenom malá písmena. Poznamenejte si `documentEndpoint` pole v odpovědi z příkazu.

    ```bash
    az cosmosdb create --kind GlobalDocumentDB \
        -g <your-azure-group-name> \
        -n <your-azure-COSMOS-DB-name-in-lower-case-letters>
    ```

4. Získejte klíč služby Azure Cosmos DB pro připojení k aplikaci. Zachovat he `primaryMasterKey`, `documentEndpoint` dosahu, kdykoli budete potřebovat v dalším kroku.

    ```bash
    az cosmosdb list-keys -g <your-azure-group-name> -n <your-azure-COSMOSDB-name>
    ```

## <a name="configure-the-todo-app-properties"></a>Konfigurace vlastností aplikace seznamu úkolů

Otevřete terminál ve vašem počítači. Zkopírujte ukázkový soubor skriptu v naklonovaného úložiště, takže si můžete přizpůsobit pro vaši databázi Cosmos DB, který jste právě vytvořili.

```bash
cd initial/spring-todo-app
cp set-env-variables-template.sh .scripts/set-env-variables.sh
```
 
Upravit `.scripts/set-env-variables.sh` ve svém oblíbeném editoru a zadejte informace o připojení služby Azure Cosmos DB. Konfigurace aplikace App Service Linux používat stejnou oblast, stejně jako předtím (`your-resource-group-region`) a skupinu prostředků (`your-azure-group-name`) používá při vytváření databáze Cosmos DB. Zvolte WEBAPP_NAME, který je jedinečný, protože ho nemůže mít libovolný název webové aplikace v jakémkoli nasazení Azure.

```bash
export COSMOSDB_URI=<put-your-COSMOS-DB-documentEndpoint-URI-here>
export COSMOSDB_KEY=<put-your-COSMOS-DB-primaryMasterKey-here>
export COSMOSDB_DBNAME=<put-your-COSMOS-DB-name-here>

// App Service Linux Configuration
export RESOURCEGROUP_NAME=<put-your-resource-group-name-here>
export WEBAPP_NAME=<put-your-Webapp-name-here>
export REGION=<put-your-REGION-here>
```

Potom spusťte skript:

```bash
source .scripts/set-env-variables.sh
```
   
Tyto proměnné prostředí se používají v `application.properties` v aplikaci seznamu úkolů. Pole v souboru vlastnosti nastavit výchozí konfiguraci úložiště pro Spring Data:

```properties
azure.cosmosdb.uri=${COSMOSDB_URI}
azure.cosmosdb.key=${COSMOSDB_KEY}
azure.cosmosdb.database=${COSMOSDB_DBNAME}
```

```java
@Repository
public interface TodoItemRepository extends DocumentDbRepository<TodoItem, String> {
}
```

Potom ukázková aplikace používá `@Document` anotace naimportované z `com.microsoft.azure.spring.data.cosmosdb.core.mapping.Document` k nastavení typu entity k ukládání a správy pomocí služby Cosmos DB:

```java
@Document
public class TodoItem {
    private String id;
    private String description;
    private String owner;
    private boolean finished;
```

## <a name="run-the-sample-app"></a>Spuštění ukázkové aplikace

Použití Mavenu k spuštění ukázky.

```bash
mvn package spring-boot:run
```

Výstup by měl vypadat nějak takto.

```bash
bash-3.2$ mvn package spring-boot:run
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building spring-todo-app 2.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 


[INFO] SimpleUrlHandlerMapping - Mapped URL path [/webjars/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
[INFO] SimpleUrlHandlerMapping - Mapped URL path [/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
[INFO] WelcomePageHandlerMapping - Adding welcome page: class path resource [static/index.html]
2018-10-28 15:04:32.101  INFO 7673 --- [           main] c.m.azure.documentdb.DocumentClient      : Initializing DocumentClient with serviceEndpoint [https://sample-cosmos-db-westus.documents.azure.com:443/], ConnectionPolicy [ConnectionPolicy [requestTimeout=60, mediaRequestTimeout=300, connectionMode=Gateway, mediaReadMode=Buffered, maxPoolSize=800, idleConnectionTimeout=60, userAgentSuffix=;spring-data/2.0.6;098063be661ab767976bd5a2ec350e978faba99348207e8627375e8033277cb2, retryOptions=com.microsoft.azure.documentdb.RetryOptions@6b9fb84d, enableEndpointDiscovery=true, preferredLocations=null]], ConsistencyLevel [null]
[INFO] AnnotationMBeanExporter - Registering beans for JMX exposure on startup
[INFO] TomcatWebServer - Tomcat started on port(s): 8080 (http) with context path ''
[INFO] TodoApplication - Started TodoApplication in 45.573 seconds (JVM running for 76.534)
```

Přistupujete k aplikaci Spring úkolů po spuštění aplikace místně pomocí tohoto odkazu: [ http://localhost:8080/ ](http://localhost:8080/).

 ![](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-locally.jpg)

Pokud se zobrazí výjimky instaead zprávy "TodoApplication spustit", zkontrolujte, že `bash` skript v předchozím kroku exportované proměnné prostředí správně a správnost hodnoty pro databázi Azure Cosmos DB, které jste vytvořili.

## <a name="configure-azure-deployment"></a>Konfigurace nasazení Azure

Otevřít `pom.xml` soubor `initial/spring-boot-todo` adresáře a přidejte následující [modul plug-in Maven pro Azure App Service](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md) konfigurace.

```xml    
<plugins> 

    <!--*************************************************-->
    <!-- Deploy to Java SE in App Service Linux           -->
    <!--*************************************************-->
       
    <plugin>
        <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-webapp-maven-plugin</artifactId>
            <version>1.4.0</version>
            <configuration>
            <deploymentType>jar</deploymentType>
            
            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>
            
            <!-- Java Runtime Stack for Web App on Linux-->
            <linuxRuntime>jre8</linuxRuntime>
            
            <appSettings>
                <property>
                    <name>COSMOSDB_URI</name>
                    <value>${COSMOSDB_URI}</value>
                </property>
                <property>
                    <name>COSMOSDB_KEY</name>
                    <value>${COSMOSDB_KEY}</value>
                </property>
                <property>
                    <name>COSMOSDB_DBNAME</name>
                    <value>${COSMOSDB_DBNAME}</value>
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

## <a name="deploy-to-app-service-on-linux"></a>Nasazení do App Service v Linuxu

Použití `azure-webapp:deploy` cíle Maven k nasazení aplikace seznamu úkolů do služby Azure App Service v Linuxu.

```bash

// Deploy
bash-3.2$ mvn azure-webapp:deploy
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building spring-todo-app 2.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- azure-webapp-maven-plugin:1.4.0:deploy (default-cli) @ spring-todo-app ---
[INFO] Authenticate with Azure CLI 2.0
[INFO] Target Web App doesn't exist. Creating a new one...
[INFO] Creating App Service Plan 'ServicePlanb6ba8178-5bbb-49e7'...
[INFO] Successfully created App Service Plan.
[INFO] Successfully created Web App.
[INFO] Trying to deploy artifact to spring-todo-app...
[INFO] Successfully deployed the artifact to https://spring-todo-app.azurewebsites.net
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 02:19 min
[INFO] Finished at: 2018-10-28T15:32:03-07:00
[INFO] Final Memory: 50M/574M
[INFO] ------------------------------------------------------------------------
```

Výstup obsahuje adresu URL nasazené aplikace (v tomto příkladu `https://spring-todo-app.azurewebsites.net` ). Můžete zkopírovat tuto adresu URL do svého webového prohlížeče nebo spuštěním následujícího příkazu v okně terminálu se načíst vaše aplikace.

```bash
open https://spring-todo-app.azurewebsites.net
```

Měli byste vidět aplikaci spuštěnou pomocí vzdálené adresy URL do adresního řádku:

 ![](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-in-app-service.jpg)

## <a name="view-logs-to-troubleshoot-the-app"></a>Zobrazit protokoly pro řešení potíží s aplikací

Povolte protokolování nasazené webové aplikace v Javě ve službě App Service v Linuxu:

```bash
az webapp log config --name ${WEBAPP_NAME} \
 --resource-group ${RESOURCEGROUP_NAME} \
  --web-server-logging filesystem
```

Potom streamování protokolů webové aplikace do svého terminálu:

```bash
az webapp log tail --name ${WEBAPP_NAME} \
 --resource-group ${RESOURCEGROUP_NAME}
```

Zobrazí poslední řádky výstupu a jako nové žádosti o aplikace seznamu úkolů se streamování v v konzole. Ukončete konzolu, pomocí CTRL + C.

```bash
bash-3.2$ az webapp log tail --name ${WEBAPP_NAME}  --resource-group ${RESOURCEGROUP_NAME}
2018-10-28T22:50:17  Welcome, you are now connected to log-streaming service.
2018-10-28T22:44:56.265890407Z   _____                               
2018-10-28T22:44:56.265930308Z   /  _  \ __________ _________   ____  
2018-10-28T22:44:56.265936008Z  /  /_\  \___   /  |  \_  __ \_/ __ \ 
2018-10-28T22:44:56.265940308Z /    |    \/    /|  |  /|  | \/\  ___/ 
2018-10-28T22:44:56.265944408Z \____|__  /_____ \____/ |__|    \___  >
2018-10-28T22:44:56.265948508Z         \/      \/                  \/ 
2018-10-28T22:44:56.265952508Z A P P   S E R V I C E   O N   L I N U X
2018-10-28T22:44:56.265956408Z Documentation: https://aka.ms/webapp-linux
2018-10-28T22:44:56.266260910Z Setup openrc ...
2018-10-28T22:44:57.396926506Z Service `hwdrivers' needs non existent service `dev'
2018-10-28T22:44:57.397294409Z  * Caching service dependencies ... [ ok ]
2018-10-28T22:44:57.474152273Z Starting ssh service...
...
...
2018-10-28T22:46:13.432160734Z [INFO] AnnotationMBeanExporter - Registering beans for JMX exposure on startup
2018-10-28T22:46:13.744859424Z [INFO] TomcatWebServer - Tomcat started on port(s): 80 (http) with context path ''
2018-10-28T22:46:13.783230205Z [INFO] TodoApplication - Started TodoApplication in 57.209 seconds (JVM running for 70.815)
2018-10-28T22:46:14.887366993Z 2018-10-28 22:46:14.887  INFO 198 --- [p-nio-80-exec-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring FrameworkServlet 'dispatcherServlet'
2018-10-28T22:46:14.887637695Z [INFO] DispatcherServlet - FrameworkServlet 'dispatcherServlet': initialization started
2018-10-28T22:46:14.998479907Z [INFO] DispatcherServlet - FrameworkServlet 'dispatcherServlet': initialization completed in 111 ms

2018-10-28T22:49:20.572059062Z Sun Oct 28 22:49:20 GMT 2018 GET ======= /api/todolist =======
2018-10-28T22:49:25.850543080Z Sun Oct 28 22:49:25 GMT 2018 DELETE ======= /api/todolist/{4f41ab03-1b12-4131-a920-fe5dfec106ca} ======= 
2018-10-28T22:49:26.047126614Z Sun Oct 28 22:49:26 GMT 2018 GET ======= /api/todolist =======
2018-10-28T22:49:30.201740227Z Sun Oct 28 22:49:30 GMT 2018 POST ======= /api/todolist ======= Milk
2018-10-28T22:49:30.413468872Z Sun Oct 28 22:49:30 GMT 2018 GET ======= /api/todolist =======
```

## <a name="scale-out-the-todo-app"></a>Horizontální navýšení kapacity aplikace seznamu úkolů

Horizontální navýšení kapacity aplikace tak, že přidáte jiný pracovní proces:

```bash
az appservice plan update --number-of-workers 2 \
   --name ${WEBAPP_PLAN_NAME} \
   --resource-group <your-azure-group-name>
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud tyto prostředky nepotřebujete pro další kurz (viz [Další kroky](#next)), můžete je odstranit spuštěním následujícího příkazu ve službě Cloud Shell: 
  
```bash
az group delete --name your-azure-group-name
```

<a name="next"></a>

## <a name="next-steps"></a>Další postup

[Azure pro vývojáře v Javě](/java/azure/)
[Spring Boot](https://spring.io/projects/spring-boot), [Spring dat pro službu Cosmos DB](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db?view=azure-java-stable), [služby Azure Cosmos DB](/azure/cosmos-db/sql-api-introduction) a [služby App Service Linux ](/azure/app-service/containers/app-service-linux-intro).

Další informace o provozování aplikací v Javě ve službě App Service v Linuxu v příručce pro vývojáře.

> [!div class="nextstepaction"] 
> [Java v App Service pro Linux Příručka vývojáře](/azure/app-service/containers/app-service-linux-java)
