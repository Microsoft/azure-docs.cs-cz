---
title: Přesunutí webové aplikace, která se připočtou k uživatelům do produkčního prostředí – platforma microsoft identit | Azure
description: Naučte se vytvářet webovou aplikaci, která se přiřazuje k uživatelům (přechod do produkčního prostředí)
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
ms.openlocfilehash: 6c486e59f32afd09a9934ae2298172ccb4ee2414
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76768106"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>Webová aplikace, která se připisuje k uživatelům: Přechod do produkčního prostředí

Teď, když víte, jak získat token pro volání webových api, naučte se, jak přesunout do produkčního prostředí.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Další kroky

### <a name="same-site"></a>Stejný web

Ujistěte se, že rozumíte možným problémům s novými verzemi prohlížeče Chrome.

> [!div class="nextstepaction"]
> [Jak zacházet se změnami souborů cookie SameSite v prohlížeči Chrome](howto-handle-samesite-cookie-changes-chrome-browser.md)

### <a name="scenario-for-calling-web-apis"></a>Scénář pro volání webových api

Poté, co se vaše webová aplikace přihlásí k uživatelům, může volat webová api jménem přihlášených uživatelů. Volání webových api z webové aplikace je objektem následujícího scénáře:

> [!div class="nextstepaction"]
> [Webová aplikace, která volá webová rozhraní API](scenario-web-app-call-api-overview.md)

## <a name="deep-dive-aspnet-core-web-app-tutorial"></a>Hloubkový ponor: ASP.NET základní kurz webové aplikace

Přečtěte si další způsoby, jak se přihlásit k uživatelům pomocí tohoto kurzu ASP.NET Core: 

> [!div class="nextstepaction"]
> [Povolení webových aplikací k přihlašování uživatelů a volání rozhraní API pomocí platformy microsoftových identit pro vývojáře](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 

Tento progresivní kurz má kód připravený k produkčnímu prostředí pro webovou aplikaci, včetně toho, jak přidat přihlášení k účtům v:

- Vaše organizace
- Více organizací
- Pracovní nebo školní účty nebo osobní účty Microsoft
- [Azure AD B2C](https://aka.ms/aadb2c)
- Národní mraky

## <a name="sample-code-java-web-app"></a>Ukázkový kód: Java webová aplikace

Další informace o webové aplikaci Java najdete v této ukázce na GitHubu: 

> [!div class="nextstepaction"]
> [Webová aplikace Java, která přihlašuje uživatele pomocí platformy Microsoft identit a volá Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)
