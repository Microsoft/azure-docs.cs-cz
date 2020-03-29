---
title: Vazby mobilních aplikací pro funkce Azure
description: Zjistěte, jak používat vazby Azure Mobile Apps ve funkcích Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 952a94797e01a3931fdd151461250af0c2590c11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76120537"
---
# <a name="mobile-apps-bindings-for-azure-functions"></a>Vazby mobilních aplikací pro funkce Azure 

> [!NOTE]
> Vazby Azure Mobile Apps jsou dostupné jenom pro Azure Functions 1.x. Nejsou podporovány v Azure Functions 2.x a vyšší.

Tento článek vysvětluje, jak pracovat s vazby [Azure Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) v Azure Functions. Funkce Azure podporuje vstupní a výstupní vazby pro mobilní aplikace.

Vazby mobilních aplikací umožňují číst a aktualizovat datové tabulky v mobilních aplikacích.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Balíčky - Funkce 1.x

Vazby mobilních aplikací jsou k dispozici v balíčku [Microsoft.Azure.WebJobs.Extensions.MobileApps](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps) NuGet verze 1.x. Zdrojový kód pro balíček je v úložišti [GitHub azure-webjobs-sdk-extensions.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.MobileApps/)

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="input"></a>Vstup

Vstupní vazba mobilníaplikace načte záznam z koncového bodu mobilní tabulky a předá jej do vaší funkce. Ve funkcích Jazyka C# a F# jsou všechny změny provedené v záznamu automaticky odeslány zpět do tabulky při úspěšném ukončení funkce.

## <a name="input---example"></a>Vstup - příklad

Podívejte se na příklad pro konkrétní jazyk:

