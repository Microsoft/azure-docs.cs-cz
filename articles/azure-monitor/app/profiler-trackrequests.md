---
title: Napsat kód pro sledování požadavků pomocí Azure Application Insights | Microsoft Docs
description: Napíšete kód pro sledování požadavků pomocí Application Insights, abyste mohli získat profily pro vaše požadavky.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.custom: devx-track-csharp
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: aaa1d6df9faa20b1a561bfccdfea682af7645c18
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88930243"
---
# <a name="write-code-to-track-requests-with-application-insights"></a>Napsat kód pro sledování požadavků pomocí Application Insights

Pokud chcete zobrazit profily pro vaši aplikaci na stránce výkon, Azure Application Insights potřebuje sledovat žádosti pro vaši aplikaci. Application Insights může automaticky sledovat žádosti o aplikace, které jsou postavené na již instrumentované architektuře. Dva příklady jsou ASP.NET a ASP.NET Core. 

U jiných aplikací, jako jsou role pracovních procesů Azure Cloud Services a Service Fabric bezstavových rozhraní API, je potřeba napsat kód, který sděluje Application Insights, kde vaše požadavky začínají a končí. Po dokončení zápisu tohoto kódu se do Application Insights odesílá žádost o telemetrii. Telemetrii můžete zobrazit na stránce výkon a profily jsou shromažďovány pro tyto požadavky. 

Chcete-li ručně sledovat požadavky, postupujte následovně:

  1. V brzké době životnosti aplikace přidejte následující kód:  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      Další informace o této konfiguraci globálního instrumentace klíčů najdete v tématu [použití Service Fabric s Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md).  

  1. Pro libovolnou část kódu, kterou chcete instrumentovat, přidejte `StartOperation<RequestTelemetry>` příkaz **using** , jak je znázorněno v následujícím příkladu:

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

        Volání `StartOperation<RequestTelemetry>` v jiném `StartOperation<RequestTelemetry>` oboru se nepodporuje. `StartOperation<DependencyTelemetry>`Místo toho můžete použít ve vnořeném oboru. Příklad:  
        
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
