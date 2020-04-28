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
ms.date: 10/24/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd319dd6a83a392f6df26d07a58be22a9c8bdb61
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77063675"
---
# <a name="what-is-azure-ad-identity-governance"></a>Co je Azure AD Identity Governance?

Zásady správného řízení identity Azure Active Directory (Azure AD) umožňují vyrovnávat potřebu organizace potřebnou k zajištění produktivity zabezpečení a zaměstnanců pomocí správných procesů a viditelnosti. Poskytuje funkce, které zajistí, aby měli správná přístupová práva ke správným prostředkům. Tyto a související funkce Azure AD a Enterprise Mobility + Security umožňují zmírnit riziko přístupu ochranou, sledováním a auditováním přístupu k důležitým prostředkům – a přitom zajistit produktivitu zaměstnanců a obchodních partnerů.  

Zásady správného řízení identity umožňují organizacím provádět následující úkoly napříč zaměstnanci, obchodními partnery a dodavateli a napříč službami a aplikacemi jak místně, tak i v cloudech:

- Řídit životní cyklus identity
- Řízení životního cyklu přístupu
- Zabezpečený privilegovaný přístup pro správu

Konkrétně je určena k tomu, aby organizacím pomáhaly řešit tyto čtyři klíčové otázky:

- Kteří uživatelé by měli mít přístup k jakým prostředkům?
- Jaké jsou uživatelé s tímto přístupem?
- Existují efektivní organizační ovládací prvky pro správu přístupu?
- Mohou Auditori ověřit, zda ovládací prvky fungují?

## <a name="identity-lifecycle"></a>Životní cyklus identity

Řízení identit pomáhá organizacím dosáhnout rovnováhy mezi *produktivitou* – jak rychle může osoba získat přístup k potřebným prostředkům, například když se připojí k mojí organizaci? A *zabezpečení* – jak se má v průběhu času měnit přístup, například kvůli změnám stavu zaměstnanosti této osoby?  Správa životního cyklu identit je základem pro řízení identit a efektivní řízení škálování vyžaduje modernizaci infrastrukturu správy životního cyklu identit pro aplikace.

![Životní cyklus identity](./media/identity-governance-overview/identity-lifecycle.png)

Pro mnoho organizací je životní cyklus identit zaměstnanců vázaný na zastoupení daného uživatele v systému HCM (lidské správy velkých písmen).  Azure AD Premium automaticky zachovává identity uživatelů v Workday v rámci služby Active Directory a Azure Active Directory, jak je popsáno v [kurzu pro příchozí zřizování v Workday](../saas-apps/workday-inbound-tutorial.md).  Azure AD Premium také zahrnuje [Microsoft Identity Manager](/microsoft-identity-manager/), které mohou importovat záznamy z místních systémů HCM, jako jsou SAP, Oracle EBusiness a Oracle PeopleSoft spouštěných místně.

Stále více scénářů vyžaduje spolupráci s lidmi mimo vaši organizaci. Spolupráce [B2B Azure AD](/azure/active-directory/b2b/) umožňuje bezpečně sdílet aplikace a služby vaší organizace s uživateli typu Host a externími partnery z jakékoli organizace a přitom zachovat kontrolu nad vašimi podnikovými daty.  [Správa opravňujících k Azure AD](entitlement-management-overview.md) umožňuje vybrat uživatele organizace, kteří můžou požádat o přístup a přidat je jako hosty do adresáře vaší organizace a zajistit, aby se tito hosté odebrali, když už nepotřebují přístup.

## <a name="access-lifecycle"></a>Životní cyklus přístupu

Organizace potřebují proces pro správu přístupu nad rámec toho, co byl poprvé zřízen pro uživatele při vytvoření identity tohoto uživatele.  Kromě toho musí být organizace v rozlehlých sítích schopné efektivně škálovat, aby bylo možné průběžně vyvíjet a vymáhat zásady přístupu a ovládací prvky.

![Životní cyklus přístupu](./media/identity-governance-overview/access-lifecycle.png)

Obvykle IT deleguje rozhodnutí o schválení přístupu u tvůrců obchodních rozhodnutí.  Kromě toho může zahrnovat samotné uživatele.  Například uživatelé, kteří mají přístup k důvěrným zákaznickým datům v obchodní aplikaci společnosti v Evropě, potřebují znát zásady společnosti. Uživatelé typu Host mohou být nevědomi požadavků na zpracování dat v organizaci, které byly pozvány.

Organizace můžou proces životního cyklu přístupu automatizovat prostřednictvím technologií, jako jsou [dynamické skupiny](../users-groups-roles/groups-dynamic-membership.md), společně s zřizováním uživatelů, aby [SaaS aplikace](../saas-apps/tutorial-list.md) nebo [aplikace integrované s SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md).  Organizace také mohou určovat, kteří [Uživatelé typu Host mají přístup k místním aplikacím](../b2b/hybrid-cloud-to-on-premises.md).  Tato přístupová práva se pak dají pravidelně kontrolovat pomocí opakujících se kontrol [přístupu Azure AD](access-reviews-overview.md).   [Správa opravňujících k Azure AD](entitlement-management-overview.md) také umožňuje definovat, jak uživatelé požadují přístup mezi balíčky členství v rámci skupiny a týmu, aplikační role a role SharePointu Online.

Když se uživatel pokusí o přístup k aplikacím, Azure AD vynutil zásady [podmíněného přístupu](/azure/active-directory/conditional-access/) . Zásady podmíněného přístupu můžou například zahrnovat zobrazení [podmínek použití](../conditional-access/terms-of-use.md) a [zajištění souhlasu uživatele s těmito podmínkami](../conditional-access/require-tou.md) předtím, než bude moci získat přístup k aplikaci.

## <a name="privileged-access-lifecycle"></a>Životní cyklus privilegovaného přístupu

V minulosti byl privilegovaný přístup popsán jinými dodavateli jako samostatnou funkcí ze zásad správného řízení identity. V Microsoftu se ale domníváme, že pro privilegovaný přístup je klíčovou součástí zásad správného řízení identity – obzvláště vzhledem k možnosti nesprávného použití přidružené k těmto právům správce může dojít k organizaci. Zaměstnanci, dodavatelé a dodavatelé, kteří přijímají oprávnění správce, musí být řízeni.

![Životní cyklus privilegovaného přístupu](./media/identity-governance-overview/privileged-access-lifecycle.png)

[Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) poskytuje další ovládací prvky přizpůsobené zabezpečení přístupových práv k prostředkům, v Azure AD, Azure a dalších online službách Microsoftu.  Přístup za běhu a možnosti upozorňování při změně role poskytované službou Azure AD PIM, kromě služby Multi-Factor Authentication a podmíněného přístupu, poskytují komplexní sadu kontrolních mechanismů, které vám pomůžou zabezpečit firemní prostředky (adresáře, Office 365 a role prostředků Azure). Stejně jako u jiných forem přístupu můžou organizace použít kontroly přístupu ke konfiguraci opakovaného ověření přístupu pro všechny uživatele v rolích správce.

## <a name="getting-started"></a>Začínáme

Pokud chcete začít používat správu nároků, kontroly přístupu, Privileged Identity Management a Podmínky použití, přečtěte si na kartě Začínáme **řízení identit** v Azure Portal.

![Začínáme se zásadami správného řízení identity](./media/identity-governance-overview/getting-started.png)


Pokud máte zpětnou vazbu k funkcím zásad správného řízení identit, klikněte na získat **zpětnou vazbu?** v Azure Portal odešlete svůj názor. Tým pravidelně kontroluje vaše názory.

I když pro každého zákazníka není k dispozici žádné dokonalé řešení nebo doporučení, poskytují následující konfigurační příručky také základní zásady, které společnost Microsoft doporučuje, abyste zajistili bezpečnější a produktivní pracovní síly.

- [Konfigurace identit a přístupu k zařízením](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Zabezpečení privilegovaného přístupu](../users-groups-roles/directory-admin-roles-secure.md)

## <a name="appendix---least-privileged-roles-for-managing-in-identity-governance-features"></a>Příloha – nejnižší privilegované role pro správu v funkcích zásad správného řízení identity

Osvědčeným postupem je použití minimální privilegované role k provádění úloh správy v řízení identit. K provedení těchto úloh doporučujeme použít Azure AD PIM k aktivaci role podle potřeby. Níže jsou uvedené nejnižší privilegované role adresáře ke konfiguraci funkcí zásad správného řízení identit:

| Funkce | Nejnižší privilegovaná role |
| ------- | --------------------- |
| Správa nároků | Správce uživatelů (s výjimkou přidávání webů SharePointu Online do katalogů, které vyžadují globálního správce) |
| Kontroly přístupu | Správce uživatelů (s výjimkou kontrol přístupu pro role Azure nebo Azure AD, které vyžadují správce privilegovaných rolí) |
|Privileged Identity Management | Správce privilegovaných rolí |
| Podmínky použití | Správce zabezpečení nebo správce podmíněného přístupu |

## <a name="next-steps"></a>Další kroky

- [Co je správa nároků Azure AD?](entitlement-management-overview.md)
- [Co jsou kontroly přístupu Azure AD?](access-reviews-overview.md)
- [Co je Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)
- [Co můžu dělat s podmínkami použití?](active-directory-tou.md)


