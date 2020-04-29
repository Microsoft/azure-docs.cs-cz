---
title: Referenční dokumentace pro vývojáře v jazyce Java pro Azure Functions
description: Naučte se vyvíjet funkce pomocí Java.
ms.topic: conceptual
ms.date: 09/14/2018
ms.openlocfilehash: 19a290fe7717d7838e8fcd1d1f5cddb3f54eb812
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82145333"
---
# <a name="azure-functions-java-developer-guide"></a>Azure Functions příručka pro vývojáře Java

Modul runtime Azure Functions podporuje [jazyk Java se 8 LTS (Zulu 8.31.0.2-JRE 8.0.181-win_x64)](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/). Tato příručka obsahuje informace o složitými rozhraními psaní Azure Functions pomocí jazyka Java.

Vzhledem k tomu, že se jedná o jiné jazyky, Function App může mít jednu nebo více funkcí. Funkce jazyka Java je `public` metoda upravená s poznámkou `@FunctionName`. Tato metoda definuje položku pro funkci jazyka Java a musí být v určitém balíčku jedinečná. Jeden Function App napsaný v jazyce Java může mít více tříd s více veřejnými metodami `@FunctionName`, které jsou opatřeny poznámkami.

V tomto článku se předpokládá, že už jste si přečetli [Azure Functions referenci pro vývojáře](functions-reference.md). Měli byste také dokončit rychlé zprovoznění funkcí a vytvořit svou první funkci pomocí [Visual Studio Code](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-java) nebo [Maven](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java).

## <a name="programming-model"></a>Programovací model 

Koncepty [triggerů a vazeb](functions-triggers-bindings.md) jsou zásadní pro Azure Functions. Triggery spustí provádění kódu. Vazby poskytují způsob, jak předávat data a vracet data z funkce, aniž byste museli psát vlastní přístup k datům.

## <a name="create-java-functions"></a>Vytváření funkcí jazyka Java

Aby bylo snazší vytvářet funkce jazyka Java, existují nástroje založené na Maven a archetypes, které používají předdefinované šablony Java, které vám pomohou vytvořit projekty s určitou triggerem funkce.    

### <a name="maven-based-tooling"></a>Nástroje založené na Maven

Následující prostředí pro vývojáře mají Azure Functions nástrojů, které vám umožní vytvářet projekty funkcí Java: 

+ [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)
+ [Eclipse](functions-create-maven-eclipse.md)
+ [IntelliJ](functions-create-maven-intellij.md)

Výše uvedené odkazy na články ukazují, jak vytvořit své první funkce pomocí integrovaného vývojového prostředí (IDE) podle vašeho výběru. 

### <a name="project-scaffolding"></a>Generování uživatelského rozhraní pro projekt

Pokud dáváte přednost vývoji příkazového řádku od terminálu, nejjednodušší způsob, jak vygenerovat projekty funkcí založených na jazyce Java `Apache Maven` , je použít archetypes. Java Maven Archetype pro Azure Functions se publikuje v rámci následujícího _identifikátoru GroupID_:_artifactId_: [com. Microsoft. Azure: Azure-Functions-Archetype](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-archetype/). 

Následující příkaz vytvoří nový projekt funkce jazyka Java pomocí tohoto archetype:

```
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

Pokud chcete začít s tímto Archetype, přečtěte si [rychlý Start Java](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java). 

## <a name="create-kotlin-functions-preview"></a>Vytváření Kotlinch funkcí (Preview)

K dispozici je také Maven Archetype pro generování Kotlin funkcí. Tato Archetype, která je aktuálně ve verzi Preview, je publikovaná v rámci následujícího _identifikátoru GroupID_:_artifactId_: [com. Microsoft. Azure: Azure-Functions-Kotlin-Archetype](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-kotlin-archetype/). 

Následující příkaz vytvoří nový projekt funkce jazyka Java pomocí tohoto archetype:

```
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-kotlin-archetype
```

Pokud chcete začít s tímto Archetype, přečtěte si téma [rychlý Start Kotlin](functions-create-first-kotlin-maven.md).

## <a name="folder-structure"></a>Struktura složek

Tady je struktura složek Azure Functionsho projektu Java:

```
FunctionsProject
 | - src
 | | - main
 | | | - java
 | | | | - FunctionApp
 | | | | | - MyFirstFunction.java
 | | | | | - MySecondFunction.java
 | - target
 | | - azure-functions
 | | | - FunctionApp
 | | | | - FunctionApp.jar
 | | | | - host.json
 | | | | - MyFirstFunction
 | | | | | - function.json
 | | | | - MySecondFunction
 | | | | | - function.json
 | | | | - bin
 | | | | - lib
 | - pom.xml
