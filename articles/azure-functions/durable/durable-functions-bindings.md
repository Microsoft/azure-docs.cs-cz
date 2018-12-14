---
title: Vazby pro Durable Functions – Azure
description: Jak používat triggery a vazby pro rozšíření Durable Functions pro službu Azure Functions.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 889d26be12fef62d37a471fbe0640a2b8ecdd99c
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53337185"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Vazby pro Durable Functions (Azure Functions)

[Durable Functions](durable-functions-overview.md) rozšíření zavádí dvě nové vazby aktivační událost, která řídí provádění funkcí nástroje orchestrator a aktivity. Zavádí také výstupní vazby, který funguje jako klient pro modul runtime Durable Functions.

## <a name="orchestration-triggers"></a>Orchestrace aktivační události

Aktivační událost orchestration umožňuje vytvářet funkce trvalý orchestrator. Tato aktivační událost podporuje spouštěním nových instancí funkce nástroje orchestrator a existující funkce instancemi nástroje orchestrator, čekající "" Úloha obnovení.

Při použití nástroje Visual Studio tools pro službu Azure Functions, aktivační událost orchestration je nakonfigurovaný nástrojem [OrchestrationTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationTriggerAttribute.html) atribut rozhraní .NET.

Při psaní funkcí nástroje orchestrator v skriptovací jazyky (například JavaScript nebo C# skriptování), aktivační událost orchestration je definován následující objekt JSON v `bindings` pole *function.json* soubor:

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration` je název orchestraci. Jedná se o hodnotu, která musí ho klienti používat ji k spuštění nových instancí této funkce nástroje orchestrator. Tato vlastnost je nepovinná. Pokud není zadán, použije se název funkce.

Interně tato vazba aktivační událost dotazuje řadu fronty ve výchozí účet úložiště pro danou aplikaci funkcí. Tyto fronty jsou podrobnosti interní implementace rozšíření, což je důvod, proč nejsou explicitně nakonfigurovat ve vlastnostech vazby.

### <a name="trigger-behavior"></a>Chování aktivační událost

Tady jsou některé poznámky o orchestraci aktivační události:

* **Dělení na vlákna jedním** – jeden dispečerského vlákna se používá pro všechny spuštění funkce nástroje orchestrator na jednom hostiteli instance. Z tohoto důvodu je důležité zajistit, že kód funkce nástroje orchestrator je efektivní a neprovádí žádné vstupně-výstupních operací. Je také důležité zajistit, že toto vlákno není vhodné použít libovolné asynchronní práce s výjimkou při čekání na na trvalý funkce specifické pro typy úloh.
* **Zpracování zpráv Poison** – neexistuje žádná podpora nezpracovatelná zpráva byla v aktivačních událostech Orchestrace.
* **Zpráva viditelnost** -Orchestrace trigger zprávy vyřazených z fronty a udržovat neviditelné konfigurovatelné doby trvání. Zda se tyto zprávy se obnovuje automaticky, pokud jsou aplikace function app běží a je v pořádku.
* **Návratové hodnoty** – návratové hodnoty jsou serializovat do formátu JSON a ukládají do tabulky historie Orchestrace ve službě Azure Table storage. Tyto návratové hodnoty nemůžou dotazovat klient Orchestrace vazba, je popsáno dále.

> [!WARNING]
> Funkce nástroje Orchestrator nikdy použijte všechny vstupní nebo výstupní vazbu než orchestraci aktivovat vazby. To má potenciál způsobit problémy s příponou trvalé úlohy, protože tyto vazby nemusí dodržovat jedním – práce s vlákny a pravidla vstupně-výstupních operací.

> [!WARNING]
> Funkce jazyka JavaScript orchestrator by měly být deklarovány nikdy `async`.

### <a name="trigger-usage-net"></a>Použití aktivační události (.NET)

Vazba podporuje aktivační událost Orchestrace vstupů a výstupů. Tady jsou některé možnosti vědět o vstupu a výstupu zpracování:

* **vstupy** – funkce rozhraní .NET Orchestrace podporují pouze [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) jako typ parametru. Deserializace vstupů přímo v signatuře funkce není podporována. Musí používat kód [GetInput\<T >](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetInput__1)(.NET) nebo `getInput` – metoda (JavaScript) pro načtení vstupy funkce nástroje orchestrator. Tyto vstupy musí být JSON Serializovatelné typy.
* **Vypíše** -Orchestrace triggery podpoře výstupní hodnoty, jakož i vstupy. Návratový typ funkce se používá pro přiřazení výstupní hodnota a musí být serializovatelný JSON. Pokud funkce .NET vrátí `Task` nebo `void`, `null` hodnota bude uložena jako výstup.

### <a name="trigger-sample"></a>Aktivační událost vzorku

Následuje příklad, jak může vypadat nejjednodušší funkce orchestrátoru "Hello World":

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (funguje pouze 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> `context` Objektu v jazyce JavaScript nepředstavuje DurableOrchestrationContext, ale [funkce kontextu jako celek.](../functions-reference-node.md#context-object). Orchestrace metody prostřednictvím můžete přistupovat `context` objektu `df` vlastnost.

> [!NOTE]
> Používejte JavaScript orchestrátorů `return`. `durable-functions` Knihovny se postará o volání `context.done` metody.

Většina funkcí nástroje orchestrator volání funkce aktivity, takže tady je příklad "Hello World", který ukazuje, jak voláním funkce aktivity:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (funguje pouze 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-triggers"></a>Aktivační události aktivit

Aktivační událost aktivity umožňuje vytvářet funkce, které jsou volány funkce nástroje orchestrator.

Pokud používáte Visual Studio, aktivační událost pro aktivitu je nakonfigurovaný nástrojem [ActivityTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.ActivityTriggerAttribute.html) atribut rozhraní .NET.

Pokud používáte VS Code nebo na webu Azure portal pro vývoj, aktivační událost pro aktivitu je definován následující objekt JSON v `bindings` pole *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` je název aktivity. Jedná se o hodnotu, která funkce produktu orchestrator používaná k volání této funkce aktivitu. Tato vlastnost je nepovinná. Pokud není zadán, použije se název funkce.

Interně tato vazba aktivační událost dotazuje fronty ve výchozí účet úložiště pro danou aplikaci funkcí. Tato fronta je podrobnosti interní implementace rozšíření, což je důvod, proč není explicitně nakonfigurovat ve vlastnostech vazby.

### <a name="trigger-behavior"></a>Chování aktivační událost

Tady jsou některé poznámky o aktivační událost aktivity:

* **Práce s vlákny** – na rozdíl od trigger Orchestrace aktivit triggery nemají žádné omezení týkající se práce s vlákny nebo vstupně-výstupních operací. Má zato, že může být jako běžné funkce.
* **Zpracování zpráv Poison** – neexistuje žádná podpora nezpracovatelná zpráva byla v aktivačních událostech aktivity.
* **Zpráva viditelnost** -vyřazených z fronty a udržovat neviditelné konfigurovatelné doby trvání aktivity aktivační událost zpráv. Zda se tyto zprávy se obnovuje automaticky, pokud jsou aplikace function app běží a je v pořádku.
* **Návratové hodnoty** – návratové hodnoty jsou serializovat do formátu JSON a ukládají do tabulky historie Orchestrace ve službě Azure Table storage.

> [!WARNING]
> Podrobnosti implementace je back-endu úložiště pro funkce aktivity a uživatelský kód by neměl přímo spolupracovat se tyto entity úložiště.

### <a name="trigger-usage-net"></a>Použití aktivační události (.NET)

Vazba podporuje aktivační událost aktivity vstupy a výstupy, stejně jako aktivační událost Orchestrace. Tady jsou některé možnosti vědět o vstupu a výstupu zpracování:

* **vstupy** – funkce rozhraní .NET aktivity nativně používat [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) jako typ parametru. Můžete také funkce protokolem aktivit mohou být deklarovány s jakýmkoli typem parametru, který serializovat na JSON. Při použití `DurableActivityContext`, můžete volat [GetInput\<T >](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html#Microsoft_Azure_WebJobs_DurableActivityContext_GetInput__1) k načtení a deserializaci funkce aktivitu vstup.
* **Vypíše** – funkce aktivity podpoře výstupní hodnoty, jakož i vstupy. Návratový typ funkce se používá pro přiřazení výstupní hodnota a musí být serializovatelný JSON. Pokud funkce .NET vrátí `Task` nebo `void`, `null` hodnota bude uložena jako výstup.
* **metadata** – funkce aktivity .NET lze svázat `string instanceId` parametr získat ID instance Orchestrace nadřazené.

### <a name="trigger-sample"></a>Aktivační událost vzorku

Následuje příklad jednoduchou funkci "Hello World" aktivity může vypadat:

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] DurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

Výchozí typ parametru pro .NET `ActivityTriggerAttribute` vazba je `DurableActivityContext`. Ale aktivační události .NET aktivity také podporu vazbu přímo do formátu JSON serializeable typy (včetně primitivní typy), může se dá zjednodušit na stejnou funkci jako následující:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (funguje pouze 2.x)

```javascript
module.exports = async function(context) {
    return `Hello ${context.bindings.name}!`;
};
```

Vazby JavaScript lze také předat v jako další parametry, takže stejnou funkci může zjednodušit následujícím způsobem:

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```

### <a name="passing-multiple-parameters"></a>Předání více parametrů

Není možné předat více parametrů funkce aktivity přímo. Doporučení v tomto případě je a zajistěte tak předání pole objektů, nebo použít [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) objektů v rozhraní .NET.

Následující příklad používá nové funkce [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) přidána s [jazyka C# 7](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7#tuples):

```csharp
[FunctionName("GetCourseRecommendations")]
public static async Task<dynamic> RunOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string major = "ComputerScience";
    int universityYear = context.GetInput<int>();

    dynamic courseRecommendations = await context.CallActivityAsync<dynamic>("CourseRecommendations", (major, universityYear));
    return courseRecommendations;
}

