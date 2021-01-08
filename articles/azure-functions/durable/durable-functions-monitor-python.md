---
title: Monitory v Durable Functions (Python) – Azure
description: Naučte se implementovat monitorování stavu pomocí rozšíření Durable Functions pro Azure Functions (Python).
author: davidmrdavid
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: azfuncdf
ms.openlocfilehash: 9083821fa03c09949daaf3166367489248a4d7d2
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98029171"
---
# <a name="monitor-scenario-in-durable-functions---github-issue-monitoring-sample"></a>Scénář monitorování ve Durable Functions – ukázka monitorování problémů na GitHubu

Model monitorování odkazuje na flexibilní opakovaný proces v pracovním postupu – například dotazování do splnění určitých podmínek. Tento článek vysvětluje ukázku, která používá Durable Functions k implementaci monitorování.

[! ZAHRNOUT trvalé funkce – předpoklady]

## <a name="scenario-overview"></a>Přehled scénáře

Tato ukázka monitoruje počet problémů v úložišti GitHub a upozorní uživatele, pokud je k dispozici více než 3 otevřené problémy. K vyžádání otevřených počtů problémů v pravidelných intervalech můžete použít běžnou funkci aktivovanou časovačem. Jedním z problémů s tímto přístupem však je **Správa životnosti**. Pokud by se měla odeslat jenom jedna výstraha, sledování se musí po třech nebo více problémech vypnout. Model monitorování může ukončit své vlastní provádění mimo jiné výhody:

* Monitory se spouštějí v intervalech, nikoli v plánech: aktivační událost časovače se *spouští* každou hodinu. monitorování *počká* jednu hodinu mezi akcemi. Akce monitorování se nepřekrývají, pokud nejsou zadány, což může být důležité pro dlouhotrvající úlohy.
* Monitory můžou mít dynamické intervaly: čekací doba se může změnit na základě nějaké podmínky.
* Monitory mohou skončit, pokud je některá podmínka splněna nebo ukončena jiným procesem.
* Monitory mohou přijímat parametry. Ukázka ukazuje, jak může být stejný proces monitorování úložiště použit pro jakékoli požadované veřejné úložiště GitHub a telefonní číslo.
* Monitory jsou škálovatelné. Vzhledem k tomu, že každý monitor je instancí orchestrace, lze vytvořit více monitorů bez nutnosti vytvářet nové funkce nebo definovat více kódů.
* Monitorování se snadno integruje do větších pracovních postupů. Monitorování může být jeden oddíl složitější funkce orchestrace nebo [dílčí orchestrace](durable-functions-sub-orchestrations.md).

## <a name="configuration"></a>Konfigurace

### <a name="configuring-twilio-integration"></a>Konfigurace integrace Twilio

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>Funkce

Tento článek vysvětluje následující funkce v ukázkové aplikaci:

