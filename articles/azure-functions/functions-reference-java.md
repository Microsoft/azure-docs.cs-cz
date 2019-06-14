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
ms.openlocfilehash: d88fda62c59d01a3703fdb583e0881aa8478a6cd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67050764"
---
# <a name="azure-functions-java-developer-guide"></a>Příručka pro vývojáře Azure Functions Java

Modul runtime Azure Functions podporuje [Java SE 8 LTS (zulu8.31.0.2 jre8.0.181 win_x64)](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/). Tato příručka obsahuje informace o složitými rozhraními vytváření Azure Functions pomocí Javy.

Funkce Java je `public` metodě upravené pomocí Poznámka `@FunctionName`. Tato metoda definuje položku pro funkce jazyka Java a musí být jedinečný v konkrétním balíčku. 

Tento článek předpokládá, že jste si už přečetli [referenční informace pro vývojáře Azure Functions](functions-reference.md). Také by se měla dokončit funkce Rychlý start k vytvoření první funkce pomocí [Visual Studio Code](functions-create-first-function-vs-code.md) nebo [Maven](functions-create-first-java-maven.md).

## <a name="programming-model"></a>Programovací model 

Koncepty [aktivačními událostmi a vazbami](functions-triggers-bindings.md) jsou základem pro Azure Functions. Triggery spuštění provádění kódu. Vazby poskytují způsob, jak předávat data a vrátit data z funkce, aniž byste museli psát kód pro přístup k vlastní data.

## <a name="folder-structure"></a>struktura složek

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

Můžete použít sdílené [host.json](functions-host-json.md) souboru konfigurace aplikace function app. Každá funkce má svůj vlastní soubor s kódem (.Java, který) a vazbu konfigurační soubor (function.json).

Můžete vložit více než jedna funkce v projektu. Vyhněte se vložení vašich funkcí do samostatných souborů JAR. `FunctionApp` v cílovém adresáři je, co se nasadí do vaší aplikace funkcí v Azure.

## <a name="triggers-and-annotations"></a>Aktivační události a poznámky

 Funkce se vyvolá triggerem, například požadavek HTTP, časovač nebo aktualizaci dat. Funkce je potřeba zpracovat tuto aktivační událost a všechny ostatní vstupy pro vytvoření jednoho nebo více výstupů.

Použití anotací Java součástí [com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) balíček, který chcete svázat vaše metody vstup a výstup. Další informace najdete v tématu [referenční dokumentace jazyka Java](/java/api/com.microsoft.azure.functions.annotation).

> [!IMPORTANT] 
> Musíte nakonfigurovat účet služby Azure Storage ve vašich [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) ke spuštění Azure Blob storage, Azure Queue storage nebo Azure Table storage triggery místně.

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

