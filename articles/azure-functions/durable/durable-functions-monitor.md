---
title: Monitorování v Durable Functions – Azure
description: Přečtěte si, jak implementovat monitorování stavu pomocí rozšíření Durable Functions pro Azure Functions.
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: ed92156df9d8e1e07b56cea4b1e64edee11d68d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "77562118"
---
# <a name="monitor-scenario-in-durable-functions---weather-watcher-sample"></a>Scénář monitorování Durable Functions – ukázka sledovacích procesů počasí

Model monitorování odkazuje na flexibilní *opakovaný* proces v pracovním postupu – například dotazování do splnění určitých podmínek. Tento článek vysvětluje ukázku, která používá [Durable Functions](durable-functions-overview.md) k implementaci monitorování.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Přehled scénáře

Tato ukázka monitoruje aktuální povětrnostní podmínky umístění a upozorní uživatele na serveru SMS, když je Skies jasný. Pomocí běžné funkce aktivované časovačem můžete kontrolovat počasí a odesílat výstrahy. Jedním z problémů s tímto přístupem však je **Správa životnosti**. Pokud by se měla odeslat jenom jedna výstraha, sledování se musí po zjištění jasného počasí zakázat. Model monitorování může ukončit své vlastní provádění mimo jiné výhody:

* Monitory se spouštějí v intervalech, nikoli v plánech: aktivační událost časovače se *spouští* každou hodinu. monitorování *počká* jednu hodinu mezi akcemi. Akce monitorování se nepřekrývají, pokud nejsou zadány, což může být důležité pro dlouhotrvající úlohy.
* Monitory můžou mít dynamické intervaly: čekací doba se může změnit na základě nějaké podmínky.
* Monitory mohou skončit, pokud je některá podmínka splněna nebo ukončena jiným procesem.
* Monitory mohou přijímat parametry. Ukázka ukazuje, jak je možné použít stejný proces monitorování počasí na požadované místo a telefonní číslo.
* Monitory jsou škálovatelné. Vzhledem k tomu, že každý monitor je instancí orchestrace, lze vytvořit více monitorů bez nutnosti vytvářet nové funkce nebo definovat více kódů.
* Monitorování se snadno integruje do větších pracovních postupů. Monitorování může být jeden oddíl složitější funkce orchestrace nebo [dílčí orchestrace](durable-functions-sub-orchestrations.md).

## <a name="configuration"></a>Konfigurace

### <a name="configuring-twilio-integration"></a>Konfigurace integrace Twilio

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

### <a name="configuring-weather-underground-integration"></a>Konfigurace integrace s počasí v podzemních přístavech

Tato ukázka zahrnuje použití povětrnostního rozhraní API ke kontrole aktuálních povětrnostních podmínek pro určité místo.

První věc, kterou potřebujete, je účet v podzemních počasí. Můžete ho vytvořit zdarma na adrese [https://www.wunderground.com/signup](https://www.wunderground.com/signup) . Jakmile budete mít účet, budete muset získat klíč rozhraní API. Můžete to udělat tak, že navštívíte [https://www.wunderground.com/weather/api](https://www.wunderground.com/weather/api/?MR=1) a pak vyberete nastavení klíče. Plán pro vývojáře Stratus je zdarma a stačí ke spuštění této ukázky.

Jakmile budete mít klíč rozhraní API, přidejte do aplikace Function App následující **nastavení aplikace** .

| Název nastavení aplikace | Popis hodnoty |
| - | - |
| **WeatherUndergroundApiKey**  | Váš kód rozhraní API pro počasí |

## <a name="the-functions"></a>Funkce

Tento článek vysvětluje následující funkce v ukázkové aplikaci:

* `E3_Monitor`: [Funkce Orchestrator](durable-functions-bindings.md#orchestration-trigger) , která provádí `E3_GetIsClear` pravidelné volání. Volá, `E3_SendGoodWeatherAlert` Pokud `E3_GetIsClear` vrátí hodnotu true.
* `E3_GetIsClear`: [Funkce Activity](durable-functions-bindings.md#activity-trigger) , která kontroluje aktuální povětrnostní podmínky pro určité místo.
* `E3_SendGoodWeatherAlert`: Funkce aktivity, která odesílá zprávu SMS prostřednictvím Twilio.

### <a name="e3_monitor-orchestrator-function"></a>E3_Monitor funkce Orchestrator

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=41-78,97-115)]

Nástroj Orchestrator vyžaduje místo, kde se má monitorovat, a telefonní číslo, na které se pošle zpráva, když se v umístění zruší zaškrtnutí. Tato data se předávají do nástroje Orchestrator jako objekt silného typu `MonitorRequest` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Funkce **E3_Monitor** používá standardní *function.js* pro funkce nástroje Orchestrator.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/function.json)]

Zde je kód, který implementuje funkci:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/index.js)]

---

Tato funkce Orchestrator provádí následující akce:

1. Získá **MonitorRequest** sestávající z *umístění* , které se má monitorovat, a *telefonní číslo* , na které se pošle oznámení SMS.
2. Určuje čas vypršení platnosti monitoru. Ukázka používá pevně zakódované hodnoty pro zkrácení.
3. Volá **E3_GetIsClear** k určení, zda v požadovaném umístění nejsou jasné Skies.
4. Pokud je počasí jasné, zavolá **E3_SendGoodWeatherAlert** k odeslání oznámení SMS požadovanému telefonnímu číslu.
5. Vytvoří trvalý časovač pro pokračování orchestrace při dalším intervalu dotazování. Ukázka používá pevně zakódované hodnoty pro zkrácení.
6. Pokračuje v běhu, dokud aktuální čas UTC neprojde časem vypršení platnosti monitoru, nebo se pošle výstraha SMS.

