---
title: Referenční informace pro vývojáře Java pro službu Azure Functions | Dokumentace Microsoftu
description: Naučte se vyvíjet funkce pomocí Javy.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: Azure functions, functions, zpracování událostí, webhook, dynamické výpočty, architektura bez serveru, java
ms.service: azure-functions
ms.devlang: java
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: routlaw
ms.openlocfilehash: 429c7c266357b4808ab3ebbb7f346cf22d9f479c
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54855386"
---
# <a name="azure-functions-java-developer-guide"></a>Příručka pro vývojáře Azure Functions Java

[!INCLUDE [functions-java-preview-note](../../includes/functions-java-preview-note.md)]

## <a name="programming-model"></a>Programovací model 

Koncepty [aktivačními událostmi a vazbami](functions-triggers-bindings.md) jsou základem pro Azure Functions. Triggery spuštění provádění kódu. Vazby poskytují způsob, jak předávat data a vrátit data z funkce, aniž byste museli psát kód pro přístup k vlastní data.

Funkce by měly být bezstavové metodu ke zpracování vstupu a výstup. Funkce by neměl záviset na všechna pole instancí třídy. Všechny funkce metody by měly být `public` a metody s poznámkou @FunctionName musí být jedinečný, jako je zadání funkce definuje název metody.

## <a name="folder-structure"></a>struktura složek

Tady je struktura složek projektu Java funkce Azure:

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

Existuje sdílený [host.json](functions-host-json.md) soubor, který můžete použít ke konfiguraci aplikace function app. Každá funkce má svůj vlastní soubor s kódem (.Java, který) a vazbu konfigurační soubor (function.json).

Můžete vložit více než jedna funkce v projektu. Vyhněte se vložení vašich funkcí do samostatných souborů JAR. FunctionApp v cílovém adresáři je, co se nasadí do vaší aplikace funkcí v Azure.

## <a name="triggers-and-annotations"></a>Aktivační události a poznámky

 Služba Azure functions se vyvolá triggerem, například požadavek HTTP, časovač nebo aktualizaci dat. Funkce je potřeba zpracovat tuto aktivační událost a všechny ostatní vstupy pro vytvoření jednoho nebo více výstupů.

Použití anotací Java součástí [com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) balíček, který chcete svázat vaše metody vstup a výstup. Další informace najdete v části [referenční dokumentace jazyka Java](/java/api/com.microsoft.azure.functions.annotation).

> [!IMPORTANT] 
> Musíte nakonfigurovat účet služby Azure Storage ve vašich [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) pro místní spuštění aktivační události Azure Storage Blob, fronty nebo tabulky.

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

Tady je vygenerovaný odpovídající `function.json` podle [azure-functions-maven-modul plug-in](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin):

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

## <a name="jdk-runtime-availability-and-support"></a>JDK běhovou dostupnost a podpora 

