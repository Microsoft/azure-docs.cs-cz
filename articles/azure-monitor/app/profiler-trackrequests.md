---
title: Napište kód pro sledování požadavků pomocí Azure Application Insights | Dokumenty společnosti Microsoft
description: Napište kód pro sledování požadavků pomocí Application Insights, abyste mohli získat profily pro vaše požadavky.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: c59cbe852a91a91c7b3adb4452328700ec718a82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671592"
---
# <a name="write-code-to-track-requests-with-application-insights"></a>Psaní kódu pro sledování požadavků pomocí application insights

Chcete-li zobrazit profily pro vaši aplikaci na stránce Výkon, Azure Application Insights potřebuje sledovat požadavky pro vaši aplikaci. Application Insights můžete automaticky sledovat požadavky na aplikace, které jsou postaveny na již instrumentované architektury. Dva příklady jsou ASP.NET a ASP.NET Core. 

Pro jiné aplikace, jako jsou role pracovních míst Azure Cloud Services a service fabric bezstavová rozhraní API, je třeba napsat kód sdělit Application Insights, kde vaše požadavky začínají a končí. Po napsání tohoto kódu se do Application Insights odešlou telemetrie požadavků. Telemetrická data můžete zobrazit na stránce Výkon a profily jsou shromažďovány pro tyto požadavky. 

Chcete-li ručně sledovat požadavky, postupujte takto:

  1. V rané fázi životnosti aplikace přidejte následující kód:  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      Další informace o této konfiguraci globálního klíče instrumentace naleznete v [tématu Použití prostředků na infrastrukturu služeb s přehledy aplikací](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md).  

  1. Pro jakýkoli kus kódu, který chcete `StartOperation<RequestTelemetry>` instrumentovat, přidejte příkaz **using** kolem něj, jak je znázorněno v následujícím příkladu:

        ```csharp
        using Microsoft.ApplicationInsights;
        using Microsoft.ApplicationInsights.DataContracts;
        ...
        var client = new TelemetryClient();
        ...
        using (var operation = client.StartOperation<RequestTelemetry>("Insert_Your_Custom_Event_Unique_Name"))
        {
          // ... Code I want to profile.
        }
        ```

        Volání `StartOperation<RequestTelemetry>` v `StartOperation<RequestTelemetry>` rámci jiného oboru není podporováno. Místo toho `StartOperation<DependencyTelemetry>` můžete použít vnořený obor. Například:  
        
        ```csharp
        using (var getDetailsOperation = client.StartOperation<RequestTelemetry>("GetProductDetails"))
        {
        try
        {
          ProductDetail details = new ProductDetail() { Id = productId };
          getDetailsOperation.Telemetry.Properties["ProductId"] = productId.ToString();
        
          // By using DependencyTelemetry, 'GetProductPrice' is correctly linked as part of the 'GetProductDetails' request.
          using (var getPriceOperation = client.StartOperation<DependencyTelemetry>("GetProductPrice"))
          {
              double price = await _priceDataBase.GetAsync(productId);
              if (IsTooCheap(price))
              {
                  throw new PriceTooLowException(productId);
              }
              details.Price = price;
          }
        
          // Similarly, note how 'GetProductReviews' doesn't establish another RequestTelemetry.
          using (var getReviewsOperation = client.StartOperation<DependencyTelemetry>("GetProductReviews"))
          {
              details.Reviews = await _reviewDataBase.GetAsync(productId);
          }
        
          getDetailsOperation.Telemetry.Success = true;
          return details;
        }
        catch(Exception ex)
        {
          getDetailsOperation.Telemetry.Success = false;
        
          // This exception gets linked to the 'GetProductDetails' request telemetry.
          client.TrackException(ex);
          throw;
        }
        }
        ```
