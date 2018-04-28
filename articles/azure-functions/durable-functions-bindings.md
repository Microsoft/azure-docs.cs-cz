---
title: Vazby pro odolná funkce – Azure
description: Jak používat triggerů a vazeb trvanlivý Functons rozšíření pro Azure Functions.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: f3952ce87394270051bd37fae271162abc04a675
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Vazby pro odolná funkce (Azure Functions)

[Trvanlivý funkce](durable-functions-overview.md) rozšíření zavádí dvě nové vazby aktivační události, které řízení provádění funkce orchestrator a aktivity. Také zavádí vazbu výstup, který funguje jako klienta pro modul runtime trvanlivý funkce.

## <a name="orchestration-triggers"></a>Aktivační události Orchestration

Aktivační událost orchestration umožňuje vytvářet odolné orchestrator funkce. Této aktivační události podporuje spuštění nové instance funkce orchestrator a obnovení existující instance orchestrator funkce, které jsou "čeká na" úlohu.

Při použití sady Visual Studio tools pro Azure Functions, aktivační událost orchestration je konfigurován pomocí [OrchestrationTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationTriggerAttribute.html) atribut rozhraní .NET.

Když píšete orchestrator funkce v skriptovací jazyky (například na portálu Azure), aktivační událost orchestration je definován následující objekt JSON v `bindings` pole *function.json* souboru:

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "version": "<Optional - version label of this orchestrator function>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration` je název orchestration. Toto je hodnota, které musí klienti používat, když chtějí spusťte nové instance této funkce produktu orchestrator. Tato vlastnost je nepovinná. Pokud není zadaný, použije se název funkce.
* `version` představuje popisek verze nástroje orchestration. Klienti, kteří spustit novou instanci třídy orchestration musí obsahovat odpovídající verze popisku. Tato vlastnost je nepovinná. Pokud není zadaný, použije se prázdný řetězec. Další informace o Správa verzí, naleznete v části [Správa verzí](durable-functions-versioning.md).

> [!NOTE]
> Nastavení hodnot pro `orchestration` nebo `version` vlastnosti se nedoporučuje v tuto chvíli.

Interně tato vazba aktivační událost dotazuje řadu fronty ve výchozí účet úložiště pro aplikaci funkce. Tyto fronty jsou podrobnosti implementace interní rozšíření, a proto nejsou výslovně nakonfigurovaná tak, aby ve vlastnostech vazby.

### <a name="trigger-behavior"></a>Aktivační událost chování

Zde jsou některé poznámky k aktivační události orchestration:

* **Dělení na vlákna jedním** -vlákno dispečera jeden se používá pro všechny funkce provádění orchestrator na jednom hostiteli instanci. Z tohoto důvodu je důležité zajistit, že kód funkce orchestrator je efektivní a nebude provádět žádné vstupně-výstupních operací. Je také důležité zajistit, že tohoto podprocesu neprovádí žádné asynchronní práce s výjimkou při čeká na na typech úloh trvanlivý specifické funkce.
* **Zpracování zpráv Poison** – neexistuje žádná podpora nezpracovatelná zpráva v aktivační události orchestration.
* **Zpráva viditelnost** -vyjmutou a uchovávat neviditelná po dobu konfigurovat Orchestration aktivační událost zpráv. Zda se tyto zprávy se obnovuje automaticky, pokud je funkce aplikace běží a je v pořádku.
* **Návratové hodnoty** -vrátit hodnoty jsou serializovat na JSON a trvalé v tabulce historie orchestration Azure Table storage. Tyto návratové hodnoty můžete položit dotaz na klientem orchestration vazby, popsané dál.

> [!WARNING]
> Funkce Orchestrator nikdy použijte žádný vstup nebo výstup vazby než orchestration aktivovat vazby. Díky tomu se může způsobit problémy s příponou trvanlivý úloh, protože tyto vazby nemusí orientují jednoho vlákna a pravidla vstupně-výstupní operace.

### <a name="trigger-usage"></a>Aktivační události využití

Aktivační událost orchestration vazby podporuje vstup a výstupy. Zde jsou některé věci vědět o vstup a výstup zpracování:

