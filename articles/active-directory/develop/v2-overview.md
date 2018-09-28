---
title: Informace o v2.0 | Azure
description: Seznamte se s koncovým bodem a platformou v2.0.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: adcb1b15ed52e6954846ea09be0a87a118222c10
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989580"
---
# <a name="about-v20"></a>Informace o v2.0

Koncový bod a platforma v2.0 je k dispozici ve verzi Preview a průběžně se vylepšuje. V současné době jsou scénáře pro jednostránkové aplikace (SPA) v JavaScriptu z hlediska funkcí dokončené, takže můžete pomocí MSAL.js začít vytvářet aplikace založené na prohlížeči. Dejte nám vědět svůj názor, abychom mohli aktualizovat stav z verze Preview na obecnou dostupnost.

> [!NOTE]
> Funkce knihoven MSAL pro Android, iOS a .NET se zatím stále vyvíjejí. Můžete je vyzkoušet při vytváření aplikací a poslat nám svoje připomínky a názory.

Prostředí webu Azure Portal pro vývojáře se výrazně aktualizovalo, aby zahrnovalo veškeré vaše aplikace vytvořené prostřednictvím knihoven ADAL nebo MSAL a aby nabízelo možnosti pro širší použitelnost.

Vývojáři aplikací, kteří chtěli v minulosti ze služby Azure Active Directory (Azure AD) podporovat osobní i pracovní účty Microsoft, museli integrovat řešení v rámci dvou samostatných systémů. Koncový bod a platforma v2.0 přináší verzi rozhraní API pro ověřování, která tento proces zjednodušuje. Umožňuje přihlášení přes oba typy účtů, přičemž se integruje v rámci jediného systému. Aplikace s podporou koncového bodu v2.0 zároveň můžou využívat rozhraní REST API přes rozhraní [Microsoft Graph API](https://graph.microsoft.io) prostřednictvím obou typů účtu.

## <a name="getting-started"></a>Začínáme

V následujícím seznamu zvolte svoji oblíbenou platformu pro vytvoření aplikace pomocí knihoven a architektury Microsoft Open Source. Zároveň můžete pomocí protokolů OAuth 2.0 a OpenID Connect přímo (bez použití knihovny ověřování) odesílat a přijímat zprávy protokolu.

[!INCLUDE [v2.0 endpoint platforms](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="learn-more-about-the-v20-endpoint-and-platform"></a>Další informace o koncovém bodu a platformě v2.0

Zjistěte, jaké možnosti nabízí koncový bod služby Azure AD v2.0:

* Podívejte se na [typy aplikací, které můžete pomocí koncového bodu služby Azure AD v2.0 vytvořit](v2-app-types.md).
* Seznamte se s [limity a omezeními](active-directory-v2-limitations.md), které může koncový bod Azure AD v2.0 představovat.
* Základní informace o koncovém bodu Azure AD v2.0 získáte v tomto videu:

>[!VIDEO https://channel9.msdn.com/Events/Build/2017/P4031/player]

## <a name="additional-resources"></a>Další zdroje

Prostudujte si podrobné informace týkající se v2.0:

* [informace o protokolech v2.0](active-directory-v2-protocols.md)
* [informace o přístupových tokenech](access-tokens.md)
* [informace o `id_tokens`](id-tokens.md)
* [informace o knihovnách ověřování v2.0](reference-v2-libraries.md)
* [obory a vyžádání souhlasu uživatele pro v2.0](v2-permissions-and-consent.md)
* [rozhraní Microsoft Graph API](https://graph.microsoft.io)

> [!NOTE]
> Pokud potřebujete řešit přihlášení jen pracovními a školními účty v rámci Azure Active Directory, začněte [průvodcem Azure AD pro vývojáře](azure-ad-developers-guide.md). Koncový bod v2.0 je určen pro vývojáře, kteří explicitně potřebují řešit přihlášení osobními účty Microsoft.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