Pro místní vývoj aplikace Java function App, stáhnout a použít [Azul Zulu Enterprise pro Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) Java JDK 8 z [Azul Systems](https://www.azul.com/downloads/azure-only/zulu/). Služba Azure Functions využívá modul runtime Azul Java 8 JDK při nasazení vaší aplikace function App do cloudu.

[Podpora Azure](https://azure.microsoft.com/support/) pro problémy s JDK a funkce je k dispozici s aplikací [plán podpory kvalifikovaný](https://azure.microsoft.com/support/plans/).

## <a name="customize-jvm"></a>Přizpůsobení JVM

Funkce umožňuje přizpůsobit modul Java virtual machine (JVM) použitý ke spuštění funkcí v Javě. [Těchto možností JVM](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7) se používají ve výchozím nastavení:

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

Můžete zadat další argumenty v aplikaci nastavení s názvem `JAVA_OPTS`. Nastavení aplikace můžete přidat do aplikace function app nasadit do Azure na webu Azure portal nebo rozhraní příkazového řádku Azure.

### <a name="azure-portal"></a>portál Azure

V [webu Azure portal](https://portal.azure.com), použijte [kartu Nastavení aplikace](functions-how-to-use-azure-function-app-settings.md#settings) přidáte `JAVA_OPTS` nastavení.

### <a name="azure-cli"></a>Azure CLI

Můžete použít [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) příkazu nastavte `JAVA_OPTS`, jako v následujícím příkladu:

```azurecli-interactive
az functionapp config appsettings set --name <APP_NAME> \
--resource-group <RESOURCE_GROUP> \
--settings "JAVA_OPTS=-Djava.awt.headless=true"
```
Tento příklad povolí bezobslužného režimu. Nahraďte `<APP_NAME>` s názvem aplikace function App a `<RESOURCE_GROUP> ` s vybranou skupinou prostředků.

> [!WARNING]  
> V [plánu Consumption](functions-scale.md#consumption-plan), je nutné přidat `WEBSITE_USE_PLACEHOLDER` nastavení s hodnotou `0`.  
Toto nastavení zvýšit dobu úplné spuštění funkcí v Javě.

## <a name="third-party-libraries"></a>Knihovny třetích stran 

Služba Azure Functions podporuje použití knihovny třetích stran. Ve výchozím nastavení, zadané všechny závislosti ve vašem projektu `pom.xml` souboru jsou automaticky spojeny během [ `mvn package` ](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) cíle. Pro knihovny není stanoveno, v závislosti `pom.xml` souboru, umístit je do `lib` adresáře v kořenovém adresáři funkce. Závislosti umístěné v `lib` adresáře se přidají do třídy zavaděč systému v době běhu.

`com.microsoft.azure.functions:azure-functions-java-library` Závislostí je k dispozici na cestě ve výchozím nastavení a nemusí být součástí `lib` adresáře. Navíc [azure-functions-java-pracovní proces](https://github.com/Azure/azure-functions-java-worker) přidá závislostí uvedených [tady](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) do cesty pro třídy.

## <a name="data-type-support"></a>Podpora typ dat

Můžete použít prostý původní objekty Java (Object Pojo), typy definované v `azure-functions-java-library`, nebo primitivní datové typy, například řetězcové a celočíselné svázat vstupních nebo výstupních vazeb.

### <a name="pojos"></a>Objektů Pojo

Pro převod vstupní data na POJO, [azure-functions-java-pracovní proces](https://github.com/Azure/azure-functions-java-worker) používá [gson](https://github.com/google/gson) knihovny. Použít jako vstupy do funkce by měla být typy POJO `public`.

### <a name="binary-data"></a>Binární data

Vytvořit vazbu binárních vstupů nebo výstupů na `byte[]`, tak, že nastavíte `dataType` pole ve vašich function.json k `binary`:

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

Pokud očekáváte, že hodnoty null, použijte `Optional<T>`.

## <a name="bindings"></a>Vazby

Vstupní a výstupní vazby poskytují deklarativní způsob připojení k datům z vašeho kódu. Funkce můžete mít více vstupní a výstupní vazbu.

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

Vyvolání této funkce s žádostí HTTP. 
- Datová část požadavku HTTP je předán jako `String` argumentu `inputReq`.
- Jedna položka je načten z tabulky úložiště a je předán jako `TestInputData` na argument `inputData`.

Pro příjem batch vstupů, lze svázat `String[]`, `POJO[]`, `List<String>`, nebo `List<POJO>`.

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

Tato funkce se spustí vždycky, když je nakonfigurovaná eventhub nová data. Vzhledem k tomu, `cardinality` je nastavena na `MANY`, funkce přijímá dávky zprávy z centra událostí. `EventData` z událostí získá rozbočovači převeden na `TestEventData` pro spuštění funkce.

### <a name="output-binding-example"></a>Příklad výstupu vazby

Výstupní vazby můžete vázat na návratovou hodnotu s použitím `$return`. 

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

Můžete volat tuto funkci na požadavek protokolu HTTP. Zapíše více hodnot do fronty úložiště.

## <a name="httprequestmessage-and-httpresponsemessage"></a>Hodnota HttpRequestMessage a používá HttpResponseMessage

 Ty jsou definovány v `azure-functions-java-library`. Jsou pomocné rutiny typy pro práci s funkcí HttpTrigger.

| Speciálním typem      |       Target        | Typické použití                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    Trigger HTTP     | Získá metody, záhlaví a dotazy |
| `HttpResponseMessage` | Vazby výstupu protokolu HTTP | Vrátí stav než 200   |

## <a name="metadata"></a>Metadata

Několik triggerů odeslat [aktivovat metadat](/azure/azure-functions/functions-triggers-bindings) spolu se vstupní data. Můžete použít poznámku `@BindingName` vytvořit vazbu k aktivaci metadat.


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
V předchozím příkladu `queryValue` je vázán na parametru řetězce dotazu `name` v adrese URL žádosti http, `http://{example.host}/api/metadata?name=test`. Tady je další příklad ukazuje, jak vytvořit vazbu k `Id` z metadat aktivační událost fronty.

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
> Název zadaný v poznámce musí odpovídat vlastnosti metadat.

## <a name="execution-context"></a>Kontext spuštění

`ExecutionContext`, definované v `azure-functions-java-library`, obsahuje pomocné metody pro komunikaci s modul runtime služby functions.

### <a name="logger"></a>Protokolovací nástroj

Použití `getLogger`definované v `ExecutionContext`, zápis protokolů z kódu funkce.

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

Tady je postup pro konfiguraci vaší aplikace function app k zápisu protokolování aplikací pomocí rozhraní příkazového řádku Azure:

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Datový proud stream uložit výstup protokolování pro vaši aplikaci function app pomocí rozhraní příkazového řádku Azure, otevřete nový příkazový řádek, prostředí Bash nebo relaci Terminálové služby a zadejte následující příkaz:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
[Az webapp log tail](/cli/azure/webapp/log) příkaz má možnosti filtrování výstupu pomocí `--provider` možnost. 

Stáhnete soubory protokolů pomocí Azure CLI jako jednoho souboru ZIP, otevřete nový příkazový řádek, prostředí Bash nebo relaci Terminálové služby a zadejte následující příkaz:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

Musí mít povolené systému souborů přihlášení na webu Azure portal nebo rozhraní příkazového řádku Azure před spuštěním tohoto příkazu.

## <a name="environment-variables"></a>Proměnné prostředí

Ve službě Functions [nastavení aplikace](https://docs.microsoft.com/azure/azure-functions/functions-app-settings), jako je například připojení služby jsou řetězce, zveřejní jako proměnné prostředí během provádění. Tato nastavení přístupné prostřednictvím, `System.getenv("AzureWebJobsStorage")`.

Například můžete přidat [AppSetting](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings), s názvem `testAppSetting` a hodnota `testAppSettingValue`:

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("testAppSetting "+ System.getenv("testAppSettingValue"));
        return String.format(req);
    }
}

```

## <a name="next-steps"></a>Další postup

Další informace o vývoji pro Azure Functions v Javě naleznete na následujících odkazech:

* [Osvědčené postupy pro službu Azure Functions](functions-best-practices.md)
* [Referenční informace pro vývojáře Azure Functions](functions-reference.md)
* [Azure Functions aktivačními událostmi a vazbami](functions-triggers-bindings.md)
* Místní vývoj a ladění pomocí [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md), a [Eclipse](functions-create-maven-eclipse.md)
* [Vzdálené ladění v Javě v Azure Functions ve Visual Studiu Code](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Modul plug-in maven pro Azure Functions](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* Zjednodušení vytváření funkce až `azure-functions:add` cíle a připravit pracovní adresář pro [nasazení souboru ZIP](deployment-zip-push.md).
