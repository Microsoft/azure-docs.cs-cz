---
title: Řetězení funkcí v Durable Functions – Azure
description: Naučte se, jak spustit ukázku Durable Functions, která spouští sekvenci funkcí.
author: cgillum
ms.topic: conceptual
ms.date: 11/29/2019
ms.author: azfuncdf
ms.openlocfilehash: 8da4ce7801cc98f9ffb32eb7b506eaf1ccd877dd
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562053"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Řetězení funkcí v ukázce sekvence Durable Functions-Hello

Řetězení funkcí odkazuje na vzor spouštění sekvence funkcí v určitém pořadí. Výstup jedné funkce se často musí použít na vstup jiné funkce. Tento článek popisuje sekvenci zřetězení, kterou vytvoříte při dokončování Durable Functions rychlý Start ([C#](durable-functions-create-first-csharp.md) nebo [JavaScript](quickstart-js-vscode.md)). Další informace o Durable Functions najdete v tématu [Durable Functions Overview](durable-functions-overview.md).

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>Funkce

Tento článek vysvětluje následující funkce v ukázkové aplikaci:

* `E1_HelloSequence`: [funkce Orchestrator](durable-functions-bindings.md#orchestration-trigger) , která volá `E1_SayHello` několikrát v sekvenci. Ukládá výstupy z `E1_SayHello` volání a zaznamenává výsledky.
* `E1_SayHello`: [funkce Activity](durable-functions-bindings.md#activity-trigger) , která do řetězce přiřadí řetězec "Hello".
* `HttpStart`: funkce aktivovaná protokolem HTTP, která spouští instanci nástroje Orchestrator.

### <a name="e1_hellosequence-orchestrator-function"></a>E1_HelloSequence funkce Orchestrator

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=13-25)]

Všechny C# funkce orchestrace musí mít parametr typu `DurableOrchestrationContext`, který existuje v sestavení `Microsoft.Azure.WebJobs.Extensions.DurableTask`. Tento kontextový objekt umožňuje volat jiné funkce *aktivity* a předat vstupní parametry pomocí své `CallActivityAsync` metody.

Kód volá `E1_SayHello` třikrát v sekvenci s různými hodnotami parametrů. Návratová hodnota každého volání je přidána do seznamu `outputs`, který je vrácen na konci funkce.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

> [!NOTE]
> Durable Functions JavaScriptu jsou k dispozici pouze pro modul runtime Functions 2,0.

#### <a name="functionjson"></a>function.json

Pokud používáte Visual Studio Code nebo Azure Portal pro vývoj, tady je obsah souboru *Functions. JSON* pro funkci Orchestrator. Většina souborů *. JSON funkcí* Orchestrator vypadá téměř přesně takto.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/function.json)]

Důležitou věc je `orchestrationTrigger` typ vazby. Všechny funkce nástroje Orchestrator musí používat tento typ aktivační události.

> [!WARNING]
> Chcete-li dodržovat pravidlo "žádné I/O" funkcí nástroje Orchestrator, nepoužívejte při použití vazby triggeru `orchestrationTrigger` žádné vstupní ani výstupní vazby.  Pokud jsou vyžadovány jiné vstupní nebo výstupní vazby, měly by být použity místo toho v kontextu funkcí `activityTrigger`, které jsou volány nástrojem Orchestrator. Další informace naleznete v článku o [omezeních kódu funkce nástroje Orchestrator](durable-functions-code-constraints.md) .

#### <a name="indexjs"></a>index. js

Toto je funkce:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Všechny funkce orchestrace JavaScriptu musí zahrnovat [modul`durable-functions`](https://www.npmjs.com/package/durable-functions). Jedná se o knihovnu, která umožňuje psát Durable Functions v jazyce JavaScript. Existují tři významné rozdíly mezi funkcí Orchestrace a dalšími funkcemi jazyka JavaScript:

1. Funkce je [generátorová funkce.](https://docs.microsoft.com/scripting/javascript/advanced/iterators-and-generators-javascript)..
2. Funkce je zabalena do volání metody `orchestrator` modulu `durable-functions` (tady `df`).
3. Funkce musí být synchronní. Protože metoda ' Orchestrator ' zpracovává volání ' Context. hotov ', funkce by měla jednoduše ' Return '.

Objekt `context` obsahuje `df` odolný objekt kontextu orchestrace, který umožňuje volat jiné funkce *aktivity* a předat vstupní parametry pomocí své `callActivity` metody. Kód volá `E1_SayHello` třikrát v sekvenci s různými hodnotami parametrů, pomocí `yield` k označení, že spuštění by mělo počkat na volání funkce asynchronní aktivity, která se mají vrátit. Návratová hodnota každého volání je přidána do pole `outputs`, které je vráceno na konci funkce.

---

### <a name="e1_sayhello-activity-function"></a>Funkce aktivity E1_SayHello

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=27-32)]

