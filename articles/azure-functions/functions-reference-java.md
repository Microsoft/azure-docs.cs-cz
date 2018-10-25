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
ms.openlocfilehash: 423661b8a459abf0b3028da92d6fd3ec885bb2c9
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50025018"
---
# <a name="azure-functions-java-developer-guide"></a>Příručka pro vývojáře Azure Functions Java

[!INCLUDE [functions-java-preview-note](../../includes/functions-java-preview-note.md)]

## <a name="programming-model"></a>Programovací model 

Funkce Azure by měl být metoda bezstavové třídy, která zpracovává vstup a výstup. I když můžete napsat metody instance, nesmí funkce závisí na všechna pole instancí třídy. Všechny funkce metody musí mít `public` modifikátor přístupu.

## <a name="folder-structure"></a>struktura složek

Struktura složek projektu jazyka Java vypadá takto:

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

Použití anotací Java součástí [com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) balíček, který chcete svázat vaše metody vstup a výstup. Ukázkový kód pomocí poznámky je k dispozici v [referenční dokumentace jazyka Java](/java/api/com.microsoft.azure.functions.annotation) pro jednotlivé poznámky a ve službě Azure Functions vazby referenční dokumentaci, jako je třeba pro [triggerů HTTP](/azure/azure-functions/functions-bindings-http-webhook).

