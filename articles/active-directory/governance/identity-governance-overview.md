---
title: Zásady správného řízení identit Azure AD | Dokumentace Microsoftu
description: Zásady správného řízení identit Azure AD umožňuje vyrovnávat potřeby vaší organizace pro zabezpečení a produktivity zaměstnanců se správné procesy a viditelnost.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/25/2018
ms.author: rolyon
ms.reviewer: markwahl-msft
ms.openlocfilehash: 887c0bd4246c7c4215cb4d97dfd96db5d2aba6cb
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55170014"
---
# <a name="what-is-azure-ad-identity-governance"></a>Co je zásady správného řízení identit Azure AD?

Zásady správného řízení identit Azure Active Directory (Azure AD) umožňuje vyrovnávat potřeby vaší organizace pro zabezpečení a produktivity zaměstnanců se správné procesy a viditelnost. To vám poskytne možnosti k zajištění, že správní uživatelé mají správný přístup s vhodnými zdroji a umožňuje chránit, sledovat a auditovat přístup k důležitých datových prostředků – přitom zajistit produktivitu zaměstnanců.  

Zásady správného řízení identit umožňují organizacím takto napříč zaměstnanců, obchodními partnery a dodavateli a službami a aplikacemi:

- Řízení životního cyklu identit
- Řízení přístupu životního cyklu
- Zabezpečení správy

Konkrétně jeho účelem je pomoct organizacím tyto čtyři klíčové otázky:

- Které uživatelé by měli mít přístup k jakým prostředkům?
- Co dělají uživatelé s, která přístup?
- Jsou efektivní organizační ovládacích prvků pro správu přístupu?
- Můžete auditoři ověřit, že ovládací prvky fungují?

## <a name="identity-lifecycle"></a>Životního cyklu identit

Zásady správného řízení identit pomáhá organizacím dosáhnout rovnováhy mezi *produktivitu* – jak může uživatel měli rychlý přístup k prostředkům potřebují, například když připojí mé organizaci? A *zabezpečení* – jak by měl přístup v průběhu času měnit, například z důvodu změn na stav tohoto člověka zaměstnání?  Správa životního cyklu identit je základem pro zásady správného řízení identit a efektivní škálovatelné zásady správného řízení vyžaduje modernizaci infrastruktura správy životního cyklu identit pro aplikace.

Pro mnoho společností životního cyklu identit pro zaměstnance se váže k reprezentaci tohoto uživatele v systému HCM (řízení lidského kapitálu).  Identity uživatelů Azure AD Premium automaticky udržuje pro lidi ve Workday do Active Directory a Azure Active Directory, jak je popsáno v [Workday příchozího zřizování (preview) kurzu](../saas-apps/workday-inbound-tutorial.md).  Azure AD Premium zahrnuje také [Microsoft Identity Manageru](/microsoft-identity-manager/), který by mohl importovat záznamy z místních systémů HCM například SAP, Oracle eBusiness, Oracle PeopleSoft a.

Stále víc i scénáře vyžadují spolupráci s lidmi mimo vaši organizaci. [Azure AD s B2B](/azure/active-directory/b2b/) spolupráce umožňuje bezpečně sdílet aplikace a služby vaší organizace s hosty a externí partnery z jakékoli organizace při zachování kontroly nad firemní data.

## <a name="access-lifecycle"></a>Životní cyklus aplikace Access

Organizace potřebují proces pro správu přístupu mimo co byl zpočátku zřízen pro uživatele při vytvoření identity uživatele.  Kromě toho organizace musí být možné škálovat prostředek efektivně, abyste mohli vyvíjet a vynucovat zásady přístupu a ovládacích prvků průběžně.

Obvykle IT delegáti přístup pracovníci s rozhodovací pravomocí rozhodnutí o schválení.  Kromě toho IT může zahrnovat samotných uživatelů.  Například uživatelé, kteří přístup k důvěrným zákaznická data ve vaší společnosti marketingu aplikací v Evropě znát zásady vaší společnosti. Může být vědět o požadavky na zpracování dat v organizaci, do které byli pozváni uživatelé typu Host.

Organizace můžete automatizovat proces životního cyklu přístup díky technologiím, jako například [dynamické skupiny](../users-groups-roles/groups-dynamic-membership.md), svázaných s zřizování uživatelů pro [aplikace SaaS](../saas-apps/tutorial-list.md) nebo [integrovat se službou SCIMaplikace](../manage-apps/use-scim-to-provision-users-and-groups.md).  Organizace taky řídit, které [uživatele typu Host mají přístup k místním aplikacím](../b2b/hybrid-cloud-to-on-premises.md).  Tyto přístup k oprávnění může pak být pravidelně přezkoumávány pomocí opakovaného [kontroly přístupu Azure AD](access-reviews-overview.md).

Když se uživatel pokusí získat přístup k aplikacím, vynucuje Azure AD [podmíněného přístupu](/azure/active-directory/conditional-access/) zásady. Zásady podmíněného přístupu může obsahovat třeba zobrazování [podmínky použití](active-directory-tou.md) a [zajistit, že uživatel souhlasil s těmito podmínkami](../conditional-access/require-tou.md) před nebudete mít přístup k aplikaci.

## <a name="privileged-access-lifecycle"></a>Životní cyklus privilegovaného přístupu

V minulosti privilegovaného přístupu je popsán pomocí jiných dodavatelů jako samostatnou funkci z zásady správného řízení identit. Nicméně v Microsoftu, myslíme si, že řídící privilegovaného přístupu je klíčovou součástí správy identit – zejména zadaný riziko zneužití spojené s těmito správce, který může způsobit práva k organizaci. Zaměstnanci, Dodavatelé a dodavatelů, které převezmou práva pro správu je potřeba řídit.

Azure AD Privileged Identity Management (PIM) obsahuje další ovládací prvky přizpůsobená pro zabezpečení přístupu práv pro prostředky, v Azure AD, Microsoft Online Services, Azure a dalších.  Just-in-time přístup a upozorňování, které poskytuje Azure AD PIM, kromě vícefaktorové ověřování a podmíněný přístup, změna role poskytují že komplexní sadu kontrolu zásad správného řízení zabezpečení prostředků vaší společnosti (adresář, Office 365 a role prostředků Azure). Stejně jako u jiných forem přístup organizace můžou použít kontroly přístupu ke konfiguraci opakované vydal přístup pro všechny uživatele v rolích správce.

## <a name="getting-started"></a>Začínáme

Neplatí žádné z nabízených řešení nebo doporučení pro každý zákazník, zadejte následující konfigurace Průvodce základní zásady společnost Microsoft doporučuje zajistit zabezpečení a produktivitu zaměstnanců.

- [Konfigurace přístupu k identity a zařízení](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Zabezpečení privilegovaného přístupu](../users-groups-roles/directory-admin-roles-secure.md)


### <a name="access-reviews"></a>Kontroly přístupu

- [Co je kontrola přístupu?](access-reviews-overview.md)
- [Správa přístupu uživatelů pomocí kontrol přístupu](manage-user-access-with-access-reviews.md)
- [Správa přístupu hostů pomocí kontrol přístupu](manage-guest-access-with-access-reviews.md)
- [Zahájení kontroly přístupu role adresáře](../privileged-identity-management/pim-how-to-start-security-review.md)

### <a name="terms-of-use"></a>Podmínky použití

- [Co můžu dělat s podmínkami použití?](active-directory-tou.md)

### <a name="privileged-identity-management"></a>Privileged Identity Management

- [Co je Azure AD PIM](../privileged-identity-management/pim-configure.md)
