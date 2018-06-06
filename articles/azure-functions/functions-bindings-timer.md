---
title: Aktivační událost časovače pro Azure Functions
description: Pochopit, jak použít aktivační události časovače v Azure Functions.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: Funkce Azure, funkce zpracování událostí, dynamické výpočetní architektura bez serveru
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/27/2017
ms.author: tdykstra
ms.custom: ''
ms.openlocfilehash: 4da1ed4e9424950c39f3eb255ead2b39094597fd
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34725441"
---
# <a name="timer-trigger-for-azure-functions"></a>Aktivační událost časovače pro Azure Functions 

Tento článek vysvětluje, jak pracovat s aktivační události časovače v Azure Functions. Aktivační událost časovače vám umožní spustit funkci podle plánu. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Balíčky – funkce 1.x

Aktivační událost časovače je součástí [Microsoft.Azure.WebJobs.Extensions](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) balíček NuGet verze 2.x. Zdrojový kód pro balíček je v [azure webjobs sdk rozšíření](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Timers/) úložiště GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x"></a>Balíčky – funkce 2.x

Aktivační událost časovače je součástí [Microsoft.Azure.WebJobs.Extensions](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) balíček NuGet verze 3.x. Zdrojový kód pro balíček je v [azure webjobs sdk rozšíření](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/) úložiště GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>Příklad:

Podívejte se na konkrétní jazyk příklad:

* [C#](#trigger---c-example)
* [C# skript (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="c-example"></a>Příklad jazyka C#

Následující příklad ukazuje [C# funkce](functions-dotnet-class-library.md) používající každých pět minut:

```cs
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
    if(myTimer.IsPastDue)
    {
        log.Info("Timer is running late!");
    }
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

### <a name="c-script-example"></a>Příklad skriptu jazyka C#

Následující příklad ukazuje, aktivační událost časovače vazby ve *function.json* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) používající vazby. Funkce zapíše protokolu, která udává, zda toto volání funkce z důvodu zmeškaných plán výskyt.

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
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    if(myTimer.IsPastDue)
    {
        log.Info("Timer is running late!");
    }
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

### <a name="f-example"></a>Příklad F #

Následující příklad ukazuje, aktivační událost časovače vazby ve *function.json* souboru a [F # skript funkce](functions-reference-fsharp.md) používající vazby. Funkce zapíše protokolu, která udává, zda toto volání funkce z důvodu zmeškaných plán výskyt.

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
let Run(myTimer: TimerInfo, log: TraceWriter ) =
    if (myTimer.IsPastDue) then
        log.Info("F# function is running late.")
    let now = DateTime.Now.ToLongTimeString()
    log.Info(sprintf "F# function executed at %s!" now)
```

### <a name="javascript-example"></a>Příklad v jazyce JavaScript

Následující příklad ukazuje, aktivační událost časovače vazby ve *function.json* souboru a [funkce JavaScript, která](functions-reference-node.md) používající vazby. Funkce zapíše protokolu, která udává, zda toto volání funkce z důvodu zmeškaných plán výskyt.

Zde je vazba dat v *function.json* souboru:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Tady je kód JavaScript skriptu:

```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);   

    context.done();
};
```

## <a name="attributes"></a>Atributy

V [knihovny tříd jazyka C#](functions-dotnet-class-library.md), použijte [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs).

Konstruktoru atributu trvá výraz CRON nebo `TimeSpan`. Můžete použít `TimeSpan` pouze v případě, že funkce aplikace běží na plán služby App Service. Následující příklad ukazuje výraz CRON:

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
    if (myTimer.IsPastDue)
    {
        log.Info("Timer is running late!");
    }
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
}
 ```

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `TimerTrigger` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** | neuvedeno | Musí být nastavena na "timerTrigger". Tato vlastnost nastavena automaticky při vytváření aktivační události na portálu Azure.|
|**direction** | neuvedeno | Musí být nastavena na "v". Tato vlastnost nastavena automaticky při vytváření aktivační události na portálu Azure. |
|**Jméno** | neuvedeno | Název proměnné, který představuje objekt časovače v kódu funkce. | 
|**schedule**|**ScheduleExpression**|A [výraz CRON](#cron-expressions) nebo [časový interval](#timespan) hodnotu. A `TimeSpan` lze použít pouze pro funkce aplikace, která běží na plán služby App Service. Můžete uvést výraz plán v nastavení aplikace a nastavte tuto vlastnost na název uzavřen do nastavení aplikace **%** znaky, jako v následujícím příkladě: "% ScheduleAppSetting %". |
|**RunOnStartup**|**RunOnStartup**|Pokud `true`, funkce je volána při spuštění modulu runtime. Například modul runtime spustí, když aplikaci funkce probudí po přechodu nečinnosti z důvodu nečinnosti. funkce aplikace při restartování z důvodu změn funkce a horizontálně navýší kapacitu aplikaci funkce. Proto **runOnStartup** musí zřídka Pokud někdy být nastavena na `true`, jak bude kód provést v vysoce nepředvídatelným časech.|
|**useMonitor**|**UseMonitor**|Nastavte na `true` nebo `false` indikující, zda plán by se měly monitorovat. Plán monitorování trvá výskytů plán a usnadňuje zajištění, že plán je udržován správně i v případě, že instance funkce aplikaci restartovat. Pokud není explicitně nastavena, výchozí hodnota je `true` pro plány, které mají interval opakování, který je větší než 1 minuta. Pro plány, které aktivují více než jednou za minutu, výchozí hodnota je `false`.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Využití

Po vyvolání funkce aktivační událost časovače [časovače objekt](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) je předána do funkce. Následujícím kódu JSON je příklad reprezentací objekt časovače. 

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

`IsPastDue` Vlastnost je `true` Pokud je novější než naplánované aktuální volání funkce. Například funkce restartování aplikace může dojít k vyvolání k být načteni.

## <a name="cron-expressions"></a>Výrazy procesu CRON 

Výraz CRON pro aktivační událost časovače Azure Functions obsahuje šest polí: 

`{second} {minute} {hour} {day} {month} {day-of-week}`

Každé pole může mít jednu z následujících typů hodnot:

|Typ  |Příklad:  |Při aktivaci  |
|---------|---------|---------|
|Konkrétní hodnotou |<nobr>"0 5 *** *"</nobr>|v hh:05:00, kde je hh každou hodinu (jednou za hodinu)|
|Všechny hodnoty (`*`)|<nobr>"0 * 5 ** *"</nobr>|v 5:mm: 00 každý den, kde je mm každou minutu hodinu (60 x denně)|
|Rozsah (`-` operátor)|<nobr>"5-7 viz *"</nobr>|v hh:mm:05, hh:mm:06 a hh:mm:07, kde je HH: mm každou minutu každou hodinu (3krát minuty)|  
|Sadu hodnot (`,` operátor)|<nobr>"5,8,10 * * * * *"</nobr>|v hh:mm:05, hh:mm:08 a hh:mm:10, kde je HH: mm každou minutu každou hodinu (3krát minuty)|
|Hodnotu intervalu (`/` operátor)|<nobr>"0 * / 5 *** *"</nobr>|v hh:05:00, hh:10:00, hh:15:00, a tak dále prostřednictvím hh:55:00, kde je hh každou hodinu (12krát více než jedna hodina)|

K určení měsíců nebo dnů můžete zkratky místo číselné hodnoty. Například použijte ledna pro leden nebo Sun pro neděli.

### <a name="cron-examples"></a>Příklady procesu CRON

Zde jsou některé příklady CRON výrazy, které můžete použít pro aktivační událost časovače v Azure Functions.

|Příklad:|Při aktivaci  |
|---------|---------|
|"0 * / 5 *** *"|každých pět minut|
|"0 0 *** *"|jednou v horní části každou hodinu|
|"0 0 * / 2 ** *"|každé dvě hodiny|
|"0 0 9-17 ** *"|jednou za hodinu z 9: 00 do 17: 00|
|"0 30 9 ** *"|v 9:30:00 každý den|
|"0 30 9 ** 1-5"|v 9:30:00 každý den v týdnu|

>[!NOTE]   
>Můžete najít CRON příklady výrazů online, ale vynechejte kolika z nich `{second}` pole. Pokud zkopírujete z jednoho z nich, přidejte chybějící `{second}` pole. Obvykle je vhodné nula v tomto poli není hvězdičku.

### <a name="cron-time-zones"></a>Časová pásma procesu CRON

Čísla ve výrazu CRON odkazovat na čas a datum, není časové období. Například 5 v `hour` pole se vztahuje k 5:00 AM, není každých 5 hodin.

Použít s výrazy CRON výchozí časové pásmo je koordinovaný světový čas (UTC). Pokud chcete, aby vaše výraz CRON založený na jiném časovém pásmu, vytvořte nastavení aplikace pro funkce aplikace s názvem `WEBSITE_TIME_ZONE`. Nastavte hodnotu na název požadované časové pásmo, jak je znázorněno [Microsoft časové pásmo Index](https://technet.microsoft.com/library/cc749073). 

Například *východní běžný čas* je UTC-05:00. Má vaše časovače aktivovat ještě efektivněji na 10:00 AM EST každý den, použijte následující výraz CRON účty pro časové pásmo UTC:

```json
"schedule": "0 0 15 * * *",
``` 

Nebo vytvoření nastavení aplikace pro funkce aplikace s názvem `WEBSITE_TIME_ZONE` a nastavte hodnotu na **východní běžný čas**.  Pak použije následující výraz CRON: 

```json
"schedule": "0 0 10 * * *",
``` 

## <a name="timespan"></a>Časový interval

 A `TimeSpan` lze použít pouze pro funkce aplikace, která běží na plán služby App Service.

Na rozdíl od výraz CRON `TimeSpan` hodnota určuje časový interval mezi každé vyvolání funkce. Po dokončení funkci po spuštění déle než zadanou dobu časovač okamžitě volá funkci znovu.

Vyjádřený jako řetězec, `TimeSpan` formát je `hh:mm:ss` při `hh` je kratší než 24. Pokud první dvě číslice 24 nebo větší, formát je `dd:hh:mm`. Zde je několik příkladů:

|Příklad: |Při aktivaci  |
|---------|---------|
|"01: 00:00" | každou hodinu        |
|"00: 01:00"|každou minutu         |
|"24: 00:00" | každých 24 dní        |

## <a name="scale-out"></a>Škálování na víc systémů

Pokud aplikaci funkce horizontálně navýší kapacitu na více instancí, je spuštěna pouze jedna instance funkce aktivovaného časovačem napříč všemi instancemi.

## <a name="function-apps-sharing-storage"></a>Funkce aplikace pro sdílení úložiště

Pokud účet úložiště můžete sdílet mezi více aplikacemi funkce, ujistěte se, že každá funkce aplikace má jiné `id` v *host.json*. Můžete vynechat `id` vlastnost nebo ručně nastavit každé funkce aplikace `id` na jinou hodnotu. Aktivační událost časovače používá úložiště zámek k zajištění, že bude pouze jedna instance časovače při aplikaci funkce horizontálně navýší kapacitu na více instancí. Pokud dvě funkce aplikace sdílet stejný `id` a každá používá aktivaci časovačem, bude spuštěna pouze jedna časovače.

## <a name="retry-behavior"></a>Postup opakování

Na rozdíl od frontě aktivační události není aktivační událost časovače opakujte po selhání funkce. Pokud se nezdaří funkce, není volán znovu až po příštím podle plánu.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Přejděte na rychlé spuštění, který používá aktivaci časovačem](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Další informace o Azure functions triggerů a vazeb](functions-triggers-bindings.md)
