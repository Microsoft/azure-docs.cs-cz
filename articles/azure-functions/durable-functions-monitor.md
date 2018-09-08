---
title: Monitorování v Durable Functions – Azure
description: Zjistěte, jak implementovat monitorování stavu pomocí rozšíření Durable Functions pro službu Azure Functions.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: azfuncdf
ms.openlocfilehash: f59fcecbb1f321c447e15469cf5584c5be8d7b96
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44094346"
---
# <a name="monitor-scenario-in-durable-functions---weather-watcher-sample"></a>Scénář monitorování v Durable Functions – ukázka počasí sledovacího procesu

Vzor monitorování odkazuje na flexibilní *opakované* procesu v pracovním postupu – například dotazování, dokud jsou splněny určité podmínky. Tento článek vysvětluje, ukázky, která používá [Durable Functions](durable-functions-overview.md) provádět monitorování.

## <a name="prerequisites"></a>Požadavky

* [Nainstalujte Durable Functions](durable-functions-install.md).
* Dokončení [Hello pořadí](durable-functions-sequence.md) návodu.

## <a name="scenario-overview"></a>Přehled scénáře

Tato ukázka sleduje aktuální počasí do umístění a upozorní uživatele pomocí služby SMS, když se skies jsou jasně. Normální funkce aktivované pomocí časovače můžete použít ke kontrole počasí a odeslat oznámení. Je však jeden problém s tímto přístupem **Správa životního cyklu**. Pokud pouze jedna výstraha mají být odeslány, je potřeba monitorování sama deaktivuje po nezašifrovaný zjistí počasí. Monitorování vzor můžete ukončit svou vlastní spuštění, kromě jiných výhod:

* Monitorování spuštění v intervalech, ne Naplánuje: trigger časovače *spustí* každou hodinu; monitorování *čeká* hodinu mezi akcemi. Akce sledování se nesmějí překrývat uvedeno, což může být důležité pro dlouho běžící úlohy.
* Monitorování můžou být dynamické intervaly: doba čekání lze změnit v závislosti na některé podmínky.
* Monitorování lze ukončit, když je splněna některá podmínka nebo ukončeno jiným procesem.
* Monitorování může mít parametry. Ukázka ukazuje, jak stejným procesem monitorování počasí lze použít pro jakékoli požadované umístění a telefonní číslo.
* Monitorování jsou škálovatelné. Protože jednotlivých monitorování je instance Orchestrace, je možné vytvořit více monitorů bez nutnosti vytvářet nové funkce nebo definovat další kód.
* Monitorování dala se snadno integrovat do větší pracovních postupů. Monitorování může být jeden oddíl složitější funkce Orchestrace, nebo [dílčí Orchestrace](https://docs.microsoft.com/azure/azure-functions/durable-functions-sub-orchestrations).

## <a name="configuring-twilio-integration"></a>Konfigurace integrace platformy Twilio

[!INCLUDE [functions-twilio-integration](../../includes/functions-twilio-integration.md)]

## <a name="configuring-weather-underground-integration"></a>Konfigurace integrace nástroje alternativních počasí

Tento příklad zahrnuje použití alternativních rozhraní API počasí ke kontrole aktuální počasí pro určité místo.

První věc, kterou je třeba je počasí alternativních účet. Můžete si ho vytvořit zdarma na [ https://www.wunderground.com/signup ](https://www.wunderground.com/signup). Jakmile budete mít účet, musíte získat klíče rozhraní API. Můžete tak učinit návštěvou [ https://www.wunderground.com/weather/api ](https://www.wunderground.com/weather/api), poté vyberete nastavení klíče. Plán Stratus Developer je zdarma a stačí ke spuštění této ukázky.

Jakmile budete mít klíč rozhraní API, přidejte následující **nastavení aplikace, které** aplikaci function App.

| Název nastavení aplikace | Hodnota Popis |
| - | - |
| **WeatherUndergroundApiKey**  | Klíč rozhraní API alternativních počasí. |

## <a name="the-functions"></a>Funkce

Tento článek vysvětluje následující funkce v ukázkové aplikaci:

* `E3_Monitor`: Funkce orchestrátoru, který volá `E3_GetIsClear` pravidelně. Volá `E3_SendGoodWeatherAlert` Pokud `E3_GetIsClear` vrací hodnotu true.
* `E3_GetIsClear`Funkce: aktivitu, která kontroluje aktuální počasí pro určité místo.
* `E3_SendGoodWeatherAlert`: Aktivita funkce, která odešle zprávu SMS přes Twilio.

Následující části popisují konfiguraci a kód, který se používají pro skriptovací C# a JavaScript. Kód pro vývoj sady Visual Studio se zobrazí na konci tohoto článku.
 
## <a name="the-weather-monitoring-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>Počasí monitorování Orchestrace (Visual Studio Code a Azure portal ukázkový kód)

**E3_Monitor** funkce používá standardní *function.json* pro funkce nástroje orchestrator.

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_Monitor/function.json)]

