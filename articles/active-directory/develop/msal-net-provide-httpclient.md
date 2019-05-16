---
title: Zadejte HttpClient a proxy (MSAL.NET) | Azure
description: Další informace o zajišťování HttpClient a vlastní proxy server pro připojení k Azure AD pomocí knihovny Microsoft Authentication Library pro .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/23/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 234c9d0724021017ec8c411d637420b05284ea52
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544164"
---
# <a name="providing-your-own-httpclient-and-proxy-using-msalnet"></a>Poskytování vlastních proxy pomocí MSAL.NET a HttpClient
Při inicializaci aplikace veřejným klientem, můžete použít `.WithHttpClientFactory method` poskytnout vlastní HttpClient.  Poskytuje vlastní HttpClient umožňuje pokročilé scénáře takové velice přesně kontrolovat proxy serveru HTTP, přizpůsobení záhlaví uživatelského agenta nebo vynucení MSAL používat konkrétní HttpClient (například v ASP.NET Core webová aplikace/rozhraní API).

## <a name="initialize-with-httpclientfactory"></a>Inicializace s HttpClientFactory
Následující příklad ukazuje, jak vytvořit `HttpClientFactory` , kterou následně inicializujete veřejné klientské aplikace s ní:

```csharp
IMsalHttpClientFactory httpClientFactory = new MyHttpClientFactory();

var pca = PublicClientApplicationBuilder.Create(MsalTestConstants.ClientId) 
                                        .WithHttpClientFactory(httpClientFactory)
                                        .Build();
```

## <a name="httpclient-and-xamarin-ios"></a>HttpClient a Xamarin iOS
Pokud používáte Xamarin pro iOS, doporučuje se vytvořit `HttpClient` , který používá explicitně `NSURLSession`– na základě obslužné rutiny pro iOS 7 nebo novější. Automaticky vytvoří MSAL.NET `HttpClient` , která používá `NSURLSessionHandler` pro iOS 7 nebo novější. Další informace najdete v článku [dokumentace pro Xamarin iOS pro HttpClient](/xamarin/cross-platform/macios/http-stack).