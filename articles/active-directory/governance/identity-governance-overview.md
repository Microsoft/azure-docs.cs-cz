---
title: Zásady správného řízení identity – Azure Active Directory | Microsoft Docs
description: Azure Active Directory Identity Governance vám umožní rovnováhu mezi potřebou zabezpečení a produktivity zaměstnanců pomocí správných procesů a viditelnosti.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/29/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f09a8c2f8caae3cbb182cf2dc4621deb95f7e5c
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499665"
---
# <a name="what-is-azure-ad-identity-governance"></a>Co je Azure AD Identity Governance?

Zásady správného řízení identity Azure Active Directory (Azure AD) umožňují vyrovnávat potřebu organizace potřebnou k zajištění produktivity zabezpečení a zaměstnanců pomocí správných procesů a viditelnosti. Poskytuje vám možnosti, které zajistí, aby měli uživatelé správný přístup ke správným prostředkům a abyste mohli chránit, monitorovat a auditovat přístup k důležitým prostředkům a zároveň zajistit produktivitu zaměstnanců.  

Zásady správného řízení identity poskytují organizacím možnost provádět následující úkoly napříč zaměstnanci, obchodními partnery a dodavateli a službami a aplikacemi:

- Řídit životní cyklus identity
- Řízení životního cyklu přístupu
- Zabezpečená správa

Konkrétně je určena k tomu, aby organizacím pomáhaly řešit tyto čtyři klíčové otázky:

- Kteří uživatelé by měli mít přístup k jakým prostředkům?
- Jaké jsou uživatelé s tímto přístupem?
- Existují efektivní organizační ovládací prvky pro správu přístupu?
- Mohou Auditori ověřit, zda ovládací prvky fungují?

## <a name="identity-lifecycle"></a>Životní cyklus identity

Řízení identit pomáhá organizacím dosáhnout rovnováhy  mezi produktivitou – jak rychle může osoba získat přístup k potřebným prostředkům, například když se připojí k mojí organizaci? A *zabezpečení* – jak se má v průběhu času měnit přístup, například kvůli změnám stavu zaměstnanosti této osoby?  Správa životního cyklu identit je základem pro řízení identit a efektivní řízení škálování vyžaduje modernizaci infrastrukturu správy životního cyklu identit pro aplikace.

![Životní cyklus identity](./media/identity-governance-overview/identity-lifecycle.png)

Pro mnoho organizací je životní cyklus identit zaměstnanců vázaný na zastoupení daného uživatele v systému HCM (lidské správy velkých písmen).  Azure AD Premium automaticky zachovává identity uživatelů v Workday v rámci služby Active Directory a Azure Active Directory, jak je popsáno v [kurzu pro příchozí zřizování ve službě Workday (Preview)](../saas-apps/workday-inbound-tutorial.md).  Azure AD Premium také zahrnuje [Microsoft Identity Manager](/microsoft-identity-manager/), které mohou importovat záznamy z místních systémů HCM, jako jsou SAP, Oracle EBusiness a Oracle PeopleSoft spouštěných místně.

Stále více scénářů vyžaduje spolupráci s lidmi mimo vaši organizaci. Spolupráce [B2B Azure AD](/azure/active-directory/b2b/) umožňuje bezpečně sdílet aplikace a služby vaší organizace s uživateli typu Host a externími partnery z jakékoli organizace a přitom zachovat kontrolu nad vašimi podnikovými daty.

## <a name="access-lifecycle"></a>Životní cyklus přístupu

Organizace potřebují proces pro správu přístupu nad rámec toho, co byl poprvé zřízen pro uživatele při vytvoření identity tohoto uživatele.  Kromě toho musí být organizace v rozlehlých sítích schopné efektivně škálovat, aby bylo možné průběžně vyvíjet a vymáhat zásady přístupu a ovládací prvky.

![Životní cyklus přístupu](./media/identity-governance-overview/access-lifecycle.png)