Aktivity používají atribut `ActivityTrigger`. Pomocí poskytnuté `IDurableActivityContext` můžete provádět akce související s aktivitami, jako je například přístup k vstupní hodnotě pomocí `GetInput<T>`.

Implementace `E1_SayHello` je poměrně jednoduchá operace formátování řetězce.

Místo vazby na `IDurableActivityContext`můžete svázat přímo s typem, který je předán funkci Activity. Příklad:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=34-38)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="e1_sayhellofunctionjson"></a>E1_SayHello/Function.JSON

Soubor *Function. JSON* pro funkci Activity `E1_SayHello` je podobný jako u `E1_HelloSequence` s tím rozdílem, že používá `activityTrigger` typ vazby namísto `orchestrationTrigger` typu vazby.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/function.json)]

> [!NOTE]
> Každá funkce, která je volána funkcí orchestrace, musí používat vazbu `activityTrigger`.

Implementace `E1_SayHello` je poměrně jednoduchá operace formátování řetězce.

#### <a name="e1_sayhelloindexjs"></a>E1_SayHello/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

Na rozdíl od funkce orchestrace JavaScriptu nepotřebuje funkce aktivity žádné speciální nastavení. Vstup předaný do něj funkce Orchestrator je umístěn na objektu `context.bindings` pod názvem `activityTrigger` vazby – v tomto případě `context.bindings.name`. Název vazby lze nastavit jako parametr exportované funkce a získat k nim přímý pøístup, což je to, co dělá vzorový kód.

---

### <a name="httpstart-client-function"></a>Klientská funkce HttpStart

Můžete spustit instanci funkce Orchestrator pomocí klientské funkce. K zahájení instancí `E1_HelloSequence`použijete funkci `HttpStart` aktivované protokolem HTTP.

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs?range=13-30)]

Aby bylo možné pracovat s orchestrací, funkce musí zahrnovat vstupní vazbu `DurableClient`. K zahájení orchestrace slouží klient. Může vám také usnadnit návratovou odpověď HTTP obsahující adresy URL pro kontrolu stavu nové orchestrace.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="httpstartfunctionjson"></a>HttpStart/Function. JSON

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json?highlight=16-20)]

Aby bylo možné pracovat s orchestrací, funkce musí zahrnovat vstupní vazbu `durableClient`.

#### <a name="httpstartindexjs"></a>HttpStart/index. js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

K získání objektu `DurableOrchestrationClient` použijte `df.getClient`. K zahájení orchestrace slouží klient. Může vám také usnadnit návratovou odpověď HTTP obsahující adresy URL pro kontrolu stavu nové orchestrace.

---

## <a name="run-the-sample"></a>Spuštění ukázky

Chcete-li spustit orchestraci `E1_HelloSequence`, odešlete do funkce `HttpStart` následující požadavek HTTP POST.

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
> Můžete implementovat podobnou počáteční logiku pro jiné typy triggerů, například `queueTrigger`, `eventHubTrigger`nebo `timerTrigger`.

Podívejte se na protokoly spuštění funkce. Funkce `E1_HelloSequence` začala a byla několikrát dokončena z důvodu chování opětovného přehrání, které je popsáno v tématu [spolehlivost orchestrace](durable-functions-orchestrations.md#reliability) . Na druhé straně existovala jenom tři spuštění `E1_SayHello`, protože se tato spuštění funkce nepřehrála.

## <a name="next-steps"></a>Další kroky

Tato ukázka ukázala jednoduchou orchestraci zřetězení funkcí. Další příklad ukazuje, jak implementovat vzorek ventilátoru nebo ventilátoru.

> [!div class="nextstepaction"]
> [Spustit ukázku ventilátoru/ventilátoru](durable-functions-cloud-backup.md)
