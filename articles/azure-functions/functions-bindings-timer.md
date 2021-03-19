---
title: Aktivační událost časovače pro Azure Functions
description: Vysvětlení použití aktivačních událostí časovače v Azure Functions.
author: craigshoemaker
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.topic: reference
ms.date: 11/18/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: f99f9d240a0a9220d3b7f57cddd0a4f8ba6b6101
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104595985"
---
# <a name="timer-trigger-for-azure-functions"></a>Aktivační událost časovače pro Azure Functions

Tento článek vysvětluje, jak pracovat s triggery časovače v Azure Functions. Aktivační událost časovače umožňuje spustit funkci podle plánu.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Informace o tom, jak ručně spustit funkci aktivovanou časovačem, najdete v tématu [Ruční spuštění funkce bez protokolu HTTP aktivovaného](./functions-manually-run-non-http.md)systémem.

## <a name="packages---functions-2x-and-higher"></a>Balíčky – funkce 2. x a vyšší

Aktivační událost časovače je k dispozici v balíčku NuGet [Microsoft. Azure. WebJobs. Extensions.](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) Version 3. x. Zdrojový kód balíčku je v úložišti GitHub [Azure-WebJobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/) .

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-1x"></a>Balíčky – funkce 1. x

Aktivační událost časovače je k dispozici v balíčku NuGet [Microsoft. Azure. WebJobs. Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) , verze 2. x. Zdrojový kód balíčku je v úložišti GitHub [Azure-WebJobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Timers/) .

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>Příklad

# <a name="c"></a>[C#](#tab/csharp)

