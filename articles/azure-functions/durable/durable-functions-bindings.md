---
title: Vazby pro Durable Functions – Azure
description: Použití aktivačních procedur a vazeb pro rozšíření Durable Functions pro Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: fbee98d64d37b2cdfc515eb733324902e238a768
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383108"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Vazby pro Durable Functions (Azure Functions)

Rozšíření [Durable Functions](durable-functions-overview.md) zavádí dvě nové aktivační vazby, které ovládají provádění funkcí Orchestrator a Activity. Zavádí také výstupní vazbu, která funguje jako klient pro modul runtime Durable Functions.

## <a name="orchestration-triggers"></a>Aktivační události orchestrace

Aktivační událost orchestrace umožňuje vytvářet trvalé funkce nástroje Orchestrator. Tato aktivační událost podporuje spouštění nových instancí funkcí Orchestrator a obnovení stávajících instancí funkcí Orchestrator, které čekají na úkol.

Když použijete nástroje sady Visual Studio pro Azure Functions, aktivační událost orchestrace se nakonfiguruje pomocí atributu .NET [OrchestrationTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationTriggerAttribute.html) .

Při psaní funkcí Orchestrator v skriptovacích jazycích (například JavaScript nebo C# skriptování) je aktivační událost Orchestration definována následujícím objektem JSON v `bindings` poli *funkce Function. JSON* :

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration`je název orchestrace. Jedná se o hodnotu, kterou klienti musí použít, když chtějí začít nové instance této funkce nástroje Orchestrator. Tato vlastnost je nepovinná. Pokud není zadaný, použije se název funkce.

Interně se tato triggerová vazba dotazuje řady front ve výchozím účtu úložiště pro aplikaci Function App. Tyto fronty jsou podrobnosti o interní implementaci tohoto rozšíření, což znamená, proč nejsou explicitně nakonfigurované ve vlastnostech vazby.

### <a name="trigger-behavior"></a>Chování triggeru

Tady jsou některé poznámky k triggeru orchestrace:

* **Multithreading** – jediné vlákno dispečera se používá pro všechna spuštění funkcí Orchestrator na jedné instanci hostitele. Z tohoto důvodu je důležité zajistit, aby kód funkce nástroje Orchestrator byl efektivní a neprováděl žádné vstupně-výstupní operace. Je také důležité zajistit, aby toto vlákno nevedlo žádnou asynchronní práci s výjimkou případů, kdy čekají na konkrétní Durable Functions typy úloh.
* **Zpracování nezpracovatelných zpráv** – v aktivačních událostech orchestrace se nepodporují žádné nepoškozené zprávy.
* **Viditelnost zprávy** – aktivační události orchestrace se odřadí do fronty a zůstanou v konfigurovatelné době neviditelné. Viditelnost těchto zpráv se automaticky obnoví, pokud je aplikace Function App spuštěná a v pořádku.
* **Návratové hodnoty** – návratové hodnoty jsou serializovány do formátu JSON a trvalé v tabulce historie orchestrace v úložišti tabulek Azure. Na tyto návratové hodnoty se může dotázat vazba klienta Orchestration, která je popsána dále.

> [!WARNING]
> Funkce nástroje Orchestrator by nikdy neměly používat žádné vstupní ani výstupní vazby kromě triggeru triggeru orchestrace. To by mohlo způsobit problémy s rozšířením trvalé úlohy, protože tyto vazby nepodléhají pravidlům v jednom vláknu a I/O. Pokud chcete použít jiné vazby, přidejte je do funkce aktivity volané z vaší funkce Orchestrator.

> [!WARNING]
> Funkce Orchestrator jazyka JavaScript by nikdy neměly `async`být deklarovány.

### <a name="trigger-usage-net"></a>Využití triggeru (.NET)

Aktivační vazba Orchestration podporuje vstupy i výstupy. Zde je několik věcí, které je potřeba znát při zpracování vstupu a výstupu:

* **vstupy** – funkce orchestrace .NET podporují jako typ parametru jenom [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) . Deserializace vstupů přímo v signatuře funkce není podporována. Aby bylo možné načíst vstupy funkcí nástroje Orchestrator, kód musí použít `getInput` metodu [\<GetInput T >](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetInput__1)(.NET) nebo (JavaScript). Tyto vstupy musí být typy serializovatelných hodnot JSON.
* **výstupy** – triggery orchestrace podporují výstupní hodnoty a také vstupy. Návratová hodnota funkce slouží k přiřazení výstupní hodnoty a musí být serializovatelný pomocí formátu JSON. Pokud funkce .NET vrátí `Task` nebo `void`, `null` uloží se jako výstup hodnota.

### <a name="trigger-sample"></a>Ukázka triggeru

Tady je příklad toho, jak by nejjednodušší funkce "Hello World" Orchestrator vypadala takto:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> Objekt v JavaScriptu nepředstavuje DurableOrchestrationContext, ale [kontext funkce jako celek.](../functions-reference-node.md#context-object) `context` K metodám orchestrace můžete přistupovat prostřednictvím `context` `df` vlastnosti objektu.

> [!NOTE]
> Orchestrace JavaScriptu by měla `return`použít. Knihovna `durable-functions` se postará o `context.done` volání metody.

Většina funkcí Orchestrator Functions Activity Functions, takže zde je příklad "Hello World", který ukazuje, jak zavolat funkci aktivity:

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

#### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-triggers"></a>Aktivační události aktivit

Aktivační událost aktivity umožňuje vytvářet funkce, které jsou volány funkcemi Orchestrator.

Pokud používáte aplikaci Visual Studio, je aktivační událost aktivity nakonfigurována pomocí atributu [ActivityTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.ActivityTriggerAttribute.html) .NET.

Pokud používáte vs Code nebo Azure Portal pro vývoj, aktivační událost aktivity je definována následujícím objektem JSON v `bindings` poli *Function. JSON*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity`je název aktivity. Toto je hodnota, kterou funkce Orchestrator používají k vyvolání této funkce aktivity. Tato vlastnost je nepovinná. Pokud není zadaný, použije se název funkce.

Interně se tato triggerová vazba dotazuje fronty ve výchozím účtu úložiště pro aplikaci Function App. Tato fronta představuje interní podrobnosti implementace rozšíření, což je důvod, proč není explicitně nakonfigurován ve vlastnostech vazby.

### <a name="trigger-behavior"></a>Chování triggeru

Zde jsou některé poznámky týkající se triggeru aktivity:

* **Dělení na vlákna** – na rozdíl od triggeru orchestrace, triggery aktivit nemají žádná omezení týkající se dělení na vlákna nebo v/v. Můžou se nacházet jako s běžnými funkcemi.
* **Zpracování nezpracovatelných zpráv** – v aktivačních událostech aktivit se nepodporují žádné nepoškozené zprávy.
* **Viditelnost zprávy** – zprávy triggeru aktivit se odřadí do fronty a udržují se v konfigurovatelné době jako neviditelné. Viditelnost těchto zpráv se automaticky obnoví, pokud je aplikace Function App spuštěná a v pořádku.
* **Návratové hodnoty** – návratové hodnoty jsou serializovány do formátu JSON a trvalé v tabulce historie orchestrace v úložišti tabulek Azure.

> [!WARNING]
> Služba Storage back-endu pro funkce aktivity je podrobností implementace a kód uživatele by neměl pracovat s těmito entitami úložiště přímo.

### <a name="trigger-usage-net"></a>Využití triggeru (.NET)

Vazba triggeru aktivity podporuje vstupy i výstupy, stejně jako Trigger orchestrace. Zde je několik věcí, které je potřeba znát při zpracování vstupu a výstupu:

* **vstupy** – funkce aktivity .NET nativně používají [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) jako typ parametru. Alternativně lze funkci aktivity deklarovat s libovolným typem parametru, který je serializovatelný pomocí JSON. Při použití `DurableActivityContext`můžete volat [\<GetInput T >](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html#Microsoft_Azure_WebJobs_DurableActivityContext_GetInput__1) pro načtení a deserializaci vstupu funkce Activity.
* **výstupy** – funkce aktivity podporují výstupní hodnoty a také vstupy. Návratová hodnota funkce slouží k přiřazení výstupní hodnoty a musí být serializovatelný pomocí formátu JSON. Pokud funkce .NET vrátí `Task` nebo `void`, `null` uloží se jako výstup hodnota.
* funkce **aktivit rozhraní .NET** může vytvořit vazby k `string instanceId` parametru, aby získala ID instance nadřazené orchestrace.

### <a name="trigger-sample"></a>Ukázka triggeru

Tady je příklad toho, co může jednoduchá funkce aktivity Hello World vypadat takto:

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] DurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

Výchozí typ parametru vazby rozhraní .NET `ActivityTriggerAttribute` je. `DurableActivityContext` Nicméně triggery aktivit rozhraní .NET také podporují vazbu přímo na typy serializace JSON (včetně primitivních typů), takže stejnou funkci lze zjednodušit takto:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

```javascript
module.exports = async function(context) {
    return `Hello ${context.bindings.name}!`;
};
```

Vazby JavaScriptu lze také předat jako další parametry, takže stejnou funkci lze zjednodušit takto:

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```

### <a name="passing-multiple-parameters"></a>Předávání více parametrů

Není možné předat více parametrů funkci Activity přímo. Doporučení v tomto případě je nutné předat v poli objektů nebo použít objekty [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) v rozhraní .NET.

Následující ukázka používá nové funkce [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) přidané s [ C# 7](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7#tuples):

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

### <a name="using-input-and-output-bindings"></a>Použití vstupních a výstupních vazeb

Kromě vazby triggeru aktivity můžete použít regulární vstupní a výstupní vazby. Můžete například převést vstup na vazbu aktivity a odeslat zprávu centru EventHub pomocí výstupní vazby EventHub:

```json
{
  "bindings": [
    {
      "name": "message",
      "type": "activityTrigger",
      "direction": "in"
    },
    {
      "type": "eventHub",
      "name": "outputEventHubMessage",
      "connection": "EventhubConnectionSetting",
      "eventHubName": "eh_messages",
      "direction": "out"
  }
  ]
}
```

```javascript
module.exports = async function (context) {
    context.bindings.outputEventHubMessage = context.bindings.message;
};
```

## <a name="orchestration-client"></a>Klient Orchestration

Vazba klienta Orchestration umožňuje napsat funkce, které komunikují s funkcemi nástroje Orchestrator. Například můžete pracovat na instancích orchestrace následujícími způsoby:

* Spusťte je.
* Dotaz na jejich stav
* Ukončete je.
* Posílat do nich události při jejich spuštění.
* Vyprázdnit historii instance

Pokud používáte aplikaci Visual Studio, můžete vytvořit propojení s klientem Orchestration pomocí atributu [OrchestrationClientAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) .NET.

Pokud pro vývoj používáte skriptovací jazyky (například soubory. *CSX* nebo *. js* ), aktivační událost orchestrace je definována následujícím `bindings` objektem JSON v poli *Function. JSON*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub`– Používá se ve scénářích, kdy více aplikací Function App sdílí stejný účet úložiště, ale musí být izolované od sebe navzájem. Pokud není zadaný, použije se výchozí hodnota `host.json` z. Tato hodnota musí odpovídat hodnotě používané cílovými funkcemi nástroje Orchestrator.
* `connectionName`– Název nastavení aplikace, které obsahuje připojovací řetězec účtu úložiště. Účet úložiště reprezentovaný tímto připojovacím řetězcem musí být stejný jako ten, který používá cílové funkce nástroje Orchestrator. Pokud tento parametr nezadáte, použije se výchozí připojovací řetězec účtu úložiště pro aplikaci Function App.

> [!NOTE]
> Ve většině případů doporučujeme tyto vlastnosti vynechat a spoléhat se na výchozí chování.

### <a name="client-usage"></a>Využití klienta

Ve funkcích .NET se obvykle vytváří vazba na `DurableOrchestrationClient`, která poskytuje úplný přístup ke všem klientským rozhraním API, která podporuje Durable Functions. V jazyce JavaScript jsou stejná rozhraní API vystavena `DurableOrchestrationClient` objektem vráceným z. `getClient` Mezi rozhraní API u objektu klienta patří:

* [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)
* [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_)
* [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)
* [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)
* [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_) (pouze aktuálně .NET)

Alternativně mohou funkce .NET navazovat `IAsyncCollector<T>` vazby `T` na [](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.StartOrchestrationArgs.html) WHERE je `JObject`StartOrchestrationArgs nebo.

Další podrobnosti o těchto operacích najdete v dokumentaci k rozhraní [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) API.

> [!WARNING]
> Při vývoji místně v JavaScriptu budete muset nastavit proměnnou `WEBSITE_HOSTNAME` prostředí na `localhost:<port>`, ex. `localhost:7071`pro použití metod v `DurableOrchestrationClient`. Další informace o tomto požadavku najdete v tématu [problém GitHubu](https://github.com/Azure/azure-functions-durable-js/issues/28).

### <a name="client-sample-visual-studio-development"></a>Ukázka klienta (vývoj v aplikaci Visual Studio)

Tady je příklad funkce aktivované frontou, která spouští orchestraci HelloWorld.

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

### <a name="client-sample-not-visual-studio"></a>Ukázka klienta (ne Visual Studio)

Pokud Visual Studio nepoužíváte pro vývoj, můžete vytvořit následující soubor *Function. JSON* . Tento příklad ukazuje, jak nakonfigurovat funkci spuštěnou ve frontě, která používá vazbu klienta trvalé orchestrace:

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

Níže jsou uvedené ukázky pro konkrétní jazyk, které spouštějí nové instance funkcí nástroje Orchestrator.

#### <a name="c-sample"></a>Ukázka v jazyce C#

Následující příklad ukazuje, jak použít vazbu klienta trvalé orchestrace ke spuštění nové instance funkce ze funkce C# skriptu:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static Task<string> Run(string input, DurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

#### <a name="javascript-sample"></a>Ukázky jazyka JavaScript

Následující příklad ukazuje, jak použít vazbu klienta trvalé orchestrace k zahájení nové instance funkce z funkce JavaScriptu:

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

Další podrobnosti o počátečních instancích najdete ve [správě instancí](durable-functions-instance-management.md).

<a name="host-json"></a>

## <a name="hostjson-settings"></a>nastavení Host.JSON

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o chování kontrolního bodu a přehrání](durable-functions-checkpointing-and-replay.md)
