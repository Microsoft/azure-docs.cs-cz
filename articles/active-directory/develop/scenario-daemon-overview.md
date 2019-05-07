---
title: Démon procesu aplikace volání webových rozhraní API (přehled) – platforma identit Microsoft
description: Zjistěte, jak sestavit aplikaci démona, která volá webové rozhraní API
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 578b7cdb38b7df3fab5885d773354a36f76a4cfb
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075877"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>Scénář: Démon procesu aplikace, že volání webových rozhraní API

Přečtěte si všechno, co potřebujete k sestavení aplikace démona, která volá webové rozhraní API.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="overview"></a>Přehled

Aplikace můžete získat token k volání webového rozhraní API jménem samotné (nikoli jménem uživatele). Tento scénář je vhodný pro proces démon aplikace. Používá standard OAuth 2.0 [přihlašovací údaje pro klienta](v2-oauth2-client-creds-grant-flow.md) udělit.

![Aplikace démonů](./media/scenario-daemon-app/daemon-app.svg)

Tady jsou některé příklady případů použití pro aplikace démonů:

- Webové aplikace, které se používají ke zřízení nebo spravovat uživatele, nebo dávkové procesy v adresáři
- Desktopové aplikace, třeba (služeb systému windows na Windows) nebo procesy procesy démon v Linuxu, které provádějí úlohy batch nebo služby operační systém běží na pozadí
- Webové rozhraní API, která potřebují k práci s adresáře, nikoli konkrétní uživatele

Je dalším běžným případem, kde aplikace bez démonů používají přihlašovací údaje pro klienta: i v případě, že jednat jménem uživatele, musí pro přístup k webovému rozhraní API nebo prostředek pomocí identity z technických důvodů. Příkladem je přístup k tajným kódům v trezoru klíčů nebo Azure SQL database pro mezipaměť.

Aplikace, které získat token pro svoje vlastní identity:

- jsou důvěrné klientské aplikace. Tyto aplikace, vzhledem k tomu, že bude přístup k prostředkům bez ohledu na jejich uživatele, musí k prokázání své identity. Navíc se místo toho citlivých aplikací, které musí je schválit výbor správci tenanta Azure Active Directory (Azure AD).
- Jste se zaregistrovali tajného kódu (aplikace heslo nebo certifikát) s Azure AD. Tento tajný kód je předané při volání do služby Azure AD k získání tokenu.

## <a name="specifics"></a>Specifika

> [!IMPORTANT]
>
> - Zásah uživatele není možné aplikaci démona. Démon procesu aplikace vyžaduje svou vlastní identitu. Tento typ aplikace vyžádá přístupový token pomocí jeho identita aplikace a nabízí ten samý jeho ID aplikace, přihlašovací údaje (heslo nebo certifikát) a aplikace identifikátor URI ID do služby Azure AD. Po úspěšném ověření démona přístupového tokenu (a obdrží token obnovení) z Microsoft identity platform koncového bodu, která se pak použije k volání webového rozhraní API (a podle potřeby aktualizují).
> - Protože interakci s uživatelem není možné, nebude možné přírůstkové souhlas. Všechna požadovaná oprávnění rozhraní API je potřeba nakonfigurovat při registraci aplikace a kód aplikace pouze požadavky na staticky definovaných oprávnění. To také znamená, že démon procesu aplikace nebude podporují přírůstkové souhlas.

Pro vývojáře v prostředí začátku do konce pro tento scénář má následující aspekty:

- Démon procesu aplikace funguje jenom v tenantů Azure AD. To by nedávalo smysl jak vytvořit webovou aplikaci démona, která se pokusí manipulovat s osobním účtům Microsoft. Pokud jste vývojář – obchodní aplikace (LOB), vytvoříte aplikaci démon ve vašem tenantovi. Pokud jste nezávislý dodavatel softwaru, můžete chtít vytvořit aplikace s více tenanty démon. Ji budou muset být odsouhlasený. každý správce tenanta.
- Během [registrace aplikace](./scenario-daemon-app-registration.md), **identifikátor URI odpovědi** není potřeba. Je nutné sdílet tajných kódů a certifikátů s Azure AD a budete muset požádat o oprávnění aplikace a udělit souhlas správce používat tato oprávnění aplikace.
- [Konfigurace aplikace](./scenario-daemon-app-configuration.md) je potřeba zadat přihlašovací údaje klienta jako sdílené s Azure AD během registrace aplikace.
- [Oboru](scenario-daemon-acquire-token.md#scopes-to-request) použít k získání tokenu s přihlašovacími údaji klientů tok musí být statického oboru.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Démon procesu aplikace – registrace aplikace](./scenario-daemon-app-registration.md)
