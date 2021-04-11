---
title: Testování částí Azure Durable Functions
description: Přečtěte si, jak Durable Functions testování částí.
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: fe5a25e0296eb183ef2426e12f7bdee35633ec78
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076628"
---
# <a name="durable-functions-unit-testing"></a>Testování částí Durable Functions

Testování částí je důležitou součástí moderních postupů vývoje softwaru. Testy jednotek ověřují chování obchodní logiky a chrání před tím, než budou v budoucnu předem zjištěny zásadní změny. Durable Functions může snadno růst složitou složitost, takže zavedení testů jednotek pomůže vyhnout se neprůlomovým změnám. Následující části vysvětlují, jak otestovat testování tří funkcí – klienta orchestrace, nástroje Orchestrator a aktivity.

> [!NOTE]
> Tento článek poskytuje pokyny pro testování částí Durable Functions aplikací cílících na Durable Functions 2. x. Další informace o rozdílech mezi verzemi najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

## <a name="prerequisites"></a>Požadavky

Příklady v tomto článku vyžadují znalost následujících konceptů a platforem:

* Testování jednotek

* Odolná služba Functions

* rozhraní [xUnit](https://github.com/xunit/xunit) -Testing

* rozhraní pro návrhy [MOQ](https://github.com/moq/moq4)

## <a name="base-classes-for-mocking"></a>Základní třídy pro napodobování

Napodobování se podporuje přes toto rozhraní:

* [IDurableOrchestrationClient](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationclient), [IDurableEntityClient](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableentityclient) a [IDurableClient](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableclient)

* [IDurableOrchestrationContext](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationcontext)

* [IDurableActivityContext](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableactivitycontext)
  
* [IDurableEntityContext](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableentitycontext)

Tato rozhraní lze použít s různými triggery a vazbami, které podporuje Durable Functions. Při provádění Azure Functions spustí modul runtime Functions kód vaší funkce s konkrétní implementací těchto rozhraní. V případě testování částí můžete předat v poměrné verzi těchto rozhraní k otestování obchodní logiky.

## <a name="unit-testing-trigger-functions"></a>Aktivační funkce testování částí

V této části test jednotek ověří logiku následující funkce triggeru HTTP pro spuštění nových orchestrací.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

Úkol testování částí bude ověřovat hodnotu hlavičky, kterou jste `Retry-After` zadali v datové části odpovědi. Test jednotek tak bude napsaný některé z `IDurableClient` metod, aby bylo zajištěno předvídatelné chování.

Nejdřív používáme v tomto případě napodobnou architekturu (v tomto případě[MOQ](https://github.com/moq/moq4) ) `IDurableClient` :

```csharp
// Mock IDurableClient
var durableClientMock = new Mock<IDurableClient>();
```

> [!NOTE]
> I když můžete nasměrovat rozhraní přímo implementující rozhraní jako třídu, jednodušší rozhraní zjednodušuje proces různými způsoby. Například pokud je nová metoda přidána do rozhraní napříč podverzemi, MOQ nebude vyžadovat žádné změny kódu na rozdíl od konkrétních implementací.

Pak `StartNewAsync` je metoda napodobná, aby vracela ID známé instance.

```csharp
// Mock StartNewAsync method
durableClientMock.
    Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
    ReturnsAsync(instanceId);
```

Další je napodobná `CreateCheckStatusResponse` , aby vždycky vracela prázdnou odpověď HTTP 200.

```csharp
// Mock CreateCheckStatusResponse method
durableClientMock
    // Notice that even though the HttpStart function does not call IDurableClient.CreateCheckStatusResponse() 
    // with the optional parameter returnInternalServerErrorOnFailure, moq requires the method to be set up
    // with each of the optional parameters provided. Simply use It.IsAny<> for each optional parameter
    .Setup(x => x.CreateCheckStatusResponse(It.IsAny<HttpRequestMessage>(), instanceId, returnInternalServerErrorOnFailure: It.IsAny<bool>())
    .Returns(new HttpResponseMessage
    {
        StatusCode = HttpStatusCode.OK,
        Content = new StringContent(string.Empty),
        Headers =
        {
            RetryAfter = new RetryConditionHeaderValue(TimeSpan.FromSeconds(10))
        }
    });
```

`ILogger` je také napodobná:

```csharp
// Mock ILogger
var loggerMock = new Mock<ILogger>();
```  

Nyní `Run` je metoda volána z testu jednotky:

```csharp
// Call Orchestration trigger function
var result = await HttpStart.Run(
    new HttpRequestMessage()
    {
        Content = new StringContent("{}", Encoding.UTF8, "application/json"),
        RequestUri = new Uri("http://localhost:7071/orchestrators/E1_HelloSequence"),
    },
    durableClientMock.Object,
    functionName,
    loggerMock.Object);
 ```

 Posledním krokem je porovnat výstup s očekávanou hodnotou:

```csharp
// Validate that output is not null
Assert.NotNull(result.Headers.RetryAfter);

// Validate output's Retry-After header value
Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
```

Po zkombinování všech kroků bude test jednotky obsahovat následující kód:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HttpStartTests.cs)]

## <a name="unit-testing-orchestrator-functions"></a>Funkce nástroje Orchestrator pro testování částí

Funkce nástroje Orchestrator jsou ještě zajímavější pro testování částí, protože obvykle mají mnohem více obchodních logik.

V této části testy jednotek ověřují výstup `E1_HelloSequence` funkce Orchestrator:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

Kód testu jednotek začíná vytvořením tohoto typu:

```csharp
var durableOrchestrationContextMock = new Mock<IDurableOrchestrationContext>();
```

Pak budou nacházet volání metody aktivity:

```csharp
durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

V dalším testu jednotky bude volána `HelloSequence.Run` metoda:

```csharp
var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);
```

A nakonec se ověří výstup:

```csharp
Assert.Equal(3, result.Count);
Assert.Equal("Hello Tokyo!", result[0]);
Assert.Equal("Hello Seattle!", result[1]);
Assert.Equal("Hello London!", result[2]);
```

Po zkombinování všech kroků bude test jednotky obsahovat následující kód:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceOrchestratorTests.cs)]

## <a name="unit-testing-activity-functions"></a>Funkce aktivity testování částí

Funkce aktivity mohou být testovány jednotky stejným způsobem jako netrvanlivé funkce.

V této části testování částí ověří chování `E1_SayHello` funkce aktivity:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

A testy jednotek budou ověřovat formát výstupu. Testy jednotek mohou používat typy parametrů přímo nebo maketnou `IDurableActivityContext` třídu:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o xUnit](https://xunit.net/docs/getting-started/netcore/cmdline)
> 
> [Další informace o MOQ](https://github.com/Moq/moq4/wiki/Quickstart)
