---
title: Stav vlastní Orchestrace v Durable Functions – Azure
description: Zjistěte, jak nakonfigurovat a používat stav vlastní Orchestrace pro Durable Functions.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: azfuncdf
ms.openlocfilehash: b8017288adb75c990113b0f2ff5ba29a1f1e0a18
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49986657"
---
# <a name="custom-orchestration-status-in-durable-functions-azure-functions"></a>Stav vlastní Orchestrace v Durable Functions (Azure Functions)

Stav vlastní Orchestrace umožňuje nastavit vlastní stav hodnotu funkce nástroje orchestrator. Tento stav se poskytuje prostřednictvím rozhraní API HTTP GetStatus nebo `DurableOrchestrationClient.GetStatusAsync` rozhraní API.

> [!NOTE]
> Stav vlastní Orchestrace pro jazyk JavaScript, bude k dispozici v nadcházející verzi.

## <a name="sample-use-cases"></a>Ukázka případy použití 

### <a name="visualize-progress"></a>Vizualizace průběhu

Klienty můžete dotazovat stav koncového bodu a zobrazit průběh uživatelského rozhraní, které vizualizuje aktuální fáze spuštění. Následující příklad ukazuje průběh sdílení:

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
  [OrchestrationTrigger] DurableOrchestrationContextBase context)
{
  var outputs = new List<string>();

  outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
  context.SetCustomStatus("Tokyo");
  outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
  context.SetCustomStatus("Seattle");
  outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));
  context.SetCustomStatus("London");

  // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
  return outputs;
}

[FunctionName("E1_SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
  return $"Hello {name}!";
}
```

A pak klienta se zobrazí výstup orchestraci pouze tehdy, když `CustomStatus` pole nastavena na "Londýn":

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
  [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
  [OrchestrationClient] DurableOrchestrationClientBase starter,
  string functionName,
  ILogger log)
{
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    DurableOrchestrationStatus durableOrchestrationStatus = await starter.GetStatusAsync(instanceId);
    while (durableOrchestrationStatus.CustomStatus.ToString() != "London")
    {
      await Task.Delay(200);
      durableOrchestrationStatus = await starter.GetStatusAsync(instanceId);
    }

    HttpResponseMessage httpResponseMessage = new HttpResponseMessage(HttpStatusCode.OK)
    {
      Content = new StringContent(JsonConvert.SerializeObject(durableOrchestrationStatus))
    };

    return httpResponseMessage;
  }
}
```

### <a name="output-customization"></a>Výstup vlastního nastavení 

Další zajímavé scénář je tak, že vrací vlastní výstup na základě jedinečných charakteristik nebo interakcí segmentace uživatelů. Díky pomoci stav vlastní Orchestrace zůstane obecný kód na straně klienta. Všechny hlavní úpravy dojde na straně serveru, jak je znázorněno v následujícím příkladu:

```csharp
[FunctionName("CityRecommender")]
public static void Run(
  [OrchestrationTrigger] DurableOrchestrationContextBase context)
{
  int userChoice = context.GetInput<int>();

  switch (userChoice)
  {
    case 1:
    context.SetCustomStatus(new
    {
      recommendedCities = new[] {"Tokyo", "Seattle"},
      recommendedSeasons = new[] {"Spring", "Summer"}
     });
      break;
    case 2:
      context.SetCustomStatus(new
      {
        recommendedCity = new[] {"Seattle, London"},
        recommendedSeasons = new[] {"Summer"}
      });
        break;
      case 3:
      context.SetCustomStatus(new
      {
        recommendedCity = new[] {"Tokyo, London"},
        recommendedSeasons = new[] {"Spring", "Summer"}
      });
        break;
  }

  // Wait for user selection and refine the recommendation
} 
```

### <a name="instruction-specification"></a>Specifikace instrukce 

Orchestrator může poskytovat klientům prostřednictvím vlastního stavu jedinečný pokyny. Stav vlastní pokyny budou zmapována do kroků Orchestrace kódu:

```csharp
[FunctionName("ReserveTicket")]
public static async Task<bool> Run(
  [OrchestrationTrigger] DurableOrchestrationContextBase context)
{
  string userId = context.GetInput<string>();

  int discount = await context.CallActivityAsync<int>("CalculateDiscount", userId);

  context.SetCustomStatus(new
  {
    discount = discount,
    discountTimeout = 60,
    bookingUrl = "https://www.myawesomebookingweb.com",
  });

  bool isBookingConfirmed = await context.WaitForExternalEvent<bool>("BookingConfirmed");

  context.SetCustomStatus(isBookingConfirmed
    ? new {message = "Thank you for confirming your booking."}
    : new {message = "The booking was not confirmed on time. Please try again."});

  return isBookingConfirmed;
}
```

## <a name="sample"></a>Ukázka

V následujícím příkladu je vlastní stav nastaven.

```csharp
public static async Task SetStatusTest([OrchestrationTrigger] DurableOrchestrationContext ctx)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { nextActions = new [] {"A", "B", "C"}, foo = 2, };
    ctx.SetCustomStatus(customStatus);

    // ...do more work...
}
```

Je spuštěn orchestraci, můžete načíst externí klienti tento vlastní stav:

```http
GET /admin/extensions/DurableTaskExtension/instances/instance123

```

Klienti získáte odpovědi na následující: 

```http
{
  "runtimeStatus": "Running",
  "input": null,
  "customStatus": { "nextActions": ["A", "B", "C"], "foo": 2 },
  "output": null,
  "createdTime": "2017-10-06T18:30:24Z",
  "lastUpdatedTime": "2017-10-06T19:40:30Z"
}
```

> [!WARNING]
>  Datová část vlastní stav je omezena na 16 KB text JSON UTF-16, protože se musí být schopni vyplňoval sloupec Azure Table Storage. Vývojáři mohou pomocí externího úložiště, pokud budou potřebovat větší datovou část.


## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o rozhraní API protokolu HTTP v Durable Functions](durable-functions-http-api.md)


