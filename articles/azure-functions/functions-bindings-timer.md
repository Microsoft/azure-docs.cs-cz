---
title: Aktivační událost časovače pro Azure Functions
description: Vysvětlení použití aktivačních událostí časovače v Azure Functions.
author: craigshoemaker
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.topic: reference
ms.date: 09/08/2018
ms.author: cshoe
ms.custom: ''
ms.openlocfilehash: f4fdf25fa1403b8429e7ad7e7fc644d0355b1324
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373693"
---
# <a name="timer-trigger-for-azure-functions"></a>Aktivační událost časovače pro Azure Functions 

Tento článek vysvětluje, jak pracovat s triggery časovače v Azure Functions. Aktivační událost časovače umožňuje spustit funkci podle plánu. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Balíčky – funkce 1.x

Aktivační událost časovače je k dispozici v balíčku NuGet [Microsoft. Azure. WebJobs. Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) , verze 2. x. Zdrojový kód balíčku je v úložišti GitHub [Azure-WebJobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Timers/) .

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x-and-higher"></a>Balíčky – funkce 2. x a vyšší

Aktivační událost časovače je k dispozici v balíčku NuGet [Microsoft. Azure. WebJobs. Extensions.](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) Version 3. x. Zdrojový kód balíčku je v úložišti GitHub [Azure-WebJobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/) .

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>Příklad

# <a name="c"></a>[C#](#tab/csharp)

