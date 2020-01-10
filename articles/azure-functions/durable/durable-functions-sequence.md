---
title: Řetězení funkcí v Durable Functions – Azure
description: Naučte se, jak spustit ukázku Durable Functions, která spouští sekvenci funkcí.
author: cgillum
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: de2fd1a46d931c5d1b625094940a981509bf1488
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769553"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Řetězení funkcí v ukázce sekvence Durable Functions-Hello

Řetězení funkcí odkazuje na vzor spouštění sekvence funkcí v určitém pořadí. Výstup jedné funkce se často musí použít na vstup jiné funkce. Tento článek popisuje sekvenci zřetězení, kterou vytvoříte při dokončování Durable Functions rychlý Start ([C#](durable-functions-create-first-csharp.md) nebo [JavaScript](quickstart-js-vscode.md)). Další informace o Durable Functions najdete v tématu [Durable Functions Overview](durable-functions-overview.md).

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>Funkce

Tento článek vysvětluje následující funkce v ukázkové aplikaci:

* `E1_HelloSequence`: funkce Orchestrator, která volá `E1_SayHello` několikrát v sekvenci. Ukládá výstupy z `E1_SayHello` volání a zaznamenává výsledky.
* `E1_SayHello`: funkce Activity, která do řetězce přiřadí řetězec "Hello".

Následující části vysvětlují konfiguraci a kód, který se používá C# pro skriptování a JavaScript. Kód pro vývoj v aplikaci Visual Studio se zobrazí na konci článku.

> [!NOTE]
> Durable Functions JavaScriptu jsou k dispozici pouze pro modul runtime Functions 2,0.

## <a name="e1_hellosequence"></a>E1_HelloSequence

### <a name="functionjson-file"></a>soubor Function. JSON

Pokud používáte Visual Studio Code nebo Azure Portal pro vývoj, tady je obsah souboru *Functions. JSON* pro funkci Orchestrator. Většina souborů *. JSON funkcí* Orchestrator vypadá téměř přesně takto.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/function.json)]

Důležitou věc je `orchestrationTrigger` typ vazby. Všechny funkce nástroje Orchestrator musí používat tento typ aktivační události.

> [!WARNING]
> Chcete-li dodržovat pravidlo "žádné I/O" funkcí nástroje Orchestrator, nepoužívejte při použití vazby triggeru `orchestrationTrigger` žádné vstupní ani výstupní vazby.  Pokud jsou vyžadovány jiné vstupní nebo výstupní vazby, měly by být použity místo toho v kontextu funkcí `activityTrigger`, které jsou volány nástrojem Orchestrator. Další informace naleznete v článku o [omezeních kódu funkce nástroje Orchestrator](durable-functions-code-constraints.md) .

### <a name="c-script-visual-studio-code-and-azure-portal-sample-code"></a>C#skript (ukázkový kód Visual Studio Code a Azure Portal)

Zde je zdrojový kód:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/run.csx)]

Všechny C# funkce orchestrace musí mít parametr typu `DurableOrchestrationContext`, který existuje v sestavení `Microsoft.Azure.WebJobs.Extensions.DurableTask`. Pokud používáte C# skript, sestavení lze odkazovat pomocí `#r`ho zápisu. Tento kontextový objekt umožňuje volat jiné funkce *aktivity* a předat vstupní parametry pomocí své `CallActivityAsync` metody.

Kód volá `E1_SayHello` třikrát v sekvenci s různými hodnotami parametrů. Návratová hodnota každého volání je přidána do seznamu `outputs`, který je vrácen na konci funkce.

### <a name="javascript"></a>JavaScript

Zde je zdrojový kód:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Všechny funkce orchestrace JavaScriptu musí zahrnovat [modul`durable-functions`](https://www.npmjs.com/package/durable-functions). Jedná se o knihovnu, která umožňuje psát Durable Functions v jazyce JavaScript. Existují tři významné rozdíly mezi funkcí Orchestrace a dalšími funkcemi jazyka JavaScript:

1. Funkce je [generátorová funkce.](https://docs.microsoft.com/scripting/javascript/advanced/iterators-and-generators-javascript)
2. Funkce je zabalena do volání metody `orchestrator` modulu `durable-functions` (tady `df`).
3. Funkce musí být synchronní. Protože metoda ' Orchestrator ' zpracovává volání ' Context. hotov ', funkce by měla jednoduše ' Return '.

Objekt `context` obsahuje objekt `df` umožňuje volat jiné funkce *aktivity* a předat vstupní parametry pomocí své `callActivity` metody. Kód volá `E1_SayHello` třikrát v sekvenci s různými hodnotami parametrů, pomocí `yield` k označení, že spuštění by mělo počkat na volání funkce asynchronní aktivity, která se mají vrátit. Návratová hodnota každého volání je přidána do seznamu `outputs`, který je vrácen na konci funkce.

## <a name="e1_sayhello"></a>E1_SayHello

### <a name="functionjson-file"></a>soubor Function. JSON

Soubor *Function. JSON* pro funkci Activity `E1_SayHello` je podobný jako u `E1_HelloSequence` s tím rozdílem, že používá `activityTrigger` typ vazby namísto `orchestrationTrigger` typu vazby.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

> [!NOTE]
> Každá funkce, která je volána funkcí orchestrace, musí používat vazbu `activityTrigger`.

Implementace `E1_SayHello` je poměrně jednoduchá operace formátování řetězce.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_SayHello/run.csx)]

Tato funkce má parametr typu `DurableActivityContext`, který používá k získání vstupu, který byl předán voláním funkce Orchestrator do `CallActivityAsync<T>`.

### <a name="javascript"></a>JavaScript

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

Na rozdíl od funkce orchestrace JavaScriptu nepotřebuje funkce aktivity žádné speciální nastavení. Vstup předaný do něj funkce Orchestrator je umístěn na objektu `context.bindings` pod názvem `activityTrigger` vazby – v tomto případě `context.bindings.name`. Název vazby lze nastavit jako parametr exportované funkce a získat k nim přímý pøístup, což je to, co dělá vzorový kód.

## <a name="run-the-sample"></a>Spuštění ukázky

Chcete-li spustit orchestraci `E1_HelloSequence`, odešlete následující požadavek HTTP POST.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> Předchozí fragment kódu HTTP předpokládá, že je v souboru `host.json` položka, která odebere výchozí předponu `api/` ze všech adres URL funkcí triggeru protokolu HTTP. Značky této konfigurace najdete v souboru `host.json` v ukázkách.

Pokud například spustíte ukázku ve Function App s názvem "myfunctionapp", nahraďte "{host}" myfunctionapp.azurewebsites.net ".

Výsledkem je odpověď HTTP 202, jako je to (oříznuto pro zkrácení):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

V tomto okamžiku se orchestrace zařadí do fronty a spustí se okamžitě. Adresu URL v hlavičce `Location` lze použít ke kontrole stavu provedení.

```
GET http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

Výsledkem je stav orchestrace. Rychle běží a dokončuje se, takže se zobrazí v *dokončeném* stavu s odpovědí, která bude vypadat (oříznuté pro zkrácení):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Jak vidíte, `runtimeStatus` instance je *dokončena* a `output` obsahuje výsledek vykonávání funkcí Orchestrator serializovaného ve formátu JSON.

> [!NOTE]
> Koncový bod HTTP POST, který spustil funkci Orchestrator, se implementuje v ukázkové aplikaci jako aktivační funkce HTTP s názvem "HttpStart". Můžete implementovat podobnou počáteční logiku pro jiné typy triggerů, například `queueTrigger`, `eventHubTrigger`nebo `timerTrigger`.

Podívejte se na protokoly spuštění funkce. Funkce `E1_HelloSequence` začala a byla několikrát dokončena z důvodu chování opětovného přehrání, které je popsáno v tématu [spolehlivost orchestrace](durable-functions-orchestrations.md#reliability) . Na druhé straně existovala jenom tři spuštění `E1_SayHello`, protože se tato spuštění funkce nepřehrála.

## <a name="visual-studio-sample-code"></a>Vzorový kód sady Visual Studio

Toto je orchestrace jako jeden C# soubor v projektu sady Visual Studio:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

## <a name="next-steps"></a>Další kroky

Tato ukázka ukázala jednoduchou orchestraci zřetězení funkcí. Další příklad ukazuje, jak implementovat vzorek ventilátoru nebo ventilátoru.

> [!div class="nextstepaction"]
> [Spustit ukázku ventilátoru/ventilátoru](durable-functions-cloud-backup.md)
