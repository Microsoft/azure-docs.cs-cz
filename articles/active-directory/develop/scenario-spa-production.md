---
title: Přesunutí jednostránkové aplikace do produkčního prostředí
titleSuffix: Microsoft identity platform
description: Naučte se vytvářet jednostránkové aplikace (přechod na produkční prostředí).
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 0c1e9e6da02478f5e4703676b74fc8247a4f619b
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2021
ms.locfileid: "98753532"
---
# <a name="single-page-application-move-to-production"></a>Jednostránkové aplikace: Přesun do produkčního prostředí

Když teď víte, jak získat token pro volání webových rozhraní API, přečtěte si, jak přejít do produkčního prostředí.

## <a name="improve-your-app"></a>Vylepšení aplikace

[Povolením protokolování](msal-logging.md) zajistěte přípravu vaší aplikace.

## <a name="test-your-integration"></a>Otestování integrace

Otestujte integraci podle [kontrolního seznamu integrace Microsoft Identity Platform](identity-platform-integration-checklist.md).

## <a name="deploy-your-app"></a>Nasazení aplikace

Podívejte se na [ukázku nasazení](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter3) a Naučte se, jak nasadit vaše projekty Spa a webové rozhraní API pomocí Azure Storage a Azure App Services, v uvedeném pořadí. 

## <a name="next-steps"></a>Další kroky

- Důkladná podrobně ukázky pro rychlý Start, která vysvětluje kód pro přihlášení uživatelů a získání přístupového tokenu pro volání **rozhraní API Microsoft Graph** pomocí **MSAL.js** v [kurzu pro JavaScript Spa](./tutorial-v2-javascript-spa.md).

- Ukázka, která ukazuje, jak získat tokeny pro vlastní back-endové webové rozhraní API (ASP.NET Core) pomocí **MSAL.js**: [Spa s back-end ASP.NET](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi).

- Ukázka, která demonstruje, jak ověřit přístupové tokeny pro vaše back-end webové rozhraní API (Node.js) pomocí **Passport-Azure-AD**: [Node.js Web API (Azure AD](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2).

- Ukázka, která ukazuje, jak použít **MSAL.js** k přihlášení uživatelů v aplikaci registrované v **Azure Active Directory B2C** (Azure AD B2C): [Spa s Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp).

- Ukázka, která ukazuje použití **služby Passport-Azure-AD** k ověření přístupových tokenů pro aplikace zaregistrované ve službě **Azure Active Directory B2C** (Azure AD B2C): [Node.js webové rozhraní API (Azure AD B2C)](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi).
