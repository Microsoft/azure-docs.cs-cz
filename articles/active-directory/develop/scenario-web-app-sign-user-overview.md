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
ms.openlocfilehash: 1f9b6d4ce4048b31f17b50184f90aed4d2a8ba81
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086559"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Scénář: Webová aplikace, která přihlašuje uživatele

Seznamte se s tím, co potřebujete k vytvoření webové aplikace, která přihlašuje uživatele s platformou Microsoft identity.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Začínáme

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Pokud chcete vytvořit první přenosné webové aplikace (ASP.NET Core), které se přihlásí uživatelům, postupujte podle tohoto rychlého startu:

> [!div class="nextstepaction"]
> [Rychlé zprovoznění: ASP.NET Core webové aplikace, které přihlásí uživatele](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Chcete pochopit, jak přidat přihlášení do starší webové aplikace ASP.NET, vyzkoušejte následující kurz:

> [!div class="nextstepaction"]
> [Rychlé zprovoznění: ASP.NET webová aplikace, která přihlásí uživatele](quickstart-v2-aspnet-webapp.md)

# <a name="javatabjava"></a>[Java](#tab/java)

Pokud jste vývojářem Java, vyzkoušejte si následující rychlý Start:

> [!div class="nextstepaction"]
> [Rychlé zprovoznění: Přidání přihlašování do webové aplikace Java pomocí Microsoftu](quickstart-v2-java-webapp.md)

# <a name="pythontabpython"></a>[Python](#tab/python)

Pokud vyvíjíte pomocí Pythonu, vyzkoušejte:

> [!div class="nextstepaction"]
> [Rychlé zprovoznění: Přidání přihlašování do webové aplikace v Pythonu pomocí Microsoftu](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>Přehled

Do své webové aplikace můžete přidat ověřování, aby se mohli přihlašovat uživatelům. Když přidáte ověřování, umožníte vaší webové aplikaci přístup k informacím o omezeném profilu, a například k přizpůsobení prostředí, které nabízíte svým uživatelům. Webové aplikace ověřují uživatele ve webovém prohlížeči. V tomto scénáři webová aplikace nasměruje prohlížeč uživatele, aby je přihlásil do služby Azure AD. Azure AD vrátí odpověď pro přihlášení prostřednictvím prohlížeče uživatele, který obsahuje deklarace identity uživatele v tokenu zabezpečení. Přihlášení uživatelé využívají samotný protokol [Open ID Connect](./v2-protocols-oidc.md) , který je zjednodušený pomocí [knihoven](scenario-web-app-sign-user-app-configuration.md#libraries-used-to-protect-web-apps)middlewaru.

![Uživatelé přihlašování k webové aplikaci](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

V druhé fázi můžete také aplikaci povolit, aby vyvolala webová rozhraní API jménem přihlášeného uživatele. Tato další fáze je jiný scénář, který najdete ve [webové aplikaci volání webových rozhraní API](scenario-web-app-call-api-overview.md) .

> [!NOTE]
> Přidání přihlašování do webové aplikace se týká ochrany webové aplikace a ověření tokenu uživatele, který se nachází v knihovnách **middlewaru** . V případě rozhraní .NET tento scénář ještě nevyžaduje knihovny Microsoft Authentication Library (MSAL), které se týkají získání tokenu pro volání chráněných rozhraní API. Knihovny pro ověřování budou zavedeny do následného scénáře, pokud webová aplikace potřebuje volat webová rozhraní API.

## <a name="specifics"></a>Specifika

- Při registraci aplikace budete muset zadat jednu nebo několik (Pokud aplikaci nasadíte do několika umístění) identifikátorů URI odpovědi. V některých případech (ASP.NET/ASP.NET Core) budete muset token ID povolit. Nakonec budete chtít nastavit identifikátor URI pro odhlášení, aby se vaše aplikace znovu přihlásila k uživatelům.
- V kódu vaší aplikace budete muset zadat autoritu, ke které se webová aplikace deleguje přihlašuje. Možná budete chtít přizpůsobit ověření tokenu (zejména ve scénářích ISV).
- Webové aplikace podporují všechny typy účtů. Další informace najdete v tématu [podporované typy účtů](v2-supported-account-types.md).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Registrace aplikace](scenario-web-app-sign-user-app-registration.md)
