---
title: Trigger časovače pro službu Azure Functions
description: Vysvětlení použití aktivačních časovačích ve službě Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: Azure functions, funkce, zpracování událostí, dynamické výpočty, architektura bez serveru
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/08/2018
ms.author: cshoe
ms.custom: ''
ms.openlocfilehash: ae2d4d3fc4f5c0961b942326b2071a0553c81e8d
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50248971"
---
# <a name="timer-trigger-for-azure-functions"></a>Trigger časovače pro službu Azure Functions 

Tento článek vysvětluje, jak pracovat s aktivačními událostmi časovače ve službě Azure Functions. Trigger časovače umožňuje spouštět funkce podle plánu. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Balíčky – funkce 1.x

Trigger časovače je k dispozici v [Microsoft.Azure.WebJobs.Extensions](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) balíčku NuGet, verze 2.x. Zdrojový kód pro tento balíček je v [azure webjobs sdk rozšíření](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Timers/) úložiště GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x"></a>Balíčky – funkce 2.x

Trigger časovače je k dispozici v [Microsoft.Azure.WebJobs.Extensions](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) balíčku NuGet, verze 3.x. Zdrojový kód pro tento balíček je v [azure webjobs sdk rozšíření](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/) úložiště GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>Příklad:

Podívejte se na příklad specifické pro jazyk:

