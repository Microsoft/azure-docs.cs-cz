---
title: Referenční příručka pro vývojáře v Jazyce Pro Funkce Azure
description: Pochopit, jak rozvíjet funkce s Javou.
ms.topic: conceptual
ms.date: 09/14/2018
ms.openlocfilehash: 4b1f39ff4fd48a3ed99b34391e9cc6efdad86a5d
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673005"
---
# <a name="azure-functions-java-developer-guide"></a>Průvodce vývojářem Azure Functions Java

Runtime Azure Functions podporuje [Java SE 8 LTS (zulu8.31.0.2-jre8.0.181-win_x64)](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/). Tato příručka obsahuje informace o složitosti psaní funkcí Azure s Javou.

Jak se to stane s jinými jazyky, aplikace funkce může mít jednu nebo více funkcí. Funkce Java je `public` metoda, zdobená poznámkou `@FunctionName`. Tato metoda definuje položku pro funkci Java a musí být jedinečný v určitém balíčku. Jedna aplikace funkce napsaná v jazyce Java může `@FunctionName`mít více tříd s více veřejnými metodami anotovanými aplikacemi .

Tento článek předpokládá, že jste si již přečetli [odkaz na vývojáře Azure Functions](functions-reference.md). Měli byste také dokončit funkce rychlý start vytvořit první funkci pomocí [Visual Studio Kód](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-java) nebo [Maven](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java).

## <a name="programming-model"></a>Programovací model 

Koncepty [aktivačních událostí a vazeb](functions-triggers-bindings.md) jsou pro funkce Azure zásadní. Aktivační události spustit provádění kódu. Vazby poskytují způsob, jak předat data a vrátit data z funkce, aniž by bylo třeba zapisovat vlastní kód pro přístup k datům.

## <a name="create-java-functions"></a>Vytváření funkcí Jazyka Java

Chcete-li usnadnit vytváření funkcí Jazyka Java, existují nástroje založené na Mavenu a archetypy, které používají předdefinované šablony Jazyka Java, které vám pomohou vytvářet projekty s konkrétní aktivační událostí.    

### <a name="maven-based-tooling"></a>Nástroje založené na mavenu

Následující vývojářská prostředí mají nástroje Azure Functions, které umožňují vytvářet projekty funkcí Java: 

+ [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)
+ [Zatmění](functions-create-maven-eclipse.md)
+ [IntelliJ](functions-create-maven-intellij.md)

Výše uvedený článek odkazy ukazují, jak vytvořit své první funkce pomocí ide volby. 

### <a name="project-scaffolding"></a>Projekt Lešení

Pokud dáváte přednost vývoji příkazového řádku z terminálu, nejjednodušší způsob, jak `Apache Maven` lešení Java-založené funkce projektů je použití archetypů. Archetyp Java Maven pro funkce Azure se publikuje pod následujícím _id skupiny_:_artifactId_: [com.microsoft.azure:azure-functions-archetype](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-archetype/). 

Následující příkaz generuje nový projekt funkce Java pomocí tohoto archetypu:

```
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

Chcete-li začít používat tento archetyp, podívejte se na [rychlý start javy](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java). 

## <a name="create-kotlin-functions-preview"></a>Vytvoření kotlinských funkcí (náhled)

K dispozici je také archetyp Maven pro generování Kotlinových funkcí. Tento archetyp, aktuálně ve verzi Preview, je publikován pod následujícím _id skupiny_:_artifactId_: [com.microsoft.azure:azure-functions-kotlin-archetype](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-kotlin-archetype/). 

Následující příkaz generuje nový projekt funkce Java pomocí tohoto archetypu:

```
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-kotlin-archetype
```

Chcete-li začít používat tento archetyp, viz [kotlinský rychlý start](functions-create-first-kotlin-maven.md).

## <a name="folder-structure"></a>Struktura složek

Tady je struktura složek projektu Azure Functions Java:

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

Ke konfiguraci aplikace funkce můžete použít sdílený soubor [host.json.](functions-host-json.md) Každá funkce má svůj vlastní soubor kódu (.java) a konfigurační soubor vazby (function.json).

Do projektu můžete vložit více funkcí. Vyhněte se uvedení své funkce do samostatných sklenic. V `FunctionApp` cílovém adresáři je to, co se nasadí do vaší aplikace funkce v Azure.

## <a name="triggers-and-annotations"></a>Aktivační události a poznámky

 Funkce jsou vyvolány aktivační událostí, jako je například požadavek HTTP, časovač nebo aktualizace dat. Vaše funkce potřebuje zpracovat aktivační událost a všechny ostatní vstupy, aby vytvořily jeden nebo více výstupů.

Pomocí anotací jazyka Java, které jsou součástí balíčku [com.microsoft.azure.functions.anotation.*,](/java/api/com.microsoft.azure.functions.annotation) můžete svázat vstupy a výstupy s vašimi metodami. Další informace naleznete v [referenčních dokumentech java .](/java/api/com.microsoft.azure.functions.annotation)

> [!IMPORTANT] 
> K místnímu spuštění úložiště [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) objektů blob Azure, úložiště Front Azure nebo úložiště Azure Table je nutné místně nakonfigurovat účet azure storage.

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

Zde je generovánodpovídající `function.json` [azure-functions-maven-plugin](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin):

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

## <a name="jdk-runtime-availability-and-support"></a>Dostupnost a podpora za běhu sady JDK 

Pro místní vývoj aplikací funkcí Javy si stáhněte a použijte [azul Zulu Enterprise for Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) Java 8 JDK s [od Azul Systems](https://www.azul.com/downloads/azure-only/zulu/). Funkce Azure používá runtime Azul Java 8 JDK při nasazení funkčních aplikací do cloudu.

[Podpora Azure](https://azure.microsoft.com/support/) pro problémy s JDK a aplikace funkce je k dispozici s [plánem kvalifikované podpory](https://azure.microsoft.com/support/plans/).

## <a name="customize-jvm"></a>Přizpůsobit JVM

Funkce umožňují přizpůsobit virtuální počítač Java (JVM) používaný ke spuštění funkcí Java. Ve výchozím nastavení se používají [následující možnosti JVM:](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7)

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

Další argumenty můžete zadat v `JAVA_OPTS`nastavení aplikace s názvem . Nastavení aplikace můžete přidat do aplikace funkce nasazené do Azure na webu Azure nebo v nastavení příkazového příkazu Konzi.

### <a name="azure-portal"></a>portál Azure

Na [webu Azure Portal](https://portal.azure.com)přidejte `JAVA_OPTS` nastavení na [kartě Nastavení aplikace.](functions-how-to-use-azure-function-app-settings.md#settings)

### <a name="azure-cli"></a>Azure CLI

Můžete použít příkaz [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) pro nastavení `JAVA_OPTS`, jako v následujícím příkladu:

```azurecli-interactive
az functionapp config appsettings set --name <APP_NAME> \
--resource-group <RESOURCE_GROUP> \
--settings "JAVA_OPTS=-Djava.awt.headless=true"
```
Tento příklad umožňuje bezhlavý režim. Nahraďte `<APP_NAME>` název aplikace funkce `<RESOURCE_GROUP>` a skupinou prostředků.

> [!WARNING]  
> V [plánu Spotřeba](functions-scale.md#consumption-plan)je `WEBSITE_USE_PLACEHOLDER` nutné přidat nastavení `0`s hodnotou .  
Toto nastavení zvyšuje dobu studeného spuštění funkcí javy.

## <a name="third-party-libraries"></a>Knihovny třetích stran 

Funkce Azure podporuje použití knihoven třetích stran. Ve výchozím nastavení jsou všechny `pom.xml` závislosti zadané v souboru projektu automaticky svázány během [`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) cíle. U knihoven, které nejsou `pom.xml` v souboru zadány jako závislosti, je umístěte do `lib` adresáře v kořenovém adresáři funkce. Závislosti umístěné v `lib` adresáři jsou přidány do zavaděče třídy systému za běhu.

Závislost `com.microsoft.azure.functions:azure-functions-java-library` je k dispozici na cestě pro třídy ve výchozím nastavení `lib` a nemusí být zahrnuty do adresáře. Také [azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker) přidá závislosti [zde](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) uvedené do cesty třídy.

## <a name="data-type-support"></a>Podpora datového typu

Můžete použít plain staré Java objekty (POJOs), typy definované v `azure-functions-java-library`, nebo primitivní datové typy, jako je string a Integer pro vazbu na vstupní nebo výstupní vazby.

### <a name="pojos"></a>POJOs

