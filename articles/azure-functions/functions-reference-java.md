---
title: Referenční dokumentace pro vývojáře v jazyce Java pro Azure Functions
description: Naučte se vyvíjet funkce pomocí Java.
ms.topic: conceptual
ms.date: 09/14/2018
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 9679f6030ac889ac442a40cd852f5cc17f505756
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422514"
---
# <a name="azure-functions-java-developer-guide"></a>Azure Functions příručka pro vývojáře Java

Tato příručka obsahuje podrobné informace, které vám pomůžou úspěšně vyvíjet Azure Functions pomocí jazyka Java.

Pokud se Azure Functions vývojářům v jazyce Java, zvažte prosím, že si nejdřív přečtete jeden z následujících článků:

| Začínáme | Koncepty| 
| -- | -- |  
| <ul><li>[Funkce jazyka Java pomocí Visual Studio Code](./create-first-function-vs-code-java.md)</li><li>[Funkce Java/Maven s terminálem nebo příkazovým řádkem](./create-first-function-cli-java.md)</li><li>[Funkce jazyka Java s použitím Gradle](functions-create-first-java-gradle.md)</li><li>[Funkce jazyka Java pomocí zatmění](functions-create-maven-eclipse.md)</li><li>[Funkce jazyka Java využívající IntelliJ nápad](functions-create-maven-intellij.md)</li></ul> | <ul><li>[Příručka pro vývojáře](functions-reference.md)</li><li>[Možnosti hostování](functions-scale.md)</li><li>[Požadavky na výkon &nbsp;](functions-best-practices.md)</li></ul> |

## <a name="java-function-basics"></a>Základní informace o funkcích jazyka Java

Funkce jazyka Java je `public` Metoda upravená s poznámkou `@FunctionName` . Tato metoda definuje položku pro funkci jazyka Java a musí být v určitém balíčku jedinečná. Balíček může obsahovat více tříd s více veřejnými metodami, které jsou poznámy s `@FunctionName` . Jeden balíček se nasadí do aplikace Function App v Azure. Při spuštění v Azure poskytuje Function App kontext nasazení, spuštění a správy pro jednotlivé funkce jazyka Java.

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

Pokud dáváte přednost vývoji příkazového řádku od terminálu, nejjednodušší způsob, jak vygenerovat projekty funkcí založených na jazyce Java, je použít `Apache Maven` archetypes. Java Maven Archetype pro Azure Functions se publikuje v rámci následujícího _identifikátoru GroupID_ : _artifactId_ : [com. Microsoft. Azure: Azure-Functions-Archetype](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-archetype/). 

Následující příkaz vytvoří nový projekt funkce jazyka Java pomocí tohoto archetype:

