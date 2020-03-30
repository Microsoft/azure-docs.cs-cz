---
title: Přesun jednostránkové aplikace do produkčního prostředí – platforma identit Microsoftu | Azure
description: Naučte se vytvářet jednostránkovou aplikaci (přechod do produkčního prostředí)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 01b923e0d013fab1815456e55eac6036ded772bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701837"
---
# <a name="single-page-application-move-to-production"></a>Jednostránková aplikace: Přechod do produkčního prostředí

Teď, když víte, jak získat token pro volání webových api, naučte se, jak přejít do produkčního prostředí.

## <a name="improve-your-app"></a>Vylepšete svou aplikaci

[Povolte protokolování,](msal-logging.md) aby vaše produkční aplikace byla připravena.

## <a name="test-your-integration"></a>Otestujte svou integraci

Otestujte integraci podle [kontrolního seznamu integrace platformy identit společnosti Microsoft](identity-platform-integration-checklist.md).

## <a name="next-steps"></a>Další kroky

Podrobné prohledání ukázky rychlého startu, která vysvětluje kód pro přihlášení uživatelů a získání přístupového tokenu pro volání rozhraní Microsoft Graph API pomocí souboru MSAL.js:

> [!div class="nextstepaction"]
> [Výukový program JavaScript SPA](./tutorial-v2-javascript-spa.md)

Ukázka, která ukazuje, jak získat tokeny pro vlastní webové rozhraní API back-end pomocí MSAL.js:

> [!div class="nextstepaction"]
> [SPA s ASP.NET zadním koncem](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2)

Ukázka, která ukazuje, jak pomocí souboru MSAL.js přihlašovat uživatele v aplikaci, která je registrovaná v Azure Active Directory B2C (Azure AD B2C):

> [!div class="nextstepaction"]
> [SPA s Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)
