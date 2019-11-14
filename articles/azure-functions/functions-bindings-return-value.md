---
title: Použití návratové hodnoty z funkce Azure
description: Naučte se spravovat návratové hodnoty pro Azure Functions
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: reference
ms.date: 01/14/2019
ms.author: cshoe
ms.openlocfilehash: 8dd5a4d9d869c879ed402c5450690f0a691e1d2c
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074402"
---
# <a name="using-the-azure-function-return-value"></a>Použití návratové hodnoty funkce Azure Functions

Tento článek vysvětluje, jak vracet hodnoty fungují uvnitř funkce.

V jazycích, které mají vrácenou hodnotu, můžete svázat [výstupní vazbu](./functions-triggers-bindings.md#binding-direction) funkce s návratovou hodnotou:

* V knihovně C# tříd použijte atribut výstupní vazby pro návratovou hodnotu metody.
* V jiných jazycích nastavte vlastnost `name` v *Function. JSON* na `$return`.

Pokud existuje více výstupních vazeb, použijte vrácenou hodnotu pouze pro jednu z nich.

V C# skriptu C# a jsou alternativní způsoby odesílání dat do výstupní vazby `out` parametry a [objekty kolekce](functions-reference-csharp.md#writing-multiple-output-values).

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Zde je C# kód, který používá návratovou hodnotu pro výstupní vazbu následovanou asynchronním příkladem:

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static string Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static Task<string> Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Pravidel](#tab/csharp-script)

Tady je výstupní vazba v souboru *Function. JSON* :

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Zde je kód C# skriptu následovaný asynchronním příkladem:

```cs
public static string Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
public static Task<string> Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

# <a name="ftabfsharp"></a>[F#](#tab/fsharp)

Tady je výstupní vazba v souboru *Function. JSON* :

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Tady je F# kódu:

```fsharp
let Run(input: WorkItem, log: ILogger) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.LogInformation(sprintf "F# script processed queue message '%s'" json)
    json
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Tady je výstupní vazba v souboru *Function. JSON* :

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

V jazyce JavaScript vrátí návratová hodnota ve druhém parametru pro `context.done`:

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Tady je výstupní vazba v souboru *Function. JSON* :

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```
Tady je kód Pythonu:

```python
def main(input: azure.functions.InputStream) -> str:
    return json.dumps({
        'name': input.name,
        'length': input.length,
        'content': input.read().decode('utf-8')
    })
```

---

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zpracování chyb vazeb Azure Functions](./functions-bindings-errors.md)
