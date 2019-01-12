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
ms.openlocfilehash: 88904b27e114c27dc7b1051c755d3c831c90554f
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54232461"
---
# <a name="about-v20"></a>Informace o v2.0

Koncový bod a platforma v2.0 je k dispozici ve verzi Preview a průběžně se vylepšuje. V současné době jsou scénáře pro jednostránkové aplikace (SPA) v JavaScriptu z hlediska funkcí dokončené, takže můžete pomocí MSAL.js začít vytvářet aplikace založené na prohlížeči. Dejte nám vědět svůj názor, abychom mohli aktualizovat stav z verze Preview na obecnou dostupnost.

> [!NOTE]
> Funkce knihoven MSAL pro Android, iOS a .NET se zatím stále vyvíjejí. Můžete je vyzkoušet při vytváření aplikací a poslat nám svoje připomínky a názory.

Na webu Azure portal [registrace aplikací (preview)](quickstart-register-app.md) prostředí výrazně aktualizovali jsme teď obsahuje všechny aplikace vytvořené pomocí knihovny ADAL nebo MSAL a použitelnosti.

Vývojáři aplikací, kteří chtěli v minulosti ze služby Azure Active Directory (Azure AD) podporovat osobní i pracovní účty Microsoft, museli integrovat řešení v rámci dvou samostatných systémů. Koncový bod a platforma v2.0 přináší verzi rozhraní API pro ověřování, která tento proces zjednodušuje. Umožňuje přihlášení přes oba typy účtů, přičemž se integruje v rámci jediného systému. Aplikace s podporou koncového bodu v2.0 zároveň můžou využívat rozhraní REST API přes rozhraní [Microsoft Graph API](https://developer.microsoft.com/graph) prostřednictvím obou typů účtu.

## <a name="getting-started"></a>Začínáme

Zvolte vaši oblíbenou platformu z následujícího seznamu k sestavení aplikace s použitím Microsoft otevřete source knihoven a architektur:

[!INCLUDE [v2.0 endpoint platforms](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="learn-more-about-the-v20-endpoint-and-platform"></a>Další informace o koncovém bodu a platformě v2.0

Zjistěte, jaké možnosti nabízí koncový bod služby Azure AD v2.0:

* Podívejte se na [typy aplikací, které můžete pomocí koncového bodu služby Azure AD v2.0 vytvořit](v2-app-types.md).
* Seznamte se s [limity a omezeními](active-directory-v2-limitations.md), které může koncový bod Azure AD v2.0 představovat.

## <a name="additional-resources"></a>Další materiály

Prostudujte si podrobné informace týkající se v2.0:

* [O platforma identit Microsoft](about-microsoft-identity-platform.md)
* [informace o protokolech v2.0](active-directory-v2-protocols.md)
* [informace o přístupových tokenech](access-tokens.md)
* [informace o tokenech ID](id-tokens.md)
* [informace o knihovnách ověřování v2.0](reference-v2-libraries.md)
* [Oprávnění a souhlas ve v2.0](v2-permissions-and-consent.md)
* [Microsoft Graph API](https://developer.microsoft.com/graph)

> [!NOTE]
> Pokud potřebujete řešit přihlášení jen pracovními a školními účty v rámci Azure Active Directory, začněte [průvodcem Azure AD pro vývojáře](v1-overview.md). Koncový bod v2.0 je určen pro vývojáře, kteří explicitně potřebují řešit přihlášení osobními účty Microsoft.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
