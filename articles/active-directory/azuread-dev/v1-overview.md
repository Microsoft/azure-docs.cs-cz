---
title: Přehled Azure Active Directory pro vývojáře (v 1.0)
description: Tento článek poskytuje přehled o podepisování pracovních a školních účtů Microsoft pomocí koncového bodu a platformy Azure Active Directory v 1.0.
services: active-directory
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.subservice: azuread-dev
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/24/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: ed74e779ea87c4d6ddf58c7607c9a1e900021b48
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77164016"
---
# <a name="azure-active-directory-for-developers-v10-overview"></a>Přehled Azure Active Directory pro vývojáře (v 1.0)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Azure Active Directory (Azure AD) je cloudová služba identit umožňující vývojářům sestavovat aplikace, které bezpečně přihlásí uživatele s pracovním nebo školním účtem Microsoft. Azure AD podporuje vývojáře vytvářející jak obchodní aplikace s jedním tenantem, tak i vývojáře, kteří se zajímají o vyvíjení aplikací s více tenanty. Kromě základního přihlašování umožňuje Azure AD volat nejen rozhraní API Microsoftu, jako je [Microsoft Graph](https://docs.microsoft.com/graph/overview), ale i vlastní rozhraní API vytvořená na platformě Azure AD. Tato dokumentace vám ukáže, jak do vlastní aplikace přidat podporu Azure AD s použitím standardních oborových protokolů, jako jsou OAuth 2.0 a OpenID Connect.

> [!NOTE]
> Většina obsahu na této stránce se zaměřuje na koncový bod a platformu v 1.0, které podporují jenom pracovní nebo školní účty Microsoftu. Pokud se chcete přihlásit k uživatelským nebo osobním účtům Microsoft, přečtěte si informace na [koncovém bodu a platformě verze 2.0](../develop/v2-overview.md). Koncový bod v 2.0 nabízí jednotné vývojářské prostředí pro aplikace, které se chtějí přihlašovat ke všem identitám Microsoftu.

| | |
| --- | --- |
|[Základy ověřování](v1-authentication-scenarios.md) | Úvod k ověřování pomocí Azure AD |
|[Typy aplikací](app-types.md) | Přehled scénářů ověřování, které podporuje Azure AD |
| | |

## <a name="get-started"></a>Začínáme

Rychlé starty a kurzy v 1.0 vás provedou vytvořením aplikace na upřednostňované platformě pomocí sady SDK knihovny ověřování v Azure AD. Pokud chcete začít, podívejte se na **kurzy** **rychlý Start v 1.0** a v 1.0 v tématu [Microsoft Identity Platform (Azure Active Directory pro vývojáře)](index.yml) .

## <a name="how-to-guides"></a>Návody

Podrobné informace a návody nejběžnějších úloh v Azure AD najdete v tématu **návody v 1.0** .

## <a name="reference-topics"></a>Referenční témata

Následující články poskytují podrobné informace o rozhraních API, zprávách protokolů a termínech používaných v Azure AD.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Knihovny ověřování (ADAL)](active-directory-authentication-libraries.md)   | Přehled knihoven a sad SDK, které poskytuje Azure AD |
| [Ukázky kódu](sample-v1-code.md)                                  | Seznam všech ukázek kódu Azure AD |
| [Glosář](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)                                      | Terminologie a definice slov, která se používají v této dokumentaci |
|  |  |


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
