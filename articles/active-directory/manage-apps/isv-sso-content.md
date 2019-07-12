---
title: Povolení jednotného přihlašování pro vaše aplikace s více tenanty
description: Pokyny pro nezávislé dodavatele softwaru na integraci se službou Azure active Directory
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
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795184"
---
# <a name="enable-single-sign-on-for-your-multi-tenant-application"></a>Povolit jednotné přihlašování pro vaše aplikace s více tenanty  

Pokud svoji aplikaci pro použití jiných společností prostřednictvím nákupu nebo předplatné nabízet, zpřístupníte vaší aplikace pro zákazníky v rámci své vlastní tenanty Azure. To se označuje jako vytvoření aplikace s více tenanty. Tento koncept, najdete v článku [víceklientské aplikace v Azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications) a [Tenantů ve službě Azure Active Directory](../develop/single-and-multi-tenant-apps.md).

## <a name="what-is-single-sign-on"></a>Co je jednotné přihlašování

Jednotné přihlašování (SSO), přidá zabezpečení a pohodlí, když uživatelé přihlásit k aplikacím pomocí služby Azure Active Directory a jiných identit. Pokud aplikace je povoleno jednotné přihlašování, uživatelé nemají k zadání samostatné přihlašovací údaje pro přístup k této aplikaci. Úplné vysvětlení jednotného přihlašování. [Jednotné přihlašování k aplikacím v Azure Active Directory najdete v článku](what-is-single-sign-on.md).

## <a name="why-enable-single-sign-on-in-your-application"></a>Proč povolit jednotné přihlašování v aplikaci?

Existuje mnoho výhod pro povolení jednotného přihlašování v aplikaci pro více tenantů. Při povolení jednotného přihlašování pro vaši aplikaci:

* Vaše aplikace mohou být uvedeny na webu Azure Marketplace, kde je vaše aplikace zjistitelné pomocí miliony organizace, které používají Azure Active Directory.
  * Umožňuje zákazníkům rychle nakonfigurovat aplikaci s využitím Azure AD.

* Vaše aplikace je možné najít v galerii aplikací Office 365, Spouštěči aplikací Office 365 a v rámci Microsoft Search na webu Office.com

* Aplikace můžete použít Microsoft Graph REST API pro přístup k datům, která zvyšují produktivitu uživatele, který je k dispozici v Microsoft Graphu.

* Snížit náklady na podporu tím, že jednodušší pro vaše zákazníky.
  * Dokumentace ke službě specifické pro aplikaci coproduced s týmem Azure AD pro naše vzájemné zákazníky usnadňuje její přijetí.
  * Pokud je povoleno jednotné přihlašování jedním kliknutím, správci IT vaši zákazníci nemusí zjistěte, jak nakonfigurovat svoji aplikaci pro použití v jejich organizaci.

* Poskytují zákazníkům možnost zcela spravovat ověřování a autorizaci identita pro zaměstnance a hosta.

  * Uvedení všech účet správy a dodržování předpisů odpovědnost s vlastníkem zákazníků tyto identity.

  * Poskytuje možnost povolit nebo zakázat jednotné přihlašování pro konkrétní identitu poskytovatelů, skupiny nebo uživatelé splnění svých obchodních potřeb.

* Můžete zvýšit obchodovatelnost a adoptability. Mnoho organizací, které vyžadují (nebo držet) jejich zaměstnanci mají bezproblémové prostředí pro jednotné přihlašování ve všech aplikacích. Usnadňuje jednotné přihlašování je důležité.

* Můžete omezíte třecí plochy koncových uživatelů, což může zvýšit využití koncového uživatele a zvýšení tržeb.

## <a name="how-to-enable-single-sign-on-in-your-published-application"></a>Postup povolení jednotného přihlašování v publikované aplikace

1. [Vyberte protokol správné federation pro vaše aplikace s více tenanty](isv-choose-multi-tenant-federation.md).
1. Implementace jednotné přihlašování v aplikaci
   - Zobrazit [doprovodné materiály k vzory ověřování](../develop/v2-app-types.md)
   - Zobrazit [Azure active Directory ukázky](../develop/sample-v2-code.md) protokolů OIDC a protokolem OAuth
1. [Vytvoření Tenanta Azure](isv-tenant-multi-tenant-app.md) a testování vaší aplikace
1. [Vytvoření a publikování jednotného přihlašování k dokumentaci na webu](isv-create-sso-documentation.md).
1. [Odešlete svůj výpis aplikace](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) a vytvářet dokumentaci na webu společnosti Microsoft ve spolupráci s Microsoft.
1. [Připojte se k programu Microsoft Partner Network (zdarma) a vytvářet nejvhodnější uvedení na trh plán](https://partner.microsoft.com/en-us/explore/commercial#gtm).
