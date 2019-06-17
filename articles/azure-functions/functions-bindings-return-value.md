---
title: Používat návratovou hodnotu z funkce Azure functions
description: Naučte se spravovat návratové hodnoty pro službu Azure Functions
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 01/14/2019
ms.author: cshoe
ms.openlocfilehash: 4ccfe192eaea94cb9b199bd3c6f0bdacf1685519
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "61437714"
---
# <a name="using-the-azure-function-return-value"></a>Používat návratovou hodnotu funkce Azure

Tento článek vysvětluje, jak vrátit hodnoty pracovní uvnitř funkce.

V jazycích, které mají návratovou hodnotu, můžete vytvořit vazbu funkce [výstupní vazby](./functions-triggers-bindings.md#binding-direction) návratovou hodnotu:

* V knihovně tříd jazyka C# použijte atribut vazby výstupu pro návratovou hodnotu metody.
* V jiných jazycích, nastavte `name` vlastnost *function.json* k `$return`.

Pokud existuje více výstupní vazby, použijte vrácenou hodnotu pouze pro jeden z nich.

V jazyce C# a skript jazyka C#, jsou alternativní způsoby, jak odesílat data do výstupní vazbu `out` parametry a [kolekcí objektů](functions-reference-csharp.md#writing-multiple-output-values).

Podívejte se na konkrétní jazyk příklad znázorňující použití návratovou hodnotu:

* [C#](#c-example)
* [C# skript (.csx)](#c-script-example)
* [F#](#f-example)
* [JavaScript](#javascript-example)
* [Python](#python-example)

## <a name="c-example"></a>Příklad jazyka C#

Tady je kód jazyka C#, který používá vrácenou hodnotu pro výstupní vazby, za nímž následuje příklad asynchronní:

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

## <a name="c-script-example"></a>Ukázkový skript jazyka C#

Zde je výstupní vazbu v *function.json* souboru:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Tady je C# kód skriptu, za nímž následuje příklad asynchronní:

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

## <a name="f-example"></a>F#Příklad

Zde je výstupní vazbu v *function.json* souboru:

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

## <a name="javascript-example"></a>Příklad v jazyce JavaScript

Zde je výstupní vazbu v *function.json* souboru:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

V jazyce JavaScript, návratová hodnota přejde ve druhém parametru pro `context.done`:

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

## <a name="python-example"></a>Příklad v Pythonu

Zde je výstupní vazbu v *function.json* souboru:

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

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zpracování chyb vazeb Azure Functions](./functions-bindings-errors.md)
