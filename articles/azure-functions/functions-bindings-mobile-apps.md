---
title: Vazby Mobile Apps pro službu Azure Functions
description: Naučte se používat Azure Mobile Apps vazby ve službě Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: Azure functions, funkce, zpracování událostí, dynamické výpočty, architektura bez serveru
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 5fd220f15f363c1987f1576009519e4b2feae6b9
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55814856"
---
# <a name="mobile-apps-bindings-for-azure-functions"></a>Vazby Mobile Apps pro službu Azure Functions 

> [!NOTE]
> Azure Mobile Apps vazby jsou dostupné jenom pro Azure Functions 1.x. Nejsou podporované ve službě Azure Functions 2.x.

Tento článek vysvětluje, jak pracovat s [Azure Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) vazby ve službě Azure Functions. Azure Functions podporuje vstupní a výstupní vazby pro Mobile Apps.

Vazby Mobile Apps vám umožní číst a aktualizovat tabulky dat v mobilních aplikacích.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Balíčky – funkce 1.x

Vazby Mobile Apps jsou součástí [Microsoft.Azure.WebJobs.Extensions.MobileApps](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps) balíčku NuGet, verzi 1.x. Zdrojový kód pro tento balíček je v [azure webjobs sdk rozšíření](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.MobileApps/) úložiště GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="input"></a>Vstup

Vstupní vazby Mobile Apps načte záznam z koncového bodu mobilní tabulky a předává je do vaší funkce. V C# a F# funkce, všechny změny provedené v záznamu jsou automaticky odeslány zpět k tabulce při ukončení funkce úspěšně.

## <a name="input---example"></a>Input – příklad

Podívejte se na příklad specifické pro jazyk:

* [C# skript (.csx)](#input---c-script-example)
* JavaScript

### <a name="input---c-script-example"></a>(Vstup) – příklad skriptu jazyka C#

Následující příklad ukazuje vstupní vazby Mobile Apps v *function.json* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) , který používá vazba. Funkce aktivované zpráv fronty, která má identifikátor záznamu. Funkce přečte zadaný záznam a změní jeho `Text` vlastnost.

Zde je vazba dat v *function.json* souboru:

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "id" : "{queueTrigger}",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "in"
    }
]
}
```
[Konfigurace](#input---configuration) bodu vysvětluje tyto vlastnosti.

Tady je kód skriptu jazyka C#:

```cs
#r "Newtonsoft.Json"    
using Newtonsoft.Json.Linq;

