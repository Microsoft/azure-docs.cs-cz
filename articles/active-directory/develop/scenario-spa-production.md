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
ms.openlocfilehash: 9656da8be086724482f129efab323e02b73e117e
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954952"
---
# <a name="single-page-application-move-to-production"></a>Jednostránkové aplikace: Přesun do produkčního prostředí

Teď, když víte, jak získat token pro volání webových rozhraní API, je zde několik věcí, které je potřeba vzít v úvahu při přesunu aplikace do produkčního prostředí.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="deploy-your-app"></a>Nasazení aplikace

Podívejte se na [ukázku nasazení](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter3) a Naučte se, jak nasadit vaše projekty Spa a webové rozhraní API pomocí Azure Storage a Azure App Services, v uvedeném pořadí. 

## <a name="code-samples"></a>Ukázky kódů

Tyto ukázky kódu ukazují několik klíčových operací pro jednostránkové aplikace.
- [Spa s back-endu ASP.NET](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi): jak získat tokeny pro vlastní back-endové webové rozhraní API (ASP.NET Core) pomocí **MSAL.js**.

- [Node.js Web API (Azure AD](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2): jak ověřit přístupové tokeny pro vaše back-end webové rozhraní API (Node.js) pomocí **Passport-Azure-AD**.

- [Spa s Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp): jak pomocí **MSAL.js** přihlašovat uživatele v aplikaci, která je zaregistrovaná v **Azure Active Directory B2C** (Azure AD B2C).

- [Node.js Web API (Azure AD B2C)](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi): jak používat **Passport-Azure-AD** k ověření přístupových tokenů pro aplikace zaregistrované v **Azure Active Directory B2C** (Azure AD B2C).

## <a name="next-steps"></a>Další kroky

- [Kurz pro JavaScript Spa](./tutorial-v2-javascript-spa.md): hluboká podrobně, jak přihlašovat uživatele a získat přístupový token pro volání **rozhraní API Microsoft Graph** pomocí **MSAL.js**.