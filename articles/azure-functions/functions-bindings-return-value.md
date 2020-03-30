---
title: Použití vrácené hodnoty z funkce Azure
description: Naučte se spravovat vrácené hodnoty pro funkce Azure
author: craigshoemaker
ms.topic: reference
ms.date: 01/14/2019
ms.author: cshoe
ms.openlocfilehash: 7ba104e288204dfbf3d24f5783bf69682a286553
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74480572"
---
# <a name="using-the-azure-function-return-value"></a>Použití vrácené hodnoty funkce Azure

Tento článek vysvětluje, jak vrácené hodnoty fungují uvnitř funkce.

V jazycích, které mají vrácenou hodnotu, můžete vázat [vazbu výstupu](./functions-triggers-bindings.md#binding-direction) funkce na vrácenou hodnotu:

* V knihovně tříd jazyka C# aplikujte atribut výstupní vazby na vrácenou hodnotu metody.
* V jazyce Java použijte anotaci výstupní vazby na metodu funkce.
* V jiných jazycích `name` nastavte vlastnost *function.json* na `$return`.

Pokud existuje více výstupních vazeb, použijte vrácenou hodnotu pouze pro jednu z nich.

Ve skriptu C# a C# jsou `out` alternativní způsoby odesílání dat do výstupní vazby parametry a [objekty kolekcí](functions-reference-csharp.md#writing-multiple-output-values).

# <a name="c"></a>[C #](#tab/csharp)

Zde je kód Jazyka C#, který používá vrácenou hodnotu pro výstupní vazbu, následovaný asynchronním příkladem:

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

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Zde je výstupní vazba v souboru *function.json:*

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Tady je kód skriptu C#, následovaný asynchronním příkladem:

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

# <a name="f"></a>[F #](#tab/fsharp)

Zde je výstupní vazba v souboru *function.json:*

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Zde je kód F#:

```fsharp
let Run(input: WorkItem, log: ILogger) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.LogInformation(sprintf "F# script processed queue message '%s'" json)
    json
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

Zde je výstupní vazba v souboru *function.json:*

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

V jazyce JavaScript přejde vrácená `context.done`hodnota do druhého parametru pro :

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

# <a name="python"></a>[Python](#tab/python)

Zde je výstupní vazba v souboru *function.json:*

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```
Zde je kód Pythonu:

```python
def main(input: azure.functions.InputStream) -> str:
    return json.dumps({
        'name': input.name,
        'length': input.length,
        'content': input.read().decode('utf-8')
    })
```

# <a name="java"></a>[Java](#tab/java)

Tady je java kód, který používá vrácenou hodnotu pro výstupní vazbu:

```java
@FunctionName("QueueTrigger")
@StorageAccount("AzureWebJobsStorage")
@BlobOutput(name = "output", path = "output-container/{id}")
public static String run(
  @QueueTrigger(name = "input", queueName = "inputqueue") WorkItem input,
  final ExecutionContext context
) {
  String json = String.format("{ \"id\": \"%s\" }", input.id);
  context.getLogger().info("Java processed queue message. Item=" + json);
  return json;
}
```

---

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zpracování chyb vazby Azure Functions](./functions-bindings-errors.md)
