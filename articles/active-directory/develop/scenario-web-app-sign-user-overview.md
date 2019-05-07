---
title: Webové aplikace, který se přihlásí uživatelé (přehled) – platforma identit Microsoft
description: Zjistěte, jak sestavit webovou aplikaci, který se přihlásí uživatelé (přehled)
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b46df5e4ec8352b47f744a507fad702c37aa5fba
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075097"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Scénář: Webové aplikace, který se přihlásí uživatelé

Přečtěte si všechno, co potřebujete k vytvoření webové aplikace, které uživatelé přihlásí s platformou identity Microsoft.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Začínáme

Pokud chcete vytvořit váš první přenosné () webové aplikace ASP.NET Core, které uživatelé, postupujte podle tohoto rychlého startu:

> [!div class="nextstepaction"]
> [Rychlé zprovoznění: ASP.NET Core, které uživatelé přihlásí webová aplikace](quickstart-v2-aspnet-core-webapp.md)

Pokud chcete Zůstaňte s přední ASP.NET, vyzkoušejte si následující kurz:

> [!div class="nextstepaction"]
> [Rychlé zprovoznění: ASP.NET Core, které uživatelé přihlásí webová aplikace](quickstart-v2-aspnet-webapp.md)

## <a name="overview"></a>Přehled

Přidání ověřování do vaší webové aplikace, takže se můžete přihlásit uživatele. Přidání ověřování umožňuje webové aplikaci přistupovat k omezené profilovým informacím a například přizpůsobení možností, které nabízíte svým uživatelům. Webové aplikace ověřit uživatele ve webovém prohlížeči. V tomto scénáři webová aplikace přesměruje prohlížeč uživatele pro jejich přihlášení k Azure AD. Azure AD vrátí odpověď přihlášení prostřednictvím webového prohlížeče, který obsahuje deklarace identity o uživateli v tokenu zabezpečení. Přihlášení uživatelé využívat [Open ID Connect](./v2-protocols-oidc.md) samotný zjednodušuje použití middlewaru standardní protokol [knihovny](scenario-web-app-sign-user-app-configuration.md#libraries-used-to-protect-web-apps).

![Webové aplikace přihlásí uživatele](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

Jako druhou fázi můžete povolit také aplikace pro volání webových rozhraní API jménem přihlášeného uživatele. Tato další fázi je jiný scénář, který najdete v [webová aplikace volá webové rozhraní API](scenario-web-app-call-api-overview.md)

> [!NOTE]
> Přidání přihlašování do webové aplikace je o ochraně webové aplikace, a ověří token, který uživatel, který je co **middleware** knihovny proveďte. Tento scénář nevyžaduje, ale chráněný Microsoft Authentication knihovny (MSAL), které jsou k získání tokenu pro volání rozhraní API. Knihovny ověřování budou pouze zavedeny ve scénáři reakce webové aplikace je potřeba volat webové rozhraní API.

## <a name="specifics"></a>Specifika

- Při registraci aplikace budete muset zadat jednu nebo několik (Pokud nasadíte aplikaci do několika umístění) identifikátory URI odpovědi. V některých případech (ASP.NET/ASP.NET Core) budete muset povolit IDToken. Nakonec budete chtít nastavit odhlašování URI tak, aby vaše aplikace reaguje na uživatele podepisování navýšením kapacity.
- V kódu pro vaši aplikaci musíte poskytnout oprávnění webového přihlášení aplikace delegátů. Můžete chtít přizpůsobit ověřování tokenů (zejména ve scénářích nezávislý výrobce softwaru).
- Webové aplikace podporují všechny typy účtů. Další informace najdete v tématu [podporovaných typů účtu](v2-supported-account-types.md).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Registrace aplikace](scenario-web-app-sign-user-app-registration.md)