* **vstupy** -Orchestration funkce podporují pouze [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) jako typ parametru. Deserializace vstupy přímo v podpis funkce není podporována. Musíte použít kód [GetInput\<T >](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetInput__1) metoda načíst orchestrator funkce vstupy. JSON Serializovatelné typy musí být tyto vstupy.
* **výstupy** -aktivační události Orchestration podporují výstupní hodnoty, jakož i vstupy. Návratová hodnota funkce slouží k přiřazení výstupní hodnotu a musí být serializovatelný JSON. Pokud funkce vrátí `Task` nebo `void`, `null` hodnoty se uloží jako výstup.

> [!NOTE]
> Aktivační události Orchestration jsou podporovány pouze v jazyce C# v tuto chvíli.

### <a name="trigger-sample"></a>Ukázka aktivační události

Následuje příklad, jak může vypadat nejjednodušší orchestrator funkce "Hello, World" C#:

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```

Většina funkcí orchestrator volání funkce aktivity, zde je příklad "Hello World", které ukazuje, jak zavolat funkci aktivity:

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

## <a name="activity-triggers"></a>Aktivační události aktivity

Aktivační událost aktivity umožňuje vytvářet funkce, které jsou volány funkce produktu orchestrator.

Pokud používáte Visual Studio, aktivační události aktivity je konfigurován pomocí [ActvityTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.ActivityTriggerAttribute.html) atribut rozhraní .NET. 

Pokud používáte portál Azure pro vývoj, aktivační události aktivity definované následující objekt JSON v `bindings` pole *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "version": "<Optional - version label of this activity function>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` je název aktivity. Toto je hodnota, která orchestrator funkce používaná k volání této funkce aktivity. Tato vlastnost je nepovinná. Pokud není zadaný, použije se název funkce.
* `version` je verze popisek aktivity. Orchestrator funkce, které vyvolání aktivity musí obsahovat odpovídající verze popisku. Tato vlastnost je nepovinná. Pokud není zadaný, použije se prázdný řetězec. Další informace najdete v tématu [Správa verzí](durable-functions-versioning.md).

> [!NOTE]
> Nastavení hodnot pro `activity` nebo `version` vlastnosti se nedoporučuje v tuto chvíli.

Interně tato vazba aktivační událost dotazuje fronty ve výchozí účet úložiště pro aplikaci funkce. Tato fronta je podrobností interní implementace rozšíření, proto není explicitně konfigurován ve vlastnostech vazby.

### <a name="trigger-behavior"></a>Aktivační událost chování

Zde jsou některé poznámky k aktivační události aktivity:

* **Dělení na vlákna** – na rozdíl od orchestration aktivační událost, aktivační události aktivity nemají žádné omezení pro dělení na vlákna nebo vstupně-výstupní operace. Můžete lze používat jako běžné funkce.
* **Zpracování zpráv poising** – neexistuje žádná podpora nezpracovatelná zpráva v aktivační události aktivity.
* **Zpráva viditelnost** -vyjmutou a zachovány neviditelná pro konfigurovat doba trvání aktivity aktivační událost zpráv. Zda se tyto zprávy se obnovuje automaticky, pokud je funkce aplikace běží a je v pořádku.
* **Návratové hodnoty** -vrátit hodnoty jsou serializovat na JSON a trvalé v tabulce historie orchestration Azure Table storage.

> [!WARNING]
> Back-endu úložiště pro aktivitu funkce je podrobností implementace a uživatelský kód nesmí komunikovat přímo s tyto entity úložiště.

### <a name="trigger-usage"></a>Aktivační události využití

Aktivační událost aktivity vazby podporuje vstup a výstupy, stejně jako aktivační událost orchestration. Zde jsou některé věci vědět o vstup a výstup zpracování:

