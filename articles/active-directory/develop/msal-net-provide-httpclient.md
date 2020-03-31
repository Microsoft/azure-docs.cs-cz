---
title: Poskytnutí klienta & proxy (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Další informace o poskytování vlastní httpclient a proxy pro připojení k Azure AD pomocí Microsoft Authentication Library pro .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/23/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: dbf08e23b2bc1f657363f69df55763437e6c8a90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76695041"
---
# <a name="providing-your-own-httpclient-and-proxy-using-msalnet"></a>Poskytování vlastní httpclient a proxy pomocí MSAL.NET
Při [inicializaci aplikace veřejného klienta](msal-net-initializing-client-applications.md)můžete použít `.WithHttpClientFactory method` k poskytnutí vlastního httpclientu.  Poskytnutí vlastního httpclientu umožňuje pokročilé scénáře, jako je jemně odstupňovaná kontrola proxy protokolu HTTP, přizpůsobení záhlaví uživatelského agenta nebo vynucení msal použít konkrétní httpclient (například v ASP.NET základní webové aplikace nebo rozhraní API).

## <a name="initialize-with-httpclientfactory"></a>Inicializovat pomocí httpclientfactory
Následující příklad ukazuje vytvořit `HttpClientFactory` a potom inicializovat veřejnou klientskou aplikaci s ním:

```csharp
IMsalHttpClientFactory httpClientFactory = new MyHttpClientFactory();

var pca = PublicClientApplicationBuilder.Create(MsalTestConstants.ClientId) 
                                        .WithHttpClientFactory(httpClientFactory)
                                        .Build();
```

## <a name="httpclient-and-xamarin-ios"></a>HttpClient a Xamarin iOS
Při použití Xamarin iOS, se `HttpClient` doporučuje vytvořit, `NSURLSession`který explicitně používá obslužnou rutinu na základě iOS 7 a novější. MSAL.NET automaticky `HttpClient` vytvoří, `NSURLSessionHandler` který používá pro iOS 7 a novější. Další informace naleznete v [dokumentaci k xamarinu iOS pro httpclient](/xamarin/cross-platform/macios/http-stack).