---
title: Přesunutí jednostránkové aplikace do produkčního prostředí – Microsoft Identity Platform | Azure
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
ms.openlocfilehash: 21ba0193c3f1e19ffc74452aaceee34759c7e606
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88949010"
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

Důkladná podrobně ukázky pro rychlý Start, která vysvětluje kód pro přihlášení uživatelů a získání přístupového tokenu pro volání **rozhraní API Microsoft Graph** pomocí **MSAL.js**:

> [!div class="nextstepaction"]
> [Kurz pro JavaScript SPA](./tutorial-v2-javascript-spa.md)

Ukázka, která ukazuje, jak získat tokeny pro vlastní back-endové webové rozhraní API (ASP.NET Core) pomocí **MSAL.js**:

> [!div class="nextstepaction"]
> [SPA s back-end ASP.NET](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi)

Ukázka, která ukazuje, jak ověřit přístupové tokeny pro vaše back-end webové rozhraní API (Node.js) pomocí **Passport-Azure-AD**.

> [!div class="nextstepaction"]
> [Node.js webové rozhraní API (Azure AD)](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2)

Ukázka, která ukazuje použití **MSAL.js** k přihlášení uživatelů v aplikaci registrované v **Azure Active Directory B2C** (Azure AD B2C):

> [!div class="nextstepaction"]
> [SPA s Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)

Ukázka, která ukazuje použití **služby Passport-Azure-AD** k ověření přístupových tokenů pro aplikace zaregistrované ve službě **Azure Active Directory B2C** (Azure AD B2C)

> [!div class="nextstepaction"]
> [Node.js webové rozhraní API (Azure AD B2C)](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)