* **vstupy** – aktivita funkce nativně používat [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) jako typ parametru. Aktivita funkce Alternativně lze deklarovat s žádným typem parametr, který serializovat na JSON. Při použití `DurableActivityContext`, můžete volat [GetInput\<T >](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html#Microsoft_Azure_WebJobs_DurableActivityContext_GetInput__1) pro načtení a deserializovat funkce aktivitu vstup.
* **výstupy** – aktivita funkce podporují výstupní hodnoty, jakož i vstupy. Návratová hodnota funkce slouží k přiřazení výstupní hodnotu a musí být serializovatelný JSON. Pokud funkce vrátí `Task` nebo `void`, `null` hodnoty se uloží jako výstup.
* **metadata** – aktivita funkce můžete vázat na `string instanceId` parametr získat ID instance nadřazené orchestration.

> [!NOTE]
> Aktivační události aktivity nejsou aktuálně podporované v Node.js funkce.

### <a name="trigger-sample"></a>Ukázka aktivační události

Následuje příklad, jak může vypadat jednoduché funkce aktivity "Hello, World" C#:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] DurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

Výchozí typ parametru pro `ActivityTriggerAttribute` vazba je `DurableActivityContext`. Aktivační události aktivity také podpora vazbu přímo do formátu JSON serializeable typy (včetně primitivní typy), tak může zjednodušit stejnou funkci jako však zahrnuje:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

### <a name="passing-multiple-parameters"></a>Předávání více parametrů 

Není možné předat několik parametrů přímo do funkce aktivity. Doporučení v tomto případě je předávat pole objektů, nebo chcete použít [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) objekty.

Následující příklad používá nové funkce [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) přidána s [C# 7](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7#tuples):

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

## <a name="orchestration-client"></a>Orchestration klienta

Vazba klienta orchestration umožňuje psát funkcích, které interakci s funkcemi produktu orchestrator. Můžete například fungovat v instancích orchestration následujícími způsoby:
* Je spusťte.
* Dotaz na stav.
* Ukončení nich.
* Odesílání událostí do nich době, kdy máte spuštěny.

Pokud používáte Visual Studio, můžete vázat na orchestration klienta pomocí [OrchestrationClientAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) atribut rozhraní .NET.

Pokud používáte skriptovací jazyky (například *.csx* soubory) pro vývoj, aktivační událost orchestration definované následující objekt JSON v `bindings` pole *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "out"
}
```

* `taskHub` -Použít ve scénářích, kdy několik funkce aplikace sdílet stejný účet úložiště, ale musí být od sebe navzájem oddělené. Pokud není zadáno, výchozí hodnota z `host.json` se používá. Tato hodnota musí odpovídat hodnotě používané funkce orchestrator cíl.
* `connectionName` -Název nastavení aplikace, který obsahuje připojovací řetězec účet úložiště. Účet úložiště reprezentována tento připojovací řetězec musí být stejný jako ten, používá funkce orchestrator cíl. Pokud není zadaný, se používá účet úložiště výchozí připojovací řetězec pro funkce aplikace.

> [!NOTE]
> Ve většině případů doporučujeme, abyste vynechejte tyto vlastnosti a závisí na výchozí chování.

### <a name="client-usage"></a>Využití klienta

V jazyce C# funkce, je obvykle svázat `DurableOrchestrationClient`, které poskytuje úplný přístup na všechny klienty nepodporuje trvanlivý funkce rozhraní API. Rozhraní API na objekt klient patří:

* [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)
* [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_)
* [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)
* [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)

Alternativně můžete vázat na `IAsyncCollector<T>` kde `T` je [StartOrchestrationArgs](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.StartOrchestrationArgs.html) nebo `JObject`.

Najdete v článku [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) dokumentaci rozhraní API pro další informace o těchto operací.

### <a name="client-sample-visual-studio-development"></a>Ukázka klienta (vývoj pro Visual Studio)

Tady je příklad fronty aktivované funkce začínající orchestration "HelloWorld".

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

Pokud nepoužíváte Visual Studio pro vývoj, můžete vytvořit následující *function.json* souboru. Tento příklad ukazuje, jak nakonfigurovat funkci aktivovaného fronty, která používá klienta trvanlivý orchestration vazby:

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
      "direction": "out"
    }
  ],
  "disabled": false
} 
```

Následují ukázky pro specifický jazyk, které spusťte nové instance funkce produktu orchestrator.

#### <a name="c-sample"></a>Ukázka C#

Následující příklad ukazuje, jak použít klienta trvanlivý orchestration vytvoření vazby na spuštění nové instance funkce z funkce skriptu jazyka C#:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static Task<string> Run(string input, DurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

#### <a name="nodejs-sample"></a>Ukázku Node.js

Následující příklad ukazuje, jak použít klienta trvanlivý orchestration vytvoření vazby na spuštění nové instance funkce z Node.js funkce:

```js
module.exports = function (context, input) {
    var id = generateSomeUniqueId();
    context.bindings.starter = [{
        FunctionName: "HelloWorld",
        Input: input,
        InstanceId: id
    }];

    context.done(null, id);
};
```

Další informace o spouštění instance naleznete v [Instance správu](durable-functions-instance-management.md).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o vytváření kontrolních bodů a opětovného přehrání chování](durable-functions-checkpointing-and-replay.md)

