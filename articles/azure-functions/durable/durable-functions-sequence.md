---
title: Řetězení funkcí v Durable Functions – Azure
description: Naučte se, jak spustit ukázku Durable Functions, která spouští sekvenci funkcí.
author: cgillum
ms.topic: conceptual
ms.date: 11/29/2019
ms.author: azfuncdf
ms.openlocfilehash: f8223b1273c2a487e15e3c10d7c6852a119e4cdc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98028246"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Řetězení funkcí v ukázce sekvence Durable Functions-Hello

Řetězení funkcí odkazuje na vzor spouštění sekvence funkcí v určitém pořadí. Výstup jedné funkce se často musí použít na vstup jiné funkce. Tento článek popisuje sekvenci zřetězení, kterou vytvoříte při dokončování Durable Functions rychlý Start ([C#](durable-functions-create-first-csharp.md),  [JavaScript](quickstart-js-vscode.md)nebo [Python](quickstart-python-vscode.md)). Další informace o Durable Functions najdete v tématu [Durable Functions Overview](durable-functions-overview.md).

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>Funkce

Tento článek vysvětluje následující funkce v ukázkové aplikaci:

* `E1_HelloSequence`: [Funkce Orchestrator](durable-functions-bindings.md#orchestration-trigger) , která `E1_SayHello` v sekvenci volá víckrát. Ukládá výstupy z `E1_SayHello` volání a zaznamenává výsledky.
* `E1_SayHello`: [Funkce Activity](durable-functions-bindings.md#activity-trigger) , která do řetězce přiřadí řetězec "Hello".
* `HttpStart`: Funkce [trvalého klienta](durable-functions-bindings.md#orchestration-client) s protokolem HTTP spouští instanci nástroje Orchestrator.

### <a name="e1_hellosequence-orchestrator-function"></a>E1_HelloSequence funkce Orchestrator

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=13-25)]

Všechny funkce orchestrace jazyka C# musí mít parametr typu `DurableOrchestrationContext` , který existuje v `Microsoft.Azure.WebJobs.Extensions.DurableTask` sestavení. Tento kontextový objekt umožňuje volat jiné funkce *aktivity* a předat vstupní parametry pomocí své `CallActivityAsync` metody.

Kód volá `E1_SayHello` tři časy v pořadí s různými hodnotami parametrů. Návratová hodnota každého volání je přidána do `outputs` seznamu, který je vrácen na konci funkce.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

> [!NOTE]
> Durable Functions JavaScriptu jsou k dispozici pouze pro modul runtime Functions 3,0.

#### <a name="functionjson"></a>function.json

Pokud používáte Visual Studio Code nebo Azure Portal pro vývoj, tady je obsah *function.jsv* souboru pro funkci Orchestrator. Většina *function.js* Orchestrator se soubory vypadá téměř přesně takto.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/function.json)]

Důležitou věcí je `orchestrationTrigger` typ vazby. Všechny funkce nástroje Orchestrator musí používat tento typ aktivační události.

> [!WARNING]
> Chcete-li přidržet pravidlo "žádné I/O" funkcí nástroje Orchestrator, při použití vazby triggeru nepoužívejte žádné vstupní ani výstupní vazby `orchestrationTrigger` .  Pokud jsou vyžadovány jiné vstupní nebo výstupní vazby, měly by být použity místo toho v kontextu `activityTrigger` funkcí, které jsou volány nástrojem Orchestrator. Další informace naleznete v článku o [omezeních kódu funkce nástroje Orchestrator](durable-functions-code-constraints.md) .

#### <a name="indexjs"></a>index.js

Tady je funkce Orchestrator:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Všechny funkce orchestrace JavaScriptu musí obsahovat [ `durable-functions` modul](https://www.npmjs.com/package/durable-functions). Jedná se o knihovnu, která umožňuje psát Durable Functions v jazyce JavaScript. Existují tři významné rozdíly mezi funkcí Orchestrator a dalšími funkcemi jazyka JavaScript:

1. Funkce Orchestrator je [generátorová funkce](/scripting/javascript/advanced/iterators-and-generators-javascript).
2. Funkce je zabalena do volání `durable-functions` `orchestrator` metody modulu (zde `df` ).
3. Funkce musí být synchronní. Protože metoda ' Orchestrator ' zpracovává volání ' Context. hotov ', funkce by měla jednoduše ' Return '.

`context`Objekt obsahuje `df` objekt kontextu trvalé orchestrace, který umožňuje volat jiné funkce *aktivity* a předat vstupní parametry pomocí své `callActivity` metody. Kód volá `E1_SayHello` třikrát tři časy v pořadí s různými hodnotami parametrů, `yield` které používají k určení, že by mělo být spuštěno volání funkce asynchronní aktivity, které se mají vrátit. Návratová hodnota každého volání je přidána do `outputs` pole, které je vráceno na konci funkce.

# <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> Durable Functions Pythonu jsou k dispozici pouze pro modul runtime Functions 3,0.


#### <a name="functionjson"></a>function.json

Pokud používáte Visual Studio Code nebo Azure Portal pro vývoj, tady je obsah *function.jsv* souboru pro funkci Orchestrator. Většina *function.js* Orchestrator se soubory vypadá téměř přesně takto.

[!code-json[Main](~/samples-durable-functions-python/samples/function_chaining/E1_HelloSequence/function.json)]

Důležitou věcí je `orchestrationTrigger` typ vazby. Všechny funkce nástroje Orchestrator musí používat tento typ aktivační události.

> [!WARNING]
> Chcete-li přidržet pravidlo "žádné I/O" funkcí nástroje Orchestrator, při použití vazby triggeru nepoužívejte žádné vstupní ani výstupní vazby `orchestrationTrigger` .  Pokud jsou vyžadovány jiné vstupní nebo výstupní vazby, měly by být použity místo toho v kontextu `activityTrigger` funkcí, které jsou volány nástrojem Orchestrator. Další informace naleznete v článku o [omezeních kódu funkce nástroje Orchestrator](durable-functions-code-constraints.md) .

#### <a name="__init__py"></a>\_\_init \_ \_ . py

Tady je funkce Orchestrator:

[!code-python[Main](~/samples-durable-functions-python/samples/function_chaining/E1_HelloSequence/\_\_init\_\_.py)]

Všechny funkce orchestrace v Pythonu musí zahrnovat [ `durable-functions` balíček](https://pypi.org/project/azure-functions-durable). Jedná se o knihovnu, která umožňuje psát Durable Functions v Pythonu. Existují dva významné rozdíly mezi funkcí Orchestrator a dalšími funkcemi Pythonu:

1. Funkce Orchestrator je [generátorová funkce](https://wiki.python.org/moin/Generators).
2. _Soubor_ by měl zaregistrovat funkci Orchestrator jako Orchestrator pomocí oznámení `main = df.Orchestrator.create(<orchestrator function name>)` na konci souboru. To pomáhá ho odlišit od ostatních pomocných funkcí, které jsou deklarovány v souboru.

`context`Objekt umožňuje volat jiné funkce *aktivity* a předat vstupní parametry pomocí své `call_activity` metody. Kód volá `E1_SayHello` třikrát tři časy v pořadí s různými hodnotami parametrů, `yield` které používají k určení, že by mělo být spuštěno volání funkce asynchronní aktivity, které se mají vrátit. Návratová hodnota každého volání je vrácena na konci funkce.

---

### <a name="e1_sayhello-activity-function"></a>Funkce aktivity E1_SayHello

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=27-32)]

Aktivity používají `ActivityTrigger` atribut. Použijte `IDurableActivityContext` k provádění akcí souvisejících s aktivitou, jako je například přístup k vstupní hodnotě pomocí, poskytnuté aktivity `GetInput<T>` .

Implementace `E1_SayHello` je poměrně operace formátování řetězce s jednoduchým řetězcem.

Namísto vazby k objektu `IDurableActivityContext` lze vytvořit vazbu přímo na typ, který je předán funkci Activity. Například:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=34-38)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="e1_sayhellofunctionjson"></a>E1_SayHello/function.jsna

*function.jsv* souboru pro funkci Activity `E1_SayHello` je podobný jako s `E1_HelloSequence` tím rozdílem, že používá `activityTrigger` typ vazby místo `orchestrationTrigger` typu vazby.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/function.json)]

