---
title: Testování jednotek Azure Durable Functions
description: Přečtěte si, jak testovat trvanlivé funkce.
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: 86733f8b5b80799bad3e52c643ed27465dfc7641
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74231230"
---
# <a name="durable-functions-unit-testing"></a>Testování jednotek durable functions

Testování částí je důležitou součástí moderních postupů vývoje softwaru. Testy částí ověřují chování obchodní logiky a chrání před zaváděním nepozorosivalé změny v budoucnu. Trvalé funkce může snadno růst ve složitosti, takže zavedení testů částí pomůže vyhnout se změnám. V následujících částech je vysvětleno, jak otestovat tři typy funkcí – klientorchestraci, orchestrátor a funkce aktivity.

> [!NOTE]
> Tento článek obsahuje pokyny pro testování částí pro aplikace durable funkce cílení trvalé funkce 1.x. Dosud nebyla aktualizována, aby zohlednila změny zavedené v trvalé funkce 2.x. Další informace o rozdílech mezi verzemi naleznete v článku [verze durable functions.](durable-functions-versions.md)

## <a name="prerequisites"></a>Požadavky

Příklady v tomto článku vyžadují znalost následujících konceptů a rámců:

* Testování jednotek

* Odolná služba Functions

* [xUnit](https://xunit.github.io/) - Testovací rámec

* [moq](https://github.com/moq/moq4) - Mocking rámec

## <a name="base-classes-for-mocking"></a>Základní třídy pro zesměšňování

Výsměch je podporován prostřednictvím tří abstraktních tříd v trvanlivé funkce 1.x:

* `DurableOrchestrationClientBase`

* `DurableOrchestrationContextBase`

* `DurableActivityContextBase`

Tyto třídy jsou `DurableOrchestrationClient` `DurableOrchestrationContext`základní `DurableActivityContext` třídy pro , a které definují Orchestraation Client, Orchestrator a Activity metody. Mocks nastaví očekávané chování pro metody základní třídy, takže testování částí můžete ověřit obchodní logiku. Existuje dvoustupňový pracovní postup pro testování obchodní logiky v klientovi orchestrace a orchestrátoru:

1. Při definování podpisů orchestrace klienta a orchestrátoru použijte základní třídy namísto konkrétní implementace.
2. V testování částí zesměšňovat chování základních tříd a ověřit obchodní logiku.

Další podrobnosti naleznete v následujících odstavcích pro testování funkcí, které používají vazbu klienta orchestrace a vazby aktivační události orchestratoru.

## <a name="unit-testing-trigger-functions"></a>Funkce spouště testování částí

V této části testování částí ověří logiku následující funkce aktivační události PROTOKOLU HTTP pro spuštění nových orchestrací.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

Úkolem testování částí bude ověření hodnoty `Retry-After` hlavičky uvedené v datové části odpovědi. Takže testování částí bude `DurableOrchestrationClientBase` zesměšňovat některé metody k zajištění předvídatelné chování.

Nejprve je vyžadován a mock `DurableOrchestrationClientBase`základní třídy. Mock může být nová třída, `DurableOrchestrationClientBase`která implementuje . Nicméně, pomocí výsměchu rámce, jako [je moq](https://github.com/moq/moq4) zjednodušuje proces:

```csharp
    // Mock DurableOrchestrationClientBase
    var durableOrchestrationClientBaseMock = new Mock<DurableOrchestrationClientBase>();
```

Pak `StartNewAsync` metoda je zesměšňován vrátit známé ID instance.

```csharp
    // Mock StartNewAsync method
    durableOrchestrationClientBaseMock.
        Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
        ReturnsAsync(instanceId);
```

Dále `CreateCheckStatusResponse` je zesměšňován vždy vrátit prázdnou odpověď HTTP 200.

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

`ILogger`je také zesměšňován:

```csharp
    // Mock ILogger
    var loggerMock = new Mock<ILogger>();
```  

Nyní `Run` je metoda volána z testování částí:

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

 Posledním krokem je porovnání výstupu s očekávanou hodnotou:

```csharp
    // Validate that output is not null
    Assert.NotNull(result.Headers.RetryAfter);

    // Validate output's Retry-After header value
    Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
```

Po kombinaci všech kroků bude mít testování částí následující kód:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HttpStartTests.cs)]

## <a name="unit-testing-orchestrator-functions"></a>Funkce orchestrátoru testování částí

Funkce Orchestrator jsou ještě zajímavější pro testování částí, protože obvykle mají mnohem více obchodní logiky.

V této části budou testy částí `E1_HelloSequence` ověřovat výstup funkce Orchestrator:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

Kód testování částí začne vytvořením mocku:

```csharp
    var durableOrchestrationContextMock = new Mock<DurableOrchestrationContextBase>();
```

Pak volání metody aktivity budou zesměšňováni:

```csharp
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

Další test jednotky `HelloSequence.Run` bude volat metodu:

```csharp
    var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);
```

A nakonec bude výstup ověřen:

```csharp
    Assert.Equal(3, result.Count);
    Assert.Equal("Hello Tokyo!", result[0]);
    Assert.Equal("Hello Seattle!", result[1]);
    Assert.Equal("Hello London!", result[2]);
```

Po kombinaci všech kroků bude mít testování částí následující kód:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceOrchestratorTests.cs)]

## <a name="unit-testing-activity-functions"></a>Funkce činnosti testování částí

Funkce aktivity mohou být testovány stejně jako netrvanlivé funkce.

V této části testování částí ověří `E1_SayHello` chování funkce Aktivita:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

A testy částí ověří formát výstupu. Testy jednotek můžete použít typy `DurableActivityContextBase` parametrů přímo nebo mock třídy:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o xUnit](https://xunit.github.io/docs/getting-started-dotnet-core)
> 
> [Další informace o moq](https://github.com/Moq/moq4/wiki/Quickstart)
