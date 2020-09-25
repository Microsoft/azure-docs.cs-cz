---
title: Přihlaste se k uživatelům z webové aplikace – Microsoft Identity Platform | Azure
description: Naučte se vytvářet webové aplikace, které se přihlásí uživatelům (přehled).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 7cadb4784cbf90d283f64e12edc155d4430fab06
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91257060"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Scénář: webová aplikace, která se přihlásí uživatelům

Seznamte se s tím, co potřebujete k vytvoření webové aplikace, která používá Microsoft Identity Platform k přihlašování uživatelů.

## <a name="getting-started"></a>Začínáme

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Pokud chcete vytvořit první přenosnou webovou aplikaci (ASP.NET Core), která se přihlašuje uživatelům, postupujte podle tohoto rychlého startu:

> [!div class="nextstepaction"]
> [Rychlý Start: ASP.NET Core webovou aplikaci, která se přihlásí uživatelům](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Pokud chcete pochopit, jak přidat přihlášení do existující webové aplikace ASP.NET, vyzkoušejte následující rychlý Start:

> [!div class="nextstepaction"]
> [Rychlý Start: ASP.NET webová aplikace, která přihlásí uživatele](quickstart-v2-aspnet-webapp.md)

# <a name="java"></a>[Java](#tab/java)

Pokud jste vývojářem Java, zkuste následující rychlý Start:

> [!div class="nextstepaction"]
> [Rychlý Start: přidání přihlášení do webové aplikace Java pomocí Microsoftu](quickstart-v2-java-webapp.md)

# <a name="python"></a>[Python](#tab/python)

Pokud vyvíjíte pomocí Pythonu, zkuste následující rychlý Start:

> [!div class="nextstepaction"]
> [Rychlý Start: Přidání přihlašování do webové aplikace v Pythonu pomocí Microsoftu](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>Přehled

Do své webové aplikace můžete přidat ověřování, aby se mohli přihlašovat uživatelům. Přidání ověřování umožní vaší webové aplikaci získat přístup k informacím o omezených profilech, aby bylo možné přizpůsobit prostředí pro uživatele.

Webové aplikace ověřují uživatele ve webovém prohlížeči. V tomto scénáři webová aplikace přesměruje prohlížeč uživatele, aby je přihlásil k Azure Active Directory (Azure AD). Azure AD vrátí odpověď pro přihlášení prostřednictvím prohlížeče uživatele, který obsahuje deklarace identity uživatele v tokenu zabezpečení. Přihlášení uživatelé využívají standardní protokol [Open ID Connect](./v2-protocols-oidc.md) , který je zjednodušený pomocí [knihoven](scenario-web-app-sign-user-app-configuration.md#libraries-for-protecting-web-apps)middlewaru.

![Přihlašování uživatelů k webové aplikaci](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

V druhé fázi můžete aplikaci povolit, aby vyvolala webová rozhraní API jménem přihlášeného uživatele. Tato další fáze je jiný scénář, který najdete ve [webové aplikaci, která volá webová rozhraní API](scenario-web-app-call-api-overview.md).

> [!NOTE]
> Přidání přihlašování do webové aplikace se týká ochrany webové aplikace a ověření tokenu uživatele, který se nachází v knihovnách  **middlewaru** . V případě .NET tento scénář ještě nevyžaduje knihovnu Microsoft Authentication Library (MSAL), která má za následek získání tokenu pro volání chráněných rozhraní API. Knihovny pro ověřování budou zavedeny do následného scénáře, pokud webová aplikace potřebuje volat webová rozhraní API.

## <a name="specifics"></a>Specifika

- Při registraci aplikace budete muset zadat jednu nebo několik (Pokud aplikaci nasadíte do několika umístění) identifikátorů URI odpovědi. V některých případech (ASP.NET a ASP.NET Core) budete muset token ID povolit. Nakonec budete chtít nastavit identifikátor URI pro odhlášení, aby se vaše aplikace znovu přihlásila k uživatelům, kteří se odhlásí.
- V kódu vaší aplikace budete muset zadat autoritu, které vaše webová aplikace deleguje přihlašování. Můžete chtít přizpůsobit ověření tokenu (zejména v partnerských scénářích).
- Webové aplikace podporují všechny typy účtů. Další informace najdete v tématu [podporované typy účtů](v2-supported-account-types.md).

## <a name="recommended-reading"></a>Doporučené čtení

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Další kroky

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Registrace aplikací](./scenario-web-app-sign-user-app-registration.md?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Registrace aplikací](./scenario-web-app-sign-user-app-registration.md?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Registrace aplikací](./scenario-web-app-sign-user-app-registration.md?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Registrace aplikací](./scenario-web-app-sign-user-app-registration.md?tabs=python)

---