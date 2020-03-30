---
title: Začínáme s integrací Azure AD s aplikacemi | Dokumenty společnosti Microsoft
description: Tento článek je průvodce mandatáře pro integraci Služby Azure Active Directory (AD) s místními aplikacemi a cloudovými aplikacemi.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89b16a8479f8975d101b8a4e26dcb1885d9730bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063376"
---
# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Integrace služby Azure Active Directory s aplikací začínáme

Toto téma shrnuje proces integrace aplikací s Azure Active Directory (AD). Každá z následujících částí obsahuje stručný přehled podrobnějšího tématu, abyste mohli určit, které části tohoto průvodce začínáme jsou pro vás relevantní.

Podrobné plány nasazení si můžete stáhnout v článku [Další kroky](#next-steps).

## <a name="take-inventory"></a>Provést inventuru
Před integrací aplikací s Azure AD je důležité vědět, kde jste a kam chcete jít.  Následující otázky jsou určeny k vám pomůže přemýšlet o projektu integrace aplikací Azure AD.

### <a name="application-inventory"></a>Inventář aplikací
* Kde jsou všechny vaše aplikace? Komu patří?
* Jaký druh ověřování vaše aplikace vyžadují?
* Kdo potřebuje přístup ke kterým aplikacím?
* Chcete nasadit novou aplikaci?
  * Vytvoříte ho in-house a nasadíte na výpočetní instanci Azure?
  * Použijete ten, který je k dispozici v Galerii aplikací Azure?

### <a name="user-and-group-inventory"></a>Inventář uživatelů a skupin
* Kde se nacházejí vaše uživatelské účty?
  * Místní služby Active Directory
  * Azure AD
  * V rámci samostatné aplikační databáze, kterou vlastníte
  * V neschválených žádostech
  * Všechny výš uvedené položky
* Jaká oprávnění a přiřazení rolí mají jednotliví uživatelé aktuálně? Potřebujete zkontrolovat jejich přístup nebo jste si jisti, že váš přístup k uživateli a přiřazení rolí jsou nyní vhodné?
* Jsou skupiny již vytvořeny ve vaší místní službě Active Directory?
  * Jak jsou organizovány vaše skupiny?
  * Kdo jsou členové skupiny?
  * Jaká oprávnění nebo přiřazení rolí mají skupiny aktuálně?
* Budete před integrací potřebovat vyčistit databáze uživatelů/skupin?  (To je docela důležitá otázka. Odpadky dovnitř, odpadky ven.)

### <a name="access-management-inventory"></a>Inventář správy přístupu
* Jak v současné době spravujete přístup uživatelů k aplikacím? Musí se to změnit?  Zvažovali jste jiné způsoby správy přístupu, například pomocí [RBAC?](../../role-based-access-control/role-assignments-portal.md)
* Kdo potřebuje přístup k čemu?

Možná nemáte odpovědi na všechny tyto otázky dopředu, ale to je v pořádku.  Tato příručka vám může pomoci odpovědět na některé z těchto otázek a učinit některá informovaná rozhodnutí.

### <a name="find-unsanctioned-cloud-applications-with-cloud-discovery"></a>Vyhledání neschválených cloudových aplikací pomocí cloudového zjišťování

Jak bylo uvedeno výše, mohou existovat aplikace, které nebyly dosud spravovány vaší organizací.  V rámci inventárního procesu je možné najít neschválené cloudové aplikace. Viz [Nastavení zjišťování cloudu](/cloud-app-security/set-up-cloud-discovery).

## <a name="integrating-applications-with-azure-ad"></a>Integrace aplikací s Azure AD
Následující články popisují různé způsoby integrace aplikací s Azure AD a poskytují některé pokyny.

* [Určení služby Active Directory, která má být používána](../fundamentals/active-directory-administer.md)
* [Používání aplikací v galerii aplikací Azure](what-is-single-sign-on.md)
* [Integrace seznamu výukových programů pro aplikace SaaS](../active-directory-saas-tutorial-list.md)

### <a name="authentication-types"></a>Typy ověřování
Každá aplikace může mít různé požadavky na ověřování. Pomocí Azure AD lze podpisové certifikáty používat s aplikacemi, které používají protokoly SAML 2.0, WS-Federation nebo OpenID Connect, stejně jako jednotné přihlašování heslem. Další informace o typech ověřování aplikací pro použití ve službě Azure AD najdete v [tématu Správa certifikátů pro jednotné přihlašování federovaného ve službě Azure Active Directory](manage-certificates-for-federated-single-sign-on.md) a jednotné [přihlašování založené na hesle](what-is-single-sign-on.md).

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Povolení přisážaní k alekům s azure ad app proxy
Pomocí proxy aplikací Microsoft Azure AD můžete bezpečně poskytnout přístup k aplikacím umístěným v privátní síti, odkudkoli a na libovolném zařízení. Po instalaci konektoru proxy aplikace ve vašem prostředí, lze snadno nakonfigurovat pomocí Azure AD.

### <a name="integrating-custom-applications"></a>Integrace vlastních aplikací
Pokud píšete novou aplikaci a chcete pomoci vývojářům při využití výkonu Azure AD, najdete [v tématu Guiding vývojáři](../active-directory-applications-guiding-developers-for-lob-applications.md).

Pokud chcete přidat vlastní aplikaci do Galerie aplikací Azure, přečtěte [si "Bring your own app" s konfigurací Saml samoobslužné služby Azure AD](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/).

## <a name="managing-access-to-applications"></a>Správa přístupu k aplikacím
Následující články popisují způsoby, jak můžete spravovat přístup k aplikacím, jakmile byly integrovány s Azure AD pomocí konektorů Azure AD a Azure AD.

* [Správa přístupu k aplikacím pomocí Azure AD](what-is-access-management.md)
* [Automatizace pomocí konektorů Azure AD](../app-provisioning/user-provisioning.md)
* [Přiřazování uživatelů k aplikaci](../active-directory-applications-guiding-developers-assigning-users.md)
* [Přiřazování skupin k aplikaci](../active-directory-applications-guiding-developers-assigning-groups.md)
* [Sdílení účtů](../active-directory-sharing-accounts.md)

## <a name="next-steps"></a>Další kroky
Podrobné informace získáte od [GitHubu](https://aka.ms/deploymentplans)ke stažení plánů nasazení služby Azure Active Directory . Pro aplikace galerie si můžete stáhnout plány nasazení pro jednotné přihlašování, podmíněný přístup a zřizování uživatelů prostřednictvím [portálu Azure](https://portal.azure.com). 

Stažení plánu nasazení z webu Azure:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Vyberte **podnikové aplikace** | **vyberte** | **plán nasazení**aplikací .

Uveďte zpětnou vazbu k plánům nasazení pomocí [průzkumu plánu nasazení](https://aka.ms/DeploymentPlanFeedback).
