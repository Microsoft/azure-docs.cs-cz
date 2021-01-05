---
title: Scénář výměny tokenů platformy Microsoft Identity Platform s SAML a OIDC/OAuth v Azure Active Directory
description: Přečtěte si o běžných scénářích výměny tokenů při práci s SAML a OIDC/OAuth v Azure Active Directory.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/08/2020
ms.author: kenwith
ms.reviewer: paulgarn
ms.openlocfilehash: 5841af4225f88b36fe727b60a597af208ac830f5
ms.sourcegitcommit: 42922af070f7edf3639a79b1a60565d90bb801c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2020
ms.locfileid: "97827168"
---
# <a name="microsoft-identity-platform-token-exchange-scenarios-with-saml-and-oidcoauth"></a>Scénáře výměny tokenů platformy Microsoft Identity Platform s SAML a OIDC/OAuth

SAML a OpenID Connect (OIDC)/OAuth jsou oblíbené protokoly používané k implementaci jednoho Sign-On (SSO). Některé aplikace můžou implementovat jenom SAML a jiné můžou implementovat jenom OIDC/OAuth. Oba protokoly používají tokeny k sdělování tajných kódů. Další informace o SAML najdete v tématu [Single Sign-On Protocol SAML](single-sign-on-saml-protocol.md). Další informace o OIDC/OAuth najdete v tématu [protokoly OAuth 2,0 a OpenID Connect na platformě Microsoft Identity Platform](active-directory-v2-protocols.md).

Tento článek popisuje běžný scénář, kdy aplikace implementuje SAML, ale potřebujete zavolat do Graph API, která používá OIDC/OAuth. K dispozici jsou základní doprovodné materiály pro lidi, kteří pracují s tímto scénářem.

## <a name="scenario-you-have-a-saml-token-and-want-to-call-the-graph-api"></a>Scénář: máte token SAML a chcete volat Graph API
Mnoho aplikací je implementováno pomocí SAML. Graph API ale používá protokoly OIDC/OAuth. K přidání funkce OIDC/OAuth do aplikace SAML je možné, ale ne triviální. Jakmile je funkce OAuth v aplikaci dostupná, je možné použít Graph API.

Obecnou strategií je přidání zásobníku OIDC/OAuth do aplikace. S vaší aplikací, která implementuje obě standardy, můžete použít soubor cookie relace. Token nebudete explicitně vyměňovat. Přihlašujete uživatele v nástroji SAML, který generuje soubor cookie relace. Když Graph API vyvolá tok OAuth, použijte k ověření soubor cookie relace. Tato strategie předpokládá, že jsou kontroly podmíněného přístupu passované a že je uživatel autorizovaný.

> [!NOTE]
> Doporučenou knihovnou pro přidání chování OIDC/OAuth je knihovna Microsoft Authentication Library (MSAL). Další informace o MSAL najdete v tématu [Přehled knihovny Microsoft Authentication Library (MSAL)](msal-overview.md). Předchozí knihovna se volala Active Directory Authentication Library (ADAL), ale nedoporučuje se, protože ji nahrazuje MSAL.

## <a name="next-steps"></a>Další kroky
- [Toky ověřování a scénáře aplikací](authentication-flows-app-scenarios.md)