Obvykle IT deleguje rozhodnutí o schválení přístupu u tvůrců obchodních rozhodnutí.  Kromě toho může zahrnovat samotné uživatele.  Například uživatelé, kteří mají přístup k důvěrným zákaznickým datům v obchodní aplikaci společnosti v Evropě, potřebují znát zásady společnosti. Uživatelé typu Host mohou být nevědomi požadavků na zpracování dat v organizaci, které byly pozvány.

Organizace můžou proces životního cyklu přístupu automatizovat prostřednictvím technologií, jako jsou [dynamické skupiny](../users-groups-roles/groups-dynamic-membership.md), společně s zřizováním uživatelů, aby [SaaS aplikace](../saas-apps/tutorial-list.md) nebo [aplikace integrované s SCIM](../manage-apps/use-scim-to-provision-users-and-groups.md).  Organizace také mohou určovat, kteří [Uživatelé typu Host mají přístup k místním aplikacím](../b2b/hybrid-cloud-to-on-premises.md).  Tato přístupová práva se pak dají pravidelně kontrolovat pomocí opakujících se kontrol [přístupu Azure AD](access-reviews-overview.md).

Když se uživatel pokusí o přístup k aplikacím, Azure AD vynutil zásady [podmíněného přístupu](/azure/active-directory/conditional-access/) . Zásady podmíněného přístupu můžou například zahrnovat zobrazení [podmínek použití](../conditional-access/terms-of-use.md) a [zajištění souhlasu uživatele s těmito podmínkami](../conditional-access/require-tou.md) předtím, než bude moci získat přístup k aplikaci.

## <a name="privileged-access-lifecycle"></a>Životní cyklus privilegovaného přístupu

V minulosti byl privilegovaný přístup popsán jinými dodavateli jako samostatnou funkcí ze zásad správného řízení identity. V Microsoftu se ale domníváme, že pro privilegovaný přístup je klíčovou součástí zásad správného řízení identity – obzvláště vzhledem k možnosti nesprávného použití přidružené k těmto právům správce může dojít k organizaci. Zaměstnanci, dodavatelé a dodavatelé, kteří přijímají oprávnění správce, musí být řízeni.

![Životní cyklus privilegovaného přístupu](./media/identity-governance-overview/privileged-access-lifecycle.png)

Azure AD Privileged Identity Management (PIM) poskytuje další ovládací prvky přizpůsobené zabezpečení přístupových práv k prostředkům, v Azure AD, Azure a dalších online službách Microsoftu.  Přístup za běhu a možnosti upozorňování při změně role poskytované službou Azure AD PIM, kromě služby Multi-Factor Authentication a podmíněného přístupu, poskytují komplexní sadu kontrolních mechanismů, které vám pomůžou zabezpečit firemní prostředky (adresář, Role prostředků Office 365 a Azure). Stejně jako u jiných forem přístupu můžou organizace použít kontroly přístupu ke konfiguraci opakovaného ověření přístupu pro všechny uživatele v rolích správce.

## <a name="getting-started"></a>Začínáme

I když pro každého zákazníka není k dispozici žádné dokonalé řešení nebo doporučení, následující konfigurace poskytují průvodce, podle kterého vám základní zásady doporučuje, abyste zajistili bezpečnější a produktivní pracovní síly.

- [Konfigurace přístupu k identity a zařízení](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Zabezpečení privilegovaného přístupu](../users-groups-roles/directory-admin-roles-secure.md)

Můžete si také prohlédnout kartu Začínáme se zásadami **správného řízení identity** v Azure Portal, abyste mohli začít používat správu nároků, kontroly přístupu, Privileged Identity Management a podmínky použití.

![Začínáme se zásadami správného řízení identity](./media/identity-governance-overview/getting-started.png)

## <a name="next-steps"></a>Další postup

- [Co je Správa nároků Azure AD? (Preview)](entitlement-management-overview.md)
- [Co jsou kontroly přístupu Azure AD?](access-reviews-overview.md)
- [Co je Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)
- [Co můžu dělat s podmínkami použití?](active-directory-tou.md)
