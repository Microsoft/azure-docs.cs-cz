---
title: Aktivační událost časovače pro funkce Azure
description: Zjistěte, jak používat aktivační události časovače v Azure Functions.
author: craigshoemaker
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.topic: reference
ms.date: 09/08/2018
ms.author: cshoe
ms.custom: ''
ms.openlocfilehash: 566d6ccf43024692e19bcd6639fe5cfbbba0660d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056407"
---
# <a name="timer-trigger-for-azure-functions"></a>Aktivační událost časovače pro funkce Azure 

Tento článek vysvětluje, jak pracovat s aktivačními událostmi časovače v Azure Functions. Aktivační událost časovače umožňuje spustit funkci podle plánu. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Balíčky - Funkce 1.x

Aktivační událost časovače je k dispozici v balíčku [Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet verze 2.x. Zdrojový kód pro balíček je v úložišti [GitHub azure-webjobs-sdk-extensions.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Timers/)

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x-and-higher"></a>Balíčky - Funkce 2.x a vyšší

Aktivační událost časovače je k dispozici v balíčku [Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet verze 3.x. Zdrojový kód pro balíček je v úložišti [GitHub azure-webjobs-sdk-extensions.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/)

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>Příklad

# <a name="c"></a>[C #](#tab/csharp)

Následující příklad ukazuje [c# funkce,](functions-dotnet-class-library.md) která je spuštěna pokaždé, když minuty mají hodnotu dělitelnou pět (například pokud funkce začíná v 18:57:00, další výkon bude v 19:00:00). Objekt [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) je předán do funkce.

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

Následující příklad ukazuje vazby aktivační události časovače v souboru *function.json* a [funkci skriptu Jazyka C#,](functions-reference-csharp.md) která používá vazbu. Funkce zapíše protokol označující, zda je vyvolání této funkce z důvodu výskytu zmeškaného plánu. Objekt [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) je předán do funkce.

Zde jsou data vazby v souboru *function.json:*

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Zde je kód skriptu C#:

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

Následující příklad ukazuje vazbu aktivační události časovače v souboru *function.json* a [funkci JavaScriptu,](functions-reference-node.md) která vazbu používá. Funkce zapíše protokol označující, zda je vyvolání této funkce z důvodu výskytu zmeškaného plánu. [Objekt časovače](#usage) je předán do funkce.

Zde jsou data vazby v souboru *function.json:*

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Zde je kód JavaScript:

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

Následující příklad používá vazbu aktivační události časovače, jejíž konfigurace je popsána v souboru *function.json.* Skutečná [funkce Pythonu,](functions-reference-python.md) která používá vazbu, je popsána v souboru * __init__.py.* Objekt předaná do funkce je typu [azure.functions.TimerRequest objektu](/python/api/azure-functions/azure.functions.timerrequest). Logika funkce zapisuje do protokolů označující, zda aktuální vyvolání je z důvodu výskytu zmeškaný plán. 

Zde jsou data vazby v souboru *function.json:*

```json
{
    "name": "mytimer",
    "type": "timerTrigger",
    "direction": "in",
    "schedule": "0 */5 * * * *"
}
```

Zde je kód Pythonu:

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

Následující příklad funkce aktivuje a spustí každých pět minut. Anotace `@TimerTrigger` na funkci definuje plán pomocí stejného formátu řetězce jako [výrazy CRON](https://en.wikipedia.org/wiki/Cron#CRON_expression).

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

# <a name="c"></a>[C #](#tab/csharp)

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte [atribut TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs).

Konstruktor atributu přebírá výraz CRON `TimeSpan`nebo . Můžete použít `TimeSpan` pouze v případě, že aplikace funkce běží na plán služby App Service. `TimeSpan`není podporována pro spotřeba nebo elastické prémiové funkce.

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

Atributy nejsou podporovány skriptem jazyka C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Atributy nejsou podporovány javascriptem.

# <a name="python"></a>[Python](#tab/python)

Atributy nejsou podporovány Pythonem.

# <a name="java"></a>[Java](#tab/java)

Anotace `@TimerTrigger` na funkci definuje plán pomocí stejného formátu řetězce jako [výrazy CRON](https://en.wikipedia.org/wiki/Cron#CRON_expression).

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

Následující tabulka vysvětluje vlastnosti konfigurace vazby, které jste nastavili `TimerTrigger` v souboru *function.json* a atributu.

|vlastnost function.json | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Typ** | neuvedeno | Musí být nastavena na "timerTrigger". Tato vlastnost se nastaví automaticky při vytváření aktivační události na webu Azure Portal.|
|**direction** | neuvedeno | Musí být nastavena na "in". Tato vlastnost se nastaví automaticky při vytváření aktivační události na webu Azure Portal. |
|**Jméno** | neuvedeno | Název proměnné, která představuje objekt timer v kódu funkce. | 
|**Plán**|**ScheduleExpression**|[Výraz CRON](#ncrontab-expressions) nebo hodnota [TimeSpan.](#timespan) A `TimeSpan` lze použít pouze pro aplikaci funkce, která běží na plán služby App Service. Výraz plánu můžete umístit do nastavení aplikace a nastavit tuto vlastnost **%** na název nastavení aplikace zabalený do značek, jako v tomto příkladu: "%ScheduleAppSetting%". |
|**runOnStartup**|**Spuštění runonu**|Pokud `true`je funkce vyvolána při spuštění běhu. Například runtime spustí, když aplikace funkce probudí po nečinnosti z důvodu nečinnosti. když se aplikace funkce restartuje kvůli změnám funkce a když se aplikace funkce vyšpaí. Takže **runOnStartup** by měl jen `true`zřídka, pokud vůbec být nastavena na , zejména v produkčním prostředí. |
|**useMonitor**|**UseMonitor**|Nastavte `true` nebo `false` označte, zda má být plán sledován. Monitorování plánu trvá plán výskyty pomoci při zajištění plánu je udržována správně i při restartování aplikace funkce. Pokud není nastavena explicitně, výchozí je `true` pro plány, které mají interval opakování větší nebo rovna 1 minutu. Pro plány, které se aktivují `false`více než jednou za minutu, je výchozí nastavení .

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!CAUTION]
> Doporučujeme proti nastavení **runOnStartup** do `true` produkčního prostředí. Pomocí tohoto nastavení je spuštění kódu ve velmi nepředvídatelných časech. V některých produkčních nastaveních mohou tato další spuštění vést k výrazně vyšším nákladům na aplikace hostované v plánech spotřeby. Například s **povolenorunOnStartup** aktivační událost je vyvolána vždy, když je škálování aplikace funkce. Ujistěte se, že plně pochopit produkční chování vašich funkcí před povolením **runOnStartup** v produkčním prostředí.   

## <a name="usage"></a>Využití

Při vyvolání funkce aktivační události časovače je do funkce předán objekt timer. Následující JSON je příklad reprezentace objektu timer.

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

Vlastnost `IsPastDue` je, `true` když aktuální vyvolání funkce je pozdější, než je naplánováno. Restartování aplikace funkce může například způsobit, že vyvolání bude zmeškat.

## <a name="ncrontab-expressions"></a>Výrazy NCRONTAB 

Funkce Azure používá knihovnu [NCronTab](https://github.com/atifaziz/NCrontab) k interpretaci výrazů NCRONTAB. Výraz NCRONTAB je podobný výrazu CRON s tím rozdílem, že obsahuje další šesté pole na začátku pro časovou přesnost v sekundách:

`{second} {minute} {hour} {day} {month} {day-of-week}`

Každé pole může mít jeden z následujících typů hodnot:

|Typ  |Příklad  |Při spuštění  |
|---------|---------|---------|
|Konkrétní hodnota |<nobr>"0 5 * * * *"</nobr>|v hh:05:00, kde hh je každou hodinu (jednou za hodinu)|
|Všechny hodnoty`*`( )|<nobr>"0 * 5 * * *"</nobr>|v 5:mm:00 každý den, kde mm je každá minuta hodiny (60 krát denně)|
|Rozsah (operátor)`-`|<nobr>"5-7 * * * * *"</nobr>|na hh:mm:05,hh:mm:06, a hh:mm:07 kde hh:mm je každá minuta každou hodinu (3 krát za minutu)|
|Sada hodnot (operátor)`,`|<nobr>"5,8,10 * * * * *"</nobr>|na hh:mm:05,hh:mm:08 a hh:mm:10, kde hh:mm je každá minuta každou hodinu (3 krát za minutu)|
|Hodnota intervalu`/` (operátor)|<nobr>"0 */5 * * * *"</nobr>|v hh:00:00, hh:05:00, hh:10:00, a tak dále přes hh:55:00, kde hh je každou hodinu (12 krát za hodinu)|

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="ncrontab-examples"></a>Příklady NCRONTAB

Tady jsou některé příklady výrazů NCRONTAB, které můžete použít pro aktivační událost časovače v Azure Functions.

|Příklad|Při spuštění  |
|---------|---------|
|`"0 */5 * * * *"`|jednou za pět minut|
|`"0 0 * * * *"`|jednou v horní části každé hodiny|
|`"0 0 */2 * * *"`|jednou za dvě hodiny|
|`"0 0 9-17 * * *"`|jednou za hodinu od 9:00 do 17:00|
|`"0 30 9 * * *"`|v 9:30 každý den|
|`"0 30 9 * * 1-5"`|v 9:30 každý všední den|
|`"0 30 9 * Jan Mon"`|v 9:30 každé pondělí v lednu|


### <a name="ncrontab-time-zones"></a>Časová pásma NCRONTAB

Čísla ve výrazu CRON odkazují na čas a datum, nikoli na časové rozpětí. Například 5 v `hour` poli odkazuje na 5:00 AM, ne každých 5 hodin.

Výchozí časové pásmo používané s výrazy CRON je koordinovaný světový čas (UTC). Chcete-li mít výraz CRON založený na jiném časovém pásmu, vytvořte nastavení aplikace pro aplikaci s názvem `WEBSITE_TIME_ZONE`funkce . Nastavte hodnotu na název požadovaného časového pásma, jak je znázorněno v [indexu časového pásma společnosti Microsoft](https://technet.microsoft.com/library/cc749073).

  > [!NOTE]
  > `WEBSITE_TIME_ZONE`není aktuálně podporován v plánu Linux consumption.

Východní *standardní čas* je například UTC-05:00. Chcete-li, aby se časovač spustil každý den v 10:00 EST, použijte následující výraz NCRONTAB, který odpovídá časovému pásmu UTC:

```
"0 0 15 * * *"
``` 

Nebo vytvořte nastavení aplikace pro `WEBSITE_TIME_ZONE` aplikaci funkce s názvem a nastavte hodnotu na **východní standardní čas**.  Potom použije následující výraz NCRONTAB: 

```
"0 0 10 * * *"
``` 

Při použití `WEBSITE_TIME_ZONE`je čas upraven pro změny času v určitém časovém pásmu, jako je například letní čas. 

## <a name="timespan"></a>TimeSpan

 A `TimeSpan` lze použít pouze pro aplikaci funkce, která běží na plán služby App Service.

Na rozdíl od `TimeSpan` výrazu CRON určuje hodnota časový interval mezi jednotlivými vyvoláními funkce. Po dokončení funkce po spuštění delší než zadaný interval časovač okamžitě vyvolá funkci znovu.

Vyjádřeno jako řetězec, `TimeSpan` formát `hh:mm:ss` `hh` je, když je menší než 24. Pokud jsou první dvě číslice 24 nebo `dd:hh:mm`vyšší, formát je . Zde je několik příkladů:

|Příklad |Při spuštění  |
|---------|---------|
|"01:00:00" | každou hodinu        |
|"00:01:00"|každou minutu         |
|"24:00:00" | každých 24 dní        |
|"1.00:00:00" | Každý den        |

## <a name="scale-out"></a>Škálování na více instancí

Pokud se aplikace funkce škáluje na více instancí, je spuštěna pouze jedna instance funkce spouštěné časovačem ve všech instancích.

## <a name="function-apps-sharing-storage"></a>Funkce aplikací sdílení úložiště

Pokud sdílíte účty úložiště napříč aplikacemi funkcí, které nejsou nasazené do služby aplikace, možná budete muset explicitně přiřadit ID hostitele každé aplikaci.

| Verze funkcí | Nastavení                                              |
| ----------------- | ---------------------------------------------------- |
| 2.x (a vyšší)  | `AzureFunctionsWebHost__hostid`proměnná prostředí |
| 1.x               | `id`v *host.json*                                  |

Můžete vynechat identifikační hodnotu nebo ručně nastavit identifikaci konfigurace jednotlivých aplikací funkce na jinou hodnotu.

Aktivační událost časovače používá zámek úložiště k zajištění, že existuje pouze jedna instance časovače, když se aplikace funkce škáluje na více instancí. Pokud dvě aplikace funkcí sdílejí stejnou identifikační konfiguraci a každá používá aktivační událost časovače, spustí se pouze jeden časovač.

## <a name="retry-behavior"></a>Opakování chování

Na rozdíl od aktivační události fronty aktivační událost časovače není opakovat po selhání funkce. Pokud se funkce nezdaří, není znovu volána až do příštího plánu.

## <a name="troubleshooting"></a>Řešení potíží

Informace o tom, co dělat, když aktivační událost časovače nefunguje podle očekávání, naleznete [v tématu Vyšetřování a hlášení problémů s funkcemi spuštěnými časovačem, které se nespouštějí](https://github.com/Azure/azure-functions-host/wiki/Investigating-and-reporting-issues-with-timer-triggered-functions-not-firing).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přejít na rychlý start, který používá aktivační událost časovače](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech a vazbách funkcí Azure](functions-triggers-bindings.md)