Více instancí nástroje Orchestrator lze spustit současně voláním funkce Orchestrator vícekrát. Umístění, které se má monitorovat, a telefonní číslo, na které se má odeslat výstraha SMS, se může zadat.

### <a name="e3_getisclear-activity-function"></a>Funkce aktivity E3_GetIsClear

Stejně jako u jiných ukázek jsou funkce aktivity pomocníka běžné funkcemi, které používají `activityTrigger` vazbu triggeru. Funkce **E3_GetIsClear** získává aktuální povětrnostní podmínky pomocí rozhraní API pro počasí, které určuje, zda je nebe jasný.

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=80-85)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

*function.jsv* je definován následujícím způsobem:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/function.json)]

A zde je implementace.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/index.js)]

---

### <a name="e3_sendgoodweatheralert-activity-function"></a>Funkce aktivity E3_SendGoodWeatherAlert

Funkce **E3_SendGoodWeatherAlert** používá vazbu Twilio k odeslání zprávy SMS upozorňující koncového uživatele, že se jedná o dobrý čas pro procházení.

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=87-96,140-205)]

> [!NOTE]
> `Microsoft.Azure.WebJobs.Extensions.Twilio`Pro spuštění ukázkového kódu budete muset nainstalovat balíček NuGet.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Jeho *function.jsv systému* je jednoduchý:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/function.json)]

A zde je kód, který odesílá zprávu SMS:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/index.js)]

---

## <a name="run-the-sample"></a>Spuštění ukázky

Pomocí funkcí aktivovaných protokolem HTTP, které jsou součástí ukázky, můžete zahájit orchestraci odesláním následující žádosti HTTP POST:

```
POST https://{host}/orchestrators/E3_Monitor
Content-Length: 77
Content-Type: application/json

{ "location": { "city": "Redmond", "state": "WA" }, "phone": "+1425XXXXXXX" }
```

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
```

Instance **E3_Monitor** se spustí a zadá dotaz na aktuální povětrnostní podmínky pro požadované umístění. Pokud je počasí jasné, volá funkci aktivity k odeslání výstrahy. v opačném případě nastaví časovač. Po vypršení platnosti časovače bude orchestrace pokračovat.

Aktivitu orchestrace si můžete prohlédnout v protokolech funkce na portálu Azure Functions.

```
2018-03-01T01:14:41.649 Function started (Id=2d5fcadf-275b-4226-a174-f9f943c90cd1)
2018-03-01T01:14:42.741 Started orchestration with ID = '1608200bb2ce4b7face5fc3b8e674f2e'.
2018-03-01T01:14:42.780 Function completed (Success, Id=2d5fcadf-275b-4226-a174-f9f943c90cd1, Duration=1111ms)
2018-03-01T01:14:52.765 Function started (Id=b1b7eb4a-96d3-4f11-a0ff-893e08dd4cfb)
2018-03-01T01:14:52.890 Received monitor request. Location: Redmond, WA. Phone: +1425XXXXXXX.
2018-03-01T01:14:52.895 Instantiating monitor for Redmond, WA. Expires: 3/1/2018 7:14:52 AM.
2018-03-01T01:14:52.909 Checking current weather conditions for Redmond, WA at 3/1/2018 1:14:52 AM.
2018-03-01T01:14:52.954 Function completed (Success, Id=b1b7eb4a-96d3-4f11-a0ff-893e08dd4cfb, Duration=189ms)
2018-03-01T01:14:53.226 Function started (Id=80a4cb26-c4be-46ba-85c8-ea0c6d07d859)
2018-03-01T01:14:53.808 Function completed (Success, Id=80a4cb26-c4be-46ba-85c8-ea0c6d07d859, Duration=582ms)
2018-03-01T01:14:53.967 Function started (Id=561d0c78-ee6e-46cb-b6db-39ef639c9a2c)
2018-03-01T01:14:53.996 Next check for Redmond, WA at 3/1/2018 1:44:53 AM.
2018-03-01T01:14:54.030 Function completed (Success, Id=561d0c78-ee6e-46cb-b6db-39ef639c9a2c, Duration=62ms)
```

Orchestrace se [ukončí](durable-functions-instance-management.md) po dosažení časového limitu nebo se zjistí vymazání Skies. Můžete také použít `TerminateAsync` (.NET) nebo `terminate` (JavaScript) uvnitř jiné funkce nebo vyvolat Webhook **terminatePostUri** http, na který se odkazuje v odpovědi 202, nahrazuje `{text}` se důvodem ukončení:

```
POST https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="next-steps"></a>Další kroky

Tato ukázka ukázala, jak použít Durable Functions k monitorování stavu externího zdroje pomocí [trvalých časovačů](durable-functions-timers.md) a podmíněné logiky. Další příklad ukazuje, jak použít externí události a [trvalé časovače](durable-functions-timers.md) pro zpracování lidské interakce.

> [!div class="nextstepaction"]
> [Spuštění ukázky lidské interakce](durable-functions-phone-verification.md)
