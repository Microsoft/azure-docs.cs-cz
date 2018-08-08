---
title: Referenční informace pro vývojáře Java pro službu Azure Functions | Dokumentace Microsoftu
description: Naučte se vyvíjet funkce pomocí Javy.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: Azure functions, functions, zpracování událostí, webhook, dynamické výpočty, architektura bez serveru, java
ms.service: functions
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/07/2017
ms.author: routlaw
ms.openlocfilehash: 65964372cf2a0aa42be967f7c93749c58a9f56dd
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621765"
---
# <a name="azure-functions-java-developer-guide"></a>Příručka pro vývojáře Azure Functions Java

[!INCLUDE [functions-java-preview-note](../../includes/functions-java-preview-note.md)]

## <a name="programming-model"></a>Programovací model 

Funkce Azure by měl být metoda bezstavové třídy, která zpracovává vstup a výstup. I když jsou povolené pro zápis metody instance, nesmí funkce závisí na všechna pole instancí třídy. Všechny funkce metody musí mít `public` modifikátor přístupu.

## <a name="triggers-and-annotations"></a>Aktivační události a poznámky

Obvykle je vyvolána funkce Azure z důvodu externí aktivační události. Funkce je potřeba zpracovat triggerem a jeho přidružené vstupů a vygenerovat jeden nebo více výstupů.

Java poznámky jsou součástí `azure-functions-java-core` balíček, který chcete svázat vaše metody vstup a výstup. Podporovaná vstupní aktivační události a výstupní vazby poznámky jsou zahrnuty v následující tabulce:

Vazba | Poznámka
---|---
CosmosDB | neuvedeno
HTTP | <ul><li>`HttpTrigger`</li><li>`HttpOutput`</li></ul>
Mobile Apps | neuvedeno
Notification Hubs | neuvedeno
Storage Blob | <ul><li>`BlobTrigger`</li><li>`BlobInput`</li><li>`BlobOutput`</li></ul>
Fronta úložiště | <ul><li>`QueueTrigger`</li><li>`QueueOutput`</li></ul>
Storage Table | <ul><li>`TableInput`</li><li>`TableOutput`</li></ul>
Časovač | <ul><li>`TimerTrigger`</li></ul>
Twilio | neuvedeno

Aktivační událost vstupy a výstupy můžete také definovat v [function.json](/azure/azure-functions/functions-reference#function-code) pro vaši aplikaci.

> [!IMPORTANT] 
> Musíte nakonfigurovat účet služby Azure Storage ve vašich [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) pro místní spuštění aktivační události Azure Storage Blob, fronty nebo tabulky.

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

Stejnou funkci zapsat bez poznámek:

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

Můžete libovolně udělovat použít datové typy v jazyce Java pro vstupní a výstupní data, včetně nativních typů; Přizpůsobit typy jazyka Java a specializované Azure typy definované v `azure-functions-java-core` balíčku. Azure Functions, které modul runtime pokusí převést vstupní přijatých do typ požadovaný ve vašem kódu.

### <a name="strings"></a>Řetězce

Hodnoty předané do metod funkce bude na řetězce převedeny, pokud je odpovídající typ vstupního parametru funkce typu `String`. 

### <a name="plain-old-java-objects-pojos"></a>Prostý původní objekty Java (Object Pojo)

Řetězce ve formátu s JSON bude přetypovat na typy jazyka Java, pokud vstupní metodu funkce očekává, že tento typ Java. Tento převod umožňuje předat JSON vstupy do vašich funkcí a práci s typy jazyka Java v kódu bez nutnosti provádět převod ve svém vlastním kódu.

Použít jako vstupy do funkce musíte stejné typy POJO `public` modifikátor přístupu jako funkce metody se používají v. Není nutné deklarovat pole třídy POJO `public`. Například řetězec formátu JSON `{ "x": 3 }` může být převeden na typ následující POJO:

```Java
public class MyData {
    private int x;
}
```

### <a name="binary-data"></a>Binární data

Binární data je vyjádřena jako `byte[]` ve vašem kódu Azure functions. Vytvořit vazbu binárních vstupů nebo výstupů na vaší funkce tak, že nastavíte `dataType` pole ve vašich function.json k `binary`:

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

Použití `OutputBinding<byte[]>` typu, aby binární výstupní vazbu.


## <a name="function-method-overloading"></a>Metoda přetížení funkce

Jste oprávněni přetížení funkce metod se stejným názvem, ale s různými typy. Například můžete mít obojí `String echo(String s)` a `String echo(MyType s)` v jedné třídy a Azure Functions runtime rozhodne, který z nich se vyvolat, zkontrolujte skutečný typ vstupu (pro HTTP vstupní, typ MIME `text/plain` vede k `String` při `application/json` představuje `MyType`).

## <a name="inputs"></a>Vstupy

Vstup dělí do dvou kategorií ve službě Azure Functions: jeden je vstup triggeru a druhý je další vstupy. I když jsou v různých `function.json`, využití je stejný jako v kódu v Javě. Pojďme se například následující fragment kódu:

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

Proto po vyvolání tato funkce je datová část požadavku HTTP předává volitelně `String` argument `in` a Azure Table Storage `MyObject` typ předaný argument `obj`. Použití `Optional<T>` typ zpracování vstupy do funkce, které může mít hodnotu null.

## <a name="outputs"></a>Výstupy

Výstupy může být vyjádřena v návratovou hodnotu nebo výstupní parametry. Pokud existuje jenom jeden výstup, doporučuje se používat návratovou hodnotu. Pro několik výstupů budete muset použít výstupní parametry.

Vrácená hodnota je nejjednodušší formu výstupu, právě vrátit hodnotu libovolného typu a modul runtime služby Azure Functions se pokusí zařazování zpět do aplikace skutečný typ (například odpověď HTTP). V `functions.json`, použijete `$return` jako název výstupní vazbu.

Chcete-li vytvořit více výstupní hodnoty, použijte `OutputBinding<T>` typ definovaný v `azure-functions-java-core` balíčku. Pokud je potřeba vytvořit odpověď HTTP a vložit zprávu do fronty i, můžete napsat vypadat:

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

který by měl definovat výstupní vazbu v `function.json`:

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

Někdy funkce musí mít podrobnou kontrolu nad vstupy a výstupy. Speciální typy v `azure-functions-java-core` balíček jsou k dispozici pro manipulaci s informace o požadavku a přizpůsobit návratový stav triggeru HTTP:

| Speciálním typem      |       Cíl        | Typické použití                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    HTTP Trigger     | Metoda, záhlaví a dotazy |
| `HttpResponseMessage<T>` | Vazby výstupu protokolu HTTP | Než 200 návratový stav   |

> [!NOTE] 
> Můžete také použít `@BindingName` poznámky k získání hlavičky protokolu HTTP a dotazy. Například `@BindingName("name") String query` iteruje hlavičky požadavků HTTP a dotazy a předat metodě tuto hodnotu. Například `query` bude `"test"` Pokud je adresa URL požadavku `http://example.org/api/echo?name=test`.

### <a name="metadata"></a>Metadata

Metadata pocházejí z různých zdrojů, jako jsou hlavičky protokolu HTTP, HTTP dotazy a [aktivovat metadat](/azure/azure-functions/functions-triggers-bindings#trigger-metadata-properties). Použití `@BindingName` poznámky a metadata název má být získána hodnota.

Například `queryValue` v následujícím kódu je fragment kódu bude `"test"` Pokud je požadoaná adresa URL `http://{example.host}/api/metadata?name=test`.

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

Budete moct používat prostředí pro spouštění Azure Functions prostřednictvím `ExecutionContext` definované v objektu `azure-functions-java-core` balíčku. Použít `ExecutionContext` objektu, který chcete použít volání informace a informace o modulu runtime functions ve vašem kódu.

### <a name="logging"></a>Protokolování

Přístup k protokolovači modul runtime Functions je k dispozici prostřednictvím `ExecutionContext` objektu. Tento protokolovač se váže na Azure monitor a umožňuje příznak upozornění a chyb zjištěných při provádění funkce.

Následující příklad kódu, zaprotokoluje upozornění při přijetí textu požadavku je prázdný.

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

Je často žádoucí extrakci ven tajné informace ze zdrojového kódu z bezpečnostních důvodů. To umožňuje kód, který chcete publikovat do úložišť zdrojového kódu bez náhodně zadání přihlašovacích údajů s ostatními vývojáři. Toho lze dosáhnout jednoduše tak, že pomocí proměnné prostředí, při místním spuštění Azure Functions a při nasazení vašich funkcí v Azure.

Snadno nastavit proměnné prostředí při spuštění Azure Functions místně, můžete přidat do souboru local.settings.json těchto proměnných. Pokud není k dispozici v kořenovém adresáři vašeho projektu funkce, můžete ho vytvořit. Tady je soubor by měl vypadat:

```xml
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "AzureWebJobsDashboard": ""
  }
}
```

Každý klíč / hodnota v mapování `values` mapa bude k dispozici za běhu jako proměnné prostředí, přístupné pomocí volání `System.getenv("<keyname>")`, například `System.getenv("AzureWebJobsStorage")`. Přidání další klíč / hodnota dvojice je přijato a doporučené postupy.

> [!NOTE]
> Pokud tento přístup se používá, se opravdu vzít v úvahu, jestli local.settings.json přidáte soubor do úložiště ignorovat soubor, tak, že není potvrzená.

S vaším kódem teď v závislosti na tyto proměnné prostředí můžete přihlásit na webu Azure Portal pro stejný klíč / hodnota dvojice v vaše nastavení aplikace function app, tak, aby váš kód funguje ekvivalentně při testování místně a při nasazení do Azure.

## <a name="next-steps"></a>Další postup
Další informace najdete v následujících materiálech:

* [Osvědčené postupy pro službu Azure Functions](functions-best-practices.md)
* [Referenční informace pro vývojáře Azure Functions](functions-reference.md)
* [Azure Functions aktivačními událostmi a vazbami](functions-triggers-bindings.md)
* [Vzdálené ladění v Javě v Azure Functions ve Visual Studiu Code](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
