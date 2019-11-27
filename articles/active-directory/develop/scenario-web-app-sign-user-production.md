---
title: Webová aplikace, která přihlašuje uživatele (Přesun do produkčního prostředí) – Microsoft Identity Platform
description: Naučte se, jak vytvořit webovou aplikaci, která přihlašuje uživatele (přesunout do produkčního prostředí).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: efcc7ed4f80d0e3e9750d19ff95d010052a08b87
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2019
ms.locfileid: "74481891"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>Webová aplikace, která přihlašuje uživatele: Přesun do produkčního prostředí

Když teď víte, jak získat token pro volání webových rozhraní API, přečtěte si, jak ho přesunout do produkčního prostředí.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Další kroky

### <a name="scenario-for-calling-web-apis"></a>Scénář pro volání webových rozhraní API

Jakmile se webová aplikace přihlásí uživatelům, může volat webová rozhraní API jménem přihlášených uživatelů. Volání webových rozhraní API z webové aplikace je objektem následujícího scénáře:

> [!div class="nextstepaction"]
> [Webová aplikace, která volá webová rozhraní API](scenario-web-app-call-api-overview.md)

### <a name="deep-dive-aspnet-core-web-app-tutorial"></a>Hluboká podrobně: kurz ASP.NET Core webové aplikace

Přečtěte si o dalších způsobech, jak se přihlašovat uživatelům pomocí tohoto ASP.NET Core kurzu: 

> [!div class="nextstepaction"]
> [Umožněte vašim webovým aplikacím přihlašovat uživatele a volat rozhraní API s platformou Microsoft identity pro vývojáře.](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 

Tento postupný kurz obsahuje kód připravený pro produkční prostředí pro webovou aplikaci, včetně postupu přidání přihlášení pomocí účtů v nástroji:

- Vaše organizace
- Více organizací
- Pracovní nebo školní účty nebo osobní účty Microsoft
- [Azure AD B2C](https://aka.ms/aadb2c)
- Národní cloudy

### <a name="sample-code-java-web-app"></a>Vzorový kód: webová aplikace Java

Přečtěte si další informace o webové aplikaci Java z této ukázky na GitHubu: 

> [!div class="nextstepaction"]
> [Webová aplikace Java, která přihlašuje uživatele pomocí platformy a volání Microsoft identity Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)
