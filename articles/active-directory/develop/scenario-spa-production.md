---
title: Přesunutí jednostránkové aplikace do produkčního prostředí – Microsoft Identity Platform | Azure
description: Naučte se vytvářet jednostránkové aplikace (přechod na produkční prostředí).
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba41b29cfcea2d9dfcab2cd552040eaffa4e90ff
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965275"
---
# <a name="single-page-application-move-to-production"></a>Jednostránkové aplikace: Přesun do produkčního prostředí

Když teď víte, jak získat token pro volání webových rozhraní API, přečtěte si, jak přejít do produkčního prostředí.

## <a name="improve-your-app"></a>Vylepšení aplikace

[Povolením protokolování](msal-logging.md) zajistěte přípravu vaší aplikace.

## <a name="test-your-integration"></a>Otestování integrace

Otestujte integraci podle [kontrolního seznamu integrace Microsoft Identity Platform](identity-platform-integration-checklist.md).

## <a name="next-steps"></a>Další kroky

Hluboká podrobně ukázky pro rychlý Start, která vysvětluje kód pro přihlášení uživatelů a získání přístupového tokenu pro volání rozhraní API Microsoft Graph pomocí MSAL. js:

> [!div class="nextstepaction"]
> [Kurz pro JavaScript SPA](./tutorial-v2-javascript-spa.md)

Ukázka, která ukazuje, jak získat tokeny pro vlastní back-endové webové rozhraní API pomocí MSAL. js:

> [!div class="nextstepaction"]
> [SPA s back-end ASP.NET](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2)

Ukázka, která ukazuje, jak pomocí MSAL. js přihlašovat uživatele v aplikaci, která je zaregistrovaná ve službě Azure Active Directory B2C (Azure AD B2C):

> [!div class="nextstepaction"]
> [SPA s Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)
