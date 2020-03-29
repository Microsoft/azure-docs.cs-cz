---
title: Povolení jednotného přihlašování pro aplikaci s více tenanty
description: Pokyny pro nezávislé dodavatele softwaru pro integraci s Azure active Directory
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c89a83ade6305579e700afb86f0b9e3aca2695e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67795184"
---
# <a name="enable-single-sign-on-for-your-multi-tenant-application"></a>Povolení jednotného přihlášení pro víceklientské aplikace  

Když nabízíte aplikaci pro použití jinými společnostmi prostřednictvím nákupu nebo předplatného, zpřístupníte svou aplikaci zákazníkům v rámci jejich vlastních tenantů Azure. To se označuje jako vytvoření víceklientské aplikace. Přehled tohoto konceptu najdete [v tématu Víceklientské aplikace v Azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications) a [nájmy ve službě Azure Active Directory](../develop/single-and-multi-tenant-apps.md).

## <a name="what-is-single-sign-on"></a>Co je jednotné přihlašování

Jednotné přihlašování (SSO) přidává zabezpečení a pohodlí, když se uživatelé přihlašují k aplikacím pomocí služby Azure Active Directory a dalších identit. Pokud je aplikace povolená, uživatelé nemusí zadávat samostatná pověření pro přístup k této aplikaci. Pro úplné vysvětlení jednotného přihlášení. [Viz Jednotné přihlašování k aplikacím ve službě Azure Active Directory](what-is-single-sign-on.md).

## <a name="why-enable-single-sign-on-in-your-application"></a>Proč povolit jednotné přihlašování ve vaší aplikaci?

Povolení přihlašování ve víceklientské aplikaci má mnoho výhod. Když povolíte přihlašovat k vlastní nastavení pro vaši aplikaci:

* Vaše aplikace může být uvedená na Azure Marketplace, kde je vaše aplikace zjistitelná miliony organizací používajících Azure Active Directory.
  * Umožňuje zákazníkům rychle nakonfigurovat aplikaci pomocí služby Azure AD.

* Vaše aplikace může být zjistitelná v Galerii aplikací Office 365, Spouštěči aplikací Office 365 a v Microsoft Search na Office.com

* Vaše aplikace můžete použít rozhraní API Microsoft Graph REST pro přístup k datům, která řídí produktivitu uživatelů, která je k dispozici v aplikaci Microsoft Graph.

* Snížíte náklady na podporu tím, že to usnadníte vašim zákazníkům.
  * Dokumentace specifická pro konkrétní aplikaci koprodukovaná s týmem Azure AD pro naše vzájemné zákazníky usnadňuje přijetí.
  * Pokud je povoleno s přihlašování pomocí vlastního zabezpečení jedním kliknutím, správci IT vašich zákazníků se nemusí učit, jak nakonfigurovat vaši aplikaci pro použití v jejich organizaci.

* Svým zákazníkům umožňujete zcela spravovat ověřování a autorizaci identit svých zaměstnanců a hostů.

  * Umístění veškeré odpovědnosti za správu účtu a dodržování předpisů na vlastníka těchto identit zákazníkem.

  * Poskytování možnosti povolit nebo zakázat automatické přiznávaní pro konkrétní poskytovatele identit, skupiny nebo uživatele, aby vyhovovaly jejich obchodním potřebám.

* Zvýšíte svou prodejnost a přijatelnost. Mnoho velkých organizací vyžaduje, aby (nebo aspirovat na) jejich zaměstnanci mají bezproblémové prostředí jednotného přihlašování ve všech aplikacích. Snadné vytváření sazí je důležité.

* Můžete snížit tření koncových uživatelů, což může zvýšit využití koncových uživatelů a zvýšit vaše příjmy.

## <a name="how-to-enable-single-sign-on-in-your-published-application"></a>Jak povolit jednotné přihlašování v publikované aplikaci

1. [Zvolte správný federační protokol pro víceklientské aplikace](isv-choose-multi-tenant-federation.md).
1. Implementace nastavení sazby ve vaší aplikaci
   - Viz [pokyny k autentizačním vzorcům](../develop/v2-app-types.md)
   - Viz [Ukázky kódu služby Azure active Directory](../develop/sample-v2-code.md) pro protokoly OIDC a OAuth
1. [Vytvořte si klienta Azure](isv-tenant-multi-tenant-app.md) a otestujte svou aplikaci
1. [Vytvořte a publikujte dokumentaci k vlastnímu přikazujme na svém webu](isv-create-sso-documentation.md).
1. [Odešlete výpis aplikace](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) a staňte se partnerem společnosti Microsoft a vytvořte dokumentaci na webu společnosti Microsoft.
1. [Připojte se k programu Microsoft Partner Network (zdarma) a vytvořte si plán pro uvedení na trh](https://partner.microsoft.com/en-us/explore/commercial#gtm).
