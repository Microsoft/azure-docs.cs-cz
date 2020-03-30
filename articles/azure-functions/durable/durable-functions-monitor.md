---
title: Monitory v odolných funkcích – Azure
description: Zjistěte, jak implementovat monitorování stavu pomocí rozšíření Durable Functions pro funkce Azure.
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: ed92156df9d8e1e07b56cea4b1e64edee11d68d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562118"
---
# <a name="monitor-scenario-in-durable-functions---weather-watcher-sample"></a>Scénář sledování v aplikaci Durable Functions – ukázka sledování počasí

Vzor monitoru odkazuje na flexibilní *opakující se* proces v pracovním postupu – například dotazování, dokud nejsou splněny určité podmínky. Tento článek vysvětluje ukázku, která používá [trvalé funkce](durable-functions-overview.md) k implementaci monitorování.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Přehled scénáře

Tato ukázka monitoruje aktuální povětrnostní podmínky polohy a upozorní uživatele pomocí SMS, když je obloha čistá. Můžete použít funkci spouštění časovače ke kontrole počasí a odesílání upozornění. Jedním z problémů s tímto přístupem je však **řízení životnosti**. Pokud by měla být odeslána pouze jedna výstraha, monitor se musí po zjištění jasného počasí vypnout. Vzor monitorování může ukončit vlastní provádění, mimo jiné výhody:

* Monitory běží v intervalech, nikoli v plánech: aktivační událost časovače *se spouští* každou hodinu. monitor čeká jednu hodinu mezi *akcemi.* Akce monitoru se nebudou překrývat, pokud není zadáno, což může být důležité pro dlouhotrvající úlohy.
* Monitory mohou mít dynamické intervaly: čekací doba se může měnit v závislosti na určité podmínce.
* Monitorování může být ukončeno, pokud je splněna určitá podmínka, nebo může být ukončena jiným procesem.
* Monitory mohou přijímat parametry. Ukázka ukazuje, jak lze stejný proces sledování počasí použít na libovolné požadované místo a telefonní číslo.
* Monitory jsou škálovatelné. Vzhledem k tomu, že každý monitor je instanci orchestrace, lze vytvořit více monitorů bez nutnosti vytvářet nové funkce nebo definovat více kódu.
* Monitory se snadno integrují do větších pracovních postupů. Monitor může být jedna část složitější funkce orchestrace nebo [dílčí orchestrace](durable-functions-sub-orchestrations.md).

## <a name="configuration"></a>Konfigurace

### <a name="configuring-twilio-integration"></a>Konfigurace integrace Twilio

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

### <a name="configuring-weather-underground-integration"></a>Konfigurace integrace weather undergroundu

Tato ukázka zahrnuje použití rozhraní API weather underground ke kontrole aktuálních povětrnostních podmínek pro umístění.

První věc, kterou potřebujete, je počasí podzemní účet. Můžete si vytvořit jeden [https://www.wunderground.com/signup](https://www.wunderground.com/signup)zdarma na . Jakmile budete mít účet, budete muset získat klíč rozhraní API. Můžete tak učinit [https://www.wunderground.com/weather/api](https://www.wunderground.com/weather/api/?MR=1)tak, že navštívíte a pak vyberete Nastavení klíče. Stratus Developer plán je zdarma a stačí spustit tuto ukázku.

Jakmile budete mít klíč rozhraní API, přidejte do aplikace funkce následující **nastavení aplikace.**

| Název nastavení aplikace | Popis hodnoty |
| - | - |
| **WeatherUndergroundApiKey**  | Váš klíč rozhraní API Weather Underground. |

## <a name="the-functions"></a>Funkce

Tento článek vysvětluje následující funkce v ukázkové aplikaci:

* `E3_Monitor`: Funkce [orchestratoru,](durable-functions-bindings.md#orchestration-trigger) která pravidelně volá. `E3_GetIsClear` Volá, `E3_SendGoodWeatherAlert` `E3_GetIsClear` pokud vrátí true.
* `E3_GetIsClear`: [Funkce aktivity,](durable-functions-bindings.md#activity-trigger) která kontroluje aktuální povětrnostní podmínky pro umístění.
* `E3_SendGoodWeatherAlert`: Funkce aktivity, která odešle SMS zprávu přes Twilio.

### <a name="e3_monitor-orchestrator-function"></a>E3_Monitor funkce orchestrátoru

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=41-78,97-115)]

Orchestrator vyžaduje umístění pro sledování a telefonní číslo pro odeslání zprávy, když se stane jasné v umístění. Tato data jsou předána orchestrator jako `MonitorRequest` objekt silného typu.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Funkce **E3_Monitor** používá standardní *funkci.json* pro funkce orchestratoru.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/function.json)]

