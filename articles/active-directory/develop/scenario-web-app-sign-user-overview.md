---
title: Webová aplikace, která se podepisuje uživateli (přehled) – Microsoft Identity Platform
description: Naučte se vytvářet webové aplikace, které se přihlásí uživatelům (přehled).
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
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 316ab055a077b251e88421ab26997f8556a6e31f
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482487"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Scénář: webová aplikace, která se přihlásí uživatelům

Seznamte se s tím, co potřebujete k vytvoření webové aplikace, která používá Microsoft Identity Platform k přihlašování uživatelů.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Začínáme

# <a name="aspnet-coretabaspnetcore"></a>[Jádro ASP.NET](#tab/aspnetcore)

Pokud chcete vytvořit první přenosnou webovou aplikaci (ASP.NET Core), která se přihlašuje uživatelům, postupujte podle tohoto rychlého startu:

> [!div class="nextstepaction"]
> [Rychlý Start: ASP.NET Core webovou aplikaci, která se přihlásí uživatelům](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Pokud chcete pochopit, jak přidat přihlášení do existující webové aplikace ASP.NET, vyzkoušejte následující rychlý Start:

> [!div class="nextstepaction"]
> [Rychlý Start: ASP.NET webová aplikace, která přihlásí uživatele](quickstart-v2-aspnet-webapp.md)

# <a name="javatabjava"></a>[Java](#tab/java)

Pokud jste vývojářem Java, zkuste následující rychlý Start:

> [!div class="nextstepaction"]
> [Rychlý Start: přidání přihlášení do webové aplikace Java pomocí Microsoftu](quickstart-v2-java-webapp.md)

# <a name="pythontabpython"></a>[Python](#tab/python)

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
> Přidání přihlašování do webové aplikace se týká ochrany webové aplikace a ověření tokenu uživatele, který se nachází v knihovnách **middlewaru** . V případě .NET tento scénář ještě nevyžaduje knihovnu Microsoft Authentication Library (MSAL), která má za následek získání tokenu pro volání chráněných rozhraní API. Knihovny pro ověřování budou zavedeny do následného scénáře, pokud webová aplikace potřebuje volat webová rozhraní API.

## <a name="specifics"></a>Specifika

- Při registraci aplikace budete muset zadat jednu nebo několik (Pokud aplikaci nasadíte do několika umístění) identifikátorů URI odpovědi. V některých případech (ASP.NET a ASP.NET Core) budete muset token ID povolit. Nakonec budete chtít nastavit identifikátor URI pro odhlášení, aby se vaše aplikace znovu přihlásila k uživatelům, kteří se odhlásí.
- V kódu vaší aplikace budete muset zadat autoritu, které vaše webová aplikace deleguje přihlašování. Můžete chtít přizpůsobit ověření tokenu (zejména v partnerských scénářích).
- Webové aplikace podporují všechny typy účtů. Další informace najdete v tématu [podporované typy účtů](v2-supported-account-types.md).

## <a name="next-steps"></a>Další kroky

# <a name="aspnet-coretabaspnetcore"></a>[Jádro ASP.NET](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Registrace aplikace](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Registrace aplikace](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Registrace aplikace](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Registrace aplikace](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=python)

---