Následující příklad ukazuje [funkci jazyka C#](functions-dotnet-class-library.md) , která se spustí pokaždé, když minuty mají hodnotu dělitelnou pěti (např. Pokud funkce začíná na 18:57:00, bude další výkon na 19:00:00). [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs)Objekt je předán do funkce.

```cs
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Následující příklad ukazuje vazbu triggeru časovače v *function.js* souboru a [funkci skriptu jazyka C#](functions-reference-csharp.md) , která používá vazbu. Funkce zapíše protokol, který označuje, zda je vyvolání této funkce způsobeno chybějícím výskytem plánu. [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs)Objekt je předán do funkce.

Tady jsou data vazby v *function.js* souboru:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Tady je kód skriptu jazyka C#:

```csharp
public static void Run(TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

# <a name="java"></a>[Java](#tab/java)

Následující ukázková funkce se spustí a provede každých pět minut. `@TimerTrigger`Poznámka na funkci definuje plán pomocí stejného formátu řetězce jako [cron výrazy](https://en.wikipedia.org/wiki/Cron#CRON_expression).

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Následující příklad ukazuje vazbu triggeru časovače v *function.js* souboru a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu. Funkce zapíše protokol, který označuje, zda je vyvolání této funkce způsobeno chybějícím výskytem plánu. Do funkce se předává [objekt Timer](#usage) .

Tady jsou data vazby v *function.js* souboru:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Tady je kód JavaScriptu:

```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if (myTimer.IsPastDue)
    {
        context.log('Node is running late!');
    }
    context.log('Node timer trigger function ran!', timeStamp);   

    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Následující příklad ukazuje, jak nakonfigurovat *function.jsv* a *run.ps1* souboru pro aktivační událost časovače v [prostředí PowerShell](./functions-reference-powershell.md).

```json
{
  "bindings": [
    {
      "name": "Timer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 */5 * * * *"
    }
  ]
}
```

```powershell
# Input bindings are passed in via param block.
param($Timer)

# Get the current universal time in the default string format.
$currentUTCtime = (Get-Date).ToUniversalTime()

# The 'IsPastDue' property is 'true' when the current function invocation is later than scheduled.
if ($Timer.IsPastDue) {
    Write-Host "PowerShell timer is running late!"
}

# Write an information log with the current time.
Write-Host "PowerShell timer trigger function ran! TIME: $currentUTCtime"
```

Instance [objektu Timer](#usage) je předána jako první argument funkce.

# <a name="python"></a>[Python](#tab/python)

Následující příklad používá vazbu triggeru časovače, jejíž konfigurace je popsána v *function.jsv* souboru. Skutečná [funkce jazyka Python](functions-reference-python.md) , která používá vazbu, je popsána v souboru *__init__. py* . Objekt předaný do funkce je typu [objekt Azure. Functions. TimerRequest](/python/api/azure-functions/azure.functions.timerrequest). Logika funkce zapisuje do protokolů, které označují, zda je aktuální vyvolání způsobeno chybějícím plánovaným výskytem.

Tady jsou data vazby v *function.js* souboru:

```json
{
    "name": "mytimer",
    "type": "timerTrigger",
    "direction": "in",
    "schedule": "0 */5 * * * *"
}
```

Tady je kód Pythonu:

```python
import datetime
import logging

import azure.functions as func


def main(mytimer: func.TimerRequest) -> None:
    utc_timestamp = datetime.datetime.utcnow().replace(
        tzinfo=datetime.timezone.utc).isoformat()

    if mytimer.past_due:
        logging.info('The timer is past due!')

    logging.info('Python timer trigger function ran at %s', utc_timestamp)
```

---

## <a name="attributes-and-annotations"></a>Atributy a poznámky

# <a name="c"></a>[C#](#tab/csharp)

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte rozhraní [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs).

Konstruktor atributu přebírá výraz CRON nebo `TimeSpan` . Můžete použít `TimeSpan` jenom v případě, že aplikace Function App běží v plánu App Service. `TimeSpan` se nepodporuje pro funkce spotřeby nebo elastické prémie.

Následující příklad ukazuje výraz CRON:

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Skripty jazyka C# nepodporují atributy.

# <a name="java"></a>[Java](#tab/java)

`@TimerTrigger`Poznámka na funkci definuje plán pomocí stejného formátu řetězce jako [cron výrazy](https://en.wikipedia.org/wiki/Cron#CRON_expression).

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell nepodporuje atributy.

# <a name="python"></a>[Python](#tab/python)

Python nepodporuje atributy.

---

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.jspro* soubor a `TimerTrigger` atribut.

|function.jsvlastnost | Vlastnost atributu |Description|
|---------|---------|----------------------|
|**textový** | Není k dispozici | Musí být nastavené na "timerTrigger". Tato vlastnost se nastaví automaticky při vytvoření triggeru v Azure Portal.|
|**směr** | Není k dispozici | Musí být nastavené na "in". Tato vlastnost se nastaví automaticky při vytvoření triggeru v Azure Portal. |
|**Jméno** | Není k dispozici | Název proměnné, která představuje objekt časovače v kódu funkce. | 
|**CXL**|**ScheduleExpression**|[Výraz cron](#ncrontab-expressions) nebo hodnota [TimeSpan](#timespan) . `TimeSpan`Dá se použít jenom pro aplikaci Function App, která běží na plánu App Service. Výraz plánu můžete zadat do nastavení aplikace a tuto vlastnost nastavit na název nastavení aplikace zabalené v **%** části znaky, jako v tomto příkladu: "% ScheduleAppSetting%". |
|**runOnStartup**|**RunOnStartup**|Pokud `true` je funkce vyvolána při spuštění modulu runtime. Například modul runtime začíná, když se aplikace funkce probudí po přechodu do stavu nečinnosti z důvodu neaktivity. Když se aplikace Function App restartuje kvůli změnám funkcí a když se aplikace Function App škáluje. Takže **runOnStartup** by měl být zřídka nastavený na `true` , zejména v produkčním prostředí. |
|**useMonitor**|**UseMonitor**|Nastavte na `true` nebo `false` , pokud chcete určit, jestli se má plán monitorovat. Plánování monitorování přetrvává i v případě, že dojde k podpoře při zajištění správné údržby plánu i v případě restartování instancí aplikace Function App. Pokud není nastavené explicitně, výchozí hodnota je `true` pro plány, které mají interval opakování větší nebo roven 1 minutě. Pro plány, které se spouštějí více než jednou za minutu, je výchozí hodnota `false` .

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!CAUTION]
> Doporučujeme nastavit **runOnStartup** na `true` v produkčním prostředí. Použití tohoto nastavení způsobí, že se kód spustí při velmi nepředvídatelných časech. V některých nastaveních produkčního prostředí můžou tato dodatečná spuštění vést k významně vyšším nákladům na aplikace hostované v plánech spotřeby. Například s povoleným **runOnStartup** je vyvolána Trigger při každé změně měřítka aplikace Function App. Než povolíte **runOnStartup** v produkčním prostředí, ujistěte se, že plně rozumíte provoznímu chování vašich funkcí.

## <a name="usage"></a>Využití

Když je vyvolána funkce Trigger časovače, je do funkce předán objekt Timer. Následující kód JSON je příklad reprezentace objektu Timer.

```json
{
    "schedule":{
    },
    "scheduleStatus": {
        "last":"2016-10-04T10:15:00+00:00",
        "lastUpdated":"2016-10-04T10:16:00+00:00",
        "next":"2016-10-04T10:20:00+00:00"
    },
    "isPastDue":false
}
```

`isPastDue`Vlastnost je v `true` případě, že aktuální volání funkce je pozdější než naplánované. Například restartování aplikace funkce může způsobit, že by se vyvolání vynechalo.

## <a name="ncrontab-expressions"></a>Výrazy NCRONTAB

Azure Functions používá knihovnu [NCronTab](https://github.com/atifaziz/NCrontab) k interpretaci výrazů NCronTab. Výraz NCRONTAB je podobný výrazu CRON s tím rozdílem, že obsahuje další šesté pole na začátku, které se má použít pro časovou přesnost v sekundách:

`{second} {minute} {hour} {day} {month} {day-of-week}`

Každé pole může mít jeden z následujících typů hodnot:

|Typ  |Příklad  |Při aktivaci  |
|---------|---------|---------|
|Konkrétní hodnota |<nobr>`0 5 * * * *`</nobr>| Jednou za hodinu dne v minutě 5 každé hodiny |
|Všechny hodnoty ( `*` )|<nobr>`0 * 5 * * *`</nobr>| V každou minutu v hodině, počínaje hodinou 5 |
|Rozsah ( `-` operátor)|<nobr>`5-7 * * * * *`</nobr>| Třikrát minutu v řádu 5 až 7 v každé minutě každého dne |
|Sada hodnot ( `,` operator)|<nobr>`5,8,10 * * * * *`</nobr>| Třikrát minutu v sekundách 5, 8 a 10 v každé minutě každého dne |
|Hodnota intervalu ( `/` operátor)|<nobr>`0 */5 * * * *`</nobr>| 12 hodin za hodinu v každé 5. den každé hodiny každého dne |

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="ncrontab-examples"></a>Příklady NCRONTAB

Tady je několik příkladů NCRONTAB výrazů, které můžete použít pro aktivační událost časovače v Azure Functions.

| Příklad            | Při aktivaci                     |
|--------------------|------------------------------------|
| `0 */5 * * * *`    | každých pět minut            |
| `0 0 * * * *`      | jednou na začátku každé hodiny      |
| `0 0 */2 * * *`    | každé dvě hodiny               |
| `0 0 9-17 * * *`   | jednou za hodinu od 9:00 do 5 odp.  |
| `0 30 9 * * *`     | každý den v 9:30.               |
| `0 30 9 * * 1-5`   | v 9:30. každý den v týdnu           |
| `0 30 9 * Jan Mon` | v 9:30 ráno každé pondělí v lednu |

> [!NOTE]
> Výraz NCRONTAB vyžaduje formát **pole Six** . Šestá pozice pole je hodnota pro sekundy, která je umístěna na začátku výrazu. V Azure se nepodporuje cron výrazy s pěti poli.

### <a name="ncrontab-time-zones"></a>NCRONTAB časová pásma

Čísla ve výrazu CRON odkazují na datum a čas, ne na časový rozsah. Například 5 v `hour` poli odkazuje na 5:00 dop. a ne každých 5 hodin.

[!INCLUDE [functions-timezone](../../includes/functions-timezone.md)]

## <a name="timespan"></a>TimeSpan

 `TimeSpan`Dá se použít jenom pro aplikaci Function App, která běží na plánu App Service.

Na rozdíl od výrazu CRON `TimeSpan` hodnota určuje časový interval mezi voláním funkce. Jakmile se funkce dokončí po spuštění déle, než je zadaný interval, časovač okamžitě vyvolá funkci znovu.

Vyjádřeno jako řetězec, `TimeSpan` formát je, `hh:mm:ss` Pokud `hh` je menší než 24. Pokud jsou první dvě číslice 24 nebo větší, formát je `dd:hh:mm` . Tady je několik příkladů:

| Příklad      | Při aktivaci |
|--------------|----------------|
| "01:00:00"   | každou hodinu     |
| "00:01:00"   | každou minutu   |
| "25:00:00"   | každých 25 dní  |
| "1,00:00:00" | Každý den      |

## <a name="scale-out"></a>Škálování na více instancí

Pokud se aplikace funkcí škáluje na více instancí, spustí se ve všech instancích jenom jedna instance funkce aktivované časovačem. Pokud je stále spuštěno nedokončené volání, nebude znovu aktivován.

## <a name="function-apps-sharing-storage"></a>Aplikace Function App pro sdílení úložiště

Pokud sdílíte účty úložiště napříč aplikacemi funkcí, které nejsou nasazené do služby App Service, může být nutné explicitně přiřadit ID hostitele ke každé aplikaci.

| Verze funkcí | Nastavení                                              |
| ----------------- | ---------------------------------------------------- |
| 2. x (a vyšší)  | `AzureFunctionsWebHost__hostid` Proměnná prostředí |
| verze               | `id` v *host.jsna*                                  |

Identifikujte hodnotu můžete vynechat nebo ručně nastavit identifikaci konfigurace aplikace Function App na jinou hodnotu.

Aktivační událost časovače používá zámek úložiště, aby zajistila, že pokud se aplikace funkcí škáluje na více instancí, je potřeba jenom jedna instance časovače. Pokud dvě aplikace Function App sdílí stejnou identifikaci konfigurace a každá z nich používá aktivační událost časovače, spustí se jenom jedno časovače.

## <a name="retry-behavior"></a>Chování opakování

Na rozdíl od triggeru fronty se aktivační událost časovače neopakuje po chybě funkce. Když funkce dojde k chybě, nebude znovu volána až do příštího plánu.

## <a name="manually-invoke-a-timer-trigger"></a>Ruční vyvolání triggeru časovače

Aktivační událost časovače pro Azure Functions poskytuje Webhook HTTP, který je možné vyvolat k ruční aktivaci funkce. To může být velmi užitečné v následujících scénářích.

* Testování integrace
* Zahození slotu jako součást aktivity typu kouřového testu nebo zahřívání
* Počáteční nasazení funkce k okamžitému naplnění mezipaměti nebo vyhledávací tabulky v databázi

Podrobnosti o tom, jak ručně aktivovat funkci aktivovanou časovačem, najdete v tématu [Ruční spuštění funkce neaktivované protokolem HTTP](./functions-manually-run-non-http.md) .

## <a name="troubleshooting"></a>Řešení potíží

Informace o tom, co dělat, když aktivační událost časovače nefunguje podle očekávání, najdete v tématu [zkoumání a hlášení problémů s aktivovanými funkcemi aktivované časovačem](https://github.com/Azure/azure-functions-host/wiki/Investigating-and-reporting-issues-with-timer-triggered-functions-not-firing).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přejít k rychlému startu, který používá aktivační událost časovače](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech a vazbách Azure Functions](functions-triggers-bindings.md)
