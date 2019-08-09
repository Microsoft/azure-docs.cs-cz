---
title: Aplikace démona volající webová rozhraní API (přehled) – Microsoft Identity Platform
description: Naučte se vytvářet aplikace démona, která volá webová rozhraní API.
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b86841cc6889eb8e716df3f6d1ac9bc7b158992
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852719"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>Scénář: Aplikace démona, která volá webová rozhraní API

Naučte se všechno, co potřebujete k vytvoření aplikace démona, která volá webová rozhraní API.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="overview"></a>Přehled

Vaše aplikace může získat token pro volání webového rozhraní API jménem sebe sama (ne jménem uživatele). Tento scénář je vhodný pro aplikace démona. Používá standardní udělení [přihlašovacích údajů klienta](v2-oauth2-client-creds-grant-flow.md) OAuth 2,0.

![Aplikace démonů](./media/scenario-daemon-app/daemon-app.svg)

Tady je několik příkladů případů použití pro aplikace démona:

- Webové aplikace, které se používají ke zřizování nebo správě uživatelů nebo zpracování procesů Batch v adresáři
- Aplikace klasické pracovní plochy (například služby systému Windows ve Windows nebo procesy démony na platformě Linux), které provádějí dávkové úlohy nebo služby operačního systému spuštěné na pozadí
- Webová rozhraní API, která potřebují manipulovat s adresáři, ne konkrétními uživateli

K dispozici je jiný běžný případ, kdy aplikace bez démona používají přihlašovací údaje klienta: i když jednají jménem uživatelů, potřebují přístup k webovému rozhraní API nebo prostředku s jejich identitou z technických důvodů. Příkladem je přístup k tajným klíčům v trezoru klíčů nebo službě Azure SQL Database pro mezipaměť.

Aplikace, které získají token pro vlastní identity:

- Jsou důvěrné klientské aplikace. Tyto aplikace, vzhledem k tomu, že získají přístup k prostředkům nezávisle na uživateli, musí prokázat jejich identitu. Místo toho jsou taky citlivé aplikace, které je potřeba schválit správci tenanta Azure Active Directory (Azure AD).
- Zaregistrovali jste tajný klíč (heslo aplikace nebo certifikát) ve službě Azure AD. Tento tajný klíč se předává v průběhu volání služby Azure AD za účelem získání tokenu.

## <a name="specifics"></a>Specifika

> [!IMPORTANT]
>
> - Interakce uživatele není u aplikace démona možná. Aplikace démona vyžaduje svoji vlastní identitu. Tento typ aplikace požaduje přístupový token pomocí jeho identity aplikace a předvádí jeho ID aplikace, pověření (heslo nebo certifikátu) a identifikátor URI ID aplikace do služby Azure AD. Po úspěšném ověření získá démon token přístupu (a obnovovací token) z koncového bodu Microsoft Identity Platform, který je pak použit k volání webového rozhraní API (a je podle potřeby aktualizován).
> - Vzhledem k tomu, že interakce uživatele není možná, nebude možné přírůstkové vyjádření souhlasu. Všechna požadovaná oprávnění rozhraní API je potřeba nakonfigurovat při registraci aplikace a kód aplikace pouze žádá staticky definovaná oprávnění. To také znamená, že aplikace démona nebude podporovat přírůstkový souhlas.

Pro vývojáře mají koncová prostředí pro tento scénář následující aspekty:

- Aplikace démona můžou pracovat jenom v klientech Azure AD. Nesmyslem je vytvořit aplikaci démona, která se pokusí manipulovat s osobními účty Microsoft. Pokud jste vývojář pro obchodní aplikace (LOB), vytvoříte v tenantovi aplikaci démona. Pokud jste nezávislý výrobce softwaru, možná budete chtít vytvořit aplikaci démona s více klienty. Bude muset být odsouhlasena každým správcem tenanta.
- V průběhu [Registrace aplikace](./scenario-daemon-app-registration.md)není **identifikátor URI odpovědi** potřeba. Potřebujete sdílet tajné klíče nebo certifikáty s Azure AD a musíte požádat o oprávnění aplikací a udělit souhlas správce, aby tato oprávnění aplikace používal.
- [Konfigurace aplikace](./scenario-daemon-app-configuration.md) musí při registraci aplikace zadat přihlašovací údaje klienta jako sdílené se službou Azure AD.
- [Rozsah](scenario-daemon-acquire-token.md#scopes-to-request) použitý k získání tokenu s tokem pověření klienta musí být statickým oborem.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Aplikace démona – registrace aplikace](./scenario-daemon-app-registration.md)