> [!NOTE]
> Všechny funkce aktivity volané funkcí orchestrace musí používat `activityTrigger` vazbu.

Implementace `E1_SayHello` je poměrně operace formátování řetězce s jednoduchým řetězcem.

#### <a name="e1_sayhelloindexjs"></a>E1_SayHello/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

Na rozdíl od funkce orchestrace, funkce Activity nepotřebuje žádné speciální nastavení. Vstup předaný do něj funkce Orchestrator je umístěn na `context.bindings` objektu pod názvem `activityTrigger` vazby – v tomto případě `context.bindings.name` . Název vazby lze nastavit jako parametr exportované funkce a získat k nim přímý pøístup, což je to, co dělá vzorový kód.

# <a name="python"></a>[Python](#tab/python)

#### <a name="e1_sayhellofunctionjson"></a>E1_SayHello/function.jsna

*function.jsv* souboru pro funkci Activity `E1_SayHello` je podobný jako s `E1_HelloSequence` tím rozdílem, že používá `activityTrigger` typ vazby místo `orchestrationTrigger` typu vazby.

[!code-json[Main](~/samples-durable-functions-python/samples/function_chaining/E1_SayHello/function.json)]

> [!NOTE]
> Všechny funkce aktivity volané funkcí orchestrace musí používat `activityTrigger` vazbu.