[FunctionName("CourseRecommendations")]
public static async Task<dynamic> Mapper([ActivityTrigger] DurableActivityContext inputs)
{
    // parse input for student's major and year in university
    (string Major, int UniversityYear) studentInfo = inputs.GetInput<(string, int)>();

    // retrieve and return course recommendations by major and university year
    return new {
        major = studentInfo.Major,
        universityYear = studentInfo.UniversityYear,
        recommendedCourses = new []
        {
            "Introduction to .NET Programming",
            "Introduction to Linux",
            "Becoming an Entrepreneur"
        }
    };
}
```

## <a name="orchestration-client"></a>Klient Orchestrace

Klient Orchestrace vazba umožňuje psát funkce, které interagují s funkcemi nástroje orchestrator. Můžete například reagovat na orchestraci instance následujícími způsoby:

* Je spusťte.
* Dotaz jejich stav.
* Ukončete je.
* Odesílání událostí k nim, zatímco běží.
* Vyprázdnit historii instance.

Pokud používáte Visual Studio, můžete svázat do klienta Orchestrace pomocí [OrchestrationClientAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) atribut rozhraní .NET.

Pokud používáte skriptovací jazyky (například *.csx* nebo *js* soubory) pro vývoj, Orchestrace aktivační události definované následující objekt JSON v `bindings` pole  *Function.JSON*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub` -Používá v situacích, kde více aplikací funkcí sdílet stejný účet úložiště, ale musí být izolované od sebe navzájem. Pokud není zadán, výchozí hodnota z `host.json` se používá. Tato hodnota musí odpovídat hodnotě používané funkcí nástroje orchestrator cíl.
* `connectionName` – Název nastavení aplikace, které obsahuje připojovací řetězec účtu úložiště. Účet úložiště, který je reprezentován tento připojovací řetězec musí být stejný jako ten používané funkcí nástroje orchestrator cíl. Pokud není zadán, je použít výchozí připojovací řetězec účtu úložiště pro danou aplikaci funkcí.