* [Skript jazyka C# (.csx)](#input---c-script-example)
* JavaScript

### <a name="input---c-script-example"></a>Příklad skriptu Input - C#

Následující příklad ukazuje vstupní vazbu mobilníaplikace v souboru *function.json* a [funkci skriptu Jazyka C#,](functions-reference-csharp.md) která používá vazbu. Funkce je spuštěna zprávou fronty, která má identifikátor záznamu. Funkce přečte zadaný záznam a `Text` upraví jeho vlastnost.

Zde jsou data vazby v souboru *function.json:*

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
[Konfigurační](#input---configuration) část vysvětluje tyto vlastnosti.

Zde je kód skriptu C#:

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

### <a name="input---javascript"></a>Vstup - JavaScript

Následující příklad ukazuje vstupní vazbu mobilních aplikací v souboru *function.json* a [funkci JavaScriptu,](functions-reference-node.md) která vazbu používá. Funkce je spuštěna zprávou fronty, která má identifikátor záznamu. Funkce přečte zadaný záznam a `Text` upraví jeho vlastnost.

Zde jsou data vazby v souboru *function.json:*

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
[Konfigurační](#input---configuration) část vysvětluje tyto vlastnosti.

Zde je kód JavaScript:

```javascript
module.exports = function (context, myQueueItem) {    
    context.log(context.bindings.record);
    context.done();
};
```

## <a name="input---attributes"></a>Vstup - atributy

V [knihovnách tříd Jazyka C#](functions-dotnet-class-library.md)použijte atribut [MobileTable.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs)

Informace o vlastnostech atributů, které lze konfigurovat, naleznete [v následující části konfigurace](#input---configuration).

## <a name="input---configuration"></a>Vstup - konfigurace

Následující tabulka vysvětluje vlastnosti konfigurace vazby, které jste nastavili `MobileTable` v souboru *function.json* a atributu.

|vlastnost function.json | Vlastnost atributu |Popis|
|---------|---------|----------------------|
| **Typ**| neuvedeno | Musí být nastavena na "mobileTable"|
| **direction**| neuvedeno |Musí být nastavena na "in"|
| **Jméno**| neuvedeno | Název vstupního parametru v podpisu funkce.|
|**Tablename** |**TableName**|Název tabulky dat mobilní aplikace|
| **Id**| **ID** | Identifikátor záznamu načíst. Může být statické nebo na základě aktivační události, která vyvolá funkci. Pokud například pro vaši funkci použijete `"id": "{queueTrigger}"` aktivační událost fronty, použije hodnotu řetězce zprávy fronty jako ID záznamu k načtení.|
|**Připojení**|**Připojení**|Název nastavení aplikace, která má adresu URL mobilní aplikace. Tato funkce používá tuto adresu URL k vytvoření požadovaných operací REST proti mobilní aplikaci. Vytvořte nastavení aplikace ve vaší funkční aplikaci, která obsahuje adresu URL mobilní `connection` aplikace, a pak ve vstupní vazbě zadejte název nastavení aplikace ve vlastnosti. Adresa URL `http://<appname>.azurewebsites.net`vypadá jako .
|**apiKey**|**ApiKey**|Název nastavení aplikace, která má klíč rozhraní API vaší mobilní aplikace. Pokud [implementujete klíč rozhraní API v mobilní aplikaci Node.js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)nebo [implementujete klíč rozhraní API v mobilní aplikaci .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key), poskytněte klíč rozhraní API . Chcete-li klíč poskytnout, vytvořte nastavení aplikace v aplikaci funkce, která obsahuje klíč rozhraní API, a pak přidejte `apiKey` vlastnost ve vstupní vazbě s názvem nastavení aplikace. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Nesdílejte klíč rozhraní API s klienty mobilních aplikací. Měl by být distribuován jenom bezpečně klientům na straně služby, jako jsou funkce Azure. Azure Functions ukládá informace o připojení a klíče rozhraní API jako nastavení aplikace tak, aby nebyly zkontrolovány do úložiště správy zdrojového kódu. To chrání vaše citlivé informace.

## <a name="input---usage"></a>Vstup - využití

Ve funkcích Jazyka C# je záznam se zadaným ID předán do pojmenovaného parametru [JObject.](https://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm) Pokud není záznam nalezen, hodnota `null`parametru je . 

Ve funkcích JavaScriptu je `context.bindings.<name>` záznam předán do objektu. Pokud není záznam nalezen, hodnota `null`parametru je . 

Ve funkcích Jazyka C# a F# jsou všechny změny provedené ve vstupním záznamu (vstupníparametr) automaticky odeslány zpět do tabulky při úspěšném ukončení funkce. Záznam nelze upravit ve funkcích JavaScriptu.

## <a name="output"></a>Výstup

Pomocí vazby výstupu mobilních aplikací zapište nový záznam do tabulky Mobilní aplikace.  

## <a name="output---example"></a>Výstup - příklad

Podívejte se na příklad pro konkrétní jazyk:

* [C #](#output---c-example)
* [Skript jazyka C# (.csx)](#output---c-script-example)
* [Javascript](#output---javascript-example)

### <a name="output---c-example"></a>Výstup – příklad jazyka C#

Následující příklad ukazuje [funkci C#,](functions-dotnet-class-library.md) která je spuštěna zprávou fronty a vytvoří záznam v tabulce mobilní aplikace.

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

### <a name="output---c-script-example"></a>Příklad skriptu jazyka C#

Následující příklad ukazuje mobilní aplikace výstupní vazby v *souboru function.json* a [c# skript funkce,](functions-reference-csharp.md) která používá vazbu. Funkce je spuštěna zprávou fronty a vytvoří nový záznam s `Text` pevně zakódovanou hodnotou pro vlastnost.

Zde jsou data vazby v souboru *function.json:*

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

[Konfigurační](#output---configuration) část vysvětluje tyto vlastnosti.

Zde je kód skriptu C#:

```cs
public static void Run(string myQueueItem, out object record)
{
    record = new {
        Text = $"I'm running in a C# function! {myQueueItem}"
    };
}
```

### <a name="output---javascript-example"></a>Výstup – příklad JavaScriptu

Následující příklad ukazuje výstupní vazbu mobilních aplikací v souboru *function.json* a [funkci JavaScriptu,](functions-reference-node.md) která vazbu používá. Funkce je spuštěna zprávou fronty a vytvoří nový záznam s `Text` pevně zakódovanou hodnotou pro vlastnost.

Zde jsou data vazby v souboru *function.json:*

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

[Konfigurační](#output---configuration) část vysvětluje tyto vlastnosti.

Zde je kód JavaScript:

```javascript
module.exports = function (context, myQueueItem) {

    context.bindings.record = {
        text : "I'm running in a Node function! Data: '" + myQueueItem + "'"
    }   

    context.done();
};
```

## <a name="output---attributes"></a>Výstup - atributy

V [knihovnách tříd Jazyka C#](functions-dotnet-class-library.md)použijte atribut [MobileTable.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs)

Informace o vlastnostech atributů, které můžete konfigurovat, naleznete v [tématu Output - configuration](#output---configuration). Zde je `MobileTable` příklad atributu v podpisu metody:

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

Úplný příklad naleznete v [tématu Output - C# example](#output---c-example).

## <a name="output---configuration"></a>Výstup - konfigurace

Následující tabulka vysvětluje vlastnosti konfigurace vazby, které jste nastavili `MobileTable` v souboru *function.json* a atributu.

|vlastnost function.json | Vlastnost atributu |Popis|
|---------|---------|----------------------|
| **Typ**| neuvedeno | Musí být nastavena na "mobileTable"|
| **direction**| neuvedeno |Musí být nastavena na "out"|
| **Jméno**| neuvedeno | Název výstupního parametru v podpisu funkce.|
|**Tablename** |**TableName**|Název tabulky dat mobilní aplikace|
|**Připojení**|**MobileAppUriSetting**|Název nastavení aplikace, která má adresu URL mobilní aplikace. Tato funkce používá tuto adresu URL k vytvoření požadovaných operací REST proti mobilní aplikaci. Vytvořte nastavení aplikace ve vaší funkční aplikaci, která obsahuje adresu URL mobilní `connection` aplikace, a pak ve vstupní vazbě zadejte název nastavení aplikace ve vlastnosti. Adresa URL `http://<appname>.azurewebsites.net`vypadá jako .
|**apiKey**|**ApiKeySetting**|Název nastavení aplikace, která má klíč rozhraní API vaší mobilní aplikace. Pokud [implementujete klíč rozhraní API v back-endu mobilní aplikace Node.js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)nebo [implementujete klíč rozhraní API v back-endu mobilní aplikace .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Chcete-li klíč poskytnout, vytvořte nastavení aplikace v aplikaci funkce, která obsahuje klíč rozhraní API, a pak přidejte `apiKey` vlastnost ve vstupní vazbě s názvem nastavení aplikace. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Nesdílejte klíč rozhraní API s klienty mobilních aplikací. Měl by být distribuován jenom bezpečně klientům na straně služby, jako jsou funkce Azure. Azure Functions ukládá informace o připojení a klíče rozhraní API jako nastavení aplikace tak, aby nebyly zkontrolovány do úložiště správy zdrojového kódu. To chrání vaše citlivé informace.

## <a name="output---usage"></a>Výstup - využití

Ve skriptových funkcích jazyka C# `out object` použijte pro přístup k výstupnímu záznamu pojmenovaný výstupní parametr typu. V knihovnách tříd jazyka `MobileTable` C# lze atribut použít s libovolným z následujících typů:

* `ICollector<T>`nebo `IAsyncCollector<T>`, `T` kde `JObject` je jeden `public string Id` nebo jakýkoli typ s vlastností.
* `out JObject`
* `out T`nebo `out T[]`, `T` kde je `public string Id` libovolný Typ s vlastností.

Ve funkcích Node.js použijte `context.bindings.<name>` pro přístup k výstupnímu záznamu.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech a vazbách funkcí Azure](functions-triggers-bindings.md)
