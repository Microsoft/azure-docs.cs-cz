---
title: Testování částí Azure Durable Functions
description: Zjistěte, jak otestovat Durable Functions k jednotce.
services: functions
author: kadimitr
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: kadimitr
ms.openlocfilehash: 38db54889c1fff0406f4e21cea80e16b6fdc25d1
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54105504"
---
# <a name="durable-functions-unit-testing"></a>Odolné funkce testování

Testování jednotek je důležitou součástí moderních postupů vývoje softwaru. Jednotkové testy ověření chování obchodní logiky a chránit před Představujeme bez povšimnutí rozbíjející změny v budoucnu. Odolná služba Functions může snadno jejich složitost v tomu budeme rozbíjející změny, aby představení testování částí. Následující části popisují, jak otestovat tři funkce typy – klient Orchestrace, Orchestrator a aktivity k jednotce pro funkce.

## <a name="prerequisites"></a>Požadavky

V příkladech v tomto článku vyžadují znalost následujících konceptů a rozhraní:

* Testování částí

* Odolná služba Functions

* [xUnit](https://xunit.github.io/) – testovací rozhraní

* [moq](https://github.com/moq/moq4) -napodobování framework

## <a name="base-classes-for-mocking"></a>Základní třídy pro vytvoření modelu

Napodobování je podporované prostřednictvím tři abstraktní třídy v Durable Functions:

* [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html)

* [DurableOrchestrationContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContextBase.html)

* [DurableActivityContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContextBase.html)

Tyto třídy jsou základní třídy pro [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html), [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html), a [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) , které definují klient Orchestrace , Orchestrator a aktivitu metody. Mocks nastaví očekávané chování u metody základní třídy, test jednotky můžete ověřit obchodní logiku. Není k dispozici ve dvou krocích pracovního postupu pro testování jednotek obchodní logiku v klient Orchestrace a Orchestrator:

1. Při definování klient Orchestrace a Orchestrátorů podpisy, použijte místo konkrétní implementaci základní třídy.
2. Při testech jednotek napodobení chování základní třídy a ověřit obchodní logiku.

Další podrobnosti najdete v následujících odstavcích pro testování funkcí, které používají klienta Orchestrace vazby a orchestrátor aktivovat vazby.

## <a name="unit-testing-trigger-functions"></a>Aktivační událost funkce testování částí

V této části testování částí ověří logiky následující funkci triggeru HTTP pro spuštění nové Orchestrace.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

Úloha testu jednotek bude ověření hodnoty `Retry-After` hlavičky zadané v datové části odpovědi. Takže testování částí se napodobení některé z [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html) metody bylo zajištěno předvídatelný chování.

Nejprve model základní třídy je nutné, [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html). Model může být novou třídu, která implementuje [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html). Avšak použití napodobování architekturu jako třeba [moq](https://github.com/moq/moq4) zjednodušuje proces:

```csharp
    // Mock DurableOrchestrationClientBase
    var durableOrchestrationClientBaseMock = new Mock<DurableOrchestrationClientBase>();
```

Potom `StartNewAsync` metoda je imitace vrátit ID dobře známé instance.

```csharp
    // Mock StartNewAsync method
    durableOrchestrationClientBaseMock.
        Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
        ReturnsAsync(instanceId);
```

Další `CreateCheckStatusResponse` je imitaci chcete vždy vrátit prázdnou odpověď HTTP 200.

```csharp
    // Mock CreateCheckStatusResponse method
    durableOrchestrationClientBaseMock
        .Setup(x => x.CreateCheckStatusResponse(It.IsAny<HttpRequestMessage>(), instanceId))
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

`TraceWriter` je také imitace:

```csharp
    // Mock TraceWriter
    var traceWriterMock = new Mock<TraceWriter>(TraceLevel.Info);

```  

Nyní `Run` metoda je volána z testu jednotek:

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

 Posledním krokem je porovnat výstupu se očekávaná hodnota:

```csharp
    // Validate that output is not null
    Assert.NotNull(result.Headers.RetryAfter);

    // Validate output's Retry-After header value
    Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
```

Po spojení všech kroků testu jednotek mít následující kód:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HttpStartTests.cs)]

## <a name="unit-testing-orchestrator-functions"></a>Testování funkce nástroje orchestrator

Funkce nástroje Orchestrator je ještě zajímavější jednotky testování, protože mají obvykle mnohem víc obchodní logiku.

V této části se jednotka se testy ověření výstupu `E1_HelloSequence` funkce Orchestrátoru:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

Kód testu jednotek se spustí s vytvářením model:

```csharp
    var durableOrchestrationContextMock = new Mock<DurableOrchestrationContextBase>();
```

Pak bude imitace volání metody aktivity:

```csharp
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

Vedle testování částí zavolá `HelloSequence.Run` metody:

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

Po spojení všech kroků testu jednotek mít následující kód:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceOrchestratorTests.cs)]

## <a name="unit-testing-activity-functions"></a>Aktivita funkce testování částí

Aktivita funkce mohou být jednotky testování stejným způsobem jako jiné durable functions.

V této části testování částí ověří chování `E1_SayHello` aktivita funkce:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

A testování částí ověří formát výstupu. Testy jednotek můžete použít typy parametrů mock nebo přímo [DurableActivityContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContextBase.html) třídy:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o xUnit](https://xunit.github.io/docs/getting-started-dotnet-core)

> [Další informace o moq](https://github.com/Moq/moq4/wiki/Quickstart)