Pro převod vstupních dat na POJO používá [pracovník azure-functions-java](https://github.com/Azure/azure-functions-java-worker) [knihovnu gson.](https://github.com/google/gson) Typy POJO používané jako vstupy `public`do funkcí by měly být .

### <a name="binary-data"></a>Binární data

Spojte binární vstupy `byte[]`nebo výstupy `dataType` s ním nastavením pole `binary`ve souboru function.json na :

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

Pokud očekáváte hodnoty `Optional<T>`null, použijte .

## <a name="bindings"></a>Vazby

Vstupní a výstupní vazby poskytují deklarativní způsob připojení k datům z vašeho kódu. Funkce může mít více vstupních a výstupních vazeb.

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
- Datová část požadavku HTTP `String` je `inputReq`předána jako pro argument .
- Jedna položka je načtena z úložiště `TestInputData` tabulky a `inputData`je předána jako argument .

Chcete-li přijmout dávku vstupů, `String[]`můžete `POJO[]` `List<String>`vytvořit `List<POJO>`vazbu na , , nebo .

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

Tato funkce se aktivuje vždy, když jsou v nakonfigurovaném centru událostí nová data. Vzhledem `cardinality` k `MANY`tomu, že je nastavena na , funkce obdrží dávku zpráv z centra událostí. `EventData`z centra událostí se `TestEventData` převede na pro spuštění funkce.

### <a name="output-binding-example"></a>Příklad vazby výstupu

Výstupní vazbu můžete svázat s `$return`vrácenou hodnotou pomocí . 

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

Chcete-li odeslat `OutputBinding<T>` více výstupních hodnot, použijte definované v `azure-functions-java-library` balíčku. 

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

Vyvolat tuto funkci na HttpRequest. Zapíše více hodnot do úložiště fronty.

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage a HttpResponseMessage

 Ty jsou `azure-functions-java-library`definovány v . Jsou pomocné typy pro práci s funkcemi HttpTrigger.

| Specializovaný typ      |       Cíl        | Typické použití                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    Trigger HTTP     | Získá metodu, záhlaví nebo dotazy |
| `HttpResponseMessage` | Výstupní vazba PROTOKOLU HTTP | Stav vrácených do jiné hodu než 200   |

## <a name="metadata"></a>Metadata

Několik aktivačních událostí odesílá [metadata aktivační ch aktivačních událostí](/azure/azure-functions/functions-triggers-bindings) spolu se vstupními daty. Poznámky `@BindingName` můžete použít k vytvoření vazby pro aktivaci metadat.


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
V předchozím příkladu `queryValue` je tento parametr vázán `name` na parametr řetězce `http://{example.host}/api/metadata?name=test`dotazu v adrese URL požadavku HTTP . Zde je další příklad, který `Id` ukazuje, jak vytvořit vazbu z dotazů aktivačních událostí fronty.

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
> Název uvedený v poznámkách musí odpovídat vlastnosti metadat.

## <a name="execution-context"></a>Kontext spuštění

`ExecutionContext`, obsahuje `azure-functions-java-library`pomocné metody pro komunikaci s funkcí runtime.

### <a name="logger"></a>Logger

Pomocí `getLogger`aplikace `ExecutionContext`defined in pro zápis protokolů z kódu funkce použijte položku defined in .

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

Azure CLI můžete použít k streamování Java stdout a stderr protokolování, stejně jako další protokolování aplikací. 

Tady je postup, jak nakonfigurovat aplikaci funkcí pro zápis protokolování aplikací pomocí rozhraní příkazového příkazu Azure:

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Chcete-li streamovat výstup protokolování pro aplikaci funkcí pomocí příkazového řádku Azure, otevřete nový příkazový řádek Bash nebo terminálovou relaci a zadejte následující příkaz:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
Az [webapp log tail](/cli/azure/webapp/log) příkaz má možnosti `--provider` pro filtrování výstupu pomocí volby. 

Chcete-li stáhnout soubory protokolu jako jeden soubor ZIP pomocí rozhraní příkazového řádku Azure, otevřete nový příkazový řádek Bash nebo terminálovou relaci a zadejte následující příkaz:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

Před spuštěním tohoto příkazu musíte mít povolené protokolování systému souborů na webu Azure Portal nebo v rozhraní příkazu Azure CLI.

## <a name="environment-variables"></a>Proměnné prostředí

V funkcích jsou [nastavení aplikace](functions-app-settings.md), například připojovací řetězce služby, během provádění vystavena jako proměnné prostředí. K těmto nastavením můžete `System.getenv("AzureWebJobsStorage")`přistupovat pomocí aplikace .

Následující příklad získá [nastavení aplikace](functions-how-to-use-azure-function-app-settings.md#settings)s `myAppSetting`názvem klíče :

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("My app setting value: "+ System.getenv("myAppSetting"));
        return String.format(req);
    }
}

```

## <a name="next-steps"></a>Další kroky

Další informace o vývoji Azure Functions Java najdete v následujících zdrojích informací:

* [Osvědčené postupy pro službu Azure Functions](functions-best-practices.md)
* [Referenční informace pro vývojáře Azure Functions](functions-reference.md)
* [Azure Funkce aktivační události a vazby](functions-triggers-bindings.md)
* Místní vývoj a ladění s [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md)a [Eclipse](functions-create-maven-eclipse.md)
* [Funkce Java Azure vzdáleného ladění pomocí kódu Visual Studia](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Maven plugin pro funkce Azure](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* Zjednodušte vytváření `azure-functions:add` funkcí prostřednictvím cíle a připravte pracovní adresář pro [nasazení souborů ZIP](deployment-zip-push.md).
