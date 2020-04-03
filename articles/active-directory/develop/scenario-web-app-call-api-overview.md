---
title: Vytvoření webové aplikace, která volá webová rozhraní API – platforma identit Microsoftu | Azure
description: Přečtěte si, jak vytvořit webovou aplikaci, která volá webová API (přehled)
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
ms.openlocfilehash: 14f513306b3f0bc0c06a4143e5174c3ecddaef62
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617169"
---
# <a name="scenario-a-web-app-that-calls-web-apis"></a>Scénář: Webová aplikace, která volá webová api

Zjistěte, jak vytvořit webovou aplikaci, která přiřazuje uživatele k platformě identit Microsoftu a pak volá webová rozhraní API jménem přihlášeného uživatele.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

Tento scénář předpokládá, že jste již prošli následující scénář:

> [!div class="nextstepaction"]
> [Webová aplikace, která přihlašuje uživatele](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>Přehled

Do webové aplikace přidáte ověřování, aby mohla přihlašovat uživatele a volat webové rozhraní API jménem přihlášeného uživatele.

![Webová aplikace, která volá webová rozhraní API](./media/scenario-webapp/web-app.svg)

Webové aplikace, které volají webová api, jsou důvěrné klientské aplikace.
To je důvod, proč se zaregistrovat tajný klíč (heslo aplikace nebo certifikát) s Azure Active Directory (Azure AD). Tento tajný klíč je předán během volání do služby Azure AD získat token.

## <a name="specifics"></a>Specifika

> [!NOTE]
> Přidání přihlášení do webové aplikace je o ochraně samotné webové aplikace. Této ochrany je dosaženo pomocí *middleware* knihoven, nikoli Microsoft Authentication Library (MSAL). Předchozí [scénář, webová aplikace, která se přizpůsobuje uživatelům](scenario-web-app-sign-user-overview.md), se vztahuje na tento předmět.
>
> Tento scénář popisuje, jak volat webová api z webové aplikace. Musíte získat přístupové tokeny pro tato webová api. Pomocí knihoven MSAL získat tyto tokeny.

Vývoj pro tento scénář zahrnuje tyto konkrétní úkoly:

- Během [registrace aplikace](scenario-web-app-call-api-app-registration.md), musíte poskytnout odpověď URI, tajný klíč nebo certifikát, který má být sdílen s Azure AD. Pokud aplikaci nasadíte do několika umístění, poskytnete tyto informace pro každé umístění.
- [Konfigurace aplikace](scenario-web-app-call-api-app-configuration.md) musí poskytnout pověření klienta, které byly sdíleny s Azure AD během registrace aplikace.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Webová aplikace, která volá webová api: Registrace aplikace](scenario-web-app-call-api-app-registration.md)