Následující příklad ukazuje [ C# funkci](functions-dotnet-class-library.md) , která se spustí pokaždé, když minuty mají hodnotu dělitelnou pěti (např. Pokud funkce začíná na 18:57:00, bude další výkon na 19:00:00). Objekt [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) je předán do funkce.

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

# <a name="c-script"></a>[C#Pravidel](#tab/csharp-script)

Následující příklad ukazuje vazbu triggeru časovače v souboru *Function. JSON* a [ C# funkci skriptu](functions-reference-csharp.md) , která používá vazbu. Funkce zapíše protokol, který označuje, zda je vyvolání této funkce způsobeno chybějícím výskytem plánu. Objekt [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) je předán do funkce.

Tady jsou data vazby v souboru *Function. JSON* :

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Následující příklad ukazuje vazbu triggeru časovače v souboru *Function. JSON* a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu. Funkce zapíše protokol, který označuje, zda je vyvolání této funkce způsobeno chybějícím výskytem plánu. Do funkce se předává [objekt Timer](#usage) .

Tady jsou data vazby v souboru *Function. JSON* :

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Tady je kód jazyka JavaScript:

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

# <a name="python"></a>[Python](#tab/python)

Následující příklad používá vazbu triggeru časovače, jejíž konfigurace je popsána v souboru *Function. JSON* . Skutečná [funkce jazyka Python](functions-reference-python.md) , která používá vazbu, je popsána v souboru  *__init__. py* . Objekt předaný do funkce je typu [objekt Azure. Functions. TimerRequest](/python/api/azure-functions/azure.functions.timerrequest). Logika funkce zapisuje do protokolů, které označují, zda je aktuální vyvolání způsobeno chybějícím plánovaným výskytem. 

Tady jsou data vazby v souboru *Function. JSON* :

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

# <a name="java"></a>[Java](#tab/java)

Následující ukázková funkce se spustí a provede každých pět minut. `@TimerTrigger` anotace funkce definuje plán pomocí stejného formátu řetězce jako [cron výrazy](https://en.wikipedia.org/wiki/Cron#CRON_expression).

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

---

## <a name="attributes-and-annotations"></a>Atributy a poznámky

# <a name="c"></a>[C#](#tab/csharp)

V [ C# knihovně tříd](functions-dotnet-class-library.md)použijte [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs).

Konstruktor atributu přebírá výraz CRON nebo `TimeSpan`. `TimeSpan` můžete použít jenom v případě, že je aplikace Function App spuštěná v plánu App Service. `TimeSpan` se nepodporuje pro funkce zvýhodnění a elastických prémií.

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

# <a name="c-script"></a>[C#Pravidel](#tab/csharp-script)

C# Skript nepodporuje atributy.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

# <a name="python"></a>[Python](#tab/python)

Python nepodporuje atributy.

# <a name="java"></a>[Java](#tab/java)

`@TimerTrigger` anotace funkce definuje plán pomocí stejného formátu řetězce jako [cron výrazy](https://en.wikipedia.org/wiki/Cron#CRON_expression).

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

---

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v souboru *Function. JSON* a atributu `TimerTrigger`.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | neuvedeno | Musí být nastavené na "timerTrigger". Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal.|
|**direction** | neuvedeno | Musí být nastavena na "in". Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal. |
|**Jméno** | neuvedeno | Název proměnné, která představuje objekt časovače v kódu funkce. | 
|**schedule**|**ScheduleExpression**|[Výraz cron](#ncrontab-expressions) nebo hodnota [TimeSpan](#timespan) . `TimeSpan` se dá použít jenom pro aplikaci Function App, která běží na plánu App Service. Výraz plánu můžete zadat do nastavení aplikace a tuto vlastnost nastavit na název nastavení aplikace zabalené v **%** znaménka, jako v tomto příkladu: "% ScheduleAppSetting%". |
|**runOnStartup**|**RunOnStartup**|Pokud `true`, funkce je vyvolána při spuštění modulu runtime. Například modul runtime začíná, když se aplikace funkce probudí po přechodu do stavu nečinnosti z důvodu neaktivity. Když se aplikace Function App restartuje kvůli změnám funkcí a když se aplikace Function App škáluje. Takže **runOnStartup** by měl být zřídka nastavený na `true`, zejména v produkčním prostředí. |
|**useMonitor**|**UseMonitor**|Chcete-li určit, zda má být plán monitorován, nastavte `true` nebo `false`. Plánování monitorování přetrvává i v případě, že dojde k podpoře při zajištění správné údržby plánu i v případě restartování instancí aplikace Function App. Pokud není nastavena explicitně, výchozí hodnota je `true` pro plány, které mají interval opakování větší nebo roven 1 minute. Pro plány, které se spouštějí více než jednou za minutu, je výchozí hodnota `false`.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!CAUTION]
> Doporučujeme nastavit **runOnStartup** na `true` v produkčním prostředí. Použití tohoto nastavení způsobí, že se kód spustí při velmi nepředvídatelných časech. V některých nastaveních produkčního prostředí můžou tato dodatečná spuštění vést k významně vyšším nákladům na aplikace hostované v plánech spotřeby. Například s povoleným **runOnStartup** je vyvolána Trigger při každé změně měřítka aplikace Function App. Než povolíte **runOnStartup** v produkčním prostředí, ujistěte se, že plně rozumíte provoznímu chování vašich funkcí.   

## <a name="usage"></a>Využití

Když je vyvolána funkce Trigger časovače, je do funkce předán objekt Timer. Následující kód JSON je příklad reprezentace objektu Timer.

```json
{
    "Schedule":{
    },
    "ScheduleStatus": {
        "Last":"2016-10-04T10:15:00+00:00",
        "LastUpdated":"2016-10-04T10:16:00+00:00",
        "Next":"2016-10-04T10:20:00+00:00"
    },
    "IsPastDue":false
}
```

Vlastnost `IsPastDue` je `true`, když je aktuální funkce vyvolána později než naplánovaná. Například restartování aplikace funkce může způsobit, že by se vyvolání vynechalo.

## <a name="ncrontab-expressions"></a>Výrazy NCRONTAB 

Azure Functions používá knihovnu [NCronTab](https://github.com/atifaziz/NCrontab) k interpretaci výrazů NCronTab. Výraz NCRONTAB je podobný výrazu CRON s tím rozdílem, že obsahuje další šesté pole na začátku, které se má použít pro časovou přesnost v sekundách:

`{second} {minute} {hour} {day} {month} {day-of-week}`

Každé pole může mít jeden z následujících typů hodnot:

|Typ  |Příklad  |Při aktivaci  |
|---------|---------|---------|
|Konkrétní hodnota |<nobr>"0 5 * * * *"</nobr>|v hh: 05:00, kde HH je každou hodinu (jednou za hodinu)|
|Všechny hodnoty (`*`)|<nobr>"0 * 5 * * *"</nobr>|5: mm: 00 každý den, kde mm je každé minuty hodiny (60 dní)|
|Rozsah (operátor`-`)|<nobr>"5-7 * * * * *"</nobr>|v hh: mm: 05, hh: mm: 06 a hh: mm: 07, kde hh: mm je každou minutu každou hodinu (3 hodiny za minutu)|
|Sada hodnot (operátor`,`)|<nobr>"5, 8, 10 * * * * *"</nobr>|v hh: mm: 05, hh: mm: 08 a hh: mm: 10, kde hh: mm je každou minutu každou hodinu (3 hodiny za minutu)|
|Hodnota intervalu (operátor`/`)|<nobr>"0 */5 * * * *"</nobr>|v hh: 00:00, hh: 05:00, hh: 10:00, a tak dále až hh: 55:00, kde HH je každou hodinu (12 hodin za hodinu)|

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="ncrontab-examples"></a>Příklady NCRONTAB

Tady je několik příkladů NCRONTAB výrazů, které můžete použít pro aktivační událost časovače v Azure Functions.

|Příklad|Při aktivaci  |
|---------|---------|
|`"0 */5 * * * *"`|každých pět minut|
|`"0 0 * * * *"`|jednou na začátku každé hodiny|
|`"0 0 */2 * * *"`|každé dvě hodiny|
|`"0 0 9-17 * * *"`|jednou za hodinu od 9:00 do 5 odp.|
|`"0 30 9 * * *"`|každý den v 9:30.|
|`"0 30 9 * * 1-5"`|v 9:30. každý den v týdnu|
|`"0 30 9 * Jan Mon"`|v 9:30 ráno každé pondělí v lednu|


### <a name="ncrontab-time-zones"></a>NCRONTAB časová pásma

Čísla ve výrazu CRON odkazují na datum a čas, ne na časový rozsah. Například 5 v poli `hour` odkazuje na 5:00 a ne každých 5 hodin.

Výchozí časové pásmo použité s výrazy CRON je koordinovaný světový čas (UTC). Pokud chcete mít výraz CRON založený na jiném časovém pásmu, vytvořte nastavení aplikace pro aplikaci Function App s názvem `WEBSITE_TIME_ZONE`. Nastavte hodnotu na název požadovaného časového pásma, jak je znázorněno v [indexu časového pásma Microsoftu](https://technet.microsoft.com/library/cc749073).

  > [!NOTE]
  > `WEBSITE_TIME_ZONE` se v plánu spotřeby v systému Linux aktuálně nepodporuje.

Například *východní běžný čas* je UTC-05:00. Pokud chcete, aby se aktivovala aktivační událost časovače v 10:00.., použijte následující výraz NCRONTAB, který obsahuje účty pro časové pásmo UTC:

```
"0 0 15 * * *"
``` 

Nebo vytvořte nastavení aplikace pro aplikaci Function App s názvem `WEBSITE_TIME_ZONE` a nastavte hodnotu na **východní běžný čas**.  Pak používá následující výraz NCRONTAB: 

```
"0 0 10 * * *"
``` 

Když použijete `WEBSITE_TIME_ZONE`, čas se upraví na časové změny v konkrétním časovém pásmu, jako je například letní čas. 

## <a name="timespan"></a>TimeSpan

 `TimeSpan` se dá použít jenom pro aplikaci Function App, která běží na plánu App Service.

Na rozdíl od výrazu CRON určuje `TimeSpan` hodnota časový interval mezi voláními funkce. Jakmile se funkce dokončí po spuštění déle, než je zadaný interval, časovač okamžitě vyvolá funkci znovu.

Vyjádřeno jako řetězec, formát `TimeSpan` je `hh:mm:ss`, pokud je `hh` menší než 24. Pokud jsou první dvě číslice 24 nebo větší, formát je `dd:hh:mm`. Zde je několik příkladů:

|Příklad |Při aktivaci  |
|---------|---------|
|"01:00:00" | každou hodinu        |
|"00:01:00"|každou minutu         |
|"24:00:00" | každých 24 hodin        |
|"1,00:00:00" | Každý den        |

## <a name="scale-out"></a>Horizontální navýšení kapacity

Pokud se aplikace funkcí škáluje na více instancí, spustí se ve všech instancích jenom jedna instance funkce aktivované časovačem.

## <a name="function-apps-sharing-storage"></a>Aplikace Function App pro sdílení úložiště

Pokud sdílíte účty úložiště napříč aplikacemi funkcí, které nejsou nasazené do služby App Service, může být nutné explicitně přiřadit ID hostitele ke každé aplikaci.

| Verze funkcí | Nastavení                                              |
| ----------------- | ---------------------------------------------------- |
| 2. x (a vyšší)  | `AzureFunctionsWebHost__hostid` proměnná prostředí |
| verze               | `id` v *Host. JSON*                                  |

Identifikujte hodnotu můžete vynechat nebo ručně nastavit identifikaci konfigurace aplikace Function App na jinou hodnotu.

Aktivační událost časovače používá zámek úložiště, aby zajistila, že pokud se aplikace funkcí škáluje na více instancí, je potřeba jenom jedna instance časovače. Pokud dvě aplikace Function App sdílí stejnou identifikaci konfigurace a každá z nich používá aktivační událost časovače, spustí se jenom jedno časovače.

## <a name="retry-behavior"></a>Chování opakování

Na rozdíl od triggeru fronty se aktivační událost časovače neopakuje po chybě funkce. Když funkce dojde k chybě, nebude znovu volána až do příštího plánu.

## <a name="troubleshooting"></a>Odstraňování potíží

Informace o tom, co dělat, když aktivační událost časovače nefunguje podle očekávání, najdete v tématu [zkoumání a hlášení problémů s aktivovanými funkcemi aktivované časovačem](https://github.com/Azure/azure-functions-host/wiki/Investigating-and-reporting-issues-with-timer-triggered-functions-not-firing).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přejít k rychlému startu, který používá aktivační událost časovače](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech a vazbách Azure Functions](functions-triggers-bindings.md)
