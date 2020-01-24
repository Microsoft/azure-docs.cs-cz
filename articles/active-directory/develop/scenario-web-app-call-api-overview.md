---
title: Sestavení webové aplikace, která volá webová rozhraní API – Microsoft Identity Platform | Azure
description: Informace o tom, jak vytvořit webovou aplikaci, která volá webová rozhraní API (přehled)
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
ms.openlocfilehash: 9e123195205bb0eb88f0edd4e2dff2e0da9d84ce
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701650"
---
# <a name="scenario-web-app-that-calls-web-apis"></a>Scénář: webová aplikace, která volá webová rozhraní API.

Naučte se, jak vytvořit webovou aplikaci, která se přihlašuje uživatelům na platformě Microsoft identity, a pak zavolá webová rozhraní API jménem přihlášeného uživatele.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

Tento scénář předpokládá, že jste prošli tímto scénářem:

> [!div class="nextstepaction"]
> [Webová aplikace, která přihlásí uživatele](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>Přehled

Přidáte do své webové aplikace ověřování, které se proto může přihlašovat uživatelům a volat webové rozhraní API jménem přihlášeného uživatele.

![Webová aplikace, která volá webová rozhraní API](./media/scenario-webapp/web-app.svg)

Web Apps, která volá webová rozhraní API:

- jsou důvěrné klientské aplikace.
- To je důvod, proč zaregistrovali tajný klíč (heslo aplikace nebo certifikát) ve službě Azure AD. Tento tajný klíč se předává během volání služby Azure AD, aby získal token.

## <a name="specifics"></a>Specifika

> [!NOTE]
> Přidání přihlášení do webové aplikace nepoužívá knihovny MSAL, protože se jedná o ochranu webové aplikace. Ochrana knihoven se dosahuje knihovnami s názvem middleware. Toto bylo objekt pro uživatele, kteří se [přihlásili z předchozích scénářů, do webové aplikace](scenario-web-app-sign-user-overview.md) .
>
> Při volání webových rozhraní API z webové aplikace budete muset získat přístupové tokeny pro tato webová rozhraní API. K získání těchto tokenů můžete použít knihovny MSAL.

Koncoví prostředí vývojářů v tomto scénáři má proto konkrétní aspekty:

- Při [registraci aplikace](scenario-web-app-call-api-app-registration.md)budete muset zadat jednu nebo několik (Pokud aplikaci nasadíte do několika umístění) odpovědi na identifikátory URI, tajné kódy nebo certifikáty je potřeba sdílet s Azure AD.
- [Konfigurace aplikace](scenario-web-app-call-api-app-configuration.md) musí při registraci aplikace zadat přihlašovací údaje klienta jako sdílené se službou Azure AD.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Registrace aplikace](scenario-web-app-call-api-app-registration.md)
