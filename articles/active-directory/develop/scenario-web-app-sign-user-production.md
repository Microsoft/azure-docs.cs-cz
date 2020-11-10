---
title: Přesunutí webové aplikace, která se přihlašuje uživatelům, do produkční platformy Microsoft identity | Azure
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
ms.openlocfilehash: f438567851204a1a284955bede1525505712f4b7
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94442374"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>Webová aplikace, která přihlašuje uživatele: Přesun do produkčního prostředí

Když teď víte, jak získat token pro volání webových rozhraní API, přečtěte si, jak ho přesunout do produkčního prostředí.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="troubleshooting"></a>Odstraňování potíží

> [!NOTE]
> Když se uživatelé poprvé přihlásí k webové aplikaci, budou muset souhlasit. V některých organizacích se ale uživatelům může zobrazit zpráva podobná následující:
>
> *AppName potřebuje oprávnění pro přístup k prostředkům ve vaší organizaci, které může udělit jenom správce. Než ho budete moct používat, požádejte správce, aby aplikaci udělil oprávnění.*
>
> Důvodem je to, že správce klienta **zakázal** možnost souhlasu uživatelů. V takovém případě je potřeba kontaktovat správce klientů, aby učinili souhlas správce pro rozsahy vyžadované aplikací.

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
- [Azure AD B2C](https://aka.ms/aadb2c)
- Národní cloudy

## <a name="sample-code-java-web-app"></a>Vzorový kód: webová aplikace Java

Přečtěte si další informace o webové aplikaci Java z této ukázky na GitHubu: 

[Webová aplikace Java, která přihlašuje uživatele pomocí platformy a volání Microsoft identity Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)

## <a name="next-steps"></a>Další kroky

Jakmile se webová aplikace přihlásí uživatelům, může volat webová rozhraní API jménem přihlášených uživatelů. Volání webových rozhraní API z webové aplikace je objektem následujícího scénáře: [Webová aplikace, která volá webová rozhraní API](scenario-web-app-call-api-overview.md).