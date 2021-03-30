---
title: Přehled Azure Active Directory pro vývojáře (v 1.0)
description: Tento článek poskytuje přehled o podepisování pracovních a školních účtů Microsoft pomocí koncového bodu a platformy Azure Active Directory v 1.0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: overview
ms.workload: identity
ms.date: 10/24/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 8cb426239fe9f3dea207df06b6f2bafbdb54058a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88117664"
---
# <a name="azure-active-directory-for-developers-v10-overview"></a>Přehled Azure Active Directory pro vývojáře (v 1.0)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Azure Active Directory (Azure AD) je cloudová služba identit umožňující vývojářům sestavovat aplikace, které bezpečně přihlásí uživatele s pracovním nebo školním účtem Microsoft. Azure AD podporuje vývojáře vytvářející jak obchodní aplikace s jedním tenantem, tak i vývojáře, kteří se zajímají o vyvíjení aplikací s více tenanty. Kromě základního přihlašování umožňuje Azure AD volat nejen rozhraní API Microsoftu, jako je [Microsoft Graph](/graph/overview), ale i vlastní rozhraní API vytvořená na platformě Azure AD. Tato dokumentace vám ukáže, jak do vlastní aplikace přidat podporu Azure AD s použitím standardních oborových protokolů, jako jsou OAuth 2.0 a OpenID Connect.

> [!NOTE]
> Většina obsahu na této stránce se zaměřuje na koncový bod a platformu v 1.0, které podporují jenom pracovní nebo školní účty Microsoftu. Pokud se chcete přihlásit k uživatelským nebo osobním účtům Microsoft, přečtěte si informace na [koncovém bodu a platformě verze 2.0](../develop/v2-overview.md). Koncový bod v 2.0 nabízí jednotné vývojářské prostředí pro aplikace, které se chtějí přihlašovat ke všem identitám Microsoftu.

- [Základy ověřování](v1-authentication-scenarios.md) Úvod k ověřování pomocí Azure AD.
- [Typy aplikací](app-types.md) Přehled scénářů ověřování, které podporuje Azure AD

## <a name="get-started"></a>Začínáme

Rychlé starty a kurzy v 1.0 vás provedou vytvořením aplikace na upřednostňované platformě pomocí sady SDK knihovny ověřování v Azure AD. Pokud chcete začít, podívejte se na **kurzy** **rychlý Start v 1.0** a v 1.0 v tématu [Microsoft Identity Platform (Azure Active Directory pro vývojáře)](index.yml) .

## <a name="how-to-guides"></a>Návody

Podrobné informace a návody nejběžnějších úloh v Azure AD najdete v tématu **návody v 1.0** .

## <a name="reference-topics"></a>Referenční témata

Následující články poskytují podrobné informace o rozhraních API, zprávách protokolů a termínech používaných v Azure AD.

- [Knihovny ověřování (ADAL)](active-directory-authentication-libraries.md) Přehled knihoven a sad SDK, které poskytuje Azure AD.
- [Ukázky kódu](sample-v1-code.md) Seznam všech ukázek kódu Azure AD.
- [Glosář](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) Terminologie a definice slov, která se používají v této dokumentaci

## <a name="videos"></a>Videa

Další informace o migraci na novou platformu Microsoft identity najdete v tématu [Azure Active Directory videí pro vývojářskou platformu](videos.md) .

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]