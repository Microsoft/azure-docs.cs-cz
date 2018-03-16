---
title: Vazby Mobile Apps pro Azure Functions
description: "Pochopit, jak používat Azure Mobile Apps vazby v Azure Functions."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Funkce Azure, funkce zpracování událostí, dynamické výpočetní architektura bez serveru"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: c5fb7bdd88691c9aeab6b348507901c34502b28b
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="mobile-apps-bindings-for-azure-functions"></a>Vazby Mobile Apps pro Azure Functions 

Tento článek vysvětluje, jak pracovat s [Azure Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) vazeb v Azure Functions. Azure Functions podporuje vstup a výstup vazby pro Mobile Apps.

Vazby mobilní aplikace vám umožní číst a aktualizovat tabulky dat v mobilních aplikacích.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages"></a>Balíčky

Vazby Mobile Apps jsou součástí [Microsoft.Azure.WebJobs.Extensions.MobileApps](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps) balíček NuGet. Zdrojový kód pro balíček je v [azure webjobs sdk rozšíření](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/) úložiště GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="input"></a>Vstup

Vstupní vazba Mobile Apps načte záznam z koncového bodu mobilní tabulky a předá ji do funkce. C# a F # funkce všechny změny provedené v záznamu se automaticky odešlou zpět k tabulce při ukončení funkce úspěšně.

## <a name="input---example"></a>Vstup – příklad

Podívejte se na konkrétní jazyk příklad:

