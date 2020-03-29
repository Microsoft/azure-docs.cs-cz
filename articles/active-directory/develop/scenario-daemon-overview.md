---
title: Vytvoření aplikace pro daemon, která volá webová rozhraní API – platforma identit Microsoftu | Azure
description: Přečtěte si, jak vytvořit aplikaci pro daemon, která volá webová API
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/31/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 5718a23e5669de6ba16354a718d72b68d14bbf49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78894540"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>Scénář: Daemon aplikace, která volá webová api

Naučte se vše, co potřebujete k vytvoření daemon aplikace, která volá webová api.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="overview"></a>Přehled

Vaše aplikace může získat token pro volání webového rozhraní API jménem sebe sama (nikoli jménem uživatele). Tento scénář je užitečné pro aplikace daemon. Používá standardní oauth 2.0 [pověření klienta](v2-oauth2-client-creds-grant-flow.md) grant.

![Aplikace démonů](./media/scenario-daemon-app/daemon-app.svg)

Zde je několik příkladů případů použití aplikací pro daemon:

- Webové aplikace, které se používají k zřizování nebo správě uživatelů nebo dávkových procesů v adresáři
- Desktopové aplikace (například služby Windows v systému Windows nebo procesy daemonu v Systému Linux), které provádějí dávkové úlohy, nebo služba operačního systému spuštěná na pozadí
- Webová api, která potřebují manipulovat s adresáři, nikoli s konkrétními uživateli

Existuje další běžný případ, kdy aplikace bez daemonu používají přihlašovací údaje klienta: i když jednají jménem uživatelů, potřebují přístup k webovému rozhraní API nebo prostředku pod vlastní identitou z technických důvodů. Příkladem je přístup k tajným klíčům v azure key vault nebo databáze Azure SQL pro mezipaměť.

Aplikace, které získají token pro své vlastní identity:

- Jsou důvěrné klientské aplikace. Tyto aplikace, vzhledem k tomu, že mají přístup k prostředkům nezávisle na uživatelích, musí prokázat svou identitu. Jsou to také poměrně citlivé aplikace. Musí být schváleny správci tenanta Azure Active Directory (Azure AD).
- Zaregistrovali tajný klíč (heslo aplikace nebo certifikát) s Azure AD. Tento tajný klíč je předán během volání do služby Azure AD získat token.

## <a name="specifics"></a>Specifika

> [!IMPORTANT]
>
> - Uživatelé nemohou pracovat s aplikací daemon. Aplikace daemon vyžaduje svou vlastní identitu. Tento typ aplikace požaduje přístupový token pomocí identity aplikace a předložením id aplikace, přihlašovacích údajů (heslo nebo certifikát) a identifikátorurisu ID aplikace do služby Azure AD. Po úspěšném ověření daemon obdrží přístupový token (a obnovovací token) z koncového bodu platformy identity Microsoftu. Tento token se pak používá k volání webového rozhraní API (a aktualizuje se podle potřeby).
> - Vzhledem k tomu, že uživatelé nemohou pracovat s aplikacemi daemonu, není přírůstkový souhlas možný. Všechna požadovaná oprávnění rozhraní API je třeba nakonfigurovat při registraci aplikace. Kód aplikace pouze požaduje staticky definovaná oprávnění. To také znamená, že aplikace daemon nebude podporovat přírůstkový souhlas.

Pro vývojáře má komplexní prostředí pro tento scénář následující aspekty:

- Daemon aplikace můžete pracovat pouze v tenanty Azure AD. Nemělo by smysl vytvářet daemonovou aplikaci, která se pokouší manipulovat s osobními účty Microsoft. Pokud jste vývojář aplikací pro firmy (LOB), vytvoříte aplikaci daemon u svého tenanta. Pokud jste isv, můžete chtít vytvořit víceklientské aplikace demon. Každý správce klienta bude muset poskytnout souhlas.
- Během [registrace aplikace](./scenario-daemon-app-registration.md)není potřeba identifikátor URI odpovědi. Musíte sdílet tajné klíče nebo certifikáty nebo podepsané kontrolní výrazy s Azure AD. Musíte také požádat o oprávnění aplikací a udělit souhlas správce k použití těchto oprávnění aplikace.
- [Konfigurace aplikace](./scenario-daemon-app-configuration.md) musí poskytnout pověření klienta jako sdílené s Azure AD během registrace aplikace.
- [Obor](scenario-daemon-acquire-token.md#scopes-to-request) použitý k získání tokenu s tokem pověření klienta musí být statický obor.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Aplikace Daemon - registrace aplikace](./scenario-daemon-app-registration.md)
