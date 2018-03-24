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
ms.openlocfilehash: 6f74dd4d9cb78c1316c87bd5a261e751b9b34923
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="timer-trigger-for-azure-functions"></a>Aktivační událost časovače pro Azure Functions 

Tento článek vysvětluje, jak pracovat s aktivační události časovače v Azure Functions. Aktivační událost časovače vám umožní spustit funkci podle plánu. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages"></a>Balíčky

Aktivační událost časovače je součástí [Microsoft.Azure.WebJobs.Extensions](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) balíček NuGet. Zdrojový kód pro balíček je v [azure webjobs sdk rozšíření](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/) úložiště GitHub.

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

Konstruktoru atributu trvá výraz CRON, jak je znázorněno v následujícím příkladu:

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
   ...
}
 ```

Můžete zadat `TimeSpan` místo výraz CRON, pokud vaše aplikace funkce běží na plán služby App Service (není plánu spotřeby).

Úplný příklad najdete v tématu [příklad jazyka C#](#c-example).

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `TimerTrigger` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Typ** | neuvedeno | Musí být nastavena na "timerTrigger". Tato vlastnost nastavena automaticky při vytváření aktivační události na portálu Azure.|
|**Směr** | neuvedeno | Musí být nastavena na "v". Tato vlastnost nastavena automaticky při vytváření aktivační události na portálu Azure. |
|**name** | neuvedeno | Název proměnné, který představuje objekt časovače v kódu funkce. | 
|**schedule**|**ScheduleExpression**|Spotřeba plánu můžete definovat plány se výraz CRON. Pokud používáte plánu služby App Service, můžete také použít `TimeSpan` řetězec. Následující části popisují CRON výrazy. Můžete umístit výraz plán v nastavení aplikace a nastavte tuto vlastnost na hodnotu uzavřen do **%** znaky, jako v následujícím příkladě: "% NameOfAppSettingWithCRONExpression %". |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="cron-format"></a>Formát procesu CRON 

A [výraz CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) pro Azure Functions časovač aktivační události obsahuje tyto šesti pole: 

```
{second} {minute} {hour} {day} {month} {day-of-week}
```

>[!NOTE]   
>Řada výrazů CRON zjistíte online vynechejte `{second}` pole. Pokud zkopírujete z jednoho z nich, přidejte chybějící `{second}` pole.

### <a name="cron-time-zones"></a>Časová pásma procesu CRON

Použít s výrazy CRON výchozí časové pásmo je koordinovaný světový čas (UTC). Pokud chcete, aby vaše výraz CRON založený na jiném časovém pásmu, vytvořte nové nastavení aplikace pro funkce aplikace s názvem `WEBSITE_TIME_ZONE`. Nastavte hodnotu na název požadované časové pásmo, jak je znázorněno [Microsoft časové pásmo Index](https://technet.microsoft.com/library/cc749073(v=ws.10).aspx). 

Například *východní běžný čas* je UTC-05:00. Má vaše časovače aktivovat ještě efektivněji na 10:00 AM EST každý den, použijte následující výraz CRON účty pro časové pásmo UTC:

```json
"schedule": "0 0 15 * * *",
``` 

Alternativně můžete přidat nové nastavení aplikace pro funkce aplikace s názvem `WEBSITE_TIME_ZONE` a nastavte hodnotu na **východní běžný čas**.  Následující výraz CRON pak může použít pro 10:00 AM EST: 

```json
"schedule": "0 0 10 * * *",
``` 
### <a name="cron-examples"></a>Příklady procesu CRON

Zde jsou některé příklady CRON výrazy, které můžete použít pro aktivační událost časovače v Azure Functions. 

K aktivaci každých pět minut:

```json
"schedule": "0 */5 * * * *"
```

K aktivaci jednou v horní části každou hodinu:

```json
"schedule": "0 0 * * * *",
```

K aktivaci každé dvě hodiny:

```json
"schedule": "0 0 */2 * * *",
```

K aktivaci jednou za hodinu z 9: 00 do 17: 00:

```json
"schedule": "0 0 9-17 * * *",
```

Spouštět v 9:30:00 každý den:

```json
"schedule": "0 30 9 * * *",
```

Spouštět v 9:30:00 každý den v týdnu:

```json
"schedule": "0 30 9 * * 1-5",
```

## <a name="usage"></a>Využití

Po vyvolání funkce aktivační událost časovače [časovače objekt](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) je předána do funkce. Následujícím kódu JSON je příklad reprezentací objekt časovače. 

```json
{
    "Schedule":{
    },
    "ScheduleStatus": {
        "Last":"2016-10-04T10:15:00.012699+00:00",
        "Next":"2016-10-04T10:20:00+00:00"
    },
    "IsPastDue":false
}
```

## <a name="scale-out"></a>Škálování na víc systémů

Aktivační událost časovače podporuje víc instancí Škálováním na více systémů. Ve všech instancích je spuštěna jedna instance funkce konkrétní časovače.

## <a name="function-apps-sharing-storage"></a>Funkce aplikace pro sdílení úložiště

Pokud účet úložiště můžete sdílet mezi více aplikacemi funkce, ujistěte se, že každá funkce aplikace má jiné `id` v *host.json*. Můžete vynechat `id` vlastnost nebo ručně nastavit každé funkce aplikace `id` na jinou hodnotu. Aktivační událost časovače používá úložiště zámek k zajištění, že bude pouze jedna instance časovače při aplikaci funkce horizontálně navýší kapacitu na více instancí. Pokud dvě funkce aplikace sdílet stejný `id` a každá používá aktivaci časovačem, bude spuštěna pouze jedna časovače.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Přejděte na rychlé spuštění, který používá aktivaci časovačem](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Další informace o Azure functions triggerů a vazeb](functions-triggers-bindings.md)
