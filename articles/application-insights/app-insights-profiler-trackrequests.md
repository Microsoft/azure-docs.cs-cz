---
title: Napsání kódu pro sledování požadavků pomocí Azure Application Insights | Dokumentace Microsoftu
description: Napsání kódu pro sledování požadavků pomocí nástroje Application Insights, abyste se mohli profilů pro vaše reqeusts
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 20f408d9dd32c3fd7a0e319e4051483e3aa54dd9
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52722118"
---
# <a name="write-code-to-track-requests-with-application-insights"></a>Napsání kódu pro sledování požadavků pomocí nástroje Application Insights

Profily pro vaši aplikaci najdete na stránce výkonu musí být sledování požadavků pro vaši aplikaci Application Insights. Application Insights může automaticky sledovat žádosti o aplikace, které jsou založené na rozhraní, které již byly instrumentovány, jako je ASP.net a ASP.Net Core. Ale pro jiné aplikace, jako jsou role pracovního procesu Azure Cloud Service a Service Fabric bezstavové rozhraní API, potřeba psát kód říct Application Insights, kde žádostí o zahájení a ukončení. Tento kód jste napsali jednou žádostí telemetrie se bude posílat do Application Insights a telemetrická data zobrazí na stránce výkon a profily, nebudou se shromažďovat pro tyto požadavky. 

Tady jsou kroky je potřeba provést manuálně sledovat požadavky:


  1. V rané fázi životního cyklu aplikací přidejte následující kód:  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      Další informace o této konfiguraci globální Instrumentační klíč najdete v části [pomocí Service Fabric pomocí Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md).  

  1. Pro jsou části kódu, který chcete instrumentovat, přidejte `StartOperation<RequestTelemetry>` **použití** příkaz kolem něj, jak je znázorněno v následujícím příkladu:

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

        Volání `StartOperation<RequestTelemetry>` v jiném `StartOperation<RequestTelemetry>` obor se nepodporuje. Můžete použít `StartOperation<DependencyTelemetry>` místo ve vnořeném oboru. Příklad:  
        
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
