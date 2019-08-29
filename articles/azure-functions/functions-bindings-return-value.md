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
ms.openlocfilehash: 1ea7ec0444ba80d3494afba77ad9d7fdabd5f982
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70086414"
---
# <a name="using-the-azure-function-return-value"></a>Použití návratové hodnoty funkce Azure Functions

Tento článek vysvětluje, jak vracet hodnoty fungují uvnitř funkce.

V jazycích, které mají vrácenou hodnotu, můžete svázat [výstupní vazbu](./functions-triggers-bindings.md#binding-direction) funkce s návratovou hodnotou:

* V knihovně C# tříd použijte atribut výstupní vazby pro návratovou hodnotu metody.
* V jiných jazycích nastavte `name` vlastnost v *Function. JSON* na. `$return`

Pokud existuje více výstupních vazeb, použijte vrácenou hodnotu pouze pro jednu z nich.

V C# skriptu C# a jsou `out` alternativní způsoby, jak odesílat data do výstupní vazby, parametry a [objekty kolekce](functions-reference-csharp.md#writing-multiple-output-values).

Podívejte se na příklad konkrétního jazyka ukazující použití návratové hodnoty:

* [C#](#c-example)
* [C# skript (.csx)](#c-script-example)
* [F#](#f-example)
* [JavaScript](#javascript-example)
* [Python](#python-example)

## <a name="c-example"></a>C#případě

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

## <a name="c-script-example"></a>C#Příklad skriptu

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

## <a name="f-example"></a>F#případě

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

## <a name="javascript-example"></a>Příklad JavaScriptu

Tady je výstupní vazba v souboru *Function. JSON* :

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

V jazyce JavaScript návratová hodnota přechází do druhého parametru pro `context.done`:

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

## <a name="python-example"></a>Příklad Pythonu

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

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zpracování chyb vazeb Azure Functions](./functions-bindings-errors.md)