Zde je kód, který implementuje funkci:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/index.js)]

---

Tato funkce orchestrator provádí následující akce:

1. Získá **MonitorRequest** skládající se z *umístění* sledovat a *telefonní číslo,* na které bude odesílat oznámení SMS.
2. Určuje dobu vypršení platnosti monitoru. Ukázka používá pevně zakódomo hodnotu pro stručnost.
3. Vyzývá **E3_GetIsClear,** aby zjistil, zda na požadovaném místě je jasná obloha.
4. Pokud je počasí jasné, volání **E3_SendGoodWeatherAlert** zaúčelem odeslání SMS oznámení na požadované telefonní číslo.
5. Vytvoří trvalý časovač pro obnovení orchestrace v dalším intervalu dotazování. Ukázka používá pevně zakódomo hodnotu pro stručnost.
6. Pokračuje v běhu, dokud aktuální čas UTC nepřejde čas vypršení platnosti monitoru nebo je odeslána výstraha SMS.

Více instancí orchestratoru lze spustit současně voláním funkce orchestrator vícekrát. Lze zadat umístění, které chcete sledovat, a telefonní číslo, na které chcete odeslat upozornění sms.

### <a name="e3_getisclear-activity-function"></a>E3_GetIsClear funkce aktivity

Stejně jako u jiných vzorků, pomocné funkce `activityTrigger` aktivity jsou pravidelné funkce, které používají aktivační událost vazby. Funkce **E3_GetIsClear** získá aktuální povětrnostní podmínky pomocí rozhraní Weather Underground API a určí, zda je obloha jasná.

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=80-85)]

# <a name="javascript"></a>[Javascript](#tab/javascript)

*Funkce.json* je definována takto:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/function.json)]

A tady je implementace.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/index.js)]

---

### <a name="e3_sendgoodweatheralert-activity-function"></a>E3_SendGoodWeatherAlert funkce aktivity

Funkce **E3_SendGoodWeatherAlert** používá vazbu Twilio k odeslání zprávy SMS s upozorněním koncového uživatele, že je vhodná doba na procházku.

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=87-96,140-205)]

> [!NOTE]
> Budete muset nainstalovat `Microsoft.Azure.WebJobs.Extensions.Twilio` balíček Nuget ke spuštění ukázkového kódu.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Jeho *function.json* je jednoduchý:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/function.json)]

A tady je kód, který posílá SMS zprávu:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/index.js)]

---

## <a name="run-the-sample"></a>Spuštění ukázky

Pomocí funkcí spouštěných protokolem HTTP, které jsou součástí ukázky, můžete spustit orchestraci odesláním následujícího požadavku HTTP POST:

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

Instance **E3_Monitor** spustí a dotazuje aktuální povětrnostní podmínky pro požadované umístění. Pokud je jasné počasí, volá funkci aktivity k odeslání výstrahy; v opačném případě nastaví časovač. Po vypršení časovače orchestrace bude pokračovat.

Můžete zobrazit aktivitu orchestrace při pohledu na protokoly funkcí na portálu Azure Functions.

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

Orchestrace [bude ukončena,](durable-functions-instance-management.md) jakmile je dosaženo časového času nebo je detekována jasná obloha. Můžete také `TerminateAsync` použít (.NET) nebo `terminate` (JavaScript) uvnitř jiné funkce nebo vyvolat **terminatePostUri** HTTP POST webhook `{text}` odkazuje v odpovědi 202 výše, nahradí důvod pro ukončení:

```
POST https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="next-steps"></a>Další kroky

Tato ukázka ukázala, jak používat trvalé funkce ke sledování stavu externího zdroje pomocí trvalé časovače a podmíněné [logiky.](durable-functions-timers.md) Další ukázka ukazuje, jak používat externí události a [trvalé časovače](durable-functions-timers.md) pro zpracování lidské interakce.

> [!div class="nextstepaction"]
> [Spuštění vzorku lidské interakce](durable-functions-phone-verification.md)
