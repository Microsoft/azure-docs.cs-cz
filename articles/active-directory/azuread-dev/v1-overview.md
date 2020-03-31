---
title: Přehled služby Azure Active Directory pro vývojáře (v1.0)
description: Tento článek obsahuje přehled podepisování v pracovních a školních účtech Microsoftu pomocí koncového bodu a platformy Azure Active Directory v1.0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 10/24/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: ade350c91ebd2f3a68b52011e598f739a14c220f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154487"
---
# <a name="azure-active-directory-for-developers-v10-overview"></a>Přehled služby Azure Active Directory pro vývojáře (v1.0)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Azure Active Directory (Azure AD) je cloudová služba identit umožňující vývojářům sestavovat aplikace, které bezpečně přihlásí uživatele s pracovním nebo školním účtem Microsoft. Azure AD podporuje vývojáře vytvářející jak obchodní aplikace s jedním tenantem, tak i vývojáře, kteří se zajímají o vyvíjení aplikací s více tenanty. Kromě základního přihlašování umožňuje Azure AD volat nejen rozhraní API Microsoftu, jako je [Microsoft Graph](https://docs.microsoft.com/graph/overview), ale i vlastní rozhraní API vytvořená na platformě Azure AD. Tato dokumentace vám ukáže, jak do vlastní aplikace přidat podporu Azure AD s použitím standardních oborových protokolů, jako jsou OAuth 2.0 a OpenID Connect.

> [!NOTE]
> Většina obsahu na této stránce se zaměřuje na koncový bod a platformu v1.0, která podporuje pouze pracovní nebo školní účty Microsoft. Pokud se chcete přihlásit k spotřebitelským nebo osobním účtům Microsoft, přečtěte si informace o [koncovém bodu v2.0 a platformě](../develop/v2-overview.md). Koncový bod v2.0 nabízí jednotné vývojářské prostředí pro aplikace, které se chtějí přihlásit ke všem identitám Microsoftu.

| | |
| --- | --- |
|[Základy ověřování](v1-authentication-scenarios.md) | Úvod k ověřování pomocí Azure AD |
|[Typy aplikací](app-types.md) | Přehled scénářů ověřování, které podporuje Azure AD |
| | |

## <a name="get-started"></a>Začínáme

Rychlé starty v1.0 a kurzy vás provedou vytvářením aplikace na upřednostňované platformě pomocí sady Azure AD Authentication Library (ADAL) SDK. Další informace o tom, jak začít, najdete v kurzech **v1.0 Quickstarts** a **v1.0** na [platformě microsoftových identit (Azure Active Directory pro vývojáře).](index.yml)

## <a name="how-to-guides"></a>Návody

Podrobné informace a návody k nejběžnějším úkolům ve službě Azure AD najdete v **návodech k tématu v1.0.**

## <a name="reference-topics"></a>Referenční témata

Následující články poskytují podrobné informace o rozhraních API, zprávách protokolů a termínech používaných v Azure AD.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Knihovny ověřování (ADAL)](active-directory-authentication-libraries.md)   | Přehled knihoven a sad SDK, které poskytuje Azure AD |
| [Ukázky kódu](sample-v1-code.md)                                  | Seznam všech ukázek kódu Azure AD |
| [Slovníček](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)                                      | Terminologie a definice slov, která se používají v této dokumentaci |
|  |  |

## <a name="videos"></a>Videa

Nápovědu k migraci na novou platformu microsoftu najdete v [videích vývojářské platformy Azure Active Directory.](videos.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
