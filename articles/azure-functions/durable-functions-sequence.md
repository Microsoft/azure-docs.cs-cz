---
title: Funkce řetězení v Durable Functions – Azure
description: Zjistěte, jak ke spuštění ukázky Durable Functions, která postupně provede sekvenci funkce.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: azfuncdf
ms.openlocfilehash: c84977dacddcf9ccca7fde735ad4acb8a1523fa9
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44378699"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Funkce řetězení v Durable Functions – ukázka Hello pořadí

Funkce řetězení odkazuje na model provedení pořadí funkcí v určitém pořadí. Často výstup jedné funkce je potřeba použít se vstupem jiné funkce. Tento článek vysvětluje, ukázky, která používá [Durable Functions](durable-functions-overview.md) k implementaci funkce řetězení.

## <a name="prerequisites"></a>Požadavky

* [Nainstalujte Durable Functions](durable-functions-install.md).

## <a name="the-functions"></a>Funkce

Tento článek vysvětluje následující funkce v ukázkové aplikaci:

* `E1_HelloSequence`: Funkce orchestrátoru, který volá `E1_SayHello` více než jednou v sekvenci. Uloží výstup z `E1_SayHello` volá a zaznamená výsledky.
* `E1_SayHello`: Aktivita funkce, která připojí řetězec "Hello".

Následující části popisují konfiguraci a kód, který se používají pro skriptovací C# a JavaScript. Kód pro vývoj sady Visual Studio se zobrazí na konci tohoto článku.

> [!NOTE]
> Odolná služba Functions je dostupná v jazyce JavaScript ve v2 modul runtime služby Functions pouze.

## <a name="e1hellosequence"></a>E1_HelloSequence
### <a name="functionjson-file"></a>soubor Function.JSON

Pokud používáte Visual Studio Code nebo na webu Azure portal pro vývoj, tady je obsah *function.json* souboru pro funkci nástroje orchestrator. Většina orchestrator *function.json* soubory vypadat například takto téměř úplně stejné.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/function.json)]

Důležité je `orchestrationTrigger` typ vazby. Všechny funkce produktu orchestrator musíte použít tento typ aktivační události.

> [!WARNING]
> Budete dodržovat pravidla "bez vstupně-výstupní operace" funkcí nástroje orchestrator, nechcete používat žádné vstupní nebo výstupní vazby při použití `orchestrationTrigger` aktivovat vazby.  Pokud dalších vstupních nebo výstupních vazeb jsou potřeba, by měl místo toho se používají v rámci `activityTrigger` funkce, které jsou volány orchestrátor.

### <a name="c-script-visual-studio-code-and-azure-portal-sample-code"></a>Skript jazyka C# (Visual Studio Code a Azure portal ukázkový kód) 

Zde je zdrojový kód:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/run.csx)]

Všechny funkce Orchestrace C# musí mít parametr typu `DurableOrchestrationContext`, která existuje v `Microsoft.Azure.WebJobs.Extensions.DurableTask` sestavení. Pokud používáte skript jazyka C#, sestavení může být odkazováno pomocí `#r` zápis. Tento objekt context umožňuje volat jiné *aktivity* funkce a předání vstupních parametrů pomocí jeho [CallActivityAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_) metody.

Kód volá `E1_SayHello` třikrát v sekvenci s různými hodnotami parametrů. Vrácené hodnoty každého volání je přidán do `outputs` seznam, který je vrácen na konci funkce.

### <a name="javascript"></a>JavaScript

Zde je zdrojový kód:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Musí obsahovat všechny funkce Orchestrace jazyka JavaScript `durable-functions` modulu. Toto je knihovna JavaScript, která převede funkci Orchestrace akce do protokolu spuštění Durable v režimu out-of-proc jazyků. Existují tři významné rozdíly mezi funkce orchestraci a jiné funkce jazyka JavaScript:

