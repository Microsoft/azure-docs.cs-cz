---
title: Mobile Apps vazby pro Azure Functions
description: Naučte se používat vazby Azure Mobile Apps v Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 5ea58cc3d9f3615a74249b36f3f9ffb79caddda1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88212232"
---
# <a name="mobile-apps-bindings-for-azure-functions"></a>Mobile Apps vazby pro Azure Functions 

> [!NOTE]
> Vazby Azure Mobile Apps jsou dostupné jenom pro Azure Functions 1. x. Nepodporují se v Azure Functions 2. x a vyšších.

Tento článek vysvětluje, jak pracovat s vazbami [Azure Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop) v Azure Functions. Azure Functions podporuje vstupní a výstupní vazby pro Mobile Apps.

Vazby Mobile Apps umožňují číst a aktualizovat tabulky dat v mobilních aplikacích.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Balíčky – funkce 1. x

Vazby Mobile Apps jsou k dispozici v balíčku NuGet [Microsoft. Azure. WebJobs. Extensions. MobileApps](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps) , verze 1. x. Zdrojový kód balíčku je v úložišti GitHub [Azure-WebJobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.MobileApps/) .

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="input"></a>Vstup

Vstupní vazba Mobile Apps načte záznam z koncového bodu mobilní tabulky a předá ho do vaší funkce. V jazyce C# a F # jsou všechny změny provedené v záznamu automaticky odesílány zpět do tabulky, když funkce skončí úspěšně.

## <a name="input---example"></a>Vstupní příklad

Podívejte se na příklad konkrétního jazyka:

* [Skript jazyka C# (.csx)](#input---c-script-example)
* JavaScript

### <a name="input---c-script-example"></a>Input – příklad skriptu jazyka C#

Následující příklad ukazuje vstupní vazbu Mobile Apps v *function.js* souboru a [funkci skriptu jazyka C#](functions-reference-csharp.md) , která používá vazbu. Funkce je aktivována zprávou fronty s identifikátorem záznamu. Funkce přečte zadaný záznam a upraví jeho `Text` vlastnost.

Tady jsou data vazby v *function.js* souboru:

```json
{
"bindings": [
    {
        "name": "myQueueItem",
        "queueName": "myqueue-items",
        "connection": "",
        "type": "queueTrigger",
        "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "id": "{queueTrigger}",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "in"
    }
]
}
```
Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#input---configuration) .

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

Následující příklad ukazuje vstupní vazbu Mobile Apps v *function.js* souboru a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu. Funkce je aktivována zprávou fronty s identifikátorem záznamu. Funkce přečte zadaný záznam a upraví jeho `Text` vlastnost.

Tady jsou data vazby v *function.js* souboru:

```json
{
"bindings": [
    {
        "name": "myQueueItem",
        "queueName": "myqueue-items",
        "connection": "",
        "type": "queueTrigger",
        "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "id": "{queueTrigger}",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "in"
    }
]
}
```
Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#input---configuration) .

Tady je kód JavaScriptu:

```javascript
module.exports = function (context, myQueueItem) {    
    context.log(context.bindings.record);
    context.done();
};
```

## <a name="input---attributes"></a>Vstupní atributy

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte atribut [Mobile](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) .

Informace o vlastnostech atributů, které můžete konfigurovat, najdete v [následující části Konfigurace](#input---configuration).

## <a name="input---configuration"></a>Vstup – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.jspro* soubor a `MobileTable` atribut.

|function.jsvlastnost | Vlastnost atributu |Popis|
|---------|---------|----------------------|
| **textový**| Není k dispozici | Musí být nastavené na "mobilní".|
| **směr**| Není k dispozici |Musí být nastavené na "in"|
| **Jméno**| Není k dispozici | Název vstupního parametru v signatuře funkce.|
|**Tabulky** |**TableName**|Název tabulky dat mobilní aplikace|
| **id**| **Účet** | Identifikátor záznamu, který se má načíst Může být statický nebo založený na triggeru, který funkci vyvolá. Pokud například pro funkci použijete Trigger fronty, `"id": "{queueTrigger}"` použije se jako ID záznamu řetězcová hodnota zprávy Queue, která se má načíst.|
|**vázán**|**Připojení**|Název nastavení aplikace, které má adresu URL mobilní aplikace Funkce používá tuto adresu URL k sestavení požadovaných operací REST v mobilní aplikaci. Ve své aplikaci Function App vytvořte nastavení aplikace, které obsahuje adresu URL mobilní aplikace, a potom zadejte název nastavení aplikace ve `connection` vlastnosti ve vstupní vazbě. Adresa URL vypadá jako `http://<appname>.azurewebsites.net` .
|**apiKey**|**ApiKey**|Název nastavení aplikace, které má klíč rozhraní API mobilní aplikace. Pokud [implementujete klíč rozhraní API v mobilní aplikaci Node.js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)nebo [implementujete klíč rozhraní API v mobilní aplikaci .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key), zadejte klíč rozhraní API. Klíč zadáte tak, že ve své aplikaci Function App vytvoříte nastavení aplikace, které obsahuje klíč rozhraní API, a potom `apiKey` do vstupní vazby přidáte vlastnost s názvem nastavení aplikace. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Nesdílejte klíč rozhraní API s klienty vaší mobilní aplikace. Měla by být zabezpečená jenom pro klienty na straně služby, jako je Azure Functions. Azure Functions ukládá informace o připojení a klíče rozhraní API jako nastavení aplikace tak, aby nebyly zkontrolovány do úložiště správy zdrojového kódu. Tím se chrání vaše citlivé údaje.

## <a name="input---usage"></a>Vstup – použití

Pokud je v C# Functions nalezen záznam se zadaným ID, je předán do pojmenovaného parametru [JObject](https://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm) . Pokud záznam není nalezen, hodnota parametru je `null` . 

Ve funkcích JavaScriptu je záznam předán do `context.bindings.<name>` objektu. Pokud záznam není nalezen, hodnota parametru je `null` . 

V jazyce C# a F # jsou všechny změny, které provedete v vstupním záznamu (vstupní parametr), automaticky odesílány zpět do tabulky, když funkce skončí úspěšně. Záznam nelze upravovat ve funkcích JavaScriptu.

## <a name="output"></a>Výstup

Použijte výstupní vazbu Mobile Apps k zápisu nového záznamu do tabulky Mobile Apps.  

## <a name="output---example"></a>Výstup – příklad

Podívejte se na příklad konkrétního jazyka:

* [C#](#output---c-example)
* [Skript jazyka C# (.csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Výstup – příklad v jazyce C#

Následující příklad ukazuje [funkci jazyka C#](functions-dotnet-class-library.md) , která je aktivována zprávou fronty a vytváří záznam v tabulce mobilní aplikace.

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

### <a name="output---c-script-example"></a>Výstup – příklad skriptu C#

Následující příklad ukazuje výstupní vazbu Mobile Apps v *function.js* souboru a [funkci skriptu jazyka C#](functions-reference-csharp.md) , která používá vazbu. Funkce je aktivována zprávou fronty a vytvoří nový záznam s pevně zakódovanou hodnotou pro `Text` vlastnost.

Tady jsou data vazby v *function.js* souboru:

```json
{
"bindings": [
    {
        "name": "myQueueItem",
        "queueName": "myqueue-items",
        "connection": "",
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

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#output---configuration) .

Tady je kód skriptu jazyka C#:

```cs
public static void Run(string myQueueItem, out object record)
{
    record = new {
        Text = $"I'm running in a C# function! {myQueueItem}"
    };
}
```

### <a name="output---javascript-example"></a>Výstup – příklad JavaScriptu

Následující příklad ukazuje výstupní vazbu Mobile Apps v *function.js* souboru a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu. Funkce je aktivována zprávou fronty a vytvoří nový záznam s pevně zakódovanou hodnotou pro `Text` vlastnost.

Tady jsou data vazby v *function.js* souboru:

```json
{
"bindings": [
    {
        "name": "myQueueItem",
        "queueName": "myqueue-items",
        "connection": "",
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

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#output---configuration) .

Tady je kód JavaScriptu:

```javascript
module.exports = function (context, myQueueItem) {

    context.bindings.record = {
        text : "I'm running in a Node function! Data: '" + myQueueItem + "'"
    }   

    context.done();
};
```

## <a name="output---attributes"></a>Výstupní atributy

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte atribut [Mobile](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) .

Informace o vlastnostech atributu, které lze konfigurovat, naleznete v tématu [Output-Configuration](#output---configuration). Tady je `MobileTable` příklad atributu v signatuře metody:

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

Úplný příklad naleznete v tématu [Output-C# příklad](#output---c-example).

## <a name="output---configuration"></a>Výstup – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.jspro* soubor a `MobileTable` atribut.

|function.jsvlastnost | Vlastnost atributu |Popis|
|---------|---------|----------------------|
| **textový**| Není k dispozici | Musí být nastavené na "mobilní".|
| **směr**| Není k dispozici |Musí být nastavené na out.|
| **Jméno**| Není k dispozici | Název výstupního parametru v podpisu funkce.|
|**Tabulky** |**TableName**|Název tabulky dat mobilní aplikace|
|**vázán**|**MobileAppUriSetting**|Název nastavení aplikace, které má adresu URL mobilní aplikace Funkce používá tuto adresu URL k sestavení požadovaných operací REST v mobilní aplikaci. Ve své aplikaci Function App vytvořte nastavení aplikace, které obsahuje adresu URL mobilní aplikace, a potom zadejte název nastavení aplikace ve `connection` vlastnosti ve vstupní vazbě. Adresa URL vypadá jako `http://<appname>.azurewebsites.net` .
|**apiKey**|**ApiKeySetting**|Název nastavení aplikace, které má klíč rozhraní API mobilní aplikace. Pokud [implementujete klíč rozhraní API v back-endu mobilní aplikace Node.js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)nebo implementujete klíč rozhraní API [do back-endu mobilní aplikace .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key), zadejte klíč rozhraní API. Klíč zadáte tak, že ve své aplikaci Function App vytvoříte nastavení aplikace, které obsahuje klíč rozhraní API, a potom `apiKey` do vstupní vazby přidáte vlastnost s názvem nastavení aplikace. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Nesdílejte klíč rozhraní API s klienty vaší mobilní aplikace. Měla by být zabezpečená jenom pro klienty na straně služby, jako je Azure Functions. Azure Functions ukládá informace o připojení a klíče rozhraní API jako nastavení aplikace tak, aby nebyly zkontrolovány do úložiště správy zdrojového kódu. Tím se chrání vaše citlivé údaje.

## <a name="output---usage"></a>Výstup – využití

Ve funkcích skriptu jazyka C# použijte `out object` pro přístup k výstupnímu záznamu pojmenovaný výstupní parametr typu. V knihovnách tříd jazyka C# `MobileTable` lze atribut použít s některým z následujících typů:

* `ICollector<T>` nebo `IAsyncCollector<T>` , kde `T` je buď `JObject` nebo jakýkoli typ s `public string Id` vlastností.
* `out JObject`
* `out T` nebo `out T[]` , kde `T` je libovolný typ s `public string Id` vlastností.

V Node.js Functions použijte `context.bindings.<name>` pro přístup k výstupnímu záznamu.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech a vazbách Azure Functions](functions-triggers-bindings.md)
