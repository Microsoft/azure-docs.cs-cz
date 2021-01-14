---
title: 'Kurz: aplikace pro Linux Java s MongoDB'
description: Naučte se, jak získat datově řízenou aplikaci Java pro Linux pracující v Azure App Service s připojením k MongoDB běžícímu v Azure (Cosmos DB).
author: rloutlaw
ms.author: routlaw
ms.devlang: java
ms.topic: tutorial
ms.date: 12/10/2018
ms.custom: mvc, seodec18, seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 2c4fbefc1bb801ab4a9387054ac91e5fca14ec18
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185593"
---
# <a name="tutorial-build-a-java-spring-boot-web-app-with-azure-app-service-on-linux-and-azure-cosmos-db"></a>Kurz: Vytvoření webové aplikace Java jarní Boot pomocí Azure App Service v systému Linux a Azure Cosmos DB

Tento kurz vás provede procesem sestavení, konfigurace, nasazení a škálování webových aplikací v jazyce Java v Azure. Až budete hotovi, budete mít aplikaci pro [spouštění pružiny](https://projects.spring.io/spring-boot/) , která ukládá data v [Azure Cosmos DB](../cosmos-db/index.yml) spuštěná na [Azure App Service v systému Linux](overview.md).

![Aplikace pro spouštění pružiny, která ukládá data do Azure Cosmos DB](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-locally.jpg)

V tomto kurzu:

> [!div class="checklist"]
> * Vytvořte databázi Cosmos DB.
> * Připojit ukázkovou aplikaci k databázi a místně ji otestovat
> * Nasazení ukázkové aplikace do Azure
> * Streamování diagnostických protokolů z App Service
> * Přidání dalších instancí pro horizontální navýšení kapacity ukázkové aplikace

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Rozhraní příkazového [řádku Azure](/cli/azure/overview), které je nainstalované na vašem počítači. 
* [Git](https://git-scm.com/)
* [Java JDK](/azure/developer/java/fundamentals/java-jdk-long-term-support)
* [Maven](https://maven.apache.org)

## <a name="clone-the-sample-todo-app-and-prepare-the-repo"></a>Naklonování ukázkové aplikace TODO a příprava úložiště

V tomto kurzu se používá ukázková aplikace seznamu úkolů s webovým uživatelským rozhraním, které volá pružinovou REST APIovou [Azure Cosmos DBovou datovou pružinu](https://github.com/Microsoft/spring-data-cosmosdb). Kód aplikace je k dispozici [na GitHubu](https://github.com/Microsoft/spring-todo-app). Další informace o psaní aplikací v jazyce Java pomocí pružiny a Cosmos DB najdete v tématu o [jaře Boot Starter pomocí Azure Cosmos DB SQL API](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) a na [jarních datech Azure Cosmos DB rychlém startu](https://github.com/Microsoft/spring-data-cosmosdb#quick-start).


Spuštěním následujících příkazů v terminálu naklonujte ukázkové úložiště a nastavte prostředí ukázkové aplikace.

```bash
git clone --recurse-submodules https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git
cd e2e-java-experience-in-app-service-linux-part-2
yes | cp -rf .prep/* .
```

## <a name="create-an-azure-cosmos-db"></a>Vytvoření služby Azure Cosmos DB

Pomocí těchto kroků vytvořte v předplatném Azure Cosmos DBovou databázi. Aplikace seznamu úkolů se připojí k této databázi a uloží její data při spuštění a zůstane beze stavu aplikace bez ohledu na to, kde aplikaci spustíte.

1. Přihlaste se k rozhraní příkazového řádku Azure a volitelně můžete nastavit předplatné, pokud máte více než jedno připojení k přihlašovacím údajům.

    ```azurecli
    az login
    az account set -s <your-subscription-id>
    ```   

2. Vytvořte skupinu prostředků Azure, která označuje název skupiny prostředků.

    ```azurecli
    az group create -n <your-azure-group-name> \
        -l <your-resource-group-region>
    ```

3. Vytvořte Azure Cosmos DB s `GlobalDocumentDB` typem. Název Cosmos DB musí obsahovat jenom malá písmena. Poznamenejte si `documentEndpoint` pole v odpovědi z příkazu.

    ```azurecli
    az cosmosdb create --kind GlobalDocumentDB \
        -g <your-azure-group-name> \
        -n <your-azure-COSMOS-DB-name-in-lower-case-letters>
    ```

4. Získejte Azure Cosmos DB klíč pro připojení k aplikaci. `primaryMasterKey` `documentEndpoint` V dalším kroku nechte v nejbližším případě, že je budete potřebovat.

    ```azurecli
    az cosmosdb list-keys -g <your-azure-group-name> -n <your-azure-COSMOSDB-name>
    ```

## <a name="configure-the-todo-app-properties"></a>Konfigurace vlastností aplikace TODO

Na počítači si otevřete terminál. Zkopírujte ukázkový soubor skriptu do klonovaného úložiště, abyste ho mohli přizpůsobit vaší Cosmos DB databázi, kterou jste právě vytvořili.

```bash
cd initial/spring-todo-app
cp set-env-variables-template.sh .scripts/set-env-variables.sh
```
 
Upravte `.scripts/set-env-variables.sh` v oblíbeném editoru a zadejte informace o Azure Cosmos DB připojení. Pro konfiguraci App Service Linux použijte stejnou oblast jako předtím ( `your-resource-group-region` ) a skupinu prostředků (), která se `your-azure-group-name` používá při vytváření databáze Cosmos DB. Vyberte WEBAPP_NAME, který je jedinečný, protože nemůže duplikovat žádný název webové aplikace v žádném nasazení Azure.

```bash
export COSMOSDB_URI=<put-your-COSMOS-DB-documentEndpoint-URI-here>
export COSMOSDB_KEY=<put-your-COSMOS-DB-primaryMasterKey-here>
export COSMOSDB_DBNAME=<put-your-COSMOS-DB-name-here>

# App Service Linux Configuration
export RESOURCEGROUP_NAME=<put-your-resource-group-name-here>
export WEBAPP_NAME=<put-your-Webapp-name-here>
export REGION=<put-your-REGION-here>
```

Potom spusťte skript:

```bash
source .scripts/set-env-variables.sh
```
   
Tyto proměnné prostředí se používají v `application.properties` aplikaci seznam todo. Pole v souboru s vlastnostmi nastavily výchozí konfiguraci úložiště pro data pružiny:

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

Ukázková aplikace potom používá `@Document` anotaci importovanou z nástroje `com.microsoft.azure.spring.data.cosmosdb.core.mapping.Document` k nastavení typu entity, který chcete uložit a spravovat pomocí Cosmos DB:

```java
@Document
public class TodoItem {
    private String id;
    private String description;
    private String owner;
    private boolean finished;
```

## <a name="run-the-sample-app"></a>Spuštění ukázkové aplikace

Pomocí Maven spusťte ukázku.

```bash
mvn package spring-boot:run
```

Výstup by měl vypadat nějak takto.

```output
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

Po spuštění aplikace můžete přístup k aplikaci pružiny použít místně pomocí tohoto odkazu: `http://localhost:8080/` .

 ![Přístup k aplikaci jarní TODO místně](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-locally.jpg)

Pokud se zobrazí výjimky místo zprávy "spuštění TodoApplication", zkontrolujte, zda `bash` skript v předchozím kroku exportovali proměnné prostředí správně a zda jsou hodnoty správné pro Azure Cosmos DB databázi, kterou jste vytvořili.

## <a name="configure-azure-deployment"></a>Konfigurace nasazení Azure

Otevřete `pom.xml` soubor v `initial/spring-boot-todo` adresáři a přidejte následující  [modul plug-in webové aplikace Azure pro konfiguraci Maven](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md) .

```xml    
<plugins> 

    <!--*************************************************-->
    <!-- Deploy to Java SE in App Service Linux           -->
    <!--*************************************************-->
       
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.11.0</version>
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

## <a name="deploy-to-app-service-on-linux"></a>Nasazení na App Service v systému Linux

Použijte `mvn azure-webapp:deploy` cíl Maven k nasazení aplikace TODO pro Azure App Service v systému Linux.

```bash

# Deploy
bash-3.2$ mvn azure-webapp:deploy
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building spring-todo-app 2.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- azure-webapp-maven-plugin:1.11.0:deploy (default-cli) @ spring-todo-app ---
[INFO] Auth Type : AZURE_CLI, Auth Files : [C:\Users\testuser\.azure\azureProfile.json, C:\Users\testuser\.azure\accessTokens.json]
[INFO] Subscription : xxxxxxxxx
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

Výstup obsahuje adresu URL vaší nasazené aplikace (v tomto příkladu `https://spring-todo-app.azurewebsites.net` ). Tuto adresu URL můžete zkopírovat do webového prohlížeče nebo spuštěním následujícího příkazu v okně terminálu načíst svou aplikaci.

```bash
curl https://spring-todo-app.azurewebsites.net
```

Na adresním řádku by se měla zobrazit aplikace spuštěná se vzdálenou adresou URL:

 ![Aplikace pružinového spuštění běžící se vzdálenou adresou URL](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-in-app-service.jpg)

## <a name="stream-diagnostic-logs"></a>Streamování diagnostických protokolů

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]


## <a name="scale-out-the-todo-app"></a>Horizontální navýšení kapacity aplikace TODO

Horizontální navýšení kapacity aplikace přidáním dalšího pracovního procesu:

```azurecli
az appservice plan update --number-of-workers 2 \
   --name ${WEBAPP_PLAN_NAME} \
   --resource-group <your-azure-group-name>
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud tyto prostředky nepotřebujete pro další kurz (viz [Další kroky](#next)), můžete je odstranit spuštěním následujícího příkazu ve službě Cloud Shell: 
  
```azurecli
az group delete --name <your-azure-group-name>
```

<a name="next"></a>

## <a name="next-steps"></a>Další kroky

[Azure pro vývojáře](/java/azure/) 
 v jazyce Java [Jarní spouštění](https://spring.io/projects/spring-boot), [pružinová data pro Cosmos DB](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db?view=azure-java-stable), [Azure Cosmos DB](../cosmos-db/introduction.md) a [App Service Linux](overview.md).

Přečtěte si další informace o spouštění aplikací Java v App Service v systému Linux v příručce pro vývojáře.

> [!div class="nextstepaction"] 
> [Příručka pro vývojáře v Javě v App Service Linux](configure-language-java.md?pivots=platform-linux)
