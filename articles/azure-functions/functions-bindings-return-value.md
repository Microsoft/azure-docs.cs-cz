---
title: Použití návratové hodnoty z funkce Azure
description: Naučte se spravovat návratové hodnoty pro Azure Functions
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 01/14/2019
ms.author: cshoe
ms.openlocfilehash: 58ee9b682bc97dc4044d811392cf4ff5b51a69fd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94491354"
---
# <a name="using-the-azure-function-return-value"></a>Použití návratové hodnoty funkce Azure Functions

Tento článek vysvětluje, jak vracet hodnoty fungují uvnitř funkce.

V jazycích, které mají vrácenou hodnotu, můžete svázat [výstupní vazbu](./functions-triggers-bindings.md#binding-direction) funkce s návratovou hodnotou:

* V knihovně tříd C# použijte výstupní vazbu atributu pro návratovou hodnotu metody.
* V jazyce Java použijte výstupní vazbu poznámky k metodě Function.
* V jiných jazycích nastavte `name` vlastnost v *function.jsna* `$return` .

Pokud existuje více výstupních vazeb, použijte vrácenou hodnotu pouze pro jednu z nich.

V jazyce C# a C# alternativní způsoby odesílání dat do výstupní vazby jsou `out` parametry a [objekty kolekce](functions-reference-csharp.md#writing-multiple-output-values).

# <a name="c"></a>[C#](#tab/csharp)

Zde je kód jazyka C#, který používá návratovou hodnotu pro výstupní vazbu následovanou asynchronním příkladem:

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

Tady je výstupní vazba v *function.js* souboru:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Tady je kód skriptu jazyka C# následovaný asynchronním příkladem:

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

# <a name="f"></a>[F#](#tab/fsharp)

Tady je výstupní vazba v *function.js* souboru:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Tady je kód F #:

```fsharp
let Run(input: WorkItem, log: ILogger) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.LogInformation(sprintf "F# script processed queue message '%s'" json)
    json
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Tady je výstupní vazba v *function.js* souboru:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

V jazyce JavaScript návratová hodnota přechází do druhého parametru pro `context.done` :

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```
# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Tady je výstupní vazba v *function.js* souboru:

```json
{
    "name": "Response",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{blobname}"
}
```

Tady je kód PowerShellu, který používá návratovou hodnotu vazby výstupu http:

```powershell
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = [HttpStatusCode]::OK
    Body = $blobname
    })
```

# <a name="python"></a>[Python](#tab/python)

Tady je výstupní vazba v *function.js* souboru:

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

# <a name="java"></a>[Java](#tab/java)

Zde je kód Java, který používá návratovou hodnotu pro výstupní vazbu:

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
> [Zpracování chyb vazeb Azure Functions](./functions-bindings-errors.md)
