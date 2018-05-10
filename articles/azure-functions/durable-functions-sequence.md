---
title: Funkce řetězení trvanlivý funkcí – Azure
description: Zjistěte, jak spustit ukázku trvanlivý funkce, která postupně provede sekvenci funkce.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/19/2018
ms.author: azfuncdf
ms.openlocfilehash: e53b38bf336816ca670fad3ab70a43e5cc8b3437
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Funkce řetězení v trvanlivý funkce – ukázka pořadí Hello

Funkce řetězení odkazuje na vzor provádění pořadí funkcí v určitém pořadí. Výstup jednu funkci často potřebuje má být použita pro vstup jinou funkci. Tento článek vysvětluje vzorku, který používá [trvanlivý funkce](durable-functions-overview.md) implementovat řetězení funkce.

## <a name="prerequisites"></a>Požadavky

* [Nainstalujte trvanlivý funkce](durable-functions-install.md).

## <a name="the-functions"></a>Funkce

Tento článek vysvětluje v ukázkové aplikace následující funkce:

* `E1_HelloSequence`Funkce: orchestrator, který volá `E1_SayHello` vícekrát v pořadí. Ukládají se výstup z `E1_SayHello` volá a zaznamenává výsledky.
* `E1_SayHello`: Aktivita funkce, která přidá řetězec s text "Hello".

Následující části popisují konfiguraci a kódu, které se používají pro C# skriptování a JavaScript. Kód pro vývoj v sadě Visual Studio se zobrazí na konci tohoto článku.

> [!NOTE]
> Trvanlivý funkce je k dispozici v jazyce JavaScript v pouze runtime Functions v2.

## <a name="e1hellosequence"></a>E1_HelloSequence
### <a name="functionjson-file"></a>soubor Function.JSON

Pokud používáte Visual Studio Code nebo portál Azure pro vývoj, zde je obsah *function.json* soubor pro funkci orchestrator. Většina orchestrator *function.json* soubory vypadat například takto téměř úplně stejné.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/function.json)]

Je důležité si `orchestrationTrigger` typ vazby. Tento typ aktivační události musí používat všechny funkce produktu orchestrator.

> [!WARNING]
> S dodržováním pravidlem "žádné vstupně-výstupních operací" orchestrator funkcí, nechcete používat žádný vstup nebo výstup vazby při použití `orchestrationTrigger` aktivovat vazby.  Pokud další vstupní nebo výstupní vazby jsou potřeba, by měl místo toho používají v rámci `activityTrigger` funkce, které se nazývají nástrojem orchestrator.

### <a name="c-script-visual-studio-code-and-azure-portal-sample-code"></a>C# skript (portálu ukázkový kód pro Visual Studio Code a Azure) 

Tady je zdrojový kód:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/run.csx)]

Všechny funkce orchestration C# musí mít parametr typu `DurableOrchestrationContext`, která existuje v `Microsoft.Azure.WebJobs.Extensions.DurableTask` sestavení. Pokud používáte skript jazyka C#, sestavení, můžete odkazovat pomocí `#r` zápis. Tento objekt kontextu umožňuje volat jiné *aktivity* funkce a předejte vstupní parametry pomocí jeho [CallActivityAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_) metoda.

Volání kódu `E1_SayHello` třikrát v sekvenci s jiným parametrem hodnoty. Návratovou hodnotu volání je přidán do `outputs` seznam, který je vrácen na konci funkce.

### <a name="javascript"></a>JavaScript

Tady je zdrojový kód:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Musí zahrnovat všechny funkce jazyka JavaScript orchestration `durable-functions` modulu. Toto je knihovna JavaScript, který překládá funkce orchestration akce do protokolu spuštění je trvale out-of-proc jazyků. Existují tři významné rozdíly mezi funkce orchestration a další funkce jazyka JavaScript:

