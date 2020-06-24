---
title: Povolení jednotného přihlašování pro aplikaci s více tenanty
description: Pokyny pro nezávislé výrobce softwaru při integraci s Azure Active Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: kenwith
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26fa2b82b89c7b9ce1e05062718f2c4d0c93197c
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/14/2020
ms.locfileid: "84763274"
---
# <a name="enable-single-sign-on-for-your-multi-tenant-application"></a>Povolení jednotného přihlašování pro aplikaci s více klienty  

Když nabídnete aplikaci pro použití jinými společnostmi prostřednictvím nákupu nebo předplatného, zpřístupníte ji zákazníkům v jejich vlastních klientech Azure. To se označuje jako vytvoření víceklientské aplikace. Přehled tohoto konceptu najdete v tématu víceklientské [aplikace v Azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications) a [tenantů v Azure Active Directory](../develop/single-and-multi-tenant-apps.md).

## <a name="what-is-single-sign-on"></a>Co je jednotné přihlašování

Jednotné přihlašování (SSO) zvyšuje zabezpečení a pohodlí, když se uživatelé přihlásí k aplikacím pomocí Azure Active Directory a dalších identit. Když je aplikace povolená jednotného přihlašování, uživatelé nemusí zadávat samostatné přihlašovací údaje pro přístup k této aplikaci. Úplné vysvětlení jednotného přihlašování. [Viz jednotné přihlašování k aplikacím v Azure Active Directory](what-is-single-sign-on.md).

## <a name="why-enable-single-sign-on-in-your-application"></a>Proč v aplikaci povolit jednotné přihlašování?

K povolení jednotného přihlašování v aplikaci pro více tenantů máte spoustu výhod. Pokud pro svou aplikaci povolíte jednotné přihlašování:

* Vaše aplikace může být uvedená v Azure Marketplace, kde je vaše aplikace zjistitelná miliony organizací pomocí Azure Active Directory.
  * Umožňuje zákazníkům rychlou konfiguraci aplikace pomocí Azure AD.

* Vaše aplikace může být zjistitelná v galerii aplikací Office 365, ve Spouštěči aplikací Office 365 a v rámci služby Microsoft Search na Office.com.

* Vaše aplikace může používat REST API Microsoft Graph pro přístup k datům, která řídí produktivitu uživatelů, která je k dispozici z Microsoft Graph.

* Snížit náklady na podporu tím, že zákazníkům usnadníte práci.
  * Dokumentace specifická pro aplikaci vytvořená s týmem Azure AD pro naše vzájemné zákazníky usnadňuje přijímání.
  * Pokud je jednotné přihlašování jedním kliknutím povolené, správci IT nemusí vědět, jak konfigurovat aplikaci pro použití ve své organizaci.

* Zákazníkům poskytnete možnost plně spravovat ověřování a autorizaci identity svých zaměstnanců a hostů.

  * Zadáváme zodpovědnost za správu účtů a dodržování předpisů s vlastníkem zákazníka těchto identit.

  * Poskytování možnosti povolit nebo zakázat jednotné přihlašování pro konkrétní poskytovatele identity, skupiny nebo uživatele, aby splnili své obchodní potřeby.

* Zvýšíte svou obchodovatelnost a účinnost. Mnoho velkých organizací vyžaduje, aby jejich zaměstnanci (nebo snažíme) měli bezproblémové prostředí jednotného přihlašování napříč všemi aplikacemi. Zjednodušení jednotného přihlašování je důležité.

* Omezíte tření koncového uživatele, což může zvýšit využití koncovými uživateli a zvýšit vaši tržby.

## <a name="how-to-enable-single-sign-on-in-your-published-application"></a>Jak ve vaší publikované aplikaci povolit jednotné přihlašování

1. [Vyberte správný federační protokol pro vaši víceklientské aplikaci](isv-choose-multi-tenant-federation.md).
1. Implementace jednotného přihlašování v aplikaci
   - Zobrazit [pokyny k vzorům ověřování](../develop/v2-app-types.md)
   - Viz [ukázky kódu Azure Active Directory](../develop/sample-v2-code.md) pro OIDC a protokoly OAuth.
1. [Vytvoření tenanta Azure](isv-tenant-multi-tenant-app.md) a testování aplikace
1. [Vytvořte a publikujte na svém webu dokumentaci jednotného přihlašování](isv-create-sso-documentation.md).
1. [Odesláním seznamu aplikací](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) a partnera s Microsoftem můžete vytvořit dokumentaci na webu Microsoftu.
1. [Připojte se k Microsoft Partner Network (zdarma) a vytvořte svůj přejít na trh](https://partner.microsoft.com/en-us/explore/commercial#gtm).
