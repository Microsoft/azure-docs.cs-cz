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
ms.openlocfilehash: bd6717c132d32d54c16e7678695a09e35181a057
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086552"
---
# <a name="web-app-that-signs-in-users---move-to-production"></a>Webová aplikace, která přihlašuje uživatele – přesune se do produkčního prostředí

Když teď víte, jak získat token pro volání webových rozhraní API, přečtěte si, jak ho přesunout do produkčního prostředí.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Další kroky

### <a name="calling-web-apis-scenario"></a>Scénář volání webových rozhraní API

Jakmile se vaše webová aplikace přihlásí uživatelům, může volat webová rozhraní API jménem přihlášených uživatelů. Volání webových rozhraní API z webové aplikace je objektem následujícího scénáře:

> [!div class="nextstepaction"]
> [Webová aplikace, která volá webová rozhraní API](scenario-web-app-call-api-overview.md)

### <a name="deep-dive---aspnet-core-web-app-tutorial"></a>Kurz pro rozsáhlou podrobně-ASP.NET Core Web App

Přečtěte si o dalších způsobech přihlašování uživatelů pomocí ASP.NET Core kurzu: [MS-identity-aspnetcore-WebApp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial). Tato ukázka je postupný kurz s kódem připraveným pro produkci webové aplikace, včetně postupu přidání přihlášení pomocí účtů v nástroji:

- vaše organizace,
- více organizací,
- pracovní nebo školní účty nebo osobní účet Microsoft,
- pomocí [Azure AD B2C](https://aka.ms/aadb2c),
- nebo v národních cloudech.

### <a name="sample-code---java-web-app"></a>Ukázka kódu – webová aplikace Java

Přečtěte si další informace o webové aplikaci Java z ukázky na GitHubu: [Webová aplikace Java, která přihlašuje uživatele pomocí platformy a volání Microsoft identity Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)