```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

Pokud chcete začít s tímto Archetype, přečtěte si [rychlý Start Java](./create-first-function-cli-java.md).

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

Ke konfiguraci aplikace Function App můžete použít sdílenou [host.js](functions-host-json.md) souboru. Každá funkce má svůj vlastní soubor kódu (. Java) a konfigurační soubor vazby (function.json).

Do projektu lze umístit více než jednu funkci. Vyhněte se vkládání funkcí do samostatných jar. V `FunctionApp` cílovém adresáři je to, co se nasadí do vaší aplikace Function App v Azure.

## <a name="triggers-and-annotations"></a>Aktivační události a poznámky

 Funkce jsou vyvolány triggerem, jako je požadavek HTTP, časovač nebo aktualizace dat. Vaše funkce potřebuje zpracovat tento Trigger a všechny ostatní vstupy a vytvoří jeden nebo více výstupů.

Použijte poznámky Java obsažené v balíčku [com. Microsoft. Azure. Functions. Annotation. *](/java/api/com.microsoft.azure.functions.annotation) pro svázání vstupu a výstupů s vašimi metodami. Další informace najdete v [referenční dokumentaci Java](/java/api/com.microsoft.azure.functions.annotation).

> [!IMPORTANT] 
> Musíte nakonfigurovat účet Azure Storage v [local.settings.jsna](./functions-run-local.md#local-settings-file) pro spuštění služby Azure Blob Storage, úložiště Azure Queue Storage nebo služby Azure Table Storage místně.

Příklad:

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {HttpMethod.POST},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

Tady je vygenerovaný odpovídající modul `function.json` [Azure-Functions-Maven-plugin](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin):

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

## <a name="java-versions"></a>Verze Java

Verze Java, která se používá při vytváření aplikace Function App, na které běží funkce v Azure, je určená v souboru pom.xml. Maven Archetype aktuálně generuje pom.xml pro jazyk Java 8, kterou můžete před publikováním změnit. Verze Java v pom.xml by měla odpovídat verzi, ve které jste svou aplikaci místně vyvinuli a otestovali. 

### <a name="supported-versions"></a>Podporované verze

V následující tabulce jsou uvedeny aktuální podporované verze jazyka Java pro každou hlavní verzi modulu runtime funkcí podle operačního systému:

| Verze funkcí | Verze Java (Windows) | Verze Java (Linux) |
| ----- | ----- | --- |
| 3.x | 11 <br/>8 | 11 <br/>8 |
| 2.x | 8 | neuvedeno |

Pokud pro nasazení nezadáte verzi Java, Maven Archetype ve výchozím nastavení je Java 8 během nasazování do Azure.

### <a name="specify-the-deployment-version"></a>Zadat verzi nasazení

Verzi Java, která cílí na Maven Archetype, můžete řídit pomocí `-DjavaVersion` parametru. Hodnota tohoto parametru může být buď `8` nebo `11` . 

Maven Archetype vygeneruje pom.xml, která cílí na zadanou verzi Java. Následující prvky v pom.xml označují verzi jazyka Java, která se má použít:

| Prvek |  Hodnota Java 8 | Hodnota Java 11 | Popis |
| ---- | ---- | ---- | --- |
| **`Java.version`** | 1.8 | 11 | Verze jazyka Java, kterou používá modul Maven-Compiler-plugin. |
| **`JavaVersion`** | 8 | 11 | Verze Java hostovaná aplikací Function App v Azure |

Následující příklady znázorňují nastavení pro jazyk Java 8 v příslušných částech souboru pom.xml:

#### `Java.version`
:::code language="xml" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="12-19" highlight="14":::

#### `JavaVersion`
:::code language="xml" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="77-85" highlight="80":::

> [!IMPORTANT]
> Musíte mít správně nastavenou proměnnou prostředí JAVA_HOME do adresáře JDK, který se používá při kompilaci kódu pomocí Maven. Ujistěte se, že je verze JDK alespoň tak vysoká jako `Java.version` nastavení. 

### <a name="specify-the-deployment-os"></a>Zadejte operační systém nasazení

Maven také umožňuje určit operační systém, na kterém běží aplikace Function App v Azure. Pomocí `os` elementu vyberte operační systém. 

| Prvek |  Windows | Linux | Docker |
| ---- | ---- | ---- | --- |
| **`os`** | Windows | Linux | Dockeru |

Následující příklad ukazuje nastavení operačního systému v `runtime` části souboru pom.xml:

:::code language="xml" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="77-85" highlight="79":::
 
## <a name="jdk-runtime-availability-and-support"></a>Dostupnost a podpora modulu runtime JDK 

Pro místní vývoj aplikací funkcí Java Stáhněte a použijte odpovídající [Azul Zulu Enterprise pro Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) Java sady JDK ze [systémů Azul](https://www.azul.com/downloads/azure-only/zulu/). Azure Functions používá modul runtime Java JDK Azul při nasazení aplikace Function App do cloudu.

[Podpora Azure](https://azure.microsoft.com/support/) pro problémy s aplikacemi sady JDK a Functions je dostupná s [kvalifikovaným plánem podpory](https://azure.microsoft.com/support/plans/).

## <a name="customize-jvm"></a>Přizpůsobení JVM

Funkce umožňují přizpůsobit virtuální počítač Java (JVM), který se používá ke spouštění funkcí jazyka Java. Ve výchozím nastavení se používají [následující možnosti JVM](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7) :

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

Další argumenty můžete zadat v nastavení aplikace s názvem `JAVA_OPTS` . Do aplikace Function App nasazenou do Azure můžete přidat nastavení aplikace v Azure Portal nebo v rozhraní příkazového řádku Azure.

> [!IMPORTANT]  
> V plánu spotřeby musíte také přidat nastavení WEBSITE_USE_PLACEHOLDER s hodnotou 0, aby přizpůsobení fungovalo. Toto nastavení zvyšuje dobu studeného startu pro funkce jazyka Java.

### <a name="azure-portal"></a>Azure Portal

V [Azure Portal](https://portal.azure.com)přidejte nastavení pomocí [karty nastavení aplikace](functions-how-to-use-azure-function-app-settings.md#settings) `JAVA_OPTS` .

### <a name="azure-cli"></a>Azure CLI

Pomocí příkazu [AZ functionapp config appSettings set](/cli/azure/functionapp/config/appsettings) můžete nastavit `JAVA_OPTS` , jak je uvedeno v následujícím příkladu:

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

Azure Functions podporuje použití knihoven třetích stran. Ve výchozím nastavení jsou všechny závislosti zadané v `pom.xml` souboru projektu automaticky seskupeny během tohoto [`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) cíle. Pro knihovny, které nejsou zadané jako závislosti v `pom.xml` souboru, je umístěte do `lib` adresáře v kořenovém adresáři funkce. Závislosti, které jsou umístěny v `lib` adresáři, jsou přidány do zaváděcího programu Systémové třídy za běhu.