public static void Run(string myQueueItem, JObject record)
{
    if (record != null)
    {
        record["Text"] = "This has changed.";
    }    
}
```

### <a name="input---javascript"></a>(Vstup) – JavaScript

Následující příklad ukazuje vstupní vazby Mobile Apps v *function.json* souboru a [funkce jazyka JavaScript](functions-reference-node.md) , který používá vazba. Funkce aktivované zpráv fronty, která má identifikátor záznamu. Funkce přečte zadaný záznam a změní jeho `Text` vlastnost.

Zde je vazba dat v *function.json* souboru:

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "id" : "{queueTrigger}",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "in"
    }
]
}
```
[Konfigurace](#input---configuration) bodu vysvětluje tyto vlastnosti.

Tady je kód jazyka JavaScript:

```javascript
module.exports = function (context, myQueueItem) {    
    context.log(context.bindings.record);
    context.done();
};
```

## <a name="input---attributes"></a>(Vstup) – atributy

V [knihoven tříd C#](functions-dotnet-class-library.md), použijte [MobileTable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) atribut.

Informace o atribut vlastnosti, které můžete nakonfigurovat, najdete v tématu [následující konfigurační oddíl](#input---configuration).

## <a name="input---configuration"></a>Vstup - konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `MobileTable` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
| **type**|| Musí být nastavena na "mobileTable"|
| **direction**||Musí být nastavena na "in"|
| **Jméno**|| Název vstupního parametru v signatuře funkce.|
|**tableName** |**TableName**|Název tabulky dat v mobilní aplikaci|
| **id**| **ID** | Identifikátor záznamu, který chcete načíst. Může být statické nebo závislosti na aktivační událost, která volá funkci. Například, pokud používáte aktivační událost fronty funkce a pak `"id": "{queueTrigger}"` používá hodnotu řetězce zprávy fronty jako ID záznamu k načtení.|
|**připojení**|**připojení**|Název nastavení aplikace, která má adresa URL mobilní aplikace. Funkce používá tuto adresu URL k vytvoření požadované operace REST pro mobilní aplikace. Vytvoření nastavení aplikace v aplikaci function app, který obsahuje adresu URL v mobilní aplikaci a pak zadejte název nastavení aplikace, které v `connection` vlastnost vstupní vazby. Adresa URL vypadá jako `http://<appname>.azurewebsites.net`.
|**apiKey**|**ApiKey**|Název nastavení aplikace, který má klíč rozhraní API mobilní aplikaci. Zadejte if klíče rozhraní API můžete [implementovat klíč rozhraní API v mobilní aplikaci Node.js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key), nebo [implementovat klíč rozhraní API v mobilní aplikaci .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Zadejte klíč, vytvořit nastavení aplikace v aplikaci function app, který obsahuje klíč rozhraní API a potom přidat `apiKey` vlastnost s názvem nastavení aplikace, které vaše vstupní vazby. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Nesdílejte klíč rozhraní API s klienty mobilních aplikací. Ji by měl pouze distribuovat bezpečně klientům straně služby, jako je Azure Functions. Služba Azure Functions ukládá informace o připojení a klíče rozhraní API jako nastavení aplikace, tak, aby se změnami do úložiště řízení zdrojů. To chrání vaše citlivé informace.

## <a name="input---usage"></a>(Vstup) – využití

V funkcí jazyka C#, když se najde záznam se zadaným ID, je jí předán do pojmenované [JObject](https://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm) parametru. Pokud záznam není nalezen, je hodnota parametru `null`. 

Do funkce jazyka JavaScript, je předán záznam `context.bindings.<name>` objektu. Pokud záznam není nalezen, je hodnota parametru `null`. 

V C# a F# funkce a změny provedené na vstup záznamu (vstupní parametr) jsou automaticky odeslány zpět k tabulce při ukončení funkce úspěšně. Nelze upravit záznam v funkce jazyka JavaScript.

## <a name="output"></a>Výstup

Pomocí Mobile Apps výstupní vazbu k zápisu nového záznamu do tabulky Mobile Apps.  

## <a name="output---example"></a>Výstup – příklad

Podívejte se na příklad specifické pro jazyk:

* [C#](#output---c-example)
* [C# skript (.csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Výstup – příklad v jazyce C#

Následující příklad ukazuje [funkce jazyka C#](functions-dotnet-class-library.md) , který se aktivuje zprávy fronty a vytvoří záznam v tabulce mobilní aplikace.

```csharp
[FunctionName("MobileAppsOutput")]        
[return: MobileTable(ApiKeySetting = "MyMobileAppKey", TableName = "MyTable", MobileAppUriSetting = "MyMobileAppUri")]
public static object Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
    TraceWriter log)
{
    return new { Text = $"I'm running in a C# function! {myQueueItem}" };
}
```

### <a name="output---c-script-example"></a>Výstup – příklad skriptu jazyka C#

Následující příklad ukazuje Mobile Apps výstupní vazby ve *function.json* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) , který používá vazba. Funkce aktivované zprávy fronty a vytvoří nový záznam s pevně zakódované hodnotou `Text` vlastnost.

Zde je vazba dat v *function.json* souboru:

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
    "name": "record",
    "type": "mobileTable",
    "tableName": "MyTable",
    "connection": "My_MobileApp_Url",
    "apiKey": "My_MobileApp_Key",
    "direction": "out"
    }
]
}
```

[Konfigurace](#output---configuration) bodu vysvětluje tyto vlastnosti.

Tady je kód skriptu jazyka C#:

```cs
public static void Run(string myQueueItem, out object record)
{
    record = new {
        Text = $"I'm running in a C# function! {myQueueItem}"
    };
}
```

### <a name="output---javascript-example"></a>Výstup – příklad v jazyce JavaScript

Následující příklad ukazuje Mobile Apps výstupní vazby ve *function.json* souboru a [funkce jazyka JavaScript](functions-reference-node.md) , který používá vazba. Funkce aktivované zprávy fronty a vytvoří nový záznam s pevně zakódované hodnotou `Text` vlastnost.

Zde je vazba dat v *function.json* souboru:

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
    "name": "record",
    "type": "mobileTable",
    "tableName": "MyTable",
    "connection": "My_MobileApp_Url",
    "apiKey": "My_MobileApp_Key",
    "direction": "out"
    }
],
"disabled": false
}
```

[Konfigurace](#output---configuration) bodu vysvětluje tyto vlastnosti.

Tady je kód jazyka JavaScript:

```javascript
module.exports = function (context, myQueueItem) {

    context.bindings.record = {
        text : "I'm running in a Node function! Data: '" + myQueueItem + "'"
    }   

    context.done();
};
```

## <a name="output---attributes"></a>Výstup – atributy

V [knihoven tříd C#](functions-dotnet-class-library.md), použijte [MobileTable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) atribut.

Informace o atribut vlastnosti, které můžete nakonfigurovat, najdete v tématu [výstup - konfigurace](#output---configuration). Tady je `MobileTable` příklad atributů v podpisu metody:

```csharp
[FunctionName("MobileAppsOutput")]        
[return: MobileTable(ApiKeySetting = "MyMobileAppKey", TableName = "MyTable", MobileAppUriSetting = "MyMobileAppUri")]
public static object Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
    TraceWriter log)
{
    ...
}
```

Kompletní příklad naleznete v tématu [výstup – příklad v jazyce C#](#output---c-example).

## <a name="output---configuration"></a>Výstup – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `MobileTable` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
| **type**|| Musí být nastavena na "mobileTable"|
| **direction**||Musí být nastavena na "out"|
| **Jméno**|| Název výstupního parametru v signatuře funkce.|
|**tableName** |**TableName**|Název tabulky dat v mobilní aplikaci|
|**připojení**|**MobileAppUriSetting**|Název nastavení aplikace, která má adresa URL mobilní aplikace. Funkce používá tuto adresu URL k vytvoření požadované operace REST pro mobilní aplikace. Vytvoření nastavení aplikace v aplikaci function app, který obsahuje adresu URL v mobilní aplikaci a pak zadejte název nastavení aplikace, které v `connection` vlastnost vstupní vazby. Adresa URL vypadá jako `http://<appname>.azurewebsites.net`.
|**apiKey**|**ApiKeySetting**|Název nastavení aplikace, který má klíč rozhraní API mobilní aplikaci. Zadejte if klíče rozhraní API můžete [implementovat klíč rozhraní API v back-endu mobilní aplikace Node.js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key), nebo [implementovat klíč rozhraní API v back-endu mobilní aplikace .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Zadejte klíč, vytvořit nastavení aplikace v aplikaci function app, který obsahuje klíč rozhraní API a potom přidat `apiKey` vlastnost s názvem nastavení aplikace, které vaše vstupní vazby. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Nesdílejte klíč rozhraní API s klienty mobilních aplikací. Ji by měl pouze distribuovat bezpečně klientům straně služby, jako je Azure Functions. Služba Azure Functions ukládá informace o připojení a klíče rozhraní API jako nastavení aplikace, tak, aby se změnami do úložiště řízení zdrojů. To chrání vaše citlivé informace.

## <a name="output---usage"></a>Výstup – využití

Ve skriptu funkcí jazyka C#, použijte parametr s názvem výstupního typu `out object` pro přístup k výstupu záznamu. V knihoven tříd C# `MobileTable` atribut lze použít s žádným z následujících typů:

* `ICollector<T>` nebo `IAsyncCollector<T>`, kde `T` je buď `JObject` nebo jakýkoli typ s `public string Id` vlastnost.
* `out JObject`
* `out T` nebo `out T[]`, kde `T` je libovolný typ `public string Id` vlastnost.

Ve funkcích Node.js pomocí `context.bindings.<name>` pro přístup k výstupu záznamu.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech Azure functions a vazby](functions-triggers-bindings.md)