Stáhnout a použít [Azul Zulu Enterprise pro Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) Java JDK 8 z [Azul Systems](https://www.azul.com/downloads/azure-only/zulu/) pro místní vývoj aplikací v Javě funkce. Služba Azure Functions využívá modul runtime Azul Java 8 JDK při nasazení vaší aplikace function App do cloudu.

[Podpora Azure](https://azure.microsoft.com/en-us/support/) pro problémy s JDK a funkce je k dispozici s aplikací [plán podpory kvalifikovaný](https://azure.microsoft.com/support/plans/).

## <a name="third-party-libraries"></a>Knihovny třetích stran 

Služba Azure Functions podporuje použití knihovny třetích stran. Ve výchozím nastavení, zadané všechny závislosti ve vašem projektu `pom.xml` souboru budou automaticky seskupeny během [ `mvn package` ](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) cíle. Pro knihovny není stanoveno, v závislosti `pom.xml` souboru, umístit je do `lib` adresáře v kořenovém adresáři funkce. Závislosti umístěné v `lib` adresáře se přidají do třídy zavaděč systému v době běhu.

`com.microsoft.azure.functions:azure-functions-java-library` Závislostí je k dispozici na cestě ve výchozím nastavení a nemusí být součástí `lib` adresáře. Navíc uvedené závislosti [tady](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) jsou přidány do cesty pro třídy podle [azure-functions-java-pracovní proces](https://github.com/Azure/azure-functions-java-worker).

## <a name="data-type-support"></a>Podpora typ dat

Můžete použít prostý původní objekty Java (Object Pojo), typy definované v `azure-functions-java-library` nebo primitivní datové typy, jako je například řetězec, celé číslo pro vytvoření vazby k vstupních a výstupních vazeb.

### <a name="plain-old-java-objects-pojos"></a>Prostý původní objekty Java (Object Pojo)

Pro převod vstupní data na POJO, [azure-functions-java-pracovní proces](https://github.com/Azure/azure-functions-java-worker) používá [gson](https://github.com/google/gson) knihovny. Použít jako vstupy do funkce by měla být typy POJO `public`.

### <a name="binary-data"></a>Binární data

Vytvořit vazbu binárních vstupů nebo výstupů na `byte[]` nastavením `dataType` pole ve vašich function.json k `binary`:

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

Použití `Optional<T>` Pokud jsou očekávané hodnoty null

## <a name="bindings"></a>Vazby

Vstupní a výstupní vazby poskytují deklarativní způsob připojení k datům z vašeho kódu. Funkce můžete mít více vstupní a výstupní vazbu.

### <a name="example-input-binding"></a>Příklad vstupní vazby

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

Tato funkce se vyvolala s požadavek HTTP. 
- Datová část požadavku HTTP je předán jako `String` argumentu `inputReq`
- Jedna položka je načten z Azure Table Storage a je předán jako `TestInputData` na argument `inputData`.

Pro příjem batch vstupů, lze svázat `String[]`, `POJO[]`, `List<String>` nebo `List<POJO>`.

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

Tato funkce se spustí vždycky, když je nakonfigurovaná eventhub nová data. Jako `cardinality` je nastavena na `MANY`, funkce přijímá dávky zprávy z centra událostí. Získá EventData z centra událostí převést na `TestEventData` pro spuštění funkce.

### <a name="example-output-binding"></a>Příklad výstupní vazby

Výstupní vazby můžete vázat na návratovou hodnotu pomocí `$return` 

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

Pokud existuje více výstupní vazby, použijte vrácenou hodnotu pouze pro jeden z nich.

Chcete-li odeslat více výstupní hodnoty, použijte `OutputBinding<T>` definované v `azure-functions-java-library` balíčku. 

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

Výše uvedené funkce, které se vyvolá u HttpRequest a zapíše více hodnot do fronty Azure

## <a name="httprequestmessage-and-httpresponsemessage"></a>Hodnota HttpRequestMessage a používá HttpResponseMessage

 Hodnota HttpRequestMessage a používá HttpResponseMessage typy jsou definovány v `azure-functions-java-library` jsou pomocné rutiny typy pro práci s funkcí HttpTrigger

| Speciálním typem      |       Cíl        | Typické použití                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    Trigger HTTP     | Metoda, záhlaví a dotazy |
| `HttpResponseMessage` | Vazby výstupu protokolu HTTP | Než 200 návratový stav   |

## <a name="metadata"></a>Metadata

Několik triggerů odeslat [aktivovat metadat](/azure/azure-functions/functions-triggers-bindings) spolu se vstupní data. Můžete použít poznámku `@BindingName` vytvořit vazbu k aktivaci metadat


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
V příkladu výše `queryValue` je vázán na parametru řetězce dotazu `name` adresa URL požadavku HTTP `http://{example.host}/api/metadata?name=test`. Tady je další příklad k vytvoření vazby na `Id` z metadat aktivační událost fronty

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
> Název zadaný v poznámce musí odpovídat vlastnosti metadat

## <a name="execution-context"></a>Kontext spuštění

`ExecutionContext` podle `azure-functions-java-library` obsahuje pomocné metody pro komunikaci s modul runtime služby functions.

### <a name="logger"></a>Protokolovací nástroj

Použití `getLogger` definované v `ExecutionContext` zápis protokolů z kódu funkce.

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

Můžete použít rozhraní příkazového řádku Azure pro datový proud Java stdout a stderr protokolování, jakož i další protokolování aplikací. 

Konfigurace funkce aplikace pro zápis protokolování aplikací pomocí Azure CLI:

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Streamování uložit výstup protokolování pro aplikaci Function app pomocí Azure CLI, otevřete nový příkazový řádek, prostředí Bash nebo relaci Terminálové služby a zadejte následující příkaz:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
[Az webapp log tail](/cli/azure/webapp/log) příkaz má možnosti filtrování výstupu pomocí `--provider` možnost. 

Stáhnete soubory protokolů jako jednoho souboru ZIP pomocí Azure CLI, otevřete nový příkazový řádek, prostředí Bash nebo relaci Terminálové služby a zadejte následující příkaz:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

Musí mít povolené systému souborů přihlášení na webu Azure Portal nebo rozhraní příkazového řádku Azure před spuštěním tohoto příkazu.

## <a name="environment-variables"></a>Proměnné prostředí

Ve službě Functions [nastavení aplikace](https://docs.microsoft.com/azure/azure-functions/functions-app-settings), jako je například připojení služby jsou řetězce, zveřejní jako proměnné prostředí během provádění. Tato nastavení použijete, můžete přístup `System.getenv("AzureWebJobsStorage")`

Příklad:

Přidat [AppSetting](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings) s testAppSettingValue testAppSetting a hodnota názvu

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("testAppSetting "+ System.getenv("testAppSettingValue"));
        return String.format(req);
    }
}

```

## <a name="next-steps"></a>Další postup

Další informace o vývoji pro Azure funkce v Javě najdete v článku na následujících odkazech:

* [Osvědčené postupy pro službu Azure Functions](functions-best-practices.md)
* [Referenční informace pro vývojáře Azure Functions](functions-reference.md)
* [Azure Functions aktivačními událostmi a vazbami](functions-triggers-bindings.md)
- Místní vývoj a ladění pomocí [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md), a [Eclipse](functions-create-maven-eclipse.md). 
* [Vzdálené ladění v Javě v Azure Functions ve Visual Studiu Code](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Modul plug-in maven pro Azure Functions](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) – zjednodušení vytváření funkce až `azure-functions:add` cíle a připravit pracovní adresář pro [nasazení souboru ZIP](deployment-zip-push.md).
