---
title: Webové aplikace, že volání webových rozhraní API (přehled) – platforma identit Microsoft
description: Zjistěte, jak sestavit webovou aplikaci, že volání webových rozhraní API (přehled)
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
ms.openlocfilehash: ce45f11a697b72ebdd0fe01166a70e7b47aa8e9f
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076297"
---
# <a name="scenario-web-app-that-calls-web-apis"></a>Scénář: Webové aplikace, že volání webových rozhraní API

Zjistěte, jak sestavit webovou aplikaci na platformě Microsoft identity přihlášení uživatelů a, která volá webové rozhraní API jménem přihlášeného uživatele.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

Tento scénář supposes, když jste prošli následující scénář:

> [!div class="nextstepaction"]
> [Webová aplikace, které uživatelé přihlásí](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>Přehled

Přidání ověřování do webové aplikace, které lze tedy přihlašování uživatelů a volá webové rozhraní API jménem přihlášeného uživatele.

![Webové aplikace, že volání webových rozhraní API](./media/scenario-webapp/web-app.svg)

Webové aplikace, která volá webové rozhraní API:

- jsou důvěrné klientské aplikace.
- To je důvod, proč jsme v Azure AD zaregistrováno tajného kódu (aplikace heslo nebo certifikát). Tento tajný kód je předané při volání do služby Azure AD k získání tokenu

## <a name="specifics"></a>Specifika

> [!NOTE]
> Přidání přihlašování do webové aplikace nepoužívá MSAL knihovny, protože se jedná o ochraně webové aplikace. Ochrana knihovny se dosahuje prostřednictvím knihoven s názvem middlewaru. Toto byl objekt v předchozím scénáři [uživatelé přihlášení do webové aplikace](scenario-web-app-sign-user-overview.md)
>
> Při volání webových rozhraní API z webové aplikace, budete potřebovat k získání přístupových tokenů pro tyto webové rozhraní API. Knihovna MSAL knihovny můžete použít k získání těchto tokenů.

Prostředí koncového vývojářů pro tento scénář má proto konkrétní aspekty jako:

- Během [registrace aplikace](scenario-web-app-call-api-app-registration.md), budete muset poskytnout jednu, nebo několik (Pokud nasadíte aplikaci do několika umístění) odpovědět identifikátory URI, tajné kódy, nebo certifikáty potřeba sdílet s Azure AD.
- [Konfigurace aplikace](scenario-web-app-call-api-app-configuration.md) je potřeba zadat přihlašovací údaje klienta jako sdílené s Azure AD během registrace aplikace

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Registrace aplikace](scenario-web-app-call-api-app-registration.md)