1. Funkce [generátoru funkcí.](https://docs.microsoft.com/scripting/javascript/advanced/iterators-and-generators-javascript)
2. Funkce je zabalený ve volání `durable-functions` modulu (zde `df`).
3. Funkce končí voláním `return`, nikoli `context.done`.

`context` Objekt obsahuje `df` objektu umožňuje volat jiné *aktivity* funkce a předání vstupních parametrů pomocí jeho `callActivityAsync` metoda. Kód volá `E1_SayHello` třikrát v sekvenci s různými hodnotami parametrů, pomocí `yield` k označení spuštění by měl čekat na volání funkce asynchronní aktivity se mají vrátit. Vrácené hodnoty každého volání je přidán do `outputs` seznam, který je vrácen na konci funkce.

## <a name="e1sayhello"></a>E1_SayHello
### <a name="functionjson-file"></a>soubor Function.JSON

*Function.json* soubor pro aktivitu funkce `E1_SayHello` je podobná `E1_HelloSequence` s tím rozdílem, že používá `activityTrigger` vazby typu, nikoli `orchestrationTrigger` typ vazby.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

> [!NOTE]
> Musíte použít všechny funkce volané funkce protokolem Orchestrace `activityTrigger` vazby.

Provádění `E1_SayHello` je relativně jednoduché řetězec operace formátování.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_SayHello/run.csx)]

Tato funkce má parametr typu [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html), kterou používá k získání vstupu, který byl předán voláním funkce orchestrátoru [ `CallActivityAsync<T>` ](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_).

### <a name="javascript"></a>JavaScript

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

Na rozdíl od Orchestrace funkce JavaScriptu musí aktivita funkce jazyka JavaScript žádné speciální instalační program. Vstup předaný funkci orchestrator je umístěn na `context.bindings` objekt pod názvem `activitytrigger` vazby – v takovém případě `context.bindings.name`. Název vazby, jenž mohou být nastaveny jako parametr exportované funkce a k nim přistupuje přímo, což je, co dělá ukázkový kód.

## <a name="run-the-sample"></a>Spuštění ukázky

Ke spuštění `E1_HelloSequence` Orchestrace, odeslat požadavek následující HTTP POST.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> Předchozím fragmentu kódu HTTP předpokládá existuje položka v `host.json` souboru, který odebere výchozí `api/` předpony ze všech URL funkce triggeru HTTP. Značky můžete najít v této konfiguraci `host.json` soubor ve vzorcích.

Například pokud používáte ukázku v aplikaci function app s názvem "myfunctionapp", nahraďte "myfunctionapp.azurewebsites.net" "{hostitele}".

Výsledkem je odpověď HTTP 202 takto (oříznut pro zkrácení):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

V tomto okamžiku orchestraci je ve frontě a začne okamžitě spustit. Adresa URL v `Location` záhlaví lze použít ke kontrole stavu spuštění.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

Výsledkem je stav orchestraci. Spuštění a dokončení rychlé, takže se zobrazí v *dokončeno* stavu odpovědí, který vypadá takto (oříznut pro zkrácení):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Jak je vidět, `runtimeStatus` instance je *dokončeno* a `output` obsahuje výsledek spuštění funkce orchestrátoru serializací JSON.

> [!NOTE]
> Koncový bod HTTP POST, který spustil funkce orchestrátoru je implementována v ukázkové aplikaci jako HTTP aktivuje funkci s názvem "HttpStart". Můžete implementovat podobné starter logiku pro další typy triggerů, jako je třeba `queueTrigger`, `eventHubTrigger`, nebo `timerTrigger`.

Podívejte se na protokoly spuštění funkce. `E1_HelloSequence` Funkce spuštěno a dokončeno více než jednou z důvodu chování opakování podle [přehled](durable-functions-overview.md). Na druhé straně, došlo jenom tři prováděných `E1_SayHello` od provádění těchto funkcí není získat znovu přehrát.

## <a name="visual-studio-sample-code"></a>Visual Studio ukázkový kód

Tady je Orchestrace jako jeden soubor jazyka C# v sadě Visual Studio projekt:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

## <a name="next-steps"></a>Další postup

Tato ukázka vám ukázal, jednoduché funkce řetězení Orchestrace. Další příklad ukazuje, jak implementovat fan odesílací/fan v vzor. 

> [!div class="nextstepaction"]
> [Spustit Fan odesílací/fan v ukázce](durable-functions-cloud-backup.md)
