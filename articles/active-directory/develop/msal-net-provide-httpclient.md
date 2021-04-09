---
title: Zadejte proxy server HttpClient & (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si, jak poskytnout vlastní HttpClient a proxy server pro připojení k Azure AD pomocí knihovny Microsoft Authentication Library pro .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/23/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 6139fd1b081c69f037ec9cd3313e4a6499c39543
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98064619"
---
# <a name="providing-your-own-httpclient-and-proxy-using-msalnet"></a>Poskytování vlastních HttpClient a proxy serveru pomocí MSAL.NET
Při [inicializaci veřejné klientské aplikace](msal-net-initializing-client-applications.md)můžete použít `.WithHttpClientFactory method` k poskytnutí vlastní HttpClient.  Poskytování vlastní HttpClient umožňuje pokročilým scénářům, jako je jemně odstupňovaná kontrola proxy serveru HTTP, přizpůsobení hlaviček uživatelských agentů nebo vynucení MSAL používání konkrétního HttpClient (například v ASP.NET Core Web Apps/rozhraní API).

## <a name="initialize-with-httpclientfactory"></a>Inicializovat pomocí HttpClientFactory
Následující příklad ukazuje vytvoření `HttpClientFactory` a následné inicializaci klientské aplikace s ním:

```csharp
IMsalHttpClientFactory httpClientFactory = new MyHttpClientFactory();

var pca = PublicClientApplicationBuilder.Create(MsalTestConstants.ClientId) 
                                        .WithHttpClientFactory(httpClientFactory)
                                        .Build();
```

## <a name="httpclient-and-xamarin-ios"></a>HttpClient a Xamarin iOS
Při použití Xamarin iOS se doporučuje vytvořit `HttpClient` , který explicitně používá `NSURLSession` obslužnou rutinu založenou na systému iOS 7 a novějším. MSAL.NET automaticky vytvoří `HttpClient` , který používá systém `NSURLSessionHandler` iOS 7 a novější. Další informace najdete v dokumentaci k [Xamarin iOS pro HttpClient](/xamarin/cross-platform/macios/http-stack).