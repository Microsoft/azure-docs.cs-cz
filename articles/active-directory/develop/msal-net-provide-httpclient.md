---
title: Zadání HttpClient a proxy serveru (MSAL.NET)
titleSuffix: Microsoft identity platform
description: Přečtěte si, jak poskytnout vlastní HttpClient a proxy server pro připojení k Azure AD pomocí knihovny Microsoft Authentication Library pro .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/23/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 259c796cb3653ebdd330f5e65db76cc29c181467
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802760"
---
# <a name="providing-your-own-httpclient-and-proxy-using-msalnet"></a>Poskytování vlastních HttpClient a proxy serveru pomocí MSAL.NET
Při [inicializaci veřejné klientské aplikace](msal-net-initializing-client-applications.md)můžete použít `.WithHttpClientFactory method` k poskytnutí vlastních HttpClient.  Poskytování vlastní HttpClient umožňuje pokročilým scénářům, jako je jemně odstupňovaná kontrola proxy serveru HTTP, přizpůsobení hlaviček uživatelských agentů nebo vynucení MSAL používání konkrétního HttpClient (například v ASP.NET Core Web Apps/rozhraní API).

## <a name="initialize-with-httpclientfactory"></a>Inicializovat pomocí HttpClientFactory
Následující příklad ukazuje, jak vytvořit `HttpClientFactory` a pak s ní inicializovat veřejnou klientskou aplikaci:

```csharp
IMsalHttpClientFactory httpClientFactory = new MyHttpClientFactory();

var pca = PublicClientApplicationBuilder.Create(MsalTestConstants.ClientId) 
                                        .WithHttpClientFactory(httpClientFactory)
                                        .Build();
```

## <a name="httpclient-and-xamarin-ios"></a>HttpClient a Xamarin iOS
Při použití Xamarin iOS se doporučuje vytvořit `HttpClient`, která explicitně používá obslužnou rutinu založenou na `NSURLSession`pro iOS 7 a novější. MSAL.NET automaticky vytvoří `HttpClient`, která používá `NSURLSessionHandler` pro iOS 7 a novější. Další informace najdete v dokumentaci k [Xamarin iOS pro HttpClient](/xamarin/cross-platform/macios/http-stack).