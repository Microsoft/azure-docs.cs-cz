---
title: Příklad triggeru Azure Functions a vazeb
description: Naučte se konfigurovat vazby funkcí Azure
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 8685c0fe02ad6c68918736e857c2015e2bfb4595
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227249"
---
# <a name="azure-functions-trigger-and-binding-example"></a>Příklad triggeru Azure Functions a vazeb

Tento článek ukazuje, jak nakonfigurovat [Trigger a vazby](./functions-triggers-bindings.md) ve funkci Azure Functions.

Předpokládejme, že chcete zapsat nový řádek do služby Azure Table Storage pokaždé, když se ve službě Azure Queue Storage zobrazí nová zpráva. Tento scénář se dá implementovat pomocí triggeru služby Azure Queue Storage a výstupní vazby služby Azure Table Storage. 

Zde je soubor *Function. JSON* pro tento scénář. 

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

Prvním prvkem v poli `bindings` je Trigger úložiště Queue. Vlastnosti `type` a `direction` identifikují Trigger. Vlastnost `name` identifikuje parametr funkce, který přijímá obsah zprávy ve frontě. Název fronty, která se má monitorovat, je `queueName`a připojovací řetězec je v nastavení aplikace identifikovaném `connection`.

Druhým prvkem v poli `bindings` je výstupní vazba Azure Table Storage. Vlastnosti `type` a `direction` identifikují vazbu. Vlastnost `name` určuje, jak funkce poskytuje nový řádek tabulky, v tomto případě pomocí návratové hodnoty funkce. Název tabulky je v `tableName`a připojovací řetězec je v nastavení aplikace identifikovaném `connection`.

Pokud chcete zobrazit a upravit obsah *Function. JSON* v Azure Portal, klikněte na možnost **Rozšířený editor** na kartě **integrace** vaší funkce.

> [!NOTE]
> Hodnota `connection` je název nastavení aplikace, které obsahuje připojovací řetězec, ne samotný připojovací řetězec. Vazby používají připojovací řetězce uložené v nastavení aplikace k vykonání osvědčených postupů, které *Function. JSON* neobsahuje tajné klíče služby.

## <a name="c-script-example"></a>C#Příklad skriptu

Tady je C# kód skriptu, který funguje s touto triggerem a vazbou. Všimněte si, že název parametru, který poskytuje obsah zprávy ve frontě, je `order`. Tento název je povinný, protože hodnota vlastnosti `name` v *Function. JSON* je `order` 

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

Stejný soubor *Functions. JSON* lze použít s funkcí JavaScriptu:

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

V knihovně tříd jsou stejné informace triggerů a vazeb &mdash; front a názvů tabulek, účty úložiště, parametry funkce pro vstupní a výstupní &mdash; jsou poskytovány atributy namísto souboru Function. JSON. Tady je příklad:

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

Teď máte pracovní funkci, která se aktivuje ve frontě Azure a produkuje data do služby Azure Table Storage.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Azure Functions vzorů výrazů vazeb](./functions-bindings-expressions-patterns.md)
