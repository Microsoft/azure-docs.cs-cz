---
title: Koncový bod Azure Active Directory v2.0 | Dokumentace Microsoftu
description: Úvod do vytváření aplikací s Microsoft Account a Azure Active Directory přihlášení.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 2dee579f-fdf6-474b-bc2c-016c931eaa27
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: celested
ms.reviewer: hirsin, jmprieur, elisol, dastrock
ms.custom: aaddev
ms.openlocfilehash: 3d71e889fccaa6de59d9a528754a5e11d0220bef
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39577131"
---
# <a name="sign-in-microsoft-account-and-azure-active-directory-users-in-a-single-application"></a>Přihlaste se uživatelům v jedné aplikaci Account Microsoft a Azure Active Directory
V minulosti bylo integrace se systémy pro dva samostatné vývojáři aplikací pro podporu obou osobní účty Microsoft a pracovní účty ze služby Azure Active Directory. Koncový bod Azure Active Directory (Azure AD) verze 2.0 představuje nové verze ověřování rozhraní API, která zjednodušuje tento proces. Koncový bod Azure AD v2.0 umožňuje přihlášení z oběma typy účtů pomocí jednoho integrace. Aplikace, které používají koncového bodu Azure AD v2.0 také může využívat rozhraní REST API z [Microsoft Graph API](https://graph.microsoft.io) pomocí buď typ účtu.

## <a name="getting-started"></a>Začínáme
Zvolte vaši oblíbenou platformu z následujícího seznamu k sestavení aplikace s použitím Microsoft otevřít zdroj knihoven a architektur. Protokoly OAuth 2.0 a OpenID Connect můžete použít také k odesílání a příjem zpráv protokolu přímo bez použití knihovny ověřování.
<br />

[!INCLUDE [Azure AD v2.0 endpoint platforms](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="learn-more-about-the-azure-ad-v20-endpoint"></a>Další informace o koncový bod Azure AD v2.0
Zjistěte, co můžete dělat pomocí koncového bodu Azure AD v2.0:

* Zjišťování [typy aplikací, které můžete vytvořit s využitím koncového bodu Azure AD v2.0](active-directory-v2-flows.md).
* Vysvětlení [omezení, omezení a omezení](active-directory-v2-limitations.md) s koncovým bodem v2.0 Azure AD.
* V tomto videu získáte přehled o koncový bod Azure AD v2.0:

>[!VIDEO https://channel9.msdn.com/Events/Build/2017/P4031/player]

## <a name="additional-resources"></a>Další zdroje informací:
Prozkoumejte podrobné informace o platformě Azure AD v2.0 koncový bod:

* [Odkazovat na Azure AD v2.0 protokoly](active-directory-v2-protocols.md)
* [Azure AD v2.0 tokeny odkaz](v2-id-and-access-tokens.md)
* [Odkazovat na knihovny ověřování Azure AD v2.0](active-directory-v2-libraries.md)
* [Obory a souhlas v koncovém bodu Azure AD v2.0](v2-permissions-and-consent.md)
* [Rozhraní Microsoft Graph API](https://graph.microsoft.io)

> [!NOTE]
> Pokud potřebujete jenom se přihlásit pracovním a školním účtům ze služby Azure Active Directory, začněte tématem [Příručka pro vývojáře Azure AD](azure-ad-developers-guide.md). Koncový bod Azure AD v2.0 je určen pro použití vývojářům, kteří potřebují explicitně přihlášení osobní účty Microsoft.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
