---
title: Mobile Apps vazby pro Azure Functions
description: Naučte se používat vazby Azure Mobile Apps v Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 051b8780a102a8a1ec4d3979cc53b686e2823dc8
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928601"
---
# <a name="mobile-apps-bindings-for-azure-functions"></a>Mobile Apps vazby pro Azure Functions 

> [!NOTE]
> Vazby Azure Mobile Apps jsou dostupné jenom pro Azure Functions 1. x. Nepodporují se v Azure Functions 2. x a vyšších.

Tento článek vysvětluje, jak pracovat s vazbami [Azure Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) v Azure Functions. Azure Functions podporuje vstupní a výstupní vazby pro Mobile Apps.

Vazby Mobile Apps umožňují číst a aktualizovat tabulky dat v mobilních aplikacích.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Balíčky – funkce 1.x

Vazby Mobile Apps jsou k dispozici v balíčku NuGet [Microsoft. Azure. WebJobs. Extensions. MobileApps](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps) , verze 1. x. Zdrojový kód balíčku je v úložišti GitHub [Azure-WebJobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.MobileApps/) .

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="input"></a>Vstup

Vstupní vazba Mobile Apps načte záznam z koncového bodu mobilní tabulky a předá ho do vaší funkce. Ve C# funkcích F# a se všechny změny provedené v záznamu automaticky odešlou zpátky do tabulky, když se funkce ukončí úspěšně.

## <a name="input---example"></a>Vstupní příklad

Podívejte se na příklad specifické pro jazyk:

* [C# skript (.csx)](#input---c-script-example)
* JavaScript

### <a name="input---c-script-example"></a>Ukázka skriptu C# Input-Script

Následující příklad ukazuje vstupní vazbu Mobile Apps v souboru *Function. JSON* a [ C# funkci skriptu](functions-reference-csharp.md) , která používá vazbu. Funkce je aktivována zprávou fronty s identifikátorem záznamu. Funkce přečte zadaný záznam a upraví jeho vlastnost `Text`.

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

### <a name="input---javascript"></a>Input – JavaScript

Následující příklad ukazuje vstupní vazbu Mobile Apps v souboru *Function. JSON* a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu. Funkce je aktivována zprávou fronty s identifikátorem záznamu. Funkce přečte zadaný záznam a upraví jeho vlastnost `Text`.

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

V [ C# knihovnách tříd](functions-dotnet-class-library.md)použijte atribut [Mobile](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) .

Informace o vlastnostech atributů, které můžete konfigurovat, najdete v [následující části Konfigurace](#input---configuration).

## <a name="input---configuration"></a>Vstup - konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `MobileTable` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
| **type**|| Musí být nastavené na "mobilní".|
| **direction**||Musí být nastavené na "in"|
| **name**|| Název vstupního parametru v signatuře funkce.|
|**tableName** |**TableName**|Název tabulky dat mobilní aplikace|
| **id**| **ID** | Identifikátor záznamu, který se má načíst Může být statický nebo založený na triggeru, který funkci vyvolá. Pokud například pro funkci použijete Trigger fronty, `"id": "{queueTrigger}"` jako ID záznamu použít hodnotu řetězce zprávy Queue.|
|**připojení**|**připojení**|Název nastavení aplikace, které má adresu URL mobilní aplikace Funkce používá tuto adresu URL k sestavení požadovaných operací REST v mobilní aplikaci. Ve své aplikaci Function App vytvořte nastavení aplikace, které obsahuje adresu URL mobilní aplikace, a pak ve vstupní vazbě zadejte název nastavení aplikace ve vlastnosti `connection`. Adresa URL vypadá jako `http://<appname>.azurewebsites.net`.
|**apiKey**|**ApiKey**|Název nastavení aplikace, které má klíč rozhraní API mobilní aplikace. Pokud [implementujete klíč rozhraní API v mobilní aplikaci Node. js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)nebo [IMPLEMENTUJETE klíč rozhraní API v mobilní aplikaci .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key), zadejte klíč rozhraní API. Pokud chcete klíč zadat, ve vaší aplikaci Function App vytvořte nastavení aplikace, které obsahuje klíč rozhraní API, a potom do vstupní vazby přidejte vlastnost `apiKey` s názvem nastavení aplikace. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Nesdílejte klíč rozhraní API s klienty vaší mobilní aplikace. Měla by být zabezpečená jenom pro klienty na straně služby, jako je Azure Functions. Azure Functions ukládá informace o připojení a klíče rozhraní API jako nastavení aplikace tak, aby nebyly zkontrolovány do úložiště správy zdrojového kódu. Tím se chrání vaše citlivé údaje.

## <a name="input---usage"></a>(Vstup) – využití

V C# Functions, když se nalezne záznam se zadaným ID, se předává do pojmenovaného parametru [JObject](https://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm) . Pokud záznam není nalezen, hodnota parametru je `null`. 

Ve funkcích JavaScriptu je záznam předán do objektu `context.bindings.<name>`. Pokud záznam není nalezen, hodnota parametru je `null`. 

Ve C# funkcích F# a se všechny změny, které provedete v vstupním záznamu (vstupní parametr), automaticky odešlou zpátky do tabulky, když se funkce ukončí úspěšně. Záznam nelze upravovat ve funkcích JavaScriptu.

## <a name="output"></a>Výstup

Použijte výstupní vazbu Mobile Apps k zápisu nového záznamu do tabulky Mobile Apps.  

## <a name="output---example"></a>Výstup – příklad

Podívejte se na příklad specifické pro jazyk:

* [C#](#output---c-example)
* [C# skript (.csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Výstup – příklad v jazyce C#

Následující příklad ukazuje [ C# funkci](functions-dotnet-class-library.md) , která se aktivuje v rámci zprávy fronty a vytvoří záznam v tabulce mobilní aplikace.

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

Následující příklad ukazuje výstupní vazbu Mobile Apps v souboru *Function. JSON* a [ C# funkci skriptu](functions-reference-csharp.md) , která používá vazbu. Funkce je aktivována zprávou fronty a vytvoří nový záznam s pevně zakódovanou hodnotou pro vlastnost `Text`.

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

Následující příklad ukazuje výstupní vazbu Mobile Apps v souboru *Function. JSON* a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu. Funkce je aktivována zprávou fronty a vytvoří nový záznam s pevně zakódovanou hodnotou pro vlastnost `Text`.

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

V [ C# knihovnách tříd](functions-dotnet-class-library.md)použijte atribut [Mobile](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) .

Informace o vlastnostech atributu, které lze konfigurovat, naleznete v tématu [Output-Configuration](#output---configuration). Tady je `MobileTable` příklad atributů v podpisu metody:

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
| **type**|| Musí být nastavené na "mobilní".|
| **direction**||Musí být nastavené na out.|
| **name**|| Název výstupního parametru v podpisu funkce.|
|**tableName** |**TableName**|Název tabulky dat mobilní aplikace|
|**připojení**|**MobileAppUriSetting**|Název nastavení aplikace, které má adresu URL mobilní aplikace Funkce používá tuto adresu URL k sestavení požadovaných operací REST v mobilní aplikaci. Ve své aplikaci Function App vytvořte nastavení aplikace, které obsahuje adresu URL mobilní aplikace, a pak ve vstupní vazbě zadejte název nastavení aplikace ve vlastnosti `connection`. Adresa URL vypadá jako `http://<appname>.azurewebsites.net`.
|**apiKey**|**ApiKeySetting**|Název nastavení aplikace, které má klíč rozhraní API mobilní aplikace. Pokud [implementujete klíč rozhraní API v back-endu mobilní aplikace Node. js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)nebo [IMPLEMENTUJEte klíč rozhraní API do back-endu mobilní aplikace .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key), zadejte klíč rozhraní API. Pokud chcete klíč zadat, ve vaší aplikaci Function App vytvořte nastavení aplikace, které obsahuje klíč rozhraní API, a potom do vstupní vazby přidejte vlastnost `apiKey` s názvem nastavení aplikace. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Nesdílejte klíč rozhraní API s klienty vaší mobilní aplikace. Měla by být zabezpečená jenom pro klienty na straně služby, jako je Azure Functions. Azure Functions ukládá informace o připojení a klíče rozhraní API jako nastavení aplikace tak, aby nebyly zkontrolovány do úložiště správy zdrojového kódu. Tím se chrání vaše citlivé údaje.

## <a name="output---usage"></a>Výstup – využití

Ve C# skriptových funkcích použijte pro přístup k výstupnímu záznamu pojmenovaný výstupní parametr typu `out object`. V C# knihovnách tříd lze atribut `MobileTable` použít s některým z následujících typů:

* `ICollector<T>` nebo `IAsyncCollector<T>`, kde `T` je buď `JObject`, nebo jakýkoli typ s vlastností `public string Id`.
* `out JObject`
* `out T` nebo `out T[]`, kde `T` je libovolný typ s `public string Id`ou vlastností.

Ve funkcích Node. js použijte `context.bindings.<name>` pro přístup k výstupnímu záznamu.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech Azure functions a vazby](functions-triggers-bindings.md)