* [C#](#trigger---c-example)
* [C# skript (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)
* [Java](#trigger---java-example)

### <a name="c-example"></a>Příklad jazyka C#

Následující příklad ukazuje [funkce jazyka C#](functions-dotnet-class-library.md) , který spouští každých pět minut:

```cs
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if(myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

### <a name="c-script-example"></a>Ukázkový skript jazyka C#

Následující příklad ukazuje vazby v časovacího triggeru *function.json* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) , který používá vazba. Funkce zapíše do protokolu určující, zda je toto volání funkce z důvodu chybějící plán výskyt.

Zde je vazba dat v *function.json* souboru:

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
    if(myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

### <a name="f-example"></a>Příklad F #

Následující příklad ukazuje časovacího triggeru vazby ve *function.json* souboru a [funkce skriptu jazyka F #](functions-reference-fsharp.md) , který používá vazba. Funkce zapíše do protokolu určující, zda je toto volání funkce z důvodu chybějící plán výskyt.

Zde je vazba dat v *function.json* souboru:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Tady je kód skriptu F #:

```fsharp
let Run(myTimer: TimerInfo, log: ILogger ) =
    if (myTimer.IsPastDue) then
        log.LogInformation("F# function is running late.")
    let now = DateTime.Now.ToLongTimeString()
    log.LogInformation(sprintf "F# function executed at %s!" now)
```

### <a name="javascript-example"></a>Příklad v jazyce JavaScript

Následující příklad ukazuje časovacího triggeru vazby ve *function.json* souboru a [funkce jazyka JavaScript](functions-reference-node.md) , který používá vazba. Funkce zapíše do protokolu určující, zda je toto volání funkce z důvodu chybějící plán výskyt.

Zde je vazba dat v *function.json* souboru:

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

    if(myTimer.isPastDue)
    {
        context.log('Node is running late!');
    }
    context.log('Node timer trigger function ran!', timeStamp);   

    context.done();
};
```

### <a name="java-example"></a>Příklad pro jazyk Java

Následující příklad funkci aktivuje a spustí každých pět minut. `@TimerTrigger` Poznámka k funkci definuje plán stejný řetězec formátu jako [výrazů CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression).

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 *&#47;5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

## <a name="attributes"></a>Atributy

V [knihoven tříd C#](functions-dotnet-class-library.md), použijte [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs).

Konstruktor atributu má výraz CRONU nebo `TimeSpan`. Můžete použít `TimeSpan` pouze v případě, že aplikace function app běží na plán služby App Service. Následující příklad ukazuje výraz CRON:

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

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `TimerTrigger` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | neuvedeno | Musí být nastavena na "timerTrigger". Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal.|
|**direction** | neuvedeno | Musí být nastavena na "in". Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal. |
|**Jméno** | neuvedeno | Název proměnné, který představuje objekt časovače v kódu funkce. | 
|**schedule**|**ScheduleExpression**|A [výraz CRON](#cron-expressions) nebo [TimeSpan](#timespan) hodnotu. A `TimeSpan` lze použít pouze pro aplikaci function app, který běží na plán služby App Service. Můžete vložit výraz plán v nastavení aplikace a nastavte tuto vlastnost na název, který je obalen nastavení aplikace **%** znaky, jako v následujícím příkladu: "ScheduleAppSetting %". |
|**runOnStartup**|**runOnStartup**|Pokud `true`, funkce se vyvolala při spuštění modulu runtime. Například modul runtime spustí, když aplikace function app se obnoví po přepnutí do režimu nečinnosti z důvodu nečinnosti. aplikace function app při restartování z důvodu změn funkce a horizontálně navyšuje jeho kapacita aplikace function app. Takže **runOnStartup** je zřídka Pokud někdy třeba nastavit na `true`, zejména v produkčním prostředí. |
|**useMonitor**|**UseMonitor**|Nastavte na `true` nebo `false` označující, jestli plán by se měly monitorovat. Plán monitorování nevyřeší výskytů plán vám pomůže zajistit, že plán zachovaný správně, i v případě restartování instance aplikace funkce. Pokud není nastavený explicitně, výchozí hodnota je `true` pro plány, které mají interval opakování větší než 1 minuta. Pro plány, které aktivují více než jednou za minutu, výchozí hodnota je `false`.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!CAUTION]
> Nedoporučujeme nastavení **runOnStartup** k `true` v produkčním prostředí. Použití tohoto nastavení činí kód provést v časech s vysokou nepředvídatelné. V některých výrobní nastavení může způsobit tyto další spuštění výrazně vyšší náklady na aplikacím hostovaným v plánech spotřeby. Třeba index Mei **runOnStartup** povolené aktivační události v vyvolána pokaždé, když se škálovat vaši aplikaci function app. Ujistěte se, že plně chápete chování produkční funkcí před povolením **runOnStartup** v produkčním prostředí.   

## <a name="usage"></a>Využití

Při vyvolání funkce pro aktivaci časovače [objekt časovače](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) je předán do funkce. Následující kód JSON je příklad reprezentace objektu časovače. 

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

`IsPastDue` Vlastnost `true` Pokud je novější než naplánované aktuální volání funkce. Například může dojít k restartování aplikace funkce vyvolání vynechání.

## <a name="cron-expressions"></a>Výrazů CRON 

Azure Functions používá [NCronTab](https://github.com/atifaziz/NCrontab) knihovny interpretace výrazů CRON. Výraz CRONU obsahuje šest polí:

`{second} {minute} {hour} {day} {month} {day-of-week}`

Každé pole může mít jednu z následujících typů hodnot:

|Typ  |Příklad:  |Při aktivaci  |
|---------|---------|---------|
|Konkrétní hodnota |<nobr>"0 5 *** *"</nobr>|na hh:05:00, kde je hh každou hodinu (jednou za hodinu)|
|Všechny hodnoty (`*`)|<nobr>"0 * 5 ** *"</nobr>|na 5:mm: 00 každý den, kdy mm se každou minutu v hodině (60 za den)|
|Rozsah (`-` operátor)|<nobr>"5-7 viz *"</nobr>|na hh:mm:05 hh:mm:06 a hh:mm:07, kde je HH: mm každou minutu každou hodinu (3 x minuty)|  
|Sadu hodnot (`,` operátor)|<nobr>"5,8,10 * * * * *"</nobr>|na hh:mm:05 hh:mm:08 a hh:mm:10, kde je HH: mm každou minutu každou hodinu (3 x minuty)|
|Hodnota intervalu (`/` operátor)|<nobr>"0 * / 5 *** *"</nobr>|mít hh:05:00 hh:10:00 hh:15:00, a tak dále prostřednictvím hh:55:00, kde je hh každou hodinu (12krát větší hodiny)|

Chcete-li určit měsíců nebo i dny můžete číselné hodnoty, názvy nebo jejich zkratky názvů:

* Pro dny číselné hodnoty jsou 0 až 6, kde 0 začíná neděli.
* Jména jsou v angličtině. Příklad: `Monday`, `January`.
* Názvy jsou malá a velká písmena.
* Názvy mohou být zkrácena. Tří písmen je délka doporučené zkratku.  Příklad: `Mon`, `Jan`. 

### <a name="cron-examples"></a>Příklady procesu CRON

Tady je několik příkladů výrazů CRON, který můžete použít pro trigger časovače ve službě Azure Functions.

|Příklad:|Při aktivaci  |
|---------|---------|
|`"0 */5 * * * *"`|každých pět minut|
|`"0 0 * * * *"`|jednou v horní části každou hodinu|
|`"0 0 */2 * * *"`|každé dvě hodiny|
|`"0 0 9-17 * * *"`|každou hodinu od 9: 00 do 17: 00|
|`"0 30 9 * * *"`|v 9:30:00 každý den|
|`"0 30 9 * * 1-5"`|v 9:30:00 každý den v týdnu|
|`"0 30 9 * Jan Mon"`|v 9:30:00 v lednu každé pondělí|
>[!NOTE]   
>Můžete najít příklady výrazů CRON online, ale mnohé z nich vynechat, nechte `{second}` pole. Pokud zkopírujete z jednoho z nich, přidejte chybějící `{second}` pole. Obvykle je vhodné nulu v daném poli, není hvězdičku.

### <a name="cron-time-zones"></a>CRON časových pásem

Číslo ve výrazu CRON odkazovat na čas a datum, nejsou časové období. Například 5 v `hour` pole odkazuje na 5:00:00, ne každých 5 hodin.

Výchozí časové pásmo použita pomocí výrazů CRON je koordinovaný univerzální čas (UTC). Pokud chcete, aby podle jiného časového pásma výrazu CRON, vytvořte nastavení aplikace pro vaši aplikaci function app s názvem `WEBSITE_TIME_ZONE`. Nastavte hodnotu na název požadovaného časového pásma, jak je znázorněno [Microsoft časové pásmo Index](https://technet.microsoft.com/library/cc749073). 

Například *východní oblast (běžný čas)* je UTC-05:00. Chcete-li mít časovače triggeru fire na 10:00 dop. každý den, použijte následující výraz CRON, který účty pro časové pásmo UTC:

```json
"schedule": "0 0 15 * * *"
``` 

Nebo vytvořit nastavení aplikace pro vaši aplikaci function app s názvem `WEBSITE_TIME_ZONE` a nastavte hodnotu na **východní oblast (běžný čas)**.  Poté použije následující výraz CRON: 

```json
"schedule": "0 0 10 * * *"
``` 

Při použití `WEBSITE_TIME_ZONE`, dojde k přenastavení čas pro změny času v konkrétním časovém pásmu, jako je letní čas. 

## <a name="timespan"></a>Časový interval

 A `TimeSpan` lze použít pouze pro aplikaci function app, který běží na plán služby App Service.

Na rozdíl od výrazu CRON `TimeSpan` hodnota určuje časový interval mezi každé volání funkce. Po dokončení funkce po spuštění delší než zadaný interval časovače okamžitě volá funkci znovu.

Vyjádřená jako řetězec, `TimeSpan` formát je `hh:mm:ss` při `hh` je kratší než 24. Pokud první dvě číslice 24 nebo novější, je ve formátu `dd:hh:mm`. Zde je několik příkladů:

|Příklad: |Při aktivaci  |
|---------|---------|
|"01: 00:00" | každou hodinu        |
|"00: 01:00"|každou minutu         |
|"24: 00:00" | každých 24 dní        |

## <a name="scale-out"></a>Škálování na víc systémů

Pokud aplikaci function app škálovat do několika instancí, je spuštěna pouze jedna instance funkce aktivované pomocí časovače napříč všemi instancemi.

## <a name="function-apps-sharing-storage"></a>Sdílení úložiště aplikace Function App

Pokud sdílíte mezi více aplikací funkcí účet úložiště, ujistěte se, že každá aplikace function app má jinou `id` v *host.json*. Můžete vynechat `id` vlastnost nebo ručně nastavit vaší aplikace funkcí `id` na jinou hodnotu. Trigger časovače používá úložiště zámek k zajištění, že bude existovat pouze jedna instance časovač při aplikaci function app horizontálně navýší kapacitu na několik instancí. Pokud dvě aplikace function App sdílet stejný `id` a každý používá aktivaci časovačem, bude spuštěna pouze jedna časovače.

## <a name="retry-behavior"></a>Chování opakování

Na rozdíl od aktivační událost fronty trigger časovače nebude opakovat po selhání funkce. Pokud funkce selže, není volána znovu až do příštího podle plánu.

## <a name="troubleshooting"></a>Řešení potíží

Informace o tom, co dělat, když trigger časovače nebude fungovat podle očekávání, najdete v části [Investigating a hlášení problémů s časovačem aktivuje funkce se nespustí,](https://github.com/Azure/azure-functions-host/wiki/Investigating-and-reporting-issues-with-timer-triggered-functions-not-firing).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Přejděte na rychlý start, která využívá trigger časovače](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech Azure functions a vazby](functions-triggers-bindings.md)
