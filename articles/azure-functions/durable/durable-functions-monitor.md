---
title: Monitorování v Durable Functions – Azure
description: Přečtěte si, jak implementovat monitorování stavu pomocí rozšíření Durable Functions pro Azure Functions.
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: f8a589bd4ab4de396c0688f8022515d6fbec96a2
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769587"
---
# <a name="monitor-scenario-in-durable-functions---weather-watcher-sample"></a>Scénář monitorování Durable Functions – ukázka sledovacích procesů počasí

Model monitorování odkazuje na flexibilní *opakovaný* proces v pracovním postupu – například dotazování do splnění určitých podmínek. Tento článek vysvětluje ukázku, která používá [Durable Functions](durable-functions-overview.md) k implementaci monitorování.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Přehled scénáře

Tato ukázka monitoruje aktuální povětrnostní podmínky umístění a upozorní uživatele na serveru SMS, když je Skies jasný. Pomocí běžné funkce aktivované časovačem můžete kontrolovat počasí a odesílat výstrahy. Jedním z problémů s tímto přístupem však je **Správa životnosti**. Pokud by se měla odeslat jenom jedna výstraha, sledování se musí po zjištění jasného počasí zakázat. Model monitorování může ukončit své vlastní provádění mimo jiné výhody:

* Monitory se spouštějí v intervalech, nikoli v plánech: aktivační událost časovače se *spouští* každou hodinu. monitorování *počká* jednu hodinu mezi akcemi. Akce monitorování se nepřekrývají, pokud nejsou zadány, což může být důležité pro dlouhotrvající úlohy.
* Monitory můžou mít dynamické intervaly: čekací doba se může změnit na základě nějaké podmínky.
* Monitory mohou skončit, pokud je některá podmínka splněna nebo ukončena jiným procesem.
* Monitory mohou přijímat parametry. Ukázka ukazuje, jak je možné použít stejný proces monitorování počasí na požadované místo a telefonní číslo.
* Monitory jsou škálovatelné. Vzhledem k tomu, že každý monitor je instancí orchestrace, lze vytvořit více monitorů bez nutnosti vytvářet nové funkce nebo definovat více kódů.
* Monitorování se snadno integruje do větších pracovních postupů. Monitorování může být jeden oddíl složitější funkce orchestrace nebo [dílčí orchestrace](durable-functions-sub-orchestrations.md).

## <a name="configuring-twilio-integration"></a>Konfigurace integrace Twilio

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="configuring-weather-underground-integration"></a>Konfigurace integrace s počasí v podzemních přístavech

Tato ukázka zahrnuje použití povětrnostního rozhraní API ke kontrole aktuálních povětrnostních podmínek pro určité místo.

První věc, kterou potřebujete, je účet v podzemních počasí. Můžete ho vytvořit zdarma na [https://www.wunderground.com/signup](https://www.wunderground.com/signup). Jakmile budete mít účet, budete muset získat klíč rozhraní API. Můžete to udělat tak, že navštívíte [https://www.wunderground.com/weather/api](https://www.wunderground.com/weather/api/?MR=1)a pak vyberete nastavení klíče. Plán pro vývojáře Stratus je zdarma a stačí ke spuštění této ukázky.

Jakmile budete mít klíč rozhraní API, přidejte do aplikace Function App následující **nastavení aplikace** .

| Název nastavení aplikace | Popis hodnoty |
| - | - |
| **WeatherUndergroundApiKey**  | Váš kód rozhraní API pro počasí |

## <a name="the-functions"></a>Funkce

Tento článek vysvětluje následující funkce v ukázkové aplikaci:

* `E3_Monitor`: funkce Orchestrator, která volá `E3_GetIsClear` pravidelně. Volá `E3_SendGoodWeatherAlert`, pokud `E3_GetIsClear` vrátí hodnotu true.
* `E3_GetIsClear`: funkce aktivity, která kontroluje aktuální povětrnostní podmínky pro určité místo.
* `E3_SendGoodWeatherAlert`: funkce aktivity, která odesílá zprávu SMS prostřednictvím Twilio.

Následující části vysvětlují konfiguraci a kód, který se používá C# pro skriptování a JavaScript. Kód pro vývoj v aplikaci Visual Studio se zobrazí na konci článku.

## <a name="the-weather-monitoring-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>Orchestrace sledování počasí (ukázka Visual Studio Code a Azure Portal ukázkový kód)

Funkce **E3_Monitor** používá standardní *funkci Function. JSON* pro funkce Orchestrator.

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_Monitor/function.json)]

Zde je kód, který implementuje funkci:

### <a name="c-script"></a>C#Pravidel

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_Monitor/run.csx)]

### <a name="javascript-functions-20-only"></a>JavaScript (pouze funkce 2,0)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/index.js)]

Tato funkce Orchestrator provádí následující akce:

1. Získá **MonitorRequest** sestávající z *umístění* , které se má monitorovat, a *telefonní číslo* , na které se pošle oznámení SMS.
2. Určuje čas vypršení platnosti monitoru. Ukázka používá pevně zakódované hodnoty pro zkrácení.
3. Volá **E3_GetIsClear** k určení, zda v požadovaném umístění nejsou jasné Skies.
4. Pokud je počasí jasné, zavolá **E3_SendGoodWeatherAlert** k odeslání oznámení SMS požadovanému telefonnímu číslu.
5. Vytvoří trvalý časovač pro pokračování orchestrace při dalším intervalu dotazování. Ukázka používá pevně zakódované hodnoty pro zkrácení.
6. Pokračuje v běhu, dokud `CurrentUtcDateTime` (.NET) nebo `currentUtcDateTime` (JavaScript) neprojde časem vypršení platnosti monitoru, nebo se pošle výstraha SMS.

Několik instancí nástroje Orchestrator může běžet současně posíláním více **MonitorRequests**. Umístění, které se má monitorovat, a telefonní číslo, na které se má odeslat výstraha SMS, se může zadat.

## <a name="strongly-typed-data-transfer-net-only"></a>Přenos dat silného typu (jenom .NET)

Nástroj Orchestrator vyžaduje více dat, takže [sdílené objekty POCO](../functions-reference-csharp.md#reusing-csx-code) se používají pro přenos dat silného typu v C# nástroji a C# skript:  
[!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/MonitorRequest.csx)]

[!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/Location.csx)]

Ukázka JavaScriptu jako parametry používá regulární objekty JSON.

## <a name="helper-activity-functions"></a>Funkce aktivity pomocníka

Stejně jako u jiných ukázek jsou funkce aktivity pomocníka běžné funkcemi, které používají vazbu triggeru `activityTrigger`. Funkce **E3_GetIsClear** získává aktuální povětrnostní podmínky pomocí rozhraní API pro počasí, které určuje, zda je nebe jasný. *Funkce Function. JSON* je definována takto:

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/function.json)]

A zde je implementace. Podobně jako POCOs, který se používá pro přenos dat, je logika pro zpracování volání rozhraní API a analýza JSON odpovědi je abstraktní na sdílenou C#třídu v. Můžete ji najít v rámci [ukázkového kódu sady Visual Studio](#run-the-sample).

### <a name="c-script"></a>C#Pravidel

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/run.csx)]

### <a name="javascript-functions-20-only"></a>JavaScript (pouze funkce 2,0)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/index.js)]

Funkce **E3_SendGoodWeatherAlert** používá vazbu Twilio k odeslání zprávy SMS upozorňující koncového uživatele, že se jedná o dobrý čas pro procházení. Jeho *Function. JSON* je jednoduchý:

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/function.json)]

A zde je kód, který odesílá zprávu SMS:

### <a name="c-script"></a>C#Pravidel

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/run.csx)]

### <a name="javascript-functions-20-only"></a>JavaScript (pouze funkce 2,0)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/index.js)]

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

Orchestrace se [ukončí](durable-functions-instance-management.md) po dosažení časového limitu nebo se zjistí vymazání Skies. V rámci jiné funkce můžete také použít `TerminateAsync` (.NET) nebo `terminate` (JavaScript) nebo vyvolat **terminatePostUri** http post Webhook, na který odkazuje odpověď 202, a nahrazuje `{text}` důvod ukončení:

```
POST https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="visual-studio-sample-code"></a>Vzorový kód sady Visual Studio

Toto je orchestrace jako jeden C# soubor v projektu sady Visual Studio:

> [!NOTE]
> Pro spuštění ukázkového kódu níže budete muset nainstalovat balíček `Microsoft.Azure.WebJobs.Extensions.Twilio` NuGet.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs)]

## <a name="next-steps"></a>Další kroky

Tato ukázka ukázala, jak použít Durable Functions k monitorování stavu externího zdroje pomocí [trvalých časovačů](durable-functions-timers.md) a podmíněné logiky. Další příklad ukazuje, jak použít externí události a [trvalé časovače](durable-functions-timers.md) pro zpracování lidské interakce.

> [!div class="nextstepaction"]
> [Spuštění ukázky lidské interakce](durable-functions-phone-verification.md)
