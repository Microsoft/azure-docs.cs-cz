---
title: Přesunutí webové aplikace, která přihlásí uživatele do produkčního prostředí | Azure
titleSuffix: Microsoft identity platform
description: Naučte se, jak vytvořit webovou aplikaci, která přihlašuje uživatele (přesunout do produkčního prostředí).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: f670af1fca4b4638988e53075f092ca1bbac55b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578256"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>Webová aplikace, která přihlašuje uživatele: Přesun do produkčního prostředí

Teď, když víte, jak získat token pro volání webových rozhraní API, je zde několik věcí, které je potřeba vzít v úvahu při přesunu aplikace do produkčního prostředí.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="troubleshooting"></a>Řešení potíží
Když se uživatelé poprvé přihlásí k webové aplikaci, budou muset souhlasit. V některých organizacích se ale uživatelům může zobrazit zpráva podobná následující: *AppName potřebuje oprávnění pro přístup k prostředkům ve vaší organizaci, které může udělit jenom správce. Než ho budete moct používat, požádejte správce, aby aplikaci udělil oprávnění.*
Důvodem je to, že správce klienta **zakázal** možnost souhlasu uživatelů. V takovém případě se obraťte na správce klientů, aby učinil souhlas správce pro rozsahy vyžadované aplikací.

## <a name="same-site"></a>Stejná lokalita

Ujistěte se, že rozumíte možným problémům s novými verzemi prohlížeče Chrome: [jak zpracovávat změny souborů cookie SameSite v prohlížeči Chrome](howto-handle-samesite-cookie-changes-chrome-browser.md).

Balíček NuGet Microsoft. identity. Web zpracovává nejběžnější SameSite problémy.

## <a name="deep-dive-aspnet-core-web-app-tutorial"></a>Hluboká podrobně: kurz ASP.NET Core webové aplikace

Přečtěte si o dalších způsobech, jak se přihlašovat uživatelům pomocí tohoto ASP.NET Core kurzu: 

[Umožněte vašim webovým aplikacím přihlašovat uživatele a volat rozhraní API s platformou Microsoft identity pro vývojáře.](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial)

Tento postupný kurz obsahuje kód připravený pro produkční prostředí pro webovou aplikaci, včetně postupu přidání přihlášení pomocí účtů v nástroji:

- Vaše organizace
- Více organizací
- Pracovní nebo školní účty nebo osobní účty Microsoft
- [Azure AD B2C](../../active-directory-b2c/overview.md)
- Národní cloudy

## <a name="tutorial-nodejs-web-app"></a>Kurz: Node.js webové aplikace

Další informace o Node.js webu v tomto kurzu:

[Kurz: přihlášení uživatelů v Node.js Web App & Express](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-nodejs-webapp-msal)

## <a name="sample-code-java-web-app"></a>Vzorový kód: webová aplikace Java

Přečtěte si další informace o webové aplikaci Java z této ukázky na GitHubu: 

[Webová aplikace Java, která přihlašuje uživatele pomocí platformy a volání Microsoft identity Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)

## <a name="next-steps"></a>Další kroky

Jakmile se webová aplikace přihlásí uživatelům, může volat webová rozhraní API jménem přihlášených uživatelů. Volání webových rozhraní API z webové aplikace je objektem následujícího scénáře: [Webová aplikace, která volá webová rozhraní API](scenario-web-app-call-api-overview.md).