Implementace `E1_SayHello` je poměrně operace formátování řetězce s jednoduchým řetězcem.

#### <a name="e1_sayhello__init__py"></a>E1_SayHello/ \_ \_ init \_ \_ . py

[!code-python[Main](~/samples-durable-functions-python/samples/function_chaining/E1_SayHello/\_\_init\_\_.py)]

Na rozdíl od funkce Orchestrator nepotřebuje funkce aktivity žádné speciální nastavení. Vstup předaný do něj funkcí Orchestrator je přímo přístupný jako parametr funkce.

---

### <a name="httpstart-client-function"></a>Klientská funkce HttpStart

Můžete spustit instanci funkce Orchestrator pomocí klientské funkce. Pomocí `HttpStart` funkce aktivované protokolem HTTP spustíte instance `E1_HelloSequence` .

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs?range=13-30)]

Aby bylo možné pracovat s orchestrací, funkce musí zahrnovat `DurableClient` vstupní vazbu. K zahájení orchestrace slouží klient. Může vám také usnadnit návratovou odpověď HTTP obsahující adresy URL pro kontrolu stavu nové orchestrace.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="httpstartfunctionjson"></a>HttpStart/function.jsna

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json?highlight=16-20)]

Aby bylo možné pracovat s orchestrací, funkce musí zahrnovat `durableClient` vstupní vazbu.

#### <a name="httpstartindexjs"></a>HttpStart/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

Použijte `df.getClient` k získání `DurableOrchestrationClient` objektu. K zahájení orchestrace slouží klient. Může vám také usnadnit návratovou odpověď HTTP obsahující adresy URL pro kontrolu stavu nové orchestrace.

# <a name="python"></a>[Python](#tab/python)

#### <a name="httpstartfunctionjson"></a>HttpStart/function.jsna

[!code-json[Main](~/samples-durable-functions-python/samples/function_chaining/HttpStart/function.json)]

Aby bylo možné pracovat s orchestrací, funkce musí zahrnovat `durableClient` vstupní vazbu.

#### <a name="httpstart__init__py"></a>HttpStart/ \_ \_ init \_ \_ . py

[!code-python[Main](~/samples-durable-functions-python/samples/function_chaining/HttpStart/\_\_init\_\_.py)]

`DurableOrchestrationClient`K získání Durable Functions klienta použijte konstruktor. K zahájení orchestrace slouží klient. Může vám také usnadnit návratovou odpověď HTTP obsahující adresy URL pro kontrolu stavu nové orchestrace.

---

## <a name="run-the-sample"></a>Spuštění ukázky

Chcete-li spustit `E1_HelloSequence` orchestraci, odešlete do funkce následující požadavek HTTP Post `HttpStart` .

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> Předchozí fragment kódu HTTP předpokládá, že v souboru je položka, `host.json` která odebere výchozí `api/` předponu ze všech adres URL funkcí TRIGGERU protokolu HTTP. Značky pro tuto konfiguraci najdete v `host.json` souboru v ukázkách.

Pokud například spustíte ukázku ve Function App s názvem "myfunctionapp", nahraďte "{host}" myfunctionapp.azurewebsites.net ".

Výsledkem je odpověď HTTP 202, jako je to (oříznuto pro zkrácení):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

V tomto okamžiku se orchestrace zařadí do fronty a spustí se okamžitě. Adresu URL v `Location` hlavičce lze použít ke kontrole stavu provedení.

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

Jak vidíte, `runtimeStatus` instance je *dokončena* a `output` obsahuje výsledek serializace funkce Orchestrator ve formátu JSON.

> [!NOTE]
> Můžete implementovat podobnou počáteční logiku pro jiné typy triggerů, například, `queueTrigger` `eventHubTrigger` nebo `timerTrigger` .

Podívejte se na protokoly spuštění funkce. `E1_HelloSequence`Funkce byla spuštěna a několikrát dokončena z důvodu chování opakovaného přehrávání popsaných v tématu [spolehlivost orchestrace](durable-functions-orchestrations.md#reliability) . Na druhé straně bylo k dispozici pouze tři spuštění, `E1_SayHello` protože se tyto provádění této funkce nezobrazují.

## <a name="next-steps"></a>Další kroky

Tato ukázka ukázala jednoduchou orchestraci zřetězení funkcí. Další příklad ukazuje, jak implementovat vzorek ventilátoru nebo ventilátoru.

> [!div class="nextstepaction"]
> [Spustit ukázku ventilátoru/ventilátoru](durable-functions-cloud-backup.md)
