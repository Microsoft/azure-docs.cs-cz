---
title: "Monitorování v trvanlivý funkce – Azure"
description: "Zjistěte, jak implementovat stav monitorování pomocí funkce trvanlivý rozšíření pro Azure Functions."
services: functions
author: kashimiz
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/16/2018
ms.author: azfuncdf
ms.openlocfilehash: 617b33a9f860ce3b06ff560de22824037eab8332
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="monitor-scenario-in-durable-functions---weather-watcher-sample"></a>Scénáře monitorování v trvanlivý funkce – ukázka počasí sledovacích procesů

Vzor monitorování odkazuje flexibilní *opakovaného* procesu v pracovním postupu – například dotazování až do splnění určitých podmínek. Tento článek vysvětluje vzorku, který používá [trvanlivý funkce](durable-functions-overview.md) implementovat monitorování.

## <a name="prerequisites"></a>Požadavky

* Postupujte podle pokynů v [nainstalovat trvanlivý funkce](durable-functions-install.md) nastavit vzorku.
* Tento článek předpokládá, že jste již prošli [Hello pořadí](durable-functions-sequence.md) ukázka návod.

## <a name="scenario-overview"></a>Přehled scénáře

Tato ukázka monitoruje aktuální počasí umístění a upozorní uživatele podle SMS jsou-li skies jasné. Normální aktivovaného časovačem funkce můžete použít ke kontrole počasí a odesílání výstrah. Je však jeden problém s tímto přístupem **správu životního cyklu**. Pokud pouze jedna výstraha mají být odeslány, monitorování musí sama deaktivuje po zaškrtnutí počasí je zjištěna. Monitorování vzor můžete ukončit vlastní provádění mezi další výhody:

* Monitorování spustit v intervalech, není plánuje: aktivační událost časovače *spouští* každou hodinu; monitorování *čeká* jednu hodinu mezi akce. Akce monitorování nebude překrývat uvedeno, může být důležité pro dlouhotrvající úlohy.
* Monitorování mohou být dynamické intervaly: doba čekání můžete měnit v závislosti na nějaká podmínka.
* Monitorování můžete ukončit, pokud nějaká podmínka je dosažená nebo ukončeno jiným procesem.
* Monitorování může trvat parametry. Ukázka ukazuje, jak lze použít stejný postup počasí monitorování pro všechny požadované umístění a telefonní číslo.
* Monitorování jsou škálovatelné. Protože každé monitorování orchestration instance, bez nutnosti vytvářet nové funkce a definovat další kód lze vytvořit více monitorů.
* Monitorování snadno integrovat do větší pracovních postupů. Monitorování může být jeden oddíl složitější funkce orchestration, nebo [dílčí orchestration](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-sub-orchestrations).

## <a name="configuring-twilio-integration"></a>Konfigurace integrace Twilio

[!INCLUDE [functions-twilio-integration](../../includes/functions-twilio-integration.md)]

## <a name="configuring-weather-underground-integration"></a>Konfigurace počasí podzemní integrace

Tato ukázka, která využívá rozhraní API podzemní počasí ke kontrole aktuální počasí pro umístění.

První věcí, kterou je třeba je počasí podzemní účet. Můžete jej vytvořit zdarma v [ https://www.wunderground.com/signup ](https://www.wunderground.com/signup). Jakmile máte účet, musíte získat klíč rozhraní API. Můžete tak učinit navštivte stránky [ https://www.wunderground.com/weather/api ](https://www.wunderground.com/weather/api), pak výběrem klíč nastavení. Plán Stratus vývojáře je bezplatná a stačí ke spuštění této ukázce.

Až budete mít klíč rozhraní API, přidejte následující **nastavení aplikace** do funkce aplikace.

| Název nastavení aplikace | Hodnota Popis |
| - | - |
| **WeatherUndergroundApiKey**  | Klíč rozhraní API podzemní počasí. |

## <a name="the-functions"></a>Funkce

Tento článek vysvětluje v ukázkové aplikace následující funkce:

* `E3_Monitor`Funkce: orchestrator, který volá `E3_GetIsClear` pravidelně. Zavolá `E3_SendGoodWeatherAlert` Pokud `E3_GetIsClear` vrací hodnotu true.
* `E3_GetIsClear`: Aktivita funkce, která kontroluje aktuální počasí pro umístění.
* `E3_SendGoodWeatherAlert`: Aktivita funkce, která odešle zprávu SMS prostřednictvím Twilio.

Následující části popisují konfiguraci a kód, který se používá pro vývoj na portálu Azure. Kód pro vývoj v sadě Visual Studio se zobrazí na konci tohoto článku.
 
## <a name="the-weather-monitoring-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>Počasí monitorování orchestration (portálu ukázkový kód pro Visual Studio Code a Azure)

**E3_Monitor** funkce používá standardní *function.json* pro orchestrator funkce.

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_Monitor/function.json)]

