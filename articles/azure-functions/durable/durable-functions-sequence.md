---
title: Řetězení funkcí v Durable Functions – Azure
description: Naučte se, jak spustit ukázku Durable Functions, která spouští sekvenci funkcí.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: ee5b18ddc734335ddac2a7d3352de0e4388f445d
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933253"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Řetězení funkcí v ukázce sekvence Durable Functions-Hello

Řetězení funkcí odkazuje na vzor spouštění sekvence funkcí v určitém pořadí. Výstup jedné funkce se často musí použít na vstup jiné funkce. Tento článek popisuje sekvenci zřetězení, kterou vytvoříte při dokončování Durable Functions rychlý Start ([C#](durable-functions-create-first-csharp.md) nebo [JavaScript](quickstart-js-vscode.md)). Další informace o Durable Functions najdete v tématu [Durable Functions Overview](durable-functions-overview.md).

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>Funkce

Tento článek vysvětluje následující funkce v ukázkové aplikaci:

* `E1_HelloSequence`: Funkce nástroje Orchestrator, která `E1_SayHello` v posloupnosti volá více než jednou. Ukládá výstupy z `E1_SayHello` volání a zaznamenává výsledky.
* `E1_SayHello`: Funkce aktivity, která předřadí řetězec s řetězcem "Hello".

Následující části popisují konfiguraci a kód, který se používá ke C# skriptování a JavaScriptu. Kód pro vývoj v aplikaci Visual Studio se zobrazí na konci článku.

> [!NOTE]
> Durable Functions JavaScriptu jsou k dispozici pouze pro modul runtime Functions 2. x.

## <a name="e1_hellosequence"></a>E1_HelloSequence

### <a name="functionjson-file"></a>soubor Function. JSON

Pokud používáte Visual Studio Code nebo Azure Portal pro vývoj, tady je obsah souboru *Functions. JSON* pro funkci Orchestrator. Většina souborů *. JSON funkcí* Orchestrator vypadá téměř přesně takto.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/function.json)]

Důležitou věcí je `orchestrationTrigger` typ vazby. Všechny funkce nástroje Orchestrator musí používat tento typ aktivační události.

> [!WARNING]
> Chcete-li přidržet pravidlo "žádné I/O" funkcí nástroje Orchestrator, při použití `orchestrationTrigger` vazby triggeru nepoužívejte žádné vstupní ani výstupní vazby.  Pokud jsou vyžadovány jiné vstupní nebo výstupní vazby, měly by být použity místo toho v kontextu `activityTrigger` funkcí, které jsou volány nástrojem Orchestrator.

### <a name="c-script-visual-studio-code-and-azure-portal-sample-code"></a>C#skript (ukázkový kód Visual Studio Code a Azure Portal)

Zde je zdrojový kód:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/run.csx)]

Všechny C# funkce orchestrace musí mít parametr typu `DurableOrchestrationContext`, `Microsoft.Azure.WebJobs.Extensions.DurableTask` který existuje v sestavení. Pokud používáte C# skript, sestavení lze odkazovat pomocí `#r` zápisu. Tento kontextový objekt umožňuje volat jiné funkce *aktivity* a předat vstupní parametry pomocí své [CallActivityAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_) metody.

Kód volá `E1_SayHello` tři časy v pořadí s různými hodnotami parametrů. Návratová hodnota každého volání je přidána do `outputs` seznamu, který je vrácen na konci funkce.

### <a name="javascript"></a>JavaScript

Zde je zdrojový kód:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Všechny funkce orchestrace JavaScriptu musí obsahovat [ `durable-functions` modul](https://www.npmjs.com/package/durable-functions). Toto je knihovna, která umožňuje psát Durable Functions v jazyce JavaScript. Existují tři významné rozdíly mezi funkcí Orchestrace a dalšími funkcemi jazyka JavaScript:

1. Funkce je generátorová [funkce.](https://docs.microsoft.com/scripting/javascript/advanced/iterators-and-generators-javascript)
2. Funkce je zabalena do volání `durable-functions` `orchestrator` metody modulu (zde `df`).
3. Funkce musí být synchronní. Protože metoda ' Orchestrator ' zpracovává volání ' Context. hotov ', funkce by měla jednoduše ' Return '.

Objekt obsahuje objekt, který umožňuje volat jiné funkce *aktivity* a předat vstupní parametry pomocí své `callActivity` metody. `df` `context` Kód volá `E1_SayHello` třikrát tři časy v pořadí s různými hodnotami parametrů, které `yield` používají k určení, že by mělo být spuštěno volání funkce asynchronní aktivity, které se mají vrátit. Návratová hodnota každého volání je přidána do `outputs` seznamu, který je vrácen na konci funkce.

## <a name="e1_sayhello"></a>E1_SayHello

### <a name="functionjson-file"></a>soubor Function. JSON

Soubor *Function. JSON* pro funkci `E1_SayHello` Activity je `E1_HelloSequence` podobný tomu s tím rozdílem, že `orchestrationTrigger` používá `activityTrigger` typ vazby místo typu vazby.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

> [!NOTE]
> Každá funkce, která je volána funkcí orchestrace, musí `activityTrigger` používat vazbu.

Implementace `E1_SayHello` je poměrně operace formátování řetězce s jednoduchým řetězcem.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_SayHello/run.csx)]

Tato funkce má parametr typu [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html), který používá k získání vstupu, který byl předán voláním funkce Orchestrator do [`CallActivityAsync<T>`](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_).

### <a name="javascript"></a>JavaScript

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

Na rozdíl od funkce orchestrace JavaScriptu nepotřebuje funkce aktivity žádné speciální nastavení. Vstup předaný do něj funkce Orchestrator je umístěn na `context.bindings` objektu pod názvem `activityTrigger` vazby `context.bindings.name`– v tomto případě. Název vazby lze nastavit jako parametr exportované funkce a získat k nim přímý pøístup, což je to, co dělá vzorový kód.

## <a name="run-the-sample"></a>Spuštění ukázky

Chcete-li `E1_HelloSequence` spustit orchestraci, odešlete následující požadavek HTTP POST.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> Předchozí fragment kódu http předpokládá, že v `host.json` souboru je položka, která odebere výchozí `api/` předponu ze všech adres URL funkcí triggeru protokolu HTTP. Značky pro tuto konfiguraci najdete v `host.json` souboru v ukázkách.

Pokud například spustíte ukázku ve Function App s názvem "myfunctionapp", nahraďte "{host}" myfunctionapp.azurewebsites.net ".

Výsledkem je odpověď HTTP 202, jako je to (oříznuto pro zkrácení):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

V tomto okamžiku se orchestrace zařadí do fronty a spustí se okamžitě. Adresu URL v `Location` hlavičce lze použít ke kontrole stavu provedení.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

Výsledkem je stav orchestrace. Rychle běží a dokončuje se, takže se zobrazí v *dokončeném* stavu s odpovědí, která bude vypadat (oříznuté pro zkrácení):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Jak vidíte, `runtimeStatus` instance je `output` *dokončena* a obsahuje výsledek serializace funkce Orchestrator ve formátu JSON.

> [!NOTE]
> Koncový bod HTTP POST, který spustil funkci Orchestrator, se implementuje v ukázkové aplikaci jako aktivační funkce HTTP s názvem "HttpStart". Můžete implementovat podobnou počáteční logiku pro jiné typy triggerů, `queueTrigger` `eventHubTrigger`například, nebo `timerTrigger`.

Podívejte se na protokoly spuštění funkce. Funkce byla spuštěna a několikrát dokončena z důvodu chování opakovaného přehrávání popsaných v tématu [spolehlivost orchestrace.](durable-functions-orchestrations.md#reliability) `E1_HelloSequence` Na druhé straně bylo k dispozici pouze tři spuštění `E1_SayHello` , protože se tyto provádění této funkce nezobrazují.

## <a name="visual-studio-sample-code"></a>Vzorový kód sady Visual Studio

Toto je orchestrace jako jeden C# soubor v projektu sady Visual Studio:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

## <a name="next-steps"></a>Další kroky

Tato ukázka ukázala jednoduchou orchestraci zřetězení funkcí. Další příklad ukazuje, jak implementovat vzorek ventilátoru nebo ventilátoru.

> [!div class="nextstepaction"]
> [Spustit ukázku ventilátoru/ventilátoru](durable-functions-cloud-backup.md)
