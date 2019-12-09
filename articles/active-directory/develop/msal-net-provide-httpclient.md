---
title: Zadání HttpClient a proxy serveru (MSAL.NET)
titleSuffix: Microsoft identity platform
description: Přečtěte si, jak poskytnout vlastní HttpClient a proxy server pro připojení k Azure AD pomocí knihovny Microsoft Authentication Library pro .NET (MSAL.NET).
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: c7b9a8d90c505cb93b6cf237ed67e9c47d9be0bb
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74915580"
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