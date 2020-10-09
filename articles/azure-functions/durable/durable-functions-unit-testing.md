---
title: Testování částí Azure Durable Functions
description: Přečtěte si, jak Durable Functions testování částí.
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: 86733f8b5b80799bad3e52c643ed27465dfc7641
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "74231230"
---
# <a name="durable-functions-unit-testing"></a>Testování částí Durable Functions

Testování částí je důležitou součástí moderních postupů vývoje softwaru. Testy jednotek ověřují chování obchodní logiky a chrání před tím, než budou v budoucnu předem zjištěny zásadní změny. Durable Functions může snadno růst složitou složitost, takže zavedení testů jednotek pomůže vyhnout se neprůlomovým změnám. Následující části vysvětlují, jak otestovat testování tří funkcí – klienta orchestrace, nástroje Orchestrator a aktivity.

> [!NOTE]
> Tento článek poskytuje pokyny pro testování částí Durable Functions aplikací cílících na Durable Functions 1. x. Ještě nebyla aktualizována na účet pro změny, které byly zavedeny v Durable Functions 2. x. Další informace o rozdílech mezi verzemi najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

## <a name="prerequisites"></a>Požadavky

Příklady v tomto článku vyžadují znalost následujících konceptů a platforem:

* Testování jednotek

* Odolná služba Functions

* rozhraní [xUnit](https://xunit.github.io/) -Testing

* rozhraní pro návrhy [MOQ](https://github.com/moq/moq4)

## <a name="base-classes-for-mocking"></a>Základní třídy pro napodobování

Napodobování se podporuje prostřednictvím tří abstraktních tříd v Durable Functions 1. x:

* `DurableOrchestrationClientBase`

* `DurableOrchestrationContextBase`

* `DurableActivityContextBase`

Tyto třídy jsou základní třídy pro `DurableOrchestrationClient` , `DurableOrchestrationContext` a, `DurableActivityContext` které definují orchestraci klienta, nástroje Orchestrator a metody aktivity. Modely nastaví očekávané chování pro metody základní třídy, aby test jednotek mohl ověřit obchodní logiku. Pro testování částí obchodní logiky v klientu Orchestration a Orchestrator je k dispozici dva kroky.

1. Použijte základní třídy namísto konkrétní implementace při definování signatury funkcí klienta a nástroje Orchestrator.
2. V testování částí je chování základních tříd a ověření obchodní logiky.

Další podrobnosti najdete v následujících odstavcích pro testování funkcí, které používají vazbu klienta Orchestration a aktivační vazbu nástroje Orchestrator.

## <a name="unit-testing-trigger-functions"></a>Aktivační funkce testování částí

V této části test jednotek ověří logiku následující funkce triggeru HTTP pro spuštění nových orchestrací.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

Úkol testování částí bude ověřovat hodnotu hlavičky, kterou jste `Retry-After` zadali v datové části odpovědi. Test jednotek tak bude napsaný některé z `DurableOrchestrationClientBase` metod, aby bylo zajištěno předvídatelné chování.

Nejprve je vyžadován druh základní třídy, `DurableOrchestrationClientBase` . Přípravou může být nová třída, která implementuje `DurableOrchestrationClientBase` . Nicméně použití napodobované architektury, jako je [MOQ](https://github.com/moq/moq4) , zjednodušuje proces:

```csharp
    // Mock DurableOrchestrationClientBase
    var durableOrchestrationClientBaseMock = new Mock<DurableOrchestrationClientBase>();
```

Pak `StartNewAsync` je metoda napodobná, aby vracela ID známé instance.

```csharp
    // Mock StartNewAsync method
    durableOrchestrationClientBaseMock.
        Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
        ReturnsAsync(instanceId);
```

Další je napodobná `CreateCheckStatusResponse` , aby vždycky vracela prázdnou odpověď HTTP 200.

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
        durableOrchestrationClientBaseMock.Object,
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
    var durableOrchestrationContextMock = new Mock<DurableOrchestrationContextBase>();
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

A testy jednotek budou ověřovat formát výstupu. Testy jednotek mohou používat typy parametrů přímo nebo maketnou `DurableActivityContextBase` třídu:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o xUnit](https://xunit.github.io/docs/getting-started-dotnet-core)
> 
> [Další informace o MOQ](https://github.com/Moq/moq4/wiki/Quickstart)
