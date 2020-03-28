---
title: 'Výuka: Linux Java aplikace s MongoDB'
description: Zjistěte, jak ve službě Azure App Service fungovat datově řízená aplikace Linux Java s připojením k MongoDB spuštěnému v Azure (Cosmos DB).
author: rloutlaw
ms.author: routlaw
ms.devlang: java
ms.topic: tutorial
ms.date: 12/10/2018
ms.custom: mvc, seodec18, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: e5dcb39430158db1ee9a18524d0214335a2bbbba
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80045382"
---
# <a name="tutorial-build-a-java-spring-boot-web-app-with-azure-app-service-on-linux-and-azure-cosmos-db"></a>Kurz: Vytvoření webové aplikace Java Spring Boot se službou Azure App Service na Linuxu a Azure Cosmos DB

Tento kurz vás provede procesem vytváření, konfigurace, nasazování a škálování webových aplikací Java v Azure. Po dokončení budete mít aplikaci [spring boot,](https://projects.spring.io/spring-boot/) která ukládá data v [Azure Cosmos DB](/azure/cosmos-db) spuštěnou ve službě Azure App Service na [Linuxu](/azure/app-service/containers).

![Aplikace spring boot ukládání dat v Azure Cosmos DB](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-locally.jpg)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte databázi Cosmos DB.
> * Připojení ukázkové aplikace k databázi a její místní testování
> * Nasazení ukázkové aplikace do Azure
> * Streamování diagnostických protokolů ze služby App Service
> * Přidání dalších instancí pro horizontální navýšení kapacity ukázkové aplikace

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* [Azure CLI](https://docs.microsoft.com/cli/azure/overview), nainstalované ve vašem počítači. 
* [Git](https://git-scm.com/)
* [Java JDK](https://aka.ms/azure-jdks)
* [Maven](https://maven.apache.org)

## <a name="clone-the-sample-todo-app-and-prepare-the-repo"></a>Klonujte ukázkovou aplikaci TODO a připravte repo

Tento kurz používá ukázkovou aplikaci seznamu todo s webovým uznaným rozhraním, které volá rozhraní API SPRING REST podporované [spring data Azure Cosmos DB](https://github.com/Microsoft/spring-data-cosmosdb). Kód aplikace je k dispozici [na GitHubu](https://github.com/Microsoft/spring-todo-app). Další informace o psaní aplikací Java pomocí spring a cosmos DB najdete v článku [startér jarního startu s kurzem Azure Cosmos DB SQL API](https://docs.microsoft.com/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db ) a v rychlém startu Spring Data Azure [Cosmos DB](https://github.com/Microsoft/spring-data-cosmosdb#quick-start).


Spusťte následující příkazy v terminálu klonovat ukázkové repo a nastavit ukázkové prostředí aplikace.

```bash
git clone --recurse-submodules https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git
cd e2e-java-experience-in-app-service-linux-part-2
yes | cp -rf .prep/* .
```

## <a name="create-an-azure-cosmos-db"></a>Vytvoření služby Azure Cosmos DB

Podle těchto kroků vytvořte databázi Azure Cosmos DB ve vašem předplatném. Aplikace seznamu TODO se připojí k této databázi a uloží její data při spuštění, zachování stavu aplikace bez ohledu na to, kde spustíte aplikaci.

1. Přihlaste se k nastavení azure cli a volitelně nastavte předplatné, pokud máte více než jeden připojený k přihlašovací údaje.

    ```bash
    az login
    az account set -s <your-subscription-id>
    ```   

2. Vytvořte skupinu prostředků Azure s uznanou název skupiny prostředků.

    ```bash
    az group create -n <your-azure-group-name> \
        -l <your-resource-group-region>
    ```

3. Vytvořte Azure Cosmos `GlobalDocumentDB` DB s druhem. Název Cosmos DB musí používat pouze malá písmena. Poznamenejte `documentEndpoint` si pole v odpovědi z příkazu.

    ```bash
    az cosmosdb create --kind GlobalDocumentDB \
        -g <your-azure-group-name> \
        -n <your-azure-COSMOS-DB-name-in-lower-case-letters>
    ```

4. Získejte klíč Azure Cosmos DB pro připojení k aplikaci. Udržujte `primaryMasterKey` `documentEndpoint` , v blízkosti, jak budete potřebovat v dalším kroku.

    ```bash
    az cosmosdb list-keys -g <your-azure-group-name> -n <your-azure-COSMOSDB-name>
    ```

## <a name="configure-the-todo-app-properties"></a>Konfigurace vlastností aplikace TODO

Otevřete terminál v počítači. Zkopírujte ukázkový soubor skriptu v klonovaném repo, abyste ho mohli přizpůsobit pro databázi Cosmos DB, kterou jste právě vytvořili.

```bash
cd initial/spring-todo-app
cp set-env-variables-template.sh .scripts/set-env-variables.sh
```
 
Upravte `.scripts/set-env-variables.sh` ve svém oblíbeném editoru a dodejte informace o připojení Azure Cosmos DB. Pro konfiguraci App Service Linux použijte stejnou`your-resource-group-region`oblast jako`your-azure-group-name`předtím ( ) a skupinu prostředků ( ) používanou při vytváření databáze Cosmos DB. Zvolte WEBAPP_NAME, která je jedinečná, protože nemůže duplikovat žádný název webové aplikace v žádném nasazení Azure.

```bash
export COSMOSDB_URI=<put-your-COSMOS-DB-documentEndpoint-URI-here>
export COSMOSDB_KEY=<put-your-COSMOS-DB-primaryMasterKey-here>
export COSMOSDB_DBNAME=<put-your-COSMOS-DB-name-here>

# App Service Linux Configuration
export RESOURCEGROUP_NAME=<put-your-resource-group-name-here>
export WEBAPP_NAME=<put-your-Webapp-name-here>
export REGION=<put-your-REGION-here>
```

Pak spusťte skript:

```bash
source .scripts/set-env-variables.sh
```
   
Tyto proměnné prostředí se `application.properties` používají v aplikaci seznam TODO. Pole v souboru vlastností nastavují výchozí konfiguraci úložiště pro jarní data:

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

Ukázková aplikace pak `@Document` použije poznámku `com.microsoft.azure.spring.data.cosmosdb.core.mapping.Document` importovanou z k nastavení typu entity, který má být uložen a spravován cosmos DB:

```java
@Document
public class TodoItem {
    private String id;
    private String description;
    private String owner;
    private boolean finished;
```

## <a name="run-the-sample-app"></a>Spuštění ukázkové aplikace

Použijte Maven ke spuštění ukázky.

```bash
mvn package spring-boot:run
```

Výstup by měl vypadat takto.

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

Můžete přistupovat k jarní TODO App lokálně pomocí [http://localhost:8080/](http://localhost:8080/)tohoto odkazu, jakmile je aplikace spuštěna: .

 ![Přístup k aplikaci Spring TODO místně](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-locally.jpg)

Pokud se místo zprávy "Started TodoApplication" zobrazí výjimky, zkontrolujte, zda `bash` skript v předchozím kroku správně exportoval proměnné prostředí a zda jsou hodnoty správné pro databázi Azure Cosmos DB, kterou jste vytvořili.

## <a name="configure-azure-deployment"></a>Konfigurace nasazení Azure

Otevřete `pom.xml` soubor `initial/spring-boot-todo` v adresáři a přidejte následující modul plug-in Azure Web App pro konfiguraci [Maven.](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)

```xml    
<plugins> 

    <!--*************************************************-->
    <!-- Deploy to Java SE in App Service Linux           -->
    <!--*************************************************-->
       
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.9.0</version>
        <configuration>
            <schemaVersion>v2</schemaVersion>

            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>

            <!-- Java Runtime Stack for Web App on Linux-->
            <runtime>
                 <os>linux</os>
                 <javaVersion>jre8</javaVersion>
                 <webContainer>jre8</webContainer>
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

## <a name="deploy-to-app-service-on-linux"></a>Nasazení do služby App Service na Linuxu

Pomocí `azure-webapp:deploy` cíle Maven nasadit aplikaci TODO do služby Azure App Service na Linuxu.

```bash

# Deploy
bash-3.2$ mvn azure-webapp:deploy
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building spring-todo-app 2.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- azure-webapp-maven-plugin:1.9.0:deploy (default-cli) @ spring-todo-app ---
[INFO] Target Web App doesn't exist. Creating a new one...
[INFO] Creating App Service Plan 'ServicePlanb6ba8178-5bbb-49e7'...
[INFO] Successfully created App Service Plan.
[INFO] Successfully created Web App.
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 1 resource to /home/test/e2e-java-experience-in-app-service-linux-part-2/initial/spring-todo-app/target/azure-webapp/spring-todo-app-61bb5207-6fb8-44c4-8230-c1c9e4c099f7
[INFO] Trying to deploy artifact to spring-todo-app...
[INFO] Renaming /home/test/e2e-java-experience-in-app-service-linux-part-2/initial/spring-todo-app/target/azure-webapp/spring-todo-app-61bb5207-6fb8-44c4-8230-c1c9e4c099f7/spring-todo-app-2.0-SNAPSHOT.jar to app.jar
[INFO] Deploying the zip package spring-todo-app-61bb5207-6fb8-44c4-8230-c1c9e4c099f7718326714198381983.zip...
[INFO] Successfully deployed the artifact to https://spring-todo-app.azurewebsites.net
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 02:19 min
[INFO] Finished at: 2019-11-06T15:32:03-07:00
[INFO] Final Memory: 50M/574M
[INFO] ------------------------------------------------------------------------
```

Výstup obsahuje adresu URL nasazené aplikace (v `https://spring-todo-app.azurewebsites.net` tomto příkladu). Tuto adresu URL můžete zkopírovat do webového prohlížeče nebo spustit následující příkaz v okně terminálu a načíst aplikaci.

```bash
open https://spring-todo-app.azurewebsites.net
```

V adresním řádku byste měli vidět aplikaci spuštěnou se vzdálenou adresou URL:

 ![Aplikace spring boot spuštěná se vzdálenou adresou URL](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-in-app-service.jpg)

## <a name="stream-diagnostic-logs"></a>Streamování diagnostických protokolů

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]


## <a name="scale-out-the-todo-app"></a>Horizontální navýšení kapacity aplikace TODO

Horizontální navýšení kapacity aplikace přidáním jiného pracovníka:

```bash
az appservice plan update --number-of-workers 2 \
   --name ${WEBAPP_PLAN_NAME} \
   --resource-group <your-azure-group-name>
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud tyto prostředky nepotřebujete pro další kurz (viz [Další kroky](#next)), můžete je odstranit spuštěním následujícího příkazu ve službě Cloud Shell: 
  
```bash
az group delete --name <your-azure-group-name>
```

<a name="next"></a>

## <a name="next-steps"></a>Další kroky

[Azure pro vývojáře](/java/azure/)
Java[Spring Boot](https://spring.io/projects/spring-boot), jarní data [pro Cosmos DB](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db?view=azure-java-stable), Azure [Cosmos DB](/azure/cosmos-db/sql-api-introduction) a App Service [Linux](app-service-linux-intro.md).

Další informace o spouštění aplikací Java v aplikaci App Service na Linuxu najdete v průvodci pro vývojáře.

> [!div class="nextstepaction"] 
> [Příručka pro vývojáře v Javě v App Service Linux](configure-language-java.md)
