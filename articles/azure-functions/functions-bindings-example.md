---
title: Příklad aktivační události a vazby Azure Functions
description: Naučte se konfigurovat vazby funkce Azure functions
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 8372becd614e35bead9e98e405e2694f3f16db4a
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66472329"
---
# <a name="azure-functions-trigger-and-binding-example"></a>Příklad aktivační události a vazby Azure Functions

Tento článek ukazuje, jak nakonfigurovat [aktivační událost a vazby](./functions-triggers-bindings.md) ve funkci Azure functions.

Předpokládejme, že chcete zadat nový řádek do služby Azure Table storage pokaždé, když se v Azure Queue storage se objeví nová zpráva. Tento scénář je možné implementovat pomocí Azure Queue storage triggeru a Azure Table storage výstupní vazby. 

Tady je *function.json* souboru pro tento scénář. 

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

První prvek `bindings` pole je aktivační událost fronty úložiště. `type` a `direction` vlastnosti identifikovat aktivační událost. `name` Vlastnost identifikuje parametr funkce, která přijímá obsah zprávy fronty. Název fronty k monitorování se `queueName`, a připojovací řetězec je v nastavení aplikace, které jsou identifikované `connection`.

Na druhý prvek `bindings` pole je služba Azure Table Storage výstupní vazbu. `type` a `direction` vlastnosti identifikovat vazby. `name` Vlastnost určuje, jak funkce poskytuje řádku nové tabulky, v tomto případě pomocí funkce vrátí hodnotu. Název tabulky se `tableName`, a připojovací řetězec je v nastavení aplikace, které jsou identifikované `connection`.

K zobrazení a úpravám obsahu *function.json* na webu Azure Portal, klikněte na tlačítko **Rozšířený editor** možnost **integrace** kartu vaší funkce.

> [!NOTE]
> Hodnota `connection` je název nastavení aplikace, které obsahuje připojovací řetězec, ne samotný řetězec připojení. Vazby připojení řetězce uložené v nastavení aplikace k vynucení nejlepší cvičení, která *function.json* neobsahuje tajných kódů služby.

## <a name="c-script-example"></a>Ukázkový skript jazyka C#

Tady je kód jazyka C# skript, který funguje s této aktivační události a vazby. Všimněte si, že je název parametru, která poskytuje obsah zprávy fronty `order`; tento název je povinný, protože `name` hodnoty vlastností v *function.json* je `order` 

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

## <a name="javascript-example"></a>Příklad v jazyce JavaScript

Stejné *function.json* souboru je možné pomocí funkce JavaScriptu:

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

Knihovna tříd, jedna aktivační událost a informace o vazbě &mdash; názvu tabulky a fronty, účty úložiště, funkce parametry pro vstup a výstup &mdash; poskytuje atributech namísto v souboru function.json. Tady je příklad:

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

Teď máte funkční funkce, která se aktivuje ve frontě Azure a odesílá výstupní data do služby Azure Table storage.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Služba Azure Functions vazby vzory výrazů](./functions-bindings-expressions-patterns.md)
