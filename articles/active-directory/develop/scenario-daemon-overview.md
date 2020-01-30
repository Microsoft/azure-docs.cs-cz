---
title: Sestavení aplikace démona, která volá webová rozhraní API – Microsoft Identity Platform | Azure
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
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: ffbad5981f29ade9f27a434a9273969af4ed82de
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773408"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>Scénář: aplikace démona, která volá webová rozhraní API

Naučte se všechno, co potřebujete k vytvoření aplikace démona, která volá webová rozhraní API.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="overview"></a>Přehled

Vaše aplikace může získat token pro volání webového rozhraní API jménem sebe sama (ne jménem uživatele). Tento scénář je vhodný pro aplikace démona. Používá standardní udělení [přihlašovacích údajů klienta](v2-oauth2-client-creds-grant-flow.md) OAuth 2,0.

![Aplikace démonů](./media/scenario-daemon-app/daemon-app.svg)

Tady je několik příkladů případů použití pro aplikace démona:

- Webové aplikace, které se používají ke zřizování nebo správě uživatelů nebo zpracování procesů Batch v adresáři
- Aplikace klasické pracovní plochy (například služby systému Windows v procesech Windows nebo démon na platformě Linux), které provádějí dávkové úlohy nebo služby operačního systému běžící na pozadí
- Webová rozhraní API, která potřebují manipulovat s adresáři, ne konkrétními uživateli

Existují další běžné případy, kdy aplikace bez démona používají přihlašovací údaje klienta: i když jednají jménem uživatelů, potřebují pro technické důvody přístup k webovému rozhraní API nebo prostředku v rámci vlastní identity. Příkladem je přístup k tajným klíčům v Azure Key Vault nebo databázi SQL Azure pro mezipaměť.

Aplikace, které získají token pro vlastní identity:

- jsou důvěrné klientské aplikace. Tyto aplikace, vzhledem k tomu, že získají přístup k prostředkům nezávisle na uživatelích, musí prokázat jejich identitu. Jsou také místo citlivých aplikací. Musí je schválit správci tenanta Azure Active Directory (Azure AD).
- Zaregistrovali jste tajný klíč (heslo aplikace nebo certifikát) ve službě Azure AD. Tento tajný klíč se předává během volání služby Azure AD za účelem získání tokenu.

## <a name="specifics"></a>Specifika

> [!IMPORTANT]
>
> - Uživatelé nemůžou pracovat s aplikací démona. Aplikace démona vyžaduje svoji vlastní identitu. Tento typ aplikace požaduje přístupový token pomocí jeho identity aplikace a předvádí jeho ID aplikace, pověření (heslo nebo certifikátu) a identifikátor URI ID aplikace do služby Azure AD. Po úspěšném ověření dostane démon token přístupu (a obnovovací token) z koncového bodu Microsoft Identity Platform. Pomocí tohoto tokenu se pak zavolá webové rozhraní API (a aktualizuje se podle potřeby).
> - Vzhledem k tomu, že uživatelé nemůžou pracovat s aplikacemi démona, není možné používat přírůstkový souhlas. Všechna požadovaná oprávnění rozhraní API je potřeba nakonfigurovat při registraci aplikace. Kód aplikace pouze žádá o staticky definovaná oprávnění. To také znamená, že aplikace démona nebude podporovat přírůstkový souhlas.

Pro vývojáře mají koncová prostředí pro tento scénář následující aspekty:

- Aplikace démona můžou pracovat jenom v klientech Azure AD. Nesmyslem je vytvořit aplikaci démona, která se pokusí manipulovat s osobními účty Microsoft. Pokud jste vývojář pro obchodní aplikace (LOB), vytvoříte v tenantovi aplikaci démona. Pokud jste nezávislý výrobce softwaru, možná budete chtít vytvořit víceklientské aplikace démona. Každý správce tenanta bude muset poskytnout souhlas.
- Během [Registrace aplikace](./scenario-daemon-app-registration.md)není nutné identifikátor URI odpovědi. Ke sdílení tajných kódů a certifikátů nebo podepsanou kontrolní výrazy můžete používat Azure AD. Musíte také požádat o oprávnění aplikace a udělit souhlas správce k používání oprávnění aplikace.
- [Konfigurace aplikace](./scenario-daemon-app-configuration.md) musí při registraci aplikace zadat přihlašovací údaje klienta jako sdílené se službou Azure AD.
- [Rozsah](scenario-daemon-acquire-token.md#scopes-to-request) použitý k získání tokenu s tokem pověření klienta musí být statickým oborem.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Aplikace démona – registrace aplikace](./scenario-daemon-app-registration.md)
