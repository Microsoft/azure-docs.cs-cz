---
title: Napsání kódu pro sledování požadavků pomocí Azure Application Insights | Dokumentace Microsoftu
description: Napsání kódu pro sledování požadavků pomocí nástroje Application Insights, abyste se mohli profilů pro vaše požadavky.
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 4782e560b580b7f565724dbb35ed9876bffdc256
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55882059"
---
# <a name="write-code-to-track-requests-with-application-insights"></a>Napsání kódu pro sledování požadavků pomocí nástroje Application Insights

Chcete-li zobrazit profily pro vaši aplikaci na stránce výkon, Azure Application Insights potřebuje ke sledování požadavků pro vaši aplikaci. Application Insights může automaticky sledovat žádosti o aplikace, které jsou postavené na již instrumentována architektur. Dva příklady jsou ASP.NET a ASP.NET Core. 

U ostatních aplikací, jako jsou role pracovního procesu Azure Cloud Services a Service Fabric bezstavové rozhraní API budete muset psát kód sdělit své žádosti, kde začít Application Insights a end. Poté, co jste napsali tento kód, telemetrie požadavky odeslána do služby Application Insights. Telemetrii můžete zobrazit na stránce výkon a profily se shromažďují, aby tyto požadavky. 

Můžete manuálně sledovat žádosti, postupujte takto:

  1. V rané fázi životního cyklu aplikací přidejte následující kód:  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      Další informace o této konfiguraci globální Instrumentační klíč najdete v části [pomocí Service Fabric pomocí Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md).  

  1. Pro jsou části kódu, který chcete instrumentovat, přidejte `StartOperation<RequestTelemetry>` **pomocí** příkaz kolem něj, jak je znázorněno v následujícím příkladu:

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