> [!NOTE]
> Ve většině případů doporučujeme vynechat tyto vlastnosti a Spolehněte se na výchozí chování.

### <a name="client-usage"></a>Používání klienta

Ve funkcích rozhraní .NET, obvykle svážete `DurableOrchestrationClient`, která poskytuje úplný přístup na všechny klienty odolná služba Functions podporuje rozhraní API. V jazyce JavaScript jsou vystavené stejná rozhraní API `DurableOrchestrationClient` objekt vrácený z `getClient`. Rozhraní API v objektu klient patří:

* [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)
* [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_)
* [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)
* [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)
* [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_) (aktuálně pouze rozhraní .NET)

Alternativně lze svázat funkce .NET `IAsyncCollector<T>` kde `T` je [StartOrchestrationArgs](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.StartOrchestrationArgs.html) nebo `JObject`.

Zobrazit [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) dokumentace k rozhraní API pro další podrobnosti o těchto operací.

> [!WARNING]
> Při vývoji místně v jazyce JavaScript, budete muset nastavit proměnnou prostředí `WEBSITE_HOSTNAME` k `localhost:<port>`, např. `localhost:7071` použití metod na `DurableOrchestrationClient`. Další informace o tomto požadavku najdete v tématu [problém Githubu](https://github.com/Azure/azure-functions-durable-js/issues/28).

### <a name="client-sample-visual-studio-development"></a>Ukázka klienta (vývoj pro Visual Studio)

Tady je příklad fronty aktivuje funkce, který se spustí Orchestrace "HelloWorld".

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [OrchestrationClient] DurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

### <a name="client-sample-not-visual-studio"></a>Ukázka klienta (nikoli Visual Studio)

Pokud nepoužíváte Visual Studio pro vývoj, můžete vytvořit následující *function.json* souboru. Tento příklad ukazuje postup při konfiguraci funkce aktivované fronty, která používá klient trvalý Orchestrace vazby:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "queueTrigger",
      "queueName": "durable-function-trigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    }
  ]
}
```

Toto jsou ukázky specifické pro jazyk, které spuštění nových instancí funkce nástroje orchestrator.

#### <a name="c-sample"></a>Ukázka v jazyce C#

Následující příklad ukazuje, jak použít klienta trvalý Orchestrace vazby pro spuštění nové instance funkce z funkce skriptu jazyka C#:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static Task<string> Run(string input, DurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

#### <a name="javascript-sample"></a>Ukázky jazyka JavaScript

Následující příklad ukazuje, jak použít klienta trvalý Orchestrace vazby pro spuštění nové instance funkce z funkce JavaScript:

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

Další podrobnosti o spuštění instance najdete v [Instance správu](durable-functions-instance-management.md).

<a name="host-json"></a>

## <a name="hostjson-settings"></a>nastavení Host.JSON

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o vytváření kontrolních bodů a opětovného přehrání chování](durable-functions-checkpointing-and-replay.md)