```

_* Projekt Kotlin vypadá velmi podobně, protože je stále Maven_

Ke konfiguraci aplikace Function App můžete použít sdílený soubor [Host. JSON](functions-host-json.md) . Každá funkce má svůj vlastní soubor kódu (. Java) a konfigurační soubor vazby (Function. JSON).

Do projektu lze umístit více než jednu funkci. Vyhněte se vkládání funkcí do samostatných jar. `FunctionApp` V cílovém adresáři je to, co se nasadí do vaší aplikace Function App v Azure.

## <a name="triggers-and-annotations"></a>Aktivační události a poznámky

 Funkce jsou vyvolány triggerem, jako je požadavek HTTP, časovač nebo aktualizace dat. Vaše funkce potřebuje zpracovat tento Trigger a všechny ostatní vstupy a vytvoří jeden nebo více výstupů.

Použijte poznámky Java obsažené v balíčku [com. Microsoft. Azure. Functions. Annotation. *](/java/api/com.microsoft.azure.functions.annotation) pro svázání vstupu a výstupů s vašimi metodami. Další informace najdete v [referenční dokumentaci Java](/java/api/com.microsoft.azure.functions.annotation).

> [!IMPORTANT] 
> Musíte nakonfigurovat účet Azure Storage v [místním. Settings. JSON](/azure/azure-functions/functions-run-local#local-settings-file) pro spuštění služby Azure Blob Storage, úložiště Azure Queue Storage nebo služby Azure Table Storage místně.

Příklad:

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

Tady je vygenerovaný odpovídající `function.json` modul [Azure-Functions-Maven-plugin](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin):

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.Function.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}

```

## <a name="jdk-runtime-availability-and-support"></a>Dostupnost a podpora modulu runtime JDK 

Pro místní vývoj aplikací funkcí Java Stáhněte a použijte [Azul Zulu Enterprise pro Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) Java 8 sady JDK ze [systémů Azul](https://www.azul.com/downloads/azure-only/zulu/). Při nasazení aplikací Function App do cloudu používá Azure Functions Azul Java 8 JDK runtime.

[Podpora Azure](https://azure.microsoft.com/support/) pro problémy s aplikacemi sady JDK a Functions je dostupná s [kvalifikovaným plánem podpory](https://azure.microsoft.com/support/plans/).

## <a name="customize-jvm"></a>Přizpůsobení JVM

Funkce umožňují přizpůsobit virtuální počítač Java (JVM), který se používá ke spouštění funkcí jazyka Java. Ve výchozím nastavení se používají [následující možnosti JVM](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7) :

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

Další argumenty můžete zadat v nastavení aplikace s názvem `JAVA_OPTS`. Do aplikace Function App nasazenou do Azure můžete přidat nastavení aplikace v Azure Portal nebo v rozhraní příkazového řádku Azure.

> [!IMPORTANT]  
> V plánu spotřeby musíte také přidat nastavení WEBSITE_USE_PLACEHOLDER s hodnotou 0, aby přizpůsobení fungovalo. Toto nastavení zvyšuje dobu studeného startu pro funkce jazyka Java.

### <a name="azure-portal"></a>portál Azure

V [Azure Portal](https://portal.azure.com)přidejte `JAVA_OPTS` nastavení pomocí [karty nastavení aplikace](functions-how-to-use-azure-function-app-settings.md#settings) .

### <a name="azure-cli"></a>Azure CLI

Pomocí příkazu [AZ functionapp config appSettings set](/cli/azure/functionapp/config/appsettings) můžete nastavit `JAVA_OPTS`, jak je uvedeno v následujícím příkladu:

#### <a name="consumption-plan"></a>[Plán Consumption](#tab/consumption)
```azurecli-interactive
az functionapp config appsettings set \
--settings "JAVA_OPTS=-Djava.awt.headless=true" \
"WEBSITE_USE_PLACEHOLDER=0" \
--name <APP_NAME> --resource-group <RESOURCE_GROUP>
```
#### <a name="dedicated-plan--premium-plan"></a>[Vyhrazený plán/plán Premium](#tab/dedicated+premium)
```azurecli-interactive
az functionapp config appsettings set \
--settings "JAVA_OPTS=-Djava.awt.headless=true" \
--name <APP_NAME> --resource-group <RESOURCE_GROUP>
```
---

Tento příklad povolí bezobslužný režim. Nahraďte `<APP_NAME>` názvem vaší aplikace Function App a `<RESOURCE_GROUP>` skupinou prostředků. 

## <a name="third-party-libraries"></a>Knihovny třetích stran 

Azure Functions podporuje použití knihoven třetích stran. Ve výchozím nastavení jsou všechny závislosti zadané v souboru `pom.xml` projektu automaticky seskupeny během tohoto [`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) cíle. Pro knihovny, které nejsou zadané jako závislosti `pom.xml` v souboru, je umístěte do `lib` adresáře v kořenovém adresáři funkce. Závislosti, které jsou `lib` umístěny v adresáři, jsou přidány do zaváděcího programu Systémové třídy za běhu.

Tato `com.microsoft.azure.functions:azure-functions-java-library` závislost je ve výchozím nastavení k dispozici u třídy classpath a není nutné ji do tohoto `lib` adresáře zahrnout. [Azure-Functions-Java-Worker](https://github.com/Azure/azure-functions-java-worker) také přidává [zde](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) uvedené závislosti do cesty k cestě.

## <a name="data-type-support"></a>Podpora datových typů

Můžete použít prosté staré objekty Java (POJO), typy definované v `azure-functions-java-library`nebo primitivní datové typy, jako je například řetězec a celé číslo, a vytvořit vazbu na vstupní nebo výstupní vazby.

### <a name="pojos"></a>Pojo

Pro převod vstupních dat na POJO [Azure-Functions-Java-Work](https://github.com/Azure/azure-functions-java-worker) používá knihovnu [gson](https://github.com/google/gson) . Typy POJO používané jako vstupy pro funkce by měly `public`být.

### <a name="binary-data"></a>Binární data

Vytvořte vazby na binární vstupy nebo `byte[]`výstupy na, `dataType` nastavením pole v souboru Function. JSON `binary`na:

```java
   @FunctionName("BlobTrigger")
    @StorageAccount("AzureWebJobsStorage")
     public void blobTrigger(
        @BlobTrigger(name = "content", path = "myblob/{fileName}", dataType = "binary") byte[] content,
        @BindingName("fileName") String fileName,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Blob trigger function processed a blob.\n Name: " + fileName + "\n Size: " + content.length + " Bytes");
    }
```

Pokud očekáváte hodnoty null, použijte `Optional<T>`.

## <a name="bindings"></a>Vazby

Vstupní a výstupní vazby poskytují deklarativní způsob, jak se připojit k datům v rámci vašeho kódu. Funkce může mít více vstupních a výstupních vazeb.

### <a name="input-binding-example"></a>Příklad vstupní vazby

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { "put" }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String inputReq,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") TestInputData inputData
        @TableOutput(name = "myOutputTable", tableName = "Person", connection = "AzureWebJobsStorage") OutputBinding<Person> testOutputData,
    ) {
        testOutputData.setValue(new Person(httpbody + "Partition", httpbody + "Row", httpbody + "Name"));
        return "Hello, " + inputReq + " and " + inputData.getKey() + ".";
    }

    public static class TestInputData {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
    public static class Person {
        public String PartitionKey;
        public String RowKey;
        public String Name;

        public Person(String p, String r, String n) {
            this.PartitionKey = p;
            this.RowKey = r;
            this.Name = n;
        }
    }
}
```

Tuto funkci vyvoláte s požadavkem HTTP. 
- Datová část požadavku HTTP je předána `String` jako argument pro `inputReq`argument.
- Jedna položka je načtena z tabulkového úložiště a je předána jako `TestInputData` argument `inputData`.

Pokud chcete dostávat dávku vstupů, můžete vytvořit vazby `String[]`na `POJO[]`, `List<String>`, nebo `List<POJO>`.

```java
@FunctionName("ProcessIotMessages")
    public void processIotMessages(
        @EventHubTrigger(name = "message", eventHubName = "%AzureWebJobsEventHubPath%", connection = "AzureWebJobsEventHubSender", cardinality = Cardinality.MANY) List<TestEventData> messages,
        final ExecutionContext context)
    {
        context.getLogger().info("Java Event Hub trigger received messages. Batch size: " + messages.size());
    }
    
    public class TestEventData {
    public String id;
}

```

Tato funkce se aktivuje pokaždé, když se v nakonfigurovaném centru událostí nacházejí nová data. Vzhledem k `cardinality` tomu, že `MANY`je nastavena na, funkce dostane dávku zpráv z centra událostí. `EventData`z centra událostí se převede na `TestEventData` pro provádění funkce.

### <a name="output-binding-example"></a>Příklad výstupní vazby

Výstupní vazbu k vrácené hodnotě můžete svázat pomocí `$return`. 

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("copy")
    @StorageAccount("AzureWebJobsStorage")
    @BlobOutput(name = "$return", path = "samples-output-java/{name}")
    public static String copy(@BlobTrigger(name = "blob", path = "samples-input-java/{name}") String content) {
        return content;
    }
}
```

Pokud existuje více výstupních vazeb, použijte vrácenou hodnotu pouze pro jednu z nich.

K odeslání více výstupních hodnot použijte `OutputBinding<T>` definici v `azure-functions-java-library` balíčku. 

```java
@FunctionName("QueueOutputPOJOList")
    public HttpResponseMessage QueueOutputPOJOList(@HttpTrigger(name = "req", methods = { HttpMethod.GET,
            HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "itemsOut", queueName = "test-output-java-pojo", connection = "AzureWebJobsStorage") OutputBinding<List<TestData>> itemsOut, 
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");
       
        String query = request.getQueryParameters().get("queueMessageId");
        String queueMessageId = request.getBody().orElse(query);
        itemsOut.setValue(new ArrayList<TestData>());
        if (queueMessageId != null) {
            TestData testData1 = new TestData();
            testData1.id = "msg1"+queueMessageId;
            TestData testData2 = new TestData();
            testData2.id = "msg2"+queueMessageId;

            itemsOut.getValue().add(testData1);
            itemsOut.getValue().add(testData2);

            return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + queueMessageId).build();
        } else {
            return request.createResponseBuilder(HttpStatus.INTERNAL_SERVER_ERROR)
                    .body("Did not find expected items in CosmosDB input list").build();
        }
    }

     public static class TestData {
        public String id;
    }
```

Tuto funkci jste vyvolali na HttpRequest. Zapisuje do fronty úložiště více hodnot.

## <a name="httprequestmessage-and-httpresponsemessage"></a>Zprávy HttpRequestMessage a HttpResponseMessage

 Ty jsou definované v `azure-functions-java-library`. Jsou pomocné typy pro práci s HttpTrigger funkcemi.

| Specializovaný typ      |       Cíl        | Typické použití                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    Trigger HTTP     | Načte metodu, hlavičky nebo dotazy |
| `HttpResponseMessage` | Výstupní vazba HTTP | Vrátí stav jiný než 200.   |

## <a name="metadata"></a>Metadata

Několik triggerů odesílá [metadata triggeru](/azure/azure-functions/functions-triggers-bindings) spolu se vstupními daty. K navázání `@BindingName` aktivačních metadat můžete použít poznámku.


```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class Function {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { "get", "post" }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```
V předchozím příkladu `queryValue` je svázán s parametrem `name` řetězce dotazu v adrese URL požadavku HTTP,. `http://{example.host}/api/metadata?name=test` Tady je další příklad, ve kterém se dozvíte `Id` , jak vytvořit vazby z metadat triggeru fronty.

```java
 @FunctionName("QueueTriggerMetadata")
    public void QueueTriggerMetadata(
        @QueueTrigger(name = "message", queueName = "test-input-java-metadata", connection = "AzureWebJobsStorage") String message,@BindingName("Id") String metadataId,
        @QueueOutput(name = "output", queueName = "test-output-java-metadata", connection = "AzureWebJobsStorage") OutputBinding<TestData> output,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Queue trigger function processed a message: " + message + " with metadaId:" + metadataId );
        TestData testData = new TestData();
        testData.id = metadataId;
        output.setValue(testData);
    }
```

> [!NOTE]
> Název zadaný v anotaci musí odpovídat vlastnosti metadata.

## <a name="execution-context"></a>Kontext spuštění

`ExecutionContext`definované v `azure-functions-java-library`obsahuje pomocné metody pro komunikaci s modulem runtime Functions.

### <a name="logger"></a>Nástroj

Použijte `getLogger`definované v `ExecutionContext`, k zápisu protokolů z kódu funkce.

Příklad:

```java

import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.*;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="view-logs-and-trace"></a>Zobrazit protokoly a trasování

Rozhraní příkazového řádku Azure můžete použít ke streamování protokolování Java stdout a stderr i k ostatním protokolováním aplikací. 

Tady je postup konfigurace aplikace Function App pro zápis protokolování aplikace pomocí rozhraní příkazového řádku Azure CLI:

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Pokud chcete streamovat výstup protokolování pro aplikaci Function App pomocí rozhraní příkazového řádku Azure, otevřete nový příkazový řádek, bash nebo relaci Terminálové služby a zadejte tento příkaz:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
Příkaz [AZ WebApp log Tail](/cli/azure/webapp/log) obsahuje možnosti pro filtrování výstupu pomocí `--provider` možnosti. 

Pokud chcete soubory protokolu stáhnout jako jeden soubor ZIP pomocí rozhraní příkazového řádku Azure, otevřete nový příkazový řádek, bash nebo relaci Terminálové služby a zadejte tento příkaz:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

Před spuštěním tohoto příkazu je nutné povolit protokolování systému souborů v Azure Portal nebo rozhraní příkazového řádku Azure.

## <a name="environment-variables"></a>Proměnné prostředí

V funkcích jsou [nastavení aplikace](functions-app-settings.md), jako jsou například připojovací řetězce služby, vystavena jako proměnné prostředí během provádění. K těmto nastavením můžete přistupovat pomocí, `System.getenv("AzureWebJobsStorage")`.

Následující příklad získá [nastavení aplikace](functions-how-to-use-azure-function-app-settings.md#settings)s klíčem s názvem `myAppSetting`:

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("My app setting value: "+ System.getenv("myAppSetting"));
        return String.format(req);
    }
}

```

> [!NOTE]
> Hodnota FUNCTIONS_EXTENSION_VERSION AppSetting by měla být ~ 2 nebo ~ 3 pro optimalizované prostředí pro studený start.

## <a name="next-steps"></a>Další kroky

Další informace o Azure Functions vývoj v jazyce Java najdete v následujících zdrojích informací:

* [Osvědčené postupy pro službu Azure Functions](functions-best-practices.md)
* [Referenční informace pro vývojáře Azure Functions](functions-reference.md)
* [Aktivační události a vazby Azure Functions](functions-triggers-bindings.md)
* Místní vývoj a ladění pomocí [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md)a [zatmění](functions-create-maven-eclipse.md)
* [Vzdálené ladění Java Azure Functions s Visual Studio Code](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Modul plug-in Maven pro Azure Functions](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* Zjednodušte vytváření funkcí prostřednictvím `azure-functions:add` cíle a připravte pracovní adresář pro [nasazení souboru ZIP](deployment-zip-push.md).
