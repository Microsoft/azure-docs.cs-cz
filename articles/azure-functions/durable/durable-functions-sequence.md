---
title: Řetězení funkcí v odolných funkcích – Azure
description: Zjistěte, jak spustit ukázku trvalé funkce, která spustí posloupnost funkcí.
author: cgillum
ms.topic: conceptual
ms.date: 11/29/2019
ms.author: azfuncdf
ms.openlocfilehash: 8da4ce7801cc98f9ffb32eb7b506eaf1ccd877dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562053"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Řetězení funkcí v trvanlivých funkcích – ukázka sekvence Hello

Zřetězení funkcí odkazuje na vzor provádění posloupnosti funkcí v určitém pořadí. Výstup jedné funkce je často třeba použít na vstup jiné funkce. Tento článek popisuje řetězení sekvence, které vytvoříte po dokončení rychlého startu trvalé funkce ([C#](durable-functions-create-first-csharp.md) nebo [JavaScript](quickstart-js-vscode.md)). Další informace o trvanlivé funkce, naleznete [v tématu trvalé funkce přehled](durable-functions-overview.md).

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>Funkce

Tento článek vysvětluje následující funkce v ukázkové aplikaci:

* `E1_HelloSequence`: Funkce [orchestrator,](durable-functions-bindings.md#orchestration-trigger) která volá `E1_SayHello` vícekrát v sekvenci. Ukládá výstupy z `E1_SayHello` volání a zaznamenává výsledky.
* `E1_SayHello`: [Funkce aktivity,](durable-functions-bindings.md#activity-trigger) která předcvačský řetězec s "Hello".
* `HttpStart`: Funkce spouštěná protokolem HTTP, která spustí instanci orchestrátoru.

### <a name="e1_hellosequence-orchestrator-function"></a>E1_HelloSequence funkce orchestrátoru

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=13-25)]

Všechny funkce orchestrace Jazyka C# `DurableOrchestrationContext`musí mít parametr `Microsoft.Azure.WebJobs.Extensions.DurableTask` typu , který existuje v sestavení. Tento objekt kontextu umožňuje volat další funkce *aktivity* `CallActivityAsync` a předat vstupní parametry pomocí jeho metody.

Kód volá `E1_SayHello` třikrát v pořadí s různými hodnotami parametrů. Vrácená hodnota každého volání je `outputs` přidána do seznamu, který je vrácen na konci funkce.

# <a name="javascript"></a>[Javascript](#tab/javascript)

> [!NOTE]
> Odolné funkce JavaScriptu jsou k dispozici pouze pro běhový čas Functions 2.0.

#### <a name="functionjson"></a>function.json

Pokud používáte Visual Studio Kód nebo portál Azure pro vývoj, tady je obsah souboru *function.json* pro funkci orchestrator. Většina orchestrator *function.json* soubory vypadají téměř přesně takhle.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/function.json)]

Důležité je typ `orchestrationTrigger` vazby. Všechny funkce orchestrator musí používat tento typ aktivační události.

> [!WARNING]
> Chcete-li dodržovat pravidlo "no I/O" funkcí orchestrator, nepoužívejte žádné vstupní `orchestrationTrigger` nebo výstupní vazby při použití vazby aktivační události.  Pokud jsou potřeba jiné vstupní nebo výstupní vazby, by `activityTrigger` měly být použity v kontextu funkce, které jsou volány orchestrator. Další informace naleznete v článku [omezení kódu funkce orchestratoru.](durable-functions-code-constraints.md)

#### <a name="indexjs"></a>index.js

Zde je funkce:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Všechny funkce orchestrace JavaScriptu [ `durable-functions` ](https://www.npmjs.com/package/durable-functions)musí obsahovat modul . Je to knihovna, která vám umožní psát trvalé funkce v JavaScriptu. Mezi funkcí orchestrace a dalšími funkcemi JavaScriptu existují tři významné rozdíly:

1. Funkce je [funkce generátoru.](https://docs.microsoft.com/scripting/javascript/advanced/iterators-and-generators-javascript). .
2. Funkce je zabalena do volání `durable-functions` `orchestrator` metody modulu `df`(zde).
3. Funkce musí být synchronní. Vzhledem k tomu, že 'orchestrator' metoda zpracovává volání 'context.done', funkce by měla jednoduše 'return'.

Objekt `context` obsahuje `df` objekt kontextu trvalé orchestrace, který umožňuje volat další funkce `callActivity` *aktivity* a předat vstupní parametry pomocí jeho metody. Kód volá `E1_SayHello` třikrát v pořadí s různými `yield` hodnotami parametrů, pomocí označující spuštění by měl čekat na volání funkce asynchronní aktivity, které mají být vráceny. Vrácená hodnota každého volání je `outputs` přidána do pole, které je vráceno na konci funkce.

---

### <a name="e1_sayhello-activity-function"></a>E1_SayHello funkce aktivity

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=27-32)]

