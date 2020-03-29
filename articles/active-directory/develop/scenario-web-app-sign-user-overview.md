---
title: Přihlášení uživatelů z webové aplikace – platforma identit Microsoftu | Azure
description: Přečtěte si, jak vytvořit webovou aplikaci, která se přihlásí k uživatelům (přehled)
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
ms.openlocfilehash: 403f589702fd7142f0515a3b6f19ee1b9bbb6420
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701548"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Scénář: Webová aplikace, která se připisuje k uživatelům

Přečtěte si vše, co potřebujete k vytvoření webové aplikace, která k přihlašování uživatelů používá platformu identit Microsoftu.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Začínáme

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Pokud chcete vytvořit první přenosnou (ASP.NET základní) webovou aplikaci, která se přihlásí k uživatelům, postupujte podle tohoto rychlého startu:

> [!div class="nextstepaction"]
> [Úvodní příručka: ASP.NET základní webová aplikace, která připisuje uživatele](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Pokud chcete pochopit, jak přidat přihlášení k existující ASP.NET webové aplikace, vyzkoušejte následující rychlý start:

> [!div class="nextstepaction"]
> [Úvodní příručka: ASP.NET webová aplikace, která se připisuje uživatelům](quickstart-v2-aspnet-webapp.md)

# <a name="java"></a>[Java](#tab/java)

Pokud jste vývojář v Jazyce Java, vyzkoušejte následující rychlý start:

> [!div class="nextstepaction"]
> [Úvodní příručka: Přidání přihlášení s Microsoftem do webové aplikace Java](quickstart-v2-java-webapp.md)

# <a name="python"></a>[Python](#tab/python)

Pokud vyvíjíte s Pythonem, zkuste následující rychlý start:

> [!div class="nextstepaction"]
> [Úvodní příručka: Přidání přihlášení s Microsoftem do webové aplikace Pythonu](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>Přehled

Do webové aplikace přidáte ověřování, aby se mohla přihlašovat k uživatelům. Přidání ověřování umožňuje webové aplikaci přístup k omezeným informacím o profilu za účelem přizpůsobení prostředí pro uživatele. 

Webové aplikace ověřují uživatele ve webovém prohlížeči. V tomto scénáři webová aplikace přesměruje prohlížeč uživatele k jejich přihlášení do služby Azure Active Directory (Azure AD). Azure AD vrátí odpověď přihlášení prostřednictvím prohlížeče uživatele, který obsahuje deklarace identity o uživateli v tokenu zabezpečení. Přihlašování uživatelů využívá standardní protokol [Open ID Connect,](./v2-protocols-oidc.md) který je zjednodušen použitím [middlewarových knihoven](scenario-web-app-sign-user-app-configuration.md#libraries-for-protecting-web-apps).

![Přihlášení webové aplikace k uživatelům](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

Jako druhou fázi můžete povolit aplikaci volat webová rozhraní API jménem přihlášeného uživatele. Tato další fáze je jiný scénář, který najdete ve [webové aplikaci, která volá webová api](scenario-web-app-call-api-overview.md).

> [!NOTE]
> Přidání přihlášení do webové aplikace je o ochraně webové aplikace a ověření uživatelského tokenu, což je to, co **middleware** knihovny dělají. V případě rozhraní .NET tento scénář ještě nevyžaduje Knihovnu ověřování společnosti Microsoft (MSAL), která je o získání tokenu pro volání chráněných rozhraní API. Ověřovací knihovny budou zavedeny v následném scénáři, kdy webová aplikace potřebuje volat webová api.

## <a name="specifics"></a>Specifika

- Během registrace aplikace budete muset zadat jednu nebo několik (pokud aplikaci nasadíte do několika umístění) odpovědi URI. V některých případech (ASP.NET a ASP.NET Core) budete muset povolit token ID. Nakonec budete chtít nastavit přihlašovací identifikátor URI tak, aby vaše aplikace reagovala na odhlášení uživatelů.
- V kódu pro vaši aplikaci budete muset poskytnout oprávnění, ke kterému vaše webová aplikace deleguje přihlášení. Můžete chtít přizpůsobit ověření tokenu (zejména v partnerských scénářích).
- Webové aplikace podporují všechny typy účtů. Další informace naleznete v tématu [Podporované typy účtů](v2-supported-account-types.md).

## <a name="next-steps"></a>Další kroky

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Registrace aplikace](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Registrace aplikace](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Registrace aplikace](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Registrace aplikace](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=python)

---