Aktivační událost vstupy a výstupy můžete také definovat v [function.json](/azure/azure-functions/functions-reference#function-code) funkce namísto prostřednictvím anotací. Pomocí `function.json` místo poznámky tímto způsobem se nedoporučuje.

> [!IMPORTANT] 
> Musíte nakonfigurovat účet služby Azure Storage ve vašich [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) pro místní spuštění aktivační události Azure Storage Blob, fronty nebo tabulky.

Příklad použití poznámek:

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
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

## <a name="jdk-runtime-availability-and-support"></a>JDK běhovou dostupnost a podpora 

Stáhnout a použít [Azul Zulu pro Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) JDK od [Azul Systems](https://www.azul.com/downloads/azure-only/zulu/) pro místní vývoj aplikací v Javě funkce. JDK jsou k dispozici pro Windows, Linux a macOS a [podpory Azure](https://support.microsoft.com/en-us/help/4026305/sql-contact-microsoft-azure-support) je k dispozici pro problémů při vývoji se [plán podpory kvalifikovaný](https://azure.microsoft.com/support/plans/).

## <a name="third-party-libraries"></a>Knihovny třetích stran 

Služba Azure Functions podporuje použití knihovny třetích stran. Ve výchozím nastavení, zadané všechny závislosti ve vašem projektu `pom.xml` souboru budou automaticky seskupeny během `mvn package` cíle. Pro knihovny není stanoveno, v závislosti `pom.xml` souboru, umístit je do `lib` adresáře v kořenovém adresáři funkce. Závislosti umístěné v `lib` adresáře se přidají do třídy zavaděč systému v době běhu.

`com.microsoft.azure.functions:azure-functions-java-library` Závislostí je k dispozici na cestě ve výchozím nastavení a nemusí být součástí `lib` adresáře.

## <a name="data-type-support"></a>Podpora typ dat

Můžete použít všechny datové typy v jazyce Java pro vstupní a výstupní data, včetně nativních typů; Přizpůsobit typy jazyka Java a specializované Azure typy definované v `azure-functions-java-library` balíčku. Azure Functions, které modul runtime pokusí převést vstupní přijatých do typ požadovaný ve vašem kódu.

### <a name="strings"></a>Řetězce

Hodnoty předané do metod funkce bude na řetězce převedeny, pokud je odpovídající typ vstupního parametru funkce typu `String`. 

### <a name="plain-old-java-objects-pojos"></a>Prostý původní objekty Java (Object Pojo)

Řetězce ve formátu s JSON bude přetypovat na typy jazyka Java, pokud podpis zadání funkce očekává, že tento typ Java. Tento převod umožňuje předat ve formátu JSON a práci s typy jazyka Java.

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

Může být prázdný vstupní hodnoty `null` jako argument funkce, ale doporučený způsob, jak řešit potenciální prázdné hodnoty, je použít `Optional<T>`.


## <a name="function-method-overloading"></a>Metoda přetížení funkce

Jste oprávněni přetížení funkce metod se stejným názvem, ale s různými typy. Například můžete mít obojí `String echo(String s)` a `String echo(MyType s)` ve třídě. Služba Azure Functions rozhodne, která metoda se má vyvolat na základě vstupního typu (pro HTTP vstupní, typ MIME `text/plain` vede k `String` při `application/json` představuje `MyType`).

## <a name="inputs"></a>Vstupy

Vstup dělí do dvou kategorií ve službě Azure Functions: jeden je vstup triggeru a druhý je další vstupy. I když jsou v různých `function.json`, využití je stejný jako v kódu v Javě. Pojďme se například následující fragment kódu:

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class MyClass {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { "put" }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String in,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") MyObject obj
    ) {
        return "Hello, " + in + " and " + obj.getKey() + ".";
    }

    public static class MyObject {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
}
```

Když se aktivuje tuto funkci, požadavek HTTP je předán funkci podle `String in`. Načte položku z Azure Table Storage na základě ID v adrese URL trasy a k dispozici jako `obj` v těle funkce.

## <a name="outputs"></a>Výstupy

Výstupy může být vyjádřena v návratovou hodnotu nebo výstupní parametry. Pokud existuje jenom jeden výstup, doporučuje se používat návratovou hodnotu. Pro několik výstupů budete muset použít výstupní parametry.

Vrácená hodnota je nejjednodušší formu výstupu, právě vrátit hodnotu libovolného typu a modul runtime služby Azure Functions se pokusí zařazování zpět do aplikace skutečný typ (například odpověď HTTP).  Můžete použít výstup poznámek k metodě – funkce (název vlastnosti anotace musí být $return) k definování výstupní návratovou hodnotu.

Chcete-li vytvořit více výstupní hodnoty, použijte `OutputBinding<T>` typ definovaný v `azure-functions-java-library` balíčku. Pokud je potřeba vytvořit odpověď HTTP a vložit zprávu do fronty i, můžete napsat vypadat:

Obsah objektu blob kopírování funkce například může být definován jako následující kód. `@StorageAccount` Poznámka se tady používá při prevenci duplikování vlastnosti připojení pro obě `@BlobTrigger` a `@BlobOutput`.

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class MyClass {
    @FunctionName("copy")
    @StorageAccount("AzureWebJobsStorage")
    @BlobOutput(name = "$return", path = "samples-output-java/{name}")
    public static String copy(@BlobTrigger(name = "blob", path = "samples-input-java/{name}") String content) {
        return content;
    }
}
```

Použít `OutputBinding<byte[]`> aby binární výstup hodnotu (pro parametry); návratové hodnoty, použijte `byte[]`.

## <a name="specialized-types"></a>Speciální typy

Někdy funkce musí mít podrobnou kontrolu nad vstupy a výstupy. Speciální typy v `azure-functions-java-core` balíček jsou k dispozici pro manipulaci s informace o požadavku a přizpůsobit návratový stav triggeru HTTP:

| Speciálním typem      |       Cíl        | Typické použití                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    Trigger HTTP     | Metoda, záhlaví a dotazy |
| `HttpResponseMessage<T>` | Vazby výstupu protokolu HTTP | Než 200 návratový stav   |

> [!NOTE] 
> Můžete také použít `@BindingName` poznámky k získání hlavičky protokolu HTTP a dotazy. Například `@BindingName("name") String query` iteruje hlavičky požadavků HTTP a dotazy a předat metodě tuto hodnotu. Například `query` bude `"test"` Pokud je adresa URL požadavku `http://example.org/api/echo?name=test`.

### <a name="metadata"></a>Metadata

Metadata pocházejí z různých zdrojů, jako jsou hlavičky protokolu HTTP, HTTP dotazy a [aktivovat metadat](/azure/azure-functions/functions-triggers-bindings#trigger-metadata-properties). Použití `@BindingName` poznámky a metadata název má být získána hodnota.

Například `queryValue` v následujícím kódu je fragment kódu bude `"test"` Pokud je požadoaná adresa URL `http://{example.host}/api/metadata?name=test`.

```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


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

## <a name="execution-context"></a>Kontext spuštění

Pracovat prostřednictvím prostředí pro spouštění Azure Functions `ExecutionContext` definované v objektu `azure-functions-java-library` balíčku. Použít `ExecutionContext` objektu, který chcete použít volání informace a informace o modulu runtime functions ve vašem kódu.

### <a name="custom-logging"></a>Vlastní protokolování

Přístup k protokolovači modul runtime Functions je k dispozici prostřednictvím `ExecutionContext` objektu. Tento protokolovač se váže na Azure monitor a umožňuje příznak upozornění a chyb zjištěných při provádění funkce.

Následující příklad kódu, zaprotokoluje upozornění při přijetí textu požadavku je prázdný.

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

Můžete použít rozhraní příkazového řádku Azure pro datový proud Java standardní navýšení kapacity a protokolování chyb, jakož i další protokolování aplikací. Nejprve nakonfigurujte aplikaci funkce pro zápis protokolování aplikací pomocí Azure CLI:

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

Zachovejte tajné informace, jako je například klíčů nebo tokenů mimo váš zdrojový kód z bezpečnostních důvodů. Pomocí klávesy a tokeny v kódu funkce čtení z proměnných prostředí.

Chcete-li nastavit proměnné prostředí při spuštění Azure Functions místně, můžete přidat do souboru local.settings.json tyto proměnné. Pokud není k dispozici v kořenovém adresáři vašeho projektu funkce, můžete jeden vytvořit. Tady je soubor by měl vypadat:

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
> Pokud tento přístup se používá, se opravdu local.settings.json přidáte soubor do úložiště ignorovat soubor, tak, že není potvrzená.

S vaším kódem teď v závislosti na tyto proměnné prostředí se můžete přihlásit na webu Azure portal nastavit stejný klíč / hodnota dvojice v vaše nastavení aplikace function app, tak, aby váš kód funguje ekvivalentně při testování místně a při nasazení do Azure.

## <a name="next-steps"></a>Další postup

Další informace o vývoji pro Azure funkce v Javě najdete v článku na následujících odkazech:

* [Osvědčené postupy pro službu Azure Functions](functions-best-practices.md)
* [Referenční informace pro vývojáře Azure Functions](functions-reference.md)
* [Azure Functions aktivačními událostmi a vazbami](functions-triggers-bindings.md)
- Místní vývoj a ladění pomocí [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md), a [Eclipse](functions-create-maven-eclipse.md). 
* [Vzdálené ladění v Javě v Azure Functions ve Visual Studiu Code](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Modul plug-in maven pro Azure Functions](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) – zjednodušení vytváření funkce až `azure-functions:add` cíle a připravit pracovní adresář pro [nasazení souboru ZIP](deployment-zip-push.md).