Tady je kód, který implementuje funkce:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_Monitor/run.csx)]

### <a name="javascript-functions-v2-only"></a>JavaScript (jenom funkce v2)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/index.js)]

Tato funkce orchestrátoru provede následující akce:

1. Získá **MonitorRequest** skládající se z *umístění* k monitorování a *telefonní číslo* na který se odešle oznámení SMS.
2. Určuje čas vypršení platnosti monitorování. Ukázka používá pevně zakódovaný hodnotu pro zkrácení.
3. Volání **E3_GetIsClear** k určení, zda jsou na požadované umístění skies vymazat.
4. Pokud se počasí je jasné, zavolá **E3_SendGoodWeatherAlert** odeslat oznámení SMS pro požadovaný telefonní číslo.
5. Vytvoří trvalý časovače obnovit Orchestrace při dalším intervalu dotazování. Ukázka používá pevně zakódovaný hodnotu pro zkrácení.
6. Dál běžela až [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) předá přijde čas vypršení platnosti monitorování a upozornění zprávou SMS.

Více instancemi nástroje orchestrator můžete současně spustit odesláním více **MonitorRequests**. Je možné zadat umístění pro monitorování a telefonního čísla pro zaslání upozornění zprávou SMS na.

## <a name="strongly-typed-data-transfer-net-only"></a>Přenos dat silného typu (pouze .NET)

Orchestrator vyžaduje více dat, takže [sdílené objekty POCO](functions-reference-csharp.md#reusing-csx-code) se používají pro přenos dat silného typu v jazyce C# a C# skript: [!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/MonitorRequest.csx)]

[!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/Location.csx)]

Ukázková JavaScript používá objekty regulárních JSON jako parametry.

## <a name="helper-activity-functions"></a>Pomocná funkce aktivity

Další ukázky jsou pomocné funkce aktivity běžných funkcí, které používají `activityTrigger` aktivovat vazby. **E3_GetIsClear** funkce získá aktuální počasí pomocí rozhraní API alternativních počasí a určuje, zda je oblohy vymazat. *Function.json* je definovaná následujícím způsobem:

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/function.json)]

A tady je implementace. Podobně jako POCOs použitá pro přenos dat logiky pracovat s rozhraním API volání a analyzovat odpověď, kterou je abstrahovaný JSON do sdílené třídy v jazyce C#. Najdete ho jako součást [sady Visual Studio ukázkový kód](#run-the-sample).

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/run.csx)]

### <a name="javascript-functions-v2-only"></a>JavaScript (jenom funkce v2)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/index.js)]

**E3_SendGoodWeatherAlert** funkce používá vazbu Twilio odeslat zprávu SMS upozornění koncového uživatele, že je vhodný čas pro procházení. Jeho *function.json* je jednoduchý:

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/function.json)]

A tady je kód, který se odešle zpráva SMS:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/run.csx)]

### <a name="javascript-functions-v2-only"></a>JavaScript (jenom funkce v2)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/index.js)]

## <a name="run-the-sample"></a>Spuštění ukázky

Použití ukázka funkcí aktivovanou protokolem HTTP, můžete spustit orchestraci odesláním následujících požadavku HTTP POST:

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

   > [!NOTE]
   > V současné době JavaScript Orchestrace starter funkce nemohou vracet instanci správy identifikátorů URI. Tato funkce bude přidána v novější verzi.

**E3_Monitor** spuštění instance a dotazy aktuální počasí pro požadované umístění. Pokud není zaškrtnuto, že volá funkci aktivity Odeslat výstrahu; počasí jinak nastaví časovače. Když čas vyprší, bude pokračovat orchestraci.

Uvidíte, že protokoly aktivit orchestraci pohledem na funkci na portálu Azure Functions.

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

Bude orchestraci [ukončit](durable-functions-instance-management.md#terminating-instances) po jeho vypršení časového limitu je dosaženo nebo jeho zaškrtnutí zrušte skies zjištění. Můžete také použít `TerminateAsync` uvnitř jiného funkci nebo volání **terminatePostUri** HTTP POST webhooku odkazuje v odpovědi 202 nad nahrazení `{text}` s důvod ukončení:

```
POST https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="visual-studio-sample-code"></a>Visual Studio ukázkový kód

Tady je Orchestrace jako jeden soubor jazyka C# v sadě Visual Studio projekt:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs)]

## <a name="next-steps"></a>Další postup

Tato ukázka vám ukázal, jak používat ke sledování stavu externí zdrojové Durable Functions pomocí [trvalý časovače](durable-functions-timers.md) a podmíněné logiky. Další příklad ukazuje, jak používat externí akce, které a [trvalý časovače](durable-functions-timers.md) zpracování lidské interakce.

> [!div class="nextstepaction"]
> [Spusťte ukázku lidská interakce](durable-functions-phone-verification.md)
