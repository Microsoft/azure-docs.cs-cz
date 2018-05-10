---
title: Azure testování částí trvanlivý funkce
description: Zjistěte, jak k jednotce otestovat trvanlivý funkce.
services: functions
author: kadimitr
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/28/2018
ms.author: kadimitr
ms.openlocfilehash: 7de9a6f0d4dfcb45932b89504c0d38c3c70283e9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="durable-functions-unit-testing"></a>Trvanlivý testování částí funkce

Testování částí je důležitou součástí postupy vývoj moderních softwaru. Testování částí ověřte chování obchodní logiky a chránit před Úvod bez povšimnutí nejnovějších změn v budoucnu. Trvanlivý funkce můžou snadno růst v složitost tak, aby se zabránilo nejnovější změny pomůže představení testování částí. Následující části popisují, jak k jednotce otestovat typy tři funkce – klient Orchestration, Orchestrator a aktivita funkce. 

## <a name="prerequisites"></a>Požadavky

V příkladech v tomto článku vyžadují znalosti systému následující koncepty a rozhraní: 

* Testování částí

* Odolná služba Functions 

* [xUnit](https://xunit.github.io/) – testování framework

* [moq](https://github.com/moq/moq4) -Mocking framework


## <a name="base-classes-for-mocking"></a>Základní třídy pro mocking 

Mocking je podporována prostřednictvím dvou abstraktní třídy trvanlivý funkcí:

* [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html) 

* [DurableOrchestrationContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContextBase.html)

Tyto třídy jsou základní třídy pro [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) a [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) které definují metody Orchestration klienta a Orchestrator. Mocks nastaví očekávané chování u metody třídy base tak testu jednotek můžete ověřit obchodní logiku. Je dvoustupňové pracovního postupu pro testování obchodní logiku v Orchestration klienta a Orchestrator částí:

1. Při definování podpisy Orchestration klienta a pro Orchestrator, použijte základní třídy místo konkrétní implementaci.
2. Při testování částí model chování základní třídy a ověřte obchodní logiku. 

Najít další informace v následujících odstavcích pro testování funkcí, které používají klienta orchestration vazby a orchestrator aktivovat vazby.

## <a name="unit-testing-trigger-functions"></a>Funkce aktivační událost testování částí

V této části ověří testování částí logiku následující funkce aktivace protokolu HTTP pro spuštění nové orchestrations.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

Testovací úloha jednotky bude ověřte hodnotu `Retry-After` záhlaví zadané v datové části odpovědi. Takže testování částí se model některé [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html) metody zajistit předvídatelný chování. 

Nejprve model základní třídy je požadováno, [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html). Model může být novou třídu, která implementuje [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html). Však pomocí mocking framework jako [moq](https://github.com/moq/moq4) zjednodušuje proces:    

```csharp
    // Mock DurableOrchestrationClientBase
    var durableOrchestrationClientBaseMock = new Mock<DurableOrchestrationClientBase>();
```

Potom `StartNewAsync` metoda je mocked vrátit ID známých instance.

```csharp
    // Mock StartNewAsync method
    durableOrchestrationClientBaseMock.
        Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
        ReturnsAsync(instanceId);
```

Další `CreateCheckStatusResponse` je mocked k vždy vrátí prázdnou odpověď HTTP 200.

```csharp
    // Mock CreateCheckStatusResponse method
    durableOrchestrationClientBaseMock
        .Setup(x => x.CreateCheckStatusResponse(It.IsAny<HttpRequestMessage>(), instanceId))
        .Returns(new HttpResponseMessage
        {
            StatusCode = HttpStatusCode.OK,
            Content = new StringContent(string.Empty),
        });
```

`TraceWriter` je také mocked:

```csharp
    // Mock TraceWriter
    var traceWriterMock = new Mock<TraceWriter>(TraceLevel.Info);

```  

Nyní `Run` metoda je volána z testování částí:

```csharp
    // Call Orchestration trigger function
    var result = await HttpStart.Run(
        new HttpRequestMessage()
        {
            Content = new StringContent("{}", Encoding.UTF8, "application/json"),
            RequestUri = new Uri("http://localhost:7071/orchestrators/E1_HelloSequence"),
        },
        durableOrchestrationClientBaseMock.Object, 
        functionName,
        traceWriterMock.Object);
 ``` 

 Posledním krokem je k porovnání výstup očekávanou hodnotou:

```csharp
    // Validate that output is not null
    Assert.NotNull(result.Headers.RetryAfter);

    // Validate output's Retry-After header value
    Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
```

Po spojení všechny kroky, bude mít testování částí následující kód: 

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HttpStartTests.cs)]

## <a name="unit-testing-orchestrator-functions"></a>Funkce orchestrator testování částí

Funkce Orchestrator jsou i další zajímavé pro jednotku testování, protože mají obvykle mnohem víc obchodní logiku.

V této části jednotka testy ověří výstup `E1_HelloSequence` Orchestrator funkce:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

Kód testu jednotek se spustí s vytvářením model:

```csharp
    var durableOrchestrationContextMock = new Mock<DurableOrchestrationContextBase>();
```

Potom bude možné mocked volání metod aktivity:

```csharp
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

Další testování částí zavolá `HelloSequence.Run` metoda:

```csharp
    var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);
```

A nakonec bude ověřeno výstup:

```csharp
    Assert.Equal(3, result.Count);
    Assert.Equal("Hello Tokyo!", result[0]);
    Assert.Equal("Hello Seattle!", result[1]);
    Assert.Equal("Hello London!", result[2]);
```

Po spojení všechny kroky, bude mít testování částí následující kód:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceOrchestratorTests.cs)]

## <a name="unit-testing-activity-functions"></a>Aktivita funkce testování částí

Aktivita funkce může být jednotka testována stejným způsobem jako krátkodobé funkce. Aktivita funkce nemají základní třídu pro mocking. Proto testování částí používat přímo typy parametrů.

V této části testování částí ověří chování `E1_SayHello` aktivita funkce:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

A testování částí ověří formát výstupu:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o xUnit](http://xunit.github.io/docs/getting-started-dotnet-core)

> [Další informace o moq](https://github.com/Moq/moq4/wiki/Quickstart)