1. Funkce [generátor funkce.](https://docs.microsoft.com/en-us/scripting/javascript/advanced/iterators-and-generators-javascript)
2. Funkce je uzavřen do volání `durable-functions` modulu (zde `df`).
3. Funkce končí voláním `return`, nikoli `context.done`.

`context` Objekt obsahuje `df` objektu umožňuje volat jiné *aktivity* funkce a předejte vstupní parametry pomocí jeho `callActivityAsync` metoda. Volání kódu `E1_SayHello` třikrát v sekvenci s jiným parametrem hodnoty, pomocí `yield` k označení spuštění by měl čekání na asynchronní volání funkce aktivita má být vrácen. Návratovou hodnotu volání je přidán do `outputs` seznam, který je vrácen na konci funkce.

## <a name="e1sayhello"></a>E1_SayHello
### <a name="functionjson-file"></a>soubor Function.JSON

*Function.json* soubor pro aktivitu funkce `E1_SayHello` je podobná `E1_HelloSequence` s tím rozdílem, že se používá `activityTrigger` vazby typu, nikoli `orchestrationTrigger` typ vazby.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

> [!NOTE]
> Musíte použít všechny funkce volá funkce orchestration `activityTrigger` vazby.

Implementace `E1_SayHello` je poměrně trivial řetězec formátování operaci.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_SayHello/run.csx)]

Tato funkce obsahuje parametr typu [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html), která je používá k získání vstupu, který byl předán voláním funkce orchestrator [ `CallActivityAsync<T>` ](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_).

### <a name="javascript"></a>JavaScript

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

Na rozdíl od funkce jazyka JavaScript orchestration musí aktivita funkce jazyka JavaScript žádné speciální nastavení. Vstup předaný funkci orchestrator se nachází na `context.bindings` objektu pod názvem `activitytrigger` vazby – v takovém případě `context.bindings.name`. Název vazby mohou být nastaveny jako parametr exportované funkce a k nim přistupuje přímo, což je jaké ukázkový kód.

## <a name="run-the-sample"></a>Spuštění ukázky

Spuštění `E1_HelloSequence` orchestration, odeslat požadavek na následující HTTP POST.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

Například pokud používáte ukázku v aplikaci funkce s názvem "myfunctionapp", nahraďte "myfunctionapp.azurewebsites.net" "{hostitel}".

Výsledkem je odpověď HTTP 202 takto (oříznut jako stručný výtah):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

V tomto okamžiku orchestration je zařazen do fronty a začne spustit okamžitě. Adresu URL v `Location` záhlaví lze použít ke kontrole stavu spuštění.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

Výsledkem je, stav orchestration. Ho běží a ukončuje se rychle, tak, aby ho zobrazila *dokončeno* stavu s odpovědí, který bude vypadat takto (oříznut jako stručný výtah):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Jak je vidět `runtimeStatus` instance je *dokončeno* a `output` obsahuje serializací JSON výsledek spuštění funkce produktu orchestrator.

> [!NOTE]
> Koncový bod HTTP POST, který spuštění funkce orchestrator je implementovaná v ukázkové aplikace jako HTTP aktivovat funkci s názvem "HttpStart". Můžete implementovat podobné starter logiku pro jiné typy aktivační události, jako je třeba `queueTrigger`, `eventHubTrigger`, nebo `timerTrigger`.

Podívejte se na protokoly spuštění funkce. `E1_HelloSequence` Funkce spuštěno a dokončeno vícekrát z důvodu opětovného přehrání problém popsaný v [přehled](durable-functions-overview.md). Na druhé straně nebyly jenom tři spuštěních z `E1_SayHello` vzhledem k tomu, že tyto funkce spuštěních získat přehrány není.

## <a name="visual-studio-sample-code"></a>Visual Studio ukázkový kód

Tady je orchestration jako jeden soubor jazyka C# v projektu sady Visual Studio:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

## <a name="next-steps"></a>Další postup

Tato ukázka vám ukázal jednoduché řetězení funkce orchestration. Další příklad ukazuje, jak implementovat fan odesílacího/fan v vzor. 

> [!div class="nextstepaction"]
> [Spustit Fan odesílacího/fan v ukázce](durable-functions-cloud-backup.md)
