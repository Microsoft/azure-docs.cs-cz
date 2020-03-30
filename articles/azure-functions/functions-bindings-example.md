---
title: Azure Funkce aktivační události a vazby příklad
description: Naučte se konfigurovat vazby funkcí Azure
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 8685c0fe02ad6c68918736e857c2015e2bfb4595
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74227249"
---
# <a name="azure-functions-trigger-and-binding-example"></a>Azure Funkce aktivační události a vazby příklad

Tento článek ukazuje, jak nakonfigurovat [aktivační událost a vazby](./functions-triggers-bindings.md) ve funkci Azure.

Předpokládejme, že chcete napsat nový řádek do úložiště Azure Table vždy, když se v úložišti Azure Queue zobrazí nová zpráva. Tento scénář je možné implementovat pomocí aktivační události Azure Queue Storage a výstupní vazby Azure Table Storage. 

Zde je *soubor function.json* pro tento scénář. 

```json
{
  "bindings": [
    {
      "type": "queueTrigger",
      "direction": "in",
      "name": "order",
      "queueName": "myqueue-items",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "type": "table",
      "direction": "out",
      "name": "$return",
      "tableName": "outTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

První prvek v `bindings` poli je fronta úložiště aktivační událost. `type` Vlastnosti `direction` a identifikují aktivační událost. Vlastnost `name` identifikuje parametr funkce, který přijímá obsah zprávy fronty. Název fronty, která má `queueName`být sledována, je v aplikaci `connection`a připojovací řetězec je v nastavení aplikace určeném aplikací .

Druhý prvek v `bindings` poli je výstupní vazba Azure Table Storage. `type` Vlastnosti `direction` a identifikují vazbu. Vlastnost `name` určuje, jak funkce poskytuje nový řádek tabulky, v tomto případě pomocí vrácené hodnoty funkce. Název tabulky je v `tableName`aplikaci a připojovací řetězec `connection`je v nastavení aplikace určeném aplikací .

Chcete-li zobrazit a upravit obsah *souboru function.json* na webu Azure Portal, klikněte na možnost **Rozšířený editor** na kartě **Integrace** vaší funkce.

> [!NOTE]
> Hodnota `connection` je název nastavení aplikace, která obsahuje připojovací řetězec, nikoli samotný připojovací řetězec. Vazby používají připojovací řetězce uložené v nastavení aplikace k vynucení osvědčeného režimu, který *soubor function.json* neobsahuje tajné klíče služeb.

## <a name="c-script-example"></a>Příklad skriptu jazyka C#

Zde je c# skript kód, který pracuje s touto aktivační událost a vazby. Všimněte si, že název parametru, `order`který poskytuje obsah zprávy fronty je ; Tento název je `name` povinný, protože hodnota vlastnosti *function.json* je`order` 

```cs
#r "Newtonsoft.Json"

using Microsoft.Extensions.Logging;
using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, ILogger log)
{
    return new Person() { 
            PartitionKey = "Orders", 
            RowKey = Guid.NewGuid().ToString(),  
            Name = order["Name"].ToString(),
            MobileNumber = order["MobileNumber"].ToString() };  
}
 
public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

## <a name="javascript-example"></a>Příklad JavaScriptu

Stejný soubor *function.json* lze použít s funkcí JavaScript:

```javascript
// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The second parameter to context.done is used as the value for the new row
module.exports = function (context, order) {
    order.PartitionKey = "Orders";
    order.RowKey = generateRandomId(); 

    context.done(null, order);
};

function generateRandomId() {
    return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
}
```

## <a name="class-library-example"></a>Příklad knihovny tříd

V knihovně tříd jsou stejné &mdash; fronty a názvy tabulek, účty úložiště, &mdash; parametry funkce pro vstup a výstup poskytovány atributy namísto souboru function.json. Tady je příklad:

```csharp
public static class QueueTriggerTableOutput
{
    [FunctionName("QueueTriggerTableOutput")]
    [return: Table("outTable", Connection = "MY_TABLE_STORAGE_ACCT_APP_SETTING")]
    public static Person Run(
        [QueueTrigger("myqueue-items", Connection = "MY_STORAGE_ACCT_APP_SETTING")]JObject order,
        ILogger log)
    {
        return new Person() {
                PartitionKey = "Orders",
                RowKey = Guid.NewGuid().ToString(),
                Name = order["Name"].ToString(),
                MobileNumber = order["MobileNumber"].ToString() };
    }
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

Teď máte pracovní funkci, která se aktivuje fronty Azure a výstupy dat do úložiště Azure Table.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vzory výrazů vazby Azure Functions](./functions-bindings-expressions-patterns.md)
