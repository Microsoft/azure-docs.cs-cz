---
title: Referenční informace pro vývojáře Java pro Azure Functions | Microsoft Docs
description: Pochopit, jak vyvíjet funkce s Java.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: Azure funkce, funkce, událostí zpracování, webhooků, dynamické výpočetní, bez serveru architektura, java
ms.service: functions
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/07/2017
ms.author: routlaw
ms.openlocfilehash: 71576e65d20d7e8cb7f5ff1c5f19c82439bb6807
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="azure-functions-java-developer-guide"></a>Příručka vývojáře Azure funkce Java
> [!div class="op_single_selector"]
[!INCLUDE [functions-selector-languages](../../includes/functions-selector-languages.md)]

## <a name="programming-model"></a>Programovací model 

Funkce Azure by měl být bezstavové třída metodu, která zpracovává vstup a výstup. I když budou moci zapsat metody instance, funkce nesmí být závislá na všechna pole instance třídy. Musí mít všechny funkce metody `public` – modifikátor přístupu.

## <a name="triggers-and-annotations"></a>Aktivační události a poznámky

Obvykle Azure funkce je volána z důvodu externí aktivační události. Funkce je potřeba zpracovat této aktivační události a jeho přidružené vstupy a vytvořit jeden nebo více výstupů.

Java poznámky jsou součástí `azure-functions-java-core` balíček, který chcete vytvořit vazbu vstup a výstupy vaše metody. Podporované vstupní triggerů a výstup vazby poznámky jsou zahrnuty v následující tabulce:

Vazba | Poznámka
---|---
CosmosDB | neuvedeno
HTTP | <ul><li>`HttpTrigger`</li><li>`HttpOutput`</li></ul>
Mobile Apps | neuvedeno
Notification Hubs | neuvedeno
Objekt Blob úložiště | <ul><li>`BlobTrigger`</li><li>`BlobInput`</li><li>`BlobOutput`</li></ul>
Fronta úložiště | <ul><li>`QueueTrigger`</li><li>`QueueOutput`</li></ul>
Tabulka úložiště | <ul><li>`TableInput`</li><li>`TableOutput`</li></ul>
Časovač | <ul><li>`TimerTrigger`</li></ul>
Twilio | neuvedeno

Aktivační událost vstupy a výstupy lze také definovat v [function.json](/azure/azure-functions/functions-reference#function-code) pro vaši aplikaci.

> [!IMPORTANT] 
> Je nutné nakonfigurovat účet úložiště Azure ve vaší [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) úlohu v Azure Storage Blob, fronty nebo tabulky aktivační události.

Příklad použití poznámek:

```java
import com.microsoft.azure.serverless.functions.annotation.HttpTrigger;
import com.microsoft.azure.serverless.functions.ExecutionContext;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

Stejnou funkci zapsán bez poznámky:

```java
package com.example;

public class MyClass {
    public static String echo(String in) {
        return in;
    }
}
```

s odpovídajícím `function.json`:

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
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

## <a name="data-types"></a>Typy dat

Můžete použít všechny datové typy v jazyce Java pro vstupní a výstupní data, včetně nativních typech; Přizpůsobit typy Java a specializované Azure typy definované v `azure-functions-java-core` balíčku. Funkce Azure, které modul runtime pokusí převést vstupní přijatých do typu požadoval kódu.

### <a name="strings"></a>Řetězce

Hodnoty předané do metod funkce bude přetypovat na řetězce, pokud odpovídající typ vstupního parametru pro funkci je `String`. 

### <a name="plain-old-java-objects-pojos"></a>Nešifrovaná staré objekty Java (Pojo)

Řetězce formátu s JSON bude přetypovat na typy jazyka Java, pokud vstup funkce metoda očekává typu Java. Tento převod umožňuje předat JSON vstupy do funkce a pracovat s typy Java ve vašem kódu bez nutnosti implementovat převod v kódu.

Použít jako vstupy do funkce musí stejné typy POJO `public` – modifikátor přístupu jako funkce metody jsou používány v. Nemáte deklarovat POJO třída pole `public`. Například řetězec formátu JSON `{ "x": 3 }` je možné převést na typ následující POJO:

```Java
public class MyData {
    private int x;
}
```

### <a name="binary-data"></a>Binární data

Binární data jsou reprezentována jako `byte[]` ve vašem kódu Azure functions. Svázat binární vstupů nebo výstupů funkcí nastavením `dataType` pole vaší function.json k `binary`:

```json
 {
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "blob",
      "name": "content",
      "direction": "in",
      "dataType": "binary",
      "path": "container/myfile.bin",
      "connection": "ExampleStorageAccount"
    },
  ]
}
```

Potom ji použijte v kódu funkce:

```java
// Class definition and imports are omitted here
public static String echoLength(byte[] content) {
}
```

Použití `OutputBinding<byte[]>` typu, aby vazba binární výstup.


## <a name="function-method-overloading"></a>Metoda přetížení funkce

Je povoleno přetížení funkce metod se stejným názvem, ale s různými typy. Například můžete mít obě `String echo(String s)` a `String echo(MyType s)` v jedné třídy a funkce Azure runtime rozhodne, kterého vystavitele si mají vyvolat, zkontrolujte vstupní skutečný typ (pro HTTP vstupní, typ MIME `text/plain` vede k `String` při `application/json` představuje `MyType`).

## <a name="inputs"></a>Vstupy

Vstup dělí do dvou kategorií v Azure Functions: jeden vstup aktivační události a dalších je další vstup. I když jsou v různých `function.json`, využití je stejný jako v kódu v jazyce Java. Jako příklad Podívejme následující fragment kódu:

```java
package com.example;

import com.microsoft.azure.serverless.functions.annotation.BindingName;
import java.util.Optional;

public class MyClass {
    public static String echo(Optional<String> in, @BindingName("item") MyObject obj) {
        return "Hello, " + in.orElse("Azure") + " and " + obj.getKey() + ".";
    }

    private static class MyObject {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
}
```

`@BindingName` Přijímá poznámky `String` vlastnost, která představuje název vazby aktivační události definované v `function.json`:

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "put" ],
      "route": "items/{id}"
    },
    {
      "type": "table",
      "name": "item",
      "direction": "in",
      "tableName": "items",
      "partitionKey": "Example",
      "rowKey": "{id}",
      "connection": "ExampleStorageAccount"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}
```

Takže když tato funkce je volána, datová část požadavku HTTP předá volitelný `String` argument `in` a Azure Table Storage `MyObject` typu předaný argument `obj`. Použití `Optional<T>` typ zpracování vstupy do vaší funkce, které může mít hodnotu null.

## <a name="outputs"></a>Výstupy

Výstupy může být vyjádřený v návratovou hodnotu nebo výstupní parametry. Pokud existuje jenom jeden výstup, doporučuje se použít návratovou hodnotu. Pro několik výstupů budete muset použít výstupní parametry.

Vrácená hodnota je nejjednodušší formu výstupu, právě vrátí hodnotu typu a modulu runtime Azure Functions se pokusí zařazování zpět na skutečný typ (například odpovědi HTTP). V `functions.json`, použijete `$return` jako název výstupu vazby.

Chcete-li vytvořit více hodnot výstup, použijte `OutputBinding<T>` typem definovaným v `azure-functions-java-core` balíčku. Pokud potřebujete provést odpovědi HTTP a odešlete zprávu do fronty i, můžete napsat něco podobného jako:

```java
package com.example;

import com.microsoft.azure.serverless.functions.OutputBinding;
import com.microsoft.azure.serverless.functions.annotation.BindingName;

public class MyClass {
    public static String echo(String body, 
    @QueueOutput(queueName = "messages", connection = "AzureWebJobsStorage", name = "queue") OutputBinding<String> queue) {
        String result = "Hello, " + body + ".";
        queue.setValue(result);
        return result;
    }
}
```

které měli definovat vazby výstup v `function.json`:

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "queue",
      "name": "queue",
      "direction": "out",
      "queueName": "messages",
      "connection": "AzureWebJobsStorage"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}
```
## <a name="specialized-types"></a>Speciální typy

Někdy funkce musí mít podrobnou kontrolu nad vstupy a výstupy. Typy v specializuje `azure-functions-java-core` balíček jsou k dispozici pro manipulaci s informace o požadavku a přizpůsobit návratový stav triggeru protokolu HTTP:

| Speciálním typem      |       Cíl        | Typická využití                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    Aktivace protokolu HTTP     | Získat metoda, hlavičky nebo dotazy |
| `HttpResponseMessage<T>` | Vazba výstupu protokolu HTTP | Návratový stav než 200   |

> [!NOTE] 
> Můžete také použít `@BindingName` poznámky hlavičky protokolu HTTP a dotazy. Například `@BindingName("name") String query` opakuje hlavičky požadavků HTTP a dotazy a předat tuto hodnotu do metody. Například `query` bude `"test"` Pokud je adresa URL požadavku `http://example.org/api/echo?name=test`.

### <a name="metadata"></a>Metadata

Metadata pocházejí z různých zdrojů, jako hlavičky protokolu HTTP, HTTP dotazy a [aktivovat metadata](/azure/azure-functions/functions-triggers-bindings#trigger-metadata-properties). Použití `@BindingName` poznámky společně s názvem metadata k získání hodnoty.

Například `queryValue` v následujícím kódu fragment bude `"test"` Pokud požadovaná adresa URL je `http://{example.host}/api/metadata?name=test`.

```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.serverless.functions.annotation.*;

public class MyClass {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { "get", "post" }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```

## <a name="functions-execution-context"></a>Kontext spuštění funkce

Komunikovat s Azure Functions prostředí pro spuštění prostřednictvím `ExecutionContext` objekt definovaný v `azure-functions-java-core` balíčku. Použít `ExecutionContext` objekt, který chcete použít volání informace a informace o běhu programu funkce ve vašem kódu.

### <a name="logging"></a>Protokolování

Přístup k modulu runtime protokolovač funkce je k dispozici prostřednictvím `ExecutionContext` objektu. Tohoto protokolovacího nástroje je vázaný na Azure monitorování a umožňuje příznak upozornění a chyb zjištěných při provádění funkce.

Následující příklad kódu, zaprotokoluje upozornění při textu žádosti přijaté je prázdný.

```java
import com.microsoft.azure.serverless.functions.annotation.HttpTrigger;
import com.microsoft.azure.serverless.functions.ExecutionContext;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="environment-variables"></a>Proměnné prostředí

Je často žádoucí extrahovat tajné informace ze zdrojového kódu z bezpečnostních důvodů. To umožňuje kód publikování zdrojového kódu úložiště bez omylem zadání přihlašovacích údajů k jinými vývojáři. Toho lze dosáhnout jednoduše tak, že použití proměnných prostředí, jak při místním spuštění Azure Functions tak při nasazení funkce v Azure.

Snadno nastavit proměnné prostředí při spuštění Azure Functions místně, můžete přidat do souboru local.settings.json tyto proměnné. Pokud není přítomen v kořenovém adresáři projektu funkce, klidně si ji vytvořit. Zde je, jak by měla vypadat souboru:

```xml
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "AzureWebJobsDashboard": ""
  }
}
```

Každý klíč / hodnota mapování `values` mapy bude k dispozici v době běhu jako proměnné prostředí, přístupný pro volání `System.getenv("<keyname>")`, například `System.getenv("AzureWebJobsStorage")`. Přidání další klíč / hodnota dvojice je přijatá a doporučená praxe.

> [!NOTE]
> Pokud je zvolen tento přístup, být opravdu zvážit, zda local.settings.json přidání souboru do úložiště souboru, tak, aby ignoroval není potvrzená.

Váš kód teď v závislosti na těchto proměnných prostředí vám umožní přihlásit k portálu Azure nastavte stejný klíč / hodnota dvojice v nastavení aplikace – funkce tak, aby váš kód funkce ekvivalentně při testování místně a při nasazení do Azure.

## <a name="next-steps"></a>Další postup
Další informace najdete v následujících materiálech:

* [Osvědčené postupy pro službu Azure Functions](functions-best-practices.md)
* [Referenční informace pro vývojáře Azure Functions](functions-reference.md)
* [Azure funkce triggerů a vazeb](functions-triggers-bindings.md)
* [Vzdálené ladění Java Azure Functions pomocí kódu v sadě Visual Studio](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