Tady je kód, který implementuje funkce:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_Monitor/run.csx)]

Tato funkce orchestrator provede následující akce:

1. Získá **MonitorRequest** skládající se z *umístění* ke sledování a *telefonní číslo* které ho budou odeslat SMS oznámení.
2. Určuje dobu vypršení platnosti monitorování. Ukázka používá hodnotu pevně jako stručný výtah.
3. Volání **E3_GetIsClear** k určení, zda jsou v požadované umístění zrušte skies.
4. Pokud je zřejmé, počasí, zavolá **E3_SendGoodWeatherAlert** odeslat oznámení SMS na požadovaný telefonní číslo.
5. Vytvoří trvanlivý časovač obnovit orchestration při dalším intervalu dotazování. Ukázka používá hodnotu pevně jako stručný výtah.
6. Běh dokud pokračuje [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) předává čas vypršení platnosti monitorování nebo výstrahu SMS se odesílají.

Více instancemi nástroje orchestrator můžete současně spustit odesláním více **MonitorRequests**. Lze zadat umístění pro monitorování a telefonní číslo Odeslat výstrahu SMS.

## <a name="strongly-typed-data-transfer"></a>Přenos dat silného typu

Orchestrator vyžaduje více částí dat, takže [sdílené objekty objektů POCO](functions-reference-csharp.md#reusing-csx-code) se používají pro přenos dat silného typu: [!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/MonitorRequest.csx)]

[!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/Location.csx)]

## <a name="helper-activity-functions"></a>Podpůrné funkce aktivity

Jako další ukázky podpůrné funkce aktivity jsou běžné funkce, které používají `activityTrigger` aktivovat vazby. **E3_GetIsClear** funkce získá aktuální počasí pomocí rozhraní API podzemní počasí a určuje, zda je sky zrušte. *Function.json* je definován následujícím způsobem:

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/function.json)]

A tady je implementace. Jako POCOs používaných pro přenos dat logiku a pracovat s rozhraním API volání a analyzovat odpověď, kterou je do sdílené třídy abstrahované JSON. Najdete ho v rámci [Visual Studio ukázkový kód](#run-the-sample).

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/run.csx)]

**E3_SendGoodWeatherAlert** funkce používá k odeslání serveru SMS zprávy upozornění koncového uživatele je vhodná doba pro procházení Twilio vazby. Jeho *function.json* je jednoduchý:

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/function.json)]

A zde je kód, který odešle zprávu SMS:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/run.csx)]

## <a name="run-the-sample"></a>Spuštění ukázky

Pomocí funkce aktivované protokolem HTTP zahrnuty ve vzorku, můžete spustit orchestration odesláním následující požadavku HTTP POST:

```
POST https://{host}/orchestrators/E3_Monitor
Content-Length: 77
Content-Type: application/json

{ "Location": { "City": "Redmond", "State": "WA" }, "Phone": "+1425XXXXXXX" }
```
```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
```

**E3_Monitor** instance spustí a dotazuje aktuální počasí podmínky pro požadovaný umístění. Pokud není zaškrtnuto, že zavolá funkce aktivity Odeslat výstrahu; počasí jinak nastaví časovač. Když vyprší platnost časovač, orchestration bude pokračovat.

Uvidíte, že orchestration aktivity podle funkce protokolech na portálu Azure Functions.

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

Orchestration bude [ukončit](durable-functions-instance-management.md#terminating-instances) po jeho vypršení časového limitu dosáhlo maximálního nebo zrušte skies zjištění. Můžete také použít `TerminateAsync` uvnitř jiné funkce nebo vyvolat **terminatePostUri** webhooku HTTP POST, kterou se odkazuje v 202 odpovědi výše, nahraďte `{text}` s důvod ukončení:

```
POST https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="visual-studio-sample-code"></a>Visual Studio ukázkový kód

Tady je orchestration jako jeden soubor jazyka C# v projektu sady Visual Studio:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs)]

## <a name="next-steps"></a>Další postup

Tato ukázka vám ukázal, jak použít trvanlivý funkce pro sledování stavu externího zdroje pomocí [trvanlivý časovače](durable-functions-timers.md) a podmíněnou logiku. Další příklad ukazuje, jak používat externí události a [trvanlivý časovače](durable-functions-timers.md) pro zpracování zásahem ze strany obsluhy.

> [!div class="nextstepaction"]
> [Spustit ukázku zásahem ze strany](durable-functions-phone-verification.md)