* `E3_Monitor`: [Funkce Orchestrator](durable-functions-bindings.md#orchestration-trigger) , která provádí `E3_TooManyOpenIssues` pravidelné volání. Volá, `E3_SendAlert` Pokud je návratová hodnota `E3_TooManyOpenIssues` `True` .
* `E3_TooManyOpenIssues`: [Funkce Activity](durable-functions-bindings.md#activity-trigger) , která kontroluje, jestli má úložiště moc velký počet otevřených problémů. Pro účely ukázky doporučujeme, abyste měli více než 3 otevřené problémy, které jsou příliš mnoho.
* `E3_SendAlert`: Funkce aktivity, která odesílá zprávu SMS prostřednictvím Twilio.

### <a name="e3_monitor-orchestrator-function"></a>E3_Monitor funkce Orchestrator

# <a name="python"></a>[Python](#tab/python)

Funkce **E3_Monitor** používá standardní *function.js* pro funkce nástroje Orchestrator.

[!code-json[Main](~/samples-durable-functions-python/samples/monitor/E3_Monitor/function.json)]

Zde je kód, který implementuje funkci:

[!code-python[Main](~/samples-durable-functions-python/samples/monitor/E3_Monitor/\_\_init\_\_.py)]

---

Tato funkce Orchestrator provádí následující akce:

1. Získá *úložiště* , které se má monitorovat, a *telefonní číslo* , na které se pošle oznámení SMS.
2. Určuje čas vypršení platnosti monitoru. Ukázka používá pevně zakódované hodnoty pro zkrácení.
3. Volá **E3_TooManyOpenIssues** k určení, jestli je v požadovaném úložišti moc velký počet otevřených problémů.
4. Pokud dojde k příliš mnoha problémům, volání **E3_SendAlert** k odeslání oznámení SMS na požadované telefonní číslo.
5. Vytvoří trvalý časovač pro pokračování orchestrace při dalším intervalu dotazování. Ukázka používá pevně zakódované hodnoty pro zkrácení.
6. Pokračuje v běhu, dokud aktuální čas UTC neprojde časem vypršení platnosti monitoru, nebo se pošle výstraha SMS.

Více instancí nástroje Orchestrator lze spustit současně voláním funkce Orchestrator vícekrát. Úložiště, které se má monitorovat, a telefonní číslo, na které se má odeslat výstraha SMS, se může zadat. Nakonec mějte na paměti, že funkce Orchestrator není při čekání na *časovač spuštěná* , takže se za ni nebudete účtovat.


### <a name="e3_toomanyopenissues-activity-function"></a>Funkce aktivity E3_TooManyOpenIssues

Stejně jako u jiných ukázek jsou funkce aktivity pomocníka běžné funkcemi, které používají `activityTrigger` vazbu triggeru. Funkce **E3_TooManyOpenIssues** načte seznam aktuálně otevřených problémů v úložišti a určí, zda jsou příliš mnoho z nich: více než 3 jako na naši ukázku.

# <a name="python"></a>[Python](#tab/python)

*function.jsv* je definován následujícím způsobem:

[!code-json[Main](~/samples-durable-functions-python/samples/monitor/E3_TooManyOpenIssues/function.json)]

A zde je implementace.

[!code-python[Main](~/samples-durable-functions-python/samples/monitor/E3_TooManyOpenIssues/\_\_init\_\_.py)]

---

### <a name="e3_sendalert-activity-function"></a>Funkce aktivity E3_SendAlert

Funkce **E3_SendAlert** používá vazbu Twilio k odeslání zprávy SMS s upozorněním na koncového uživatele, že nejméně 3 otevřené problémy čekají na vyřešení.

# <a name="python"></a>[Python](#tab/python)

Jeho *function.jsv systému* je jednoduchý:

[!code-json[Main](~/samples-durable-functions-python/samples/monitor/E3_TooManyOpenIssues/function.json)]

A zde je kód, který odesílá zprávu SMS:

[!code-python[Main](~/samples-durable-functions-python/samples/monitor/E3_SendAlert/\_\_init\_\_.py)]

---

## <a name="run-the-sample"></a>Spuštění ukázky

Budete potřebovat účet [GitHub](https://github.com/) . Pomocí ní vytvoříte dočasné veřejné úložiště, na které můžete otevřít problémy.

Pomocí funkcí aktivovaných protokolem HTTP, které jsou součástí ukázky, můžete zahájit orchestraci odesláním následující žádosti HTTP POST:

```
POST https://{host}/orchestrators/E3_Monitor
Content-Length: 77
Content-Type: application/json

{ "repo": "<your github handle>/<a new github repo under your user>", "phone": "+1425XXXXXXX" }
```

Například pokud vaše uživatelské jméno GitHubu je `foo` a vaše úložiště je, `bar` vaše hodnota `"repo"` by měla být `"foo/bar"` .

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
```

Instance **E3_Monitor** se spustí a zadá dotaz na zadané úložiště pro otevřené problémy. Pokud se najde aspoň 3 otevřené problémy, zavolá funkci aktivity pro odeslání výstrahy. v opačném případě nastaví časovač. Po vypršení platnosti časovače bude orchestrace pokračovat.

Aktivitu orchestrace si můžete prohlédnout v protokolech funkce na portálu Azure Functions.

```
[2020-12-04T18:24:30.007Z] Executing 'Functions.HttpStart' (Reason='This function was programmatically 
called via the host APIs.', Id=93772f6b-f4f0-405a-9d7b-be9eb7a38aa6)
[2020-12-04T18:24:30.769Z] Executing 'Functions.E3_Monitor' (Reason='(null)', Id=058e656e-bcb1-418c-95b3-49afcd07bd08)
[2020-12-04T18:24:30.847Z] Started orchestration with ID = '788420bb31754c50acbbc46e12ef4f9c'.
[2020-12-04T18:24:30.932Z] Executed 'Functions.E3_Monitor' (Succeeded, Id=058e656e-bcb1-418c-95b3-49afcd07bd08, Duration=174ms)
[2020-12-04T18:24:30.955Z] Executed 'Functions.HttpStart' (Succeeded, Id=93772f6b-f4f0-405a-9d7b-be9eb7a38aa6, Duration=1028ms)
[2020-12-04T18:24:31.229Z] Executing 'Functions.E3_TooManyOpenIssues' (Reason='(null)', Id=6fd5be5e-7f26-4b0b-98df-c3ac39125da3)
[2020-12-04T18:24:31.772Z] Executed 'Functions.E3_TooManyOpenIssues' (Succeeded, Id=6fd5be5e-7f26-4b0b-98df-c3ac39125da3, Duration=555ms)
[2020-12-04T18:24:40.754Z] Executing 'Functions.E3_Monitor' (Reason='(null)', Id=23915e4c-ddbf-46f9-b3f0-53289ed66082)
[2020-12-04T18:24:40.789Z] Executed 'Functions.E3_Monitor' (Succeeded, Id=23915e4c-ddbf-46f9-b3f0-53289ed66082, Duration=38ms)
(...trimmed...)
```

Orchestrace bude dokončena po dosažení časového limitu nebo při zjištění více než 3 otevřených problémů. Rozhraní API můžete použít také `terminate` uvnitř jiné funkce nebo vyvolat Webhook **terminatePostUri** http, na který odkazuje odpověď 202 výše. Chcete-li použít Webhook, nahraďte `{text}` důvod pro předčasné ukončení. Adresa URL POST protokolu HTTP bude vypadat přibližně takto:

```
POST https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="next-steps"></a>Další kroky

Tato ukázka ukázala, jak použít Durable Functions k monitorování stavu externího zdroje pomocí [trvalých časovačů](durable-functions-timers.md) a podmíněné logiky. Další příklad ukazuje, jak použít externí události a [trvalé časovače](durable-functions-timers.md) pro zpracování lidské interakce.

> [!div class="nextstepaction"]
> [Spuštění ukázky lidské interakce](durable-functions-phone-verification.md)
