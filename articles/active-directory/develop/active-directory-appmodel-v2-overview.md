---
title: Azure Active Directory koncového bodu v2.0 | Microsoft Docs
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
ms.reviewer: dastrock
ms.custom: aaddev
ms.openlocfilehash: 0fde3cb77389994352b62f06d370c709fa115749
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="sign-in-microsoft-account-and-azure-active-directory-users-in-a-single-application"></a>Přihlaste se uživatelům v jedné aplikaci Microsoft Account a Azure Active Directory
V minulosti bylo integrovat dvou samostatných systémech vývojáři aplikací podporují i osobní účty Microsoft a pracovní účty ze služby Azure Active Directory. Koncový bod v2.0 Azure Active Directory (Azure AD) představuje novou verzi ověřování rozhraní API, která zjednodušuje tento proces. Koncový bod v2.0 Azure AD umožňuje přihlášení z obou typů účty pomocí jednoho integrace. Aplikace, které používají koncového bodu Azure AD v2.0 můžete také používat rozhraní REST API z [Microsoft Graph API](https://graph.microsoft.io) pomocí buď typ účtu.

## <a name="getting-started"></a>Začínáme
Vyberte oblíbenou platformu z následujícího seznamu k sestavení aplikace pomocí Microsoft otevřete zdroj knihoven a architektur. Můžete taky protokoly OAuth 2.0 a OpenID Connect k odesílání a příjmu zprávy protokolu přímo bez použití knihovny pro ověřování.
<br />

[!INCLUDE [Azure AD v2.0 endpoint platforms](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="learn-more-about-the-azure-ad-v20-endpoint"></a>Další informace o koncového bodu Azure AD v2.0
Další informace o co můžete dělat s koncovým bodem v2.0 Azure AD:

* Zjišťování [typy aplikací, které můžete sestavit s koncovým bodem v2.0 Azure AD](active-directory-v2-flows.md).
* Pochopení [omezení, omezení a omezení](active-directory-v2-limitations.md) s koncovým bodem v2.0 Azure AD.
* Podívejte se na toto video přehled koncového bodu Azure AD v2.0:

>[!VIDEO https://channel9.msdn.com/Events/Build/2017/P4031/player]

## <a name="additional-resources"></a>Další zdroje informací:
Další podrobné informace o platformě Azure AD v2.0 koncový bod:

* [Azure AD v2.0 protokoly reference](active-directory-v2-protocols.md)
* [Azure AD v2.0 tokeny odkaz](active-directory-v2-tokens.md)
* [Reference knihovny ověřování Azure AD v2.0](active-directory-v2-libraries.md)
* [Obory a souhlasu v koncového bodu Azure AD v2.0](active-directory-v2-scopes.md)
* [Microsoft Graph API](https://graph.microsoft.io)

> [!NOTE]
> Pokud potřebujete přihlásit pracovní a školní účty ze služby Azure Active Directory, začínat [Příručka pro vývojáře Azure AD](active-directory-developers-guide.md). Koncový bod v2.0 Azure AD je určená pro vývojáře, kteří explicitně se muset přihlásit osobní účty Microsoft.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