Tato `com.microsoft.azure.functions:azure-functions-java-library` závislost je ve výchozím nastavení k dispozici u třídy classpath a není nutné ji do tohoto `lib` adresáře zahrnout. [Azure-Functions-Java-Worker](https://github.com/Azure/azure-functions-java-worker) také přidává [zde](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) uvedené závislosti do cesty k cestě.

## <a name="data-type-support"></a>Podpora datových typů

Můžete použít prosté staré objekty Java (POJO), typy definované v `azure-functions-java-library` nebo primitivní datové typy, jako je například řetězec a celé číslo, a vytvořit vazbu na vstupní nebo výstupní vazby.

### <a name="pojos"></a>Pojo

Pro převod vstupních dat na POJO [Azure-Functions-Java-Work](https://github.com/Azure/azure-functions-java-worker) používá knihovnu [gson](https://github.com/google/gson) . Typy POJO používané jako vstupy pro funkce by měly být `public` .

### <a name="binary-data"></a>Binární data

Navázání binárních vstupů nebo výstupů do `byte[]` , nastavením `dataType` pole v function.jsna `binary` :

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

Pokud očekáváte hodnoty null, použijte `Optional<T>` .

## <a name="bindings"></a>Vazby

Vstupní a výstupní vazby poskytují deklarativní způsob, jak se připojit k datům v rámci vašeho kódu. Funkce může mít více vstupních a výstupních vazeb.

### <a name="input-binding-example"></a>Příklad vstupní vazby

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { HttpMethod.PUT }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String inputReq,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") TestInputData inputData,
        @TableOutput(name = "myOutputTable", tableName = "Person", connection = "AzureWebJobsStorage") OutputBinding<Person> testOutputData
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
- Datová část požadavku HTTP je předána jako `String` argument pro argument `inputReq` .
- Jedna položka je načtena z tabulkového úložiště a je předána jako `TestInputData` argument `inputData` .

Pokud chcete dostávat dávku vstupů, můžete vytvořit vazby na `String[]` ,, `POJO[]` `List<String>` nebo `List<POJO>` .

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

Tato funkce se aktivuje pokaždé, když se v nakonfigurovaném centru událostí nacházejí nová data. Vzhledem `cardinality` k tomu, že je nastavena na `MANY` , funkce dostane dávku zpráv z centra událostí. `EventData` z centra událostí se převede na `TestEventData` pro provádění funkce.

### <a name="output-binding-example"></a>Příklad výstupní vazby

Výstupní vazbu k vrácené hodnotě můžete svázat pomocí `$return` . 

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

 Ty jsou definované v `azure-functions-java-library` . Jsou pomocné typy pro práci s HttpTrigger funkcemi.

| Specializovaný typ      |       Cíl        | Typické použití                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    Trigger HTTP     | Načte metodu, hlavičky nebo dotazy |
| `HttpResponseMessage` | Výstupní vazba HTTP | Vrátí stav jiný než 200.   |

## <a name="metadata"></a>Metadata

Několik triggerů odesílá [metadata triggeru](./functions-triggers-bindings.md) spolu se vstupními daty. K `@BindingName` navázání aktivačních metadat můžete použít poznámku.


```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class Function {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { HttpMethod.GET, HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```
V předchozím příkladu `queryValue` je svázán s parametrem řetězce dotazu `name` v adrese URL požadavku HTTP, `http://{example.host}/api/metadata?name=test` . Tady je další příklad, ve kterém se dozvíte, jak vytvořit vazby `Id` z metadat triggeru fronty.

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

`ExecutionContext`definované v `azure-functions-java-library` obsahuje pomocné metody pro komunikaci s modulem runtime Functions. Další informace najdete v [článku referenčních informací ExecutionContext](/java/api/com.microsoft.azure.functions.executioncontext).

### <a name="logger"></a>Nástroj

Použijte `getLogger` definované v `ExecutionContext` , k zápisu protokolů z kódu funkce.

Příklad:

```java

import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.*;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
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
Příkaz [AZ WebApp log Tail](/cli/azure/webapp/log) obsahuje možnosti pro filtrování výstupu pomocí `--provider` Možnosti. 

Pokud chcete soubory protokolu stáhnout jako jeden soubor ZIP pomocí rozhraní příkazového řádku Azure, otevřete nový příkazový řádek, bash nebo relaci Terminálové služby a zadejte tento příkaz:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

Před spuštěním tohoto příkazu je nutné povolit protokolování systému souborů v Azure Portal nebo rozhraní příkazového řádku Azure.

## <a name="environment-variables"></a>Proměnné prostředí

V funkcích jsou [nastavení aplikace](functions-app-settings.md), jako jsou například připojovací řetězce služby, vystavena jako proměnné prostředí během provádění. K těmto nastavením můžete přistupovat pomocí, `System.getenv("AzureWebJobsStorage")` .

Následující příklad získá [nastavení aplikace](functions-how-to-use-azure-function-app-settings.md#settings)s klíčem s názvem `myAppSetting` :

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
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
* [Vzdálené ladění funkcí Java pomocí Visual Studio Code](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Modul plug-in Maven pro Azure Functions](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* Zjednodušte vytváření funkcí prostřednictvím `azure-functions:add` cíle a připravte pracovní adresář pro [nasazení souboru ZIP](deployment-zip-push.md).