* [C# skript (.csx)](#input---c-script-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-script-example"></a>(Vstup) – příklad skriptu jazyka C#

Následující příklad ukazuje vstupní vazbu Mobile Apps v *function.json* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) používající vazby. Funkce se aktivuje zprávu fronty, která má identifikátor záznamu. Funkce přečte zadaný záznam a změní jeho `Text` vlastnost.

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
],
"disabled": false
}
```
[Konfigurace](#input---configuration) část vysvětluje tyto vlastnosti.

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

### <a name="input---javascript"></a>Vstup - JavaScript

Následující příklad ukazuje vstupní vazbu Mobile Apps v *function.json* souboru a [funkce JavaScript, která](functions-reference-node.md) používající vazby. Funkce se aktivuje zprávu fronty, která má identifikátor záznamu. Funkce přečte zadaný záznam a změní jeho `Text` vlastnost.

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
],
"disabled": false
}
```
[Konfigurace](#input---configuration) část vysvětluje tyto vlastnosti.

Tady je kód jazyka JavaScript:

```javascript
module.exports = function (context, myQueueItem) {    
    context.log(context.bindings.record);
    context.done();
};
```

## <a name="input---attributes"></a>(Vstup) – atributy

V [knihovny tříd jazyka C#](functions-dotnet-class-library.md), použijte [MobileTable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) atribut.

Informace o vlastnostech atributů, které můžete konfigurovat, najdete v tématu [následující konfigurační oddíl](#input---configuration).

## <a name="input---configuration"></a>Vstup - konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `MobileTable` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
| **Typ**|| Musí být nastavena na "mobileTable"|
| **Směr**||Musí být nastavena na "v"|
| **name**|| Název vstupního parametru v podpis funkce.|
|**tableName** |**TableName**|Název tabulky dat mobilní aplikace|
| **id**| **ID** | Identifikátor záznamu pro načtení. Můžou být statické nebo podle aktivační událost, která volá funkci. Pokud pak použít aktivační procedury fronty pro funkce, například `"id": "{queueTrigger}"` používá s řetězcovou hodnotou obsahující zprávy ve frontě jako ID záznamu pro načtení.|
|**Připojení**|**Připojení**|Název nastavení aplikace, který obsahuje adresu URL do mobilní aplikace. Funkce používá tuto adresu URL k vytvoření požadované operace REST pro mobilní aplikace. Vytvoření nastavení aplikace v aplikaci funkce, který obsahuje adresu URL do mobilní aplikace a potom zadejte název nastavení aplikace v `connection` vlastnost Vstupní vazba. Vypadá adresa URL `http://<appname>.azurewebsites.net`.
|**apiKey**|**ApiKey**|Název nastavení aplikace, který má klíč rozhraní API mobilní aplikaci. Zadejte Pokud klíče rozhraní API můžete [implementovat klíč rozhraní API v mobilní aplikaci Node.js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key), nebo [implementovat klíč rozhraní API v mobilní aplikaci .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Chcete-li zadat klíč, vytvoření nastavení aplikace v aplikaci funkce, který obsahuje klíč rozhraní API a pak přidejte `apiKey` vlastnost vaše Vstupní vazba s názvem nastavení aplikace. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Nesdílejte klíč rozhraní API s klienty mobilní aplikace. Je měli jenom zajistit jeho distribuci bezpečně klientům straně služby, jako je Azure Functions. Azure Functions ukládá informace o připojení a klíče rozhraní API jako nastavení aplikace, tak, aby se změnami do vašeho úložiště správy zdrojového kódu. To chrání vaše citlivé informace.

## <a name="input---usage"></a>(Vstup) – použití

V jazyce C# funkce, když se najde záznam se zadaným ID je předán do pojmenované [JObject](http://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm) parametr. Při záznamu není nalezen, je hodnota parametru `null`. 

V funkce jazyka JavaScript je záznam předána do `context.bindings.<name>` objektu. Při záznamu není nalezen, je hodnota parametru `null`. 

Funkcí jazyka C# a F #, provedené změny do vstupní záznamu (vstupní parametr) automaticky odešlou zpět k tabulce při ukončení funkce úspěšně. Nelze upravit záznam v funkce jazyka JavaScript.

## <a name="output"></a>Výstup

Použijte výstup Mobile Apps vazby k zápisu nového záznamu do tabulky mobilní aplikace.  

## <a name="output---example"></a>Výstup – příklad

Podívejte se na konkrétní jazyk příklad:

* [C#](#output---c-example)
* [C# skript (.csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Výstup – příklad jazyka C#

Následující příklad ukazuje [C# funkce](functions-dotnet-class-library.md) které se aktivuje zprávu fronty a vytvoří záznam v tabulce mobilní aplikace.

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

Následující příklad ukazuje výstup Mobile Apps vazby ve *function.json* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) používající vazby. Funkce se aktivuje zprávu fronty a vytvoří nový záznam s pevně zakódovaným hodnotou `Text` vlastnost.

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

[Konfigurace](#output---configuration) část vysvětluje tyto vlastnosti.

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

Následující příklad ukazuje výstup Mobile Apps vazby ve *function.json* souboru a [funkce JavaScript, která](functions-reference-node.md) používající vazby. Funkce se aktivuje zprávu fronty a vytvoří nový záznam s pevně zakódovaným hodnotou `Text` vlastnost.

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

[Konfigurace](#output---configuration) část vysvětluje tyto vlastnosti.

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

V [knihovny tříd jazyka C#](functions-dotnet-class-library.md), použijte [MobileTable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) atribut.

Informace o vlastnostech atributů, které můžete konfigurovat, najdete v tématu [výstup - konfigurace](#output---configuration). Tady je `MobileTable` atribut příkladu podpis metody:

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

Úplný příklad najdete v tématu [výstup - C# příklad](#output---c-example).

## <a name="output---configuration"></a>Výstup – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `MobileTable` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
| **Typ**|| Musí být nastavena na "mobileTable"|
| **Směr**||Musí být nastavena na "out"|
| **name**|| Název výstupního parametru v podpis funkce.|
|**tableName** |**TableName**|Název tabulky dat mobilní aplikace|
|**Připojení**|**MobileAppUriSetting**|Název nastavení aplikace, který obsahuje adresu URL do mobilní aplikace. Funkce používá tuto adresu URL k vytvoření požadované operace REST pro mobilní aplikace. Vytvoření nastavení aplikace v aplikaci funkce, který obsahuje adresu URL do mobilní aplikace a potom zadejte název nastavení aplikace v `connection` vlastnost Vstupní vazba. Vypadá adresa URL `http://<appname>.azurewebsites.net`.
|**apiKey**|**ApiKeySetting**|Název nastavení aplikace, který má klíč rozhraní API mobilní aplikaci. Zadejte Pokud klíče rozhraní API můžete [implementovat klíč rozhraní API vašeho back-end mobilní aplikace Node.js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key), nebo [implementovat klíč rozhraní API vašeho back-end mobilní aplikace .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Chcete-li zadat klíč, vytvoření nastavení aplikace v aplikaci funkce, který obsahuje klíč rozhraní API a pak přidejte `apiKey` vlastnost vaše Vstupní vazba s názvem nastavení aplikace. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Nesdílejte klíč rozhraní API s klienty mobilní aplikace. Je měli jenom zajistit jeho distribuci bezpečně klientům straně služby, jako je Azure Functions. Azure Functions ukládá informace o připojení a klíče rozhraní API jako nastavení aplikace, tak, aby se změnami do vašeho úložiště správy zdrojového kódu. To chrání vaše citlivé informace.

## <a name="output---usage"></a>Výstup – použití

V C# skript funkce, použijte parametr s názvem výstup typu `out object` pro přístup k výstupu záznamu. V jazyce C# knihovny tříd `MobileTable` atribut lze použít s žádným z následujících typů:

* `ICollector<T>` nebo `IAsyncCollector<T>`, kde `T` je buď `JObject` nebo jakýkoli typ s `public string Id` vlastnost.
* `out JObject`
* `out T` nebo `out T[]`, kde `T` je všech typů `public string Id` vlastnost.

Ve funkcích Node.js, použijte `context.bindings.<name>` pro přístup k výstupu záznamu.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o Azure functions triggerů a vazeb](functions-triggers-bindings.md)