Aktivity `ActivityTrigger` používají atribut. Pomocí poskytnuté `IDurableActivityContext` ho provádět akce související s aktivitou, `GetInput<T>`jako je například přístup ke vstupní hodnotě pomocí .

Implementace `E1_SayHello` je relativně triviální operace formátování řetězce.

Místo vazby `IDurableActivityContext`na , můžete vázat přímo na typ, který je předán do funkce aktivity. Například:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=34-38)]

# <a name="javascript"></a>[Javascript](#tab/javascript)

#### <a name="e1_sayhellofunctionjson"></a>E1_SayHello/function.json

Soubor *function.json* pro funkci `E1_SayHello` aktivity je `E1_HelloSequence` podobný tomu `activityTrigger` kromě toho, `orchestrationTrigger` že používá typ vazby namísto typu vazby.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/function.json)]

> [!NOTE]
> Všechny funkce volané orchestraci funkce `activityTrigger` musí používat vazbu.

Implementace `E1_SayHello` je relativně triviální operace formátování řetězce.

#### <a name="e1_sayhelloindexjs"></a>E1_SayHello/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

Na rozdíl od funkce orchestrace javascriptu nepotřebuje funkce aktivity žádné speciální nastavení. Vstup předaný funkcí orchestrator je umístěn `context.bindings` na objektu `activityTrigger` pod názvem vazby `context.bindings.name`- v tomto případě . Název vazby lze nastavit jako parametr exportované funkce a přistupovat přímo, což je to, co ukázkový kód dělá.

---

### <a name="httpstart-client-function"></a>Funkce klienta HttpStart

Můžete spustit instanci funkce orchestrator pomocí funkce klienta. Funkci spuštěnou `HttpStart` protokolem HTTP použijete `E1_HelloSequence`ke spuštění instancí aplikace .

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs?range=13-30)]

Chcete-li komunikovat s orchestrators, `DurableClient` funkce musí obsahovat vstupní vazbu. Pomocí klienta spustit orchestraci. Může také pomoci vrátit odpověď HTTP obsahující adresy URL pro kontrolu stavu nové orchestrace.

# <a name="javascript"></a>[Javascript](#tab/javascript)

#### <a name="httpstartfunctionjson"></a>HttpStart/function.json

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json?highlight=16-20)]

Chcete-li komunikovat s orchestrators, `durableClient` funkce musí obsahovat vstupní vazbu.

#### <a name="httpstartindexjs"></a>HttpStart/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

Slouží `df.getClient` k `DurableOrchestrationClient` získání objektu. Pomocí klienta spustit orchestraci. Může také pomoci vrátit odpověď HTTP obsahující adresy URL pro kontrolu stavu nové orchestrace.

---

## <a name="run-the-sample"></a>Spuštění ukázky

Chcete-li `E1_HelloSequence` spustit orchestraci, odešlete `HttpStart` do funkce následující požadavek HTTP POST.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> Předchozí fragment HTTP předpokládá, že v `host.json` souboru je položka, `api/` která odebere výchozí předponu ze všech adres URL aktivačních událostí PROTOKOLU HTTP. Značky pro tuto konfiguraci najdete v souboru `host.json` ve vzorcích.

Například pokud používáte ukázku v aplikaci funkce s názvem "myfunctionapp", nahradit "{host}" s "myfunctionapp.azurewebsites.net".

Výsledkem je odpověď HTTP 202, jako je tato (oříznutá pro stručnost):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

V tomto okamžiku orchestrace je zařazena do fronty a začne běžet okamžitě. Adresu URL `Location` v záhlaví lze použít ke kontrole stavu spuštění.

```
GET http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

Výsledkem je stav orchestrace. Běží a dokončuje se rychle, takže ji vidíte ve stavu *Dokončeno* s odpovědí, která vypadá takto (oříznutá pro stručnost):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Jak můžete vidět, `runtimeStatus` instance je *Dokončeno* a `output` obsahuje JSON serializované výsledek spuštění funkce orchestrator.

> [!NOTE]
> Podobnou logiku spuštění můžete implementovat `queueTrigger` `eventHubTrigger`pro `timerTrigger`jiné typy aktivačních událostí, například , , nebo .

Podívejte se na protokoly spuštění funkce. Funkce `E1_HelloSequence` byla spuštěna a dokončena vícekrát z důvodu chování přehrání popsaného v tématu [spolehlivosti orchestrace.](durable-functions-orchestrations.md#reliability) Na druhou stranu, tam byly pouze `E1_SayHello` tři popravy, protože tyto funkce popravy nedostanou přehrány.

## <a name="next-steps"></a>Další kroky

Tato ukázka prokázala jednoduché orchestrace řetězení funkcí. Další ukázka ukazuje, jak implementovat fan-out/fan-in vzor.

> [!div class="nextstepaction"]
> [Spusťte ukázku Fan-out/fan-in](durable-functions-cloud-backup.md)
