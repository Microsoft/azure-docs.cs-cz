---
title: Zásady správného řízení identit – služba Azure Active Directory | Dokumenty společnosti Microsoft
description: Azure Active Directory Identity Governance umožňuje vyvážit potřebu vaší organizace pro zabezpečení a produktivitu zaměstnanců se správnými procesy a viditelností.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063675"
---
# <a name="what-is-azure-ad-identity-governance"></a>Co je Azure AD Identity Governance?

Azure Active Directory (Azure AD) Identity Governance umožňuje vyvážit potřebu vaší organizace pro zabezpečení a produktivitu zaměstnanců se správnými procesy a viditelností. Poskytuje vám možnosti, které zajistí, že ti praví lidé budou mít správný přístup ke správným zdrojům. Tyto a související funkce Azure AD a Enterprise Mobility + Security umožňují zmírnit riziko přístupu ochranou, monitorováním a auditováním přístupu ke kritickým prostředkům – a současně zajišťují produktivitu zaměstnanců a obchodních partnerů.  

Zásady správného řízení identit poskytují organizacím možnost dělat následující úkoly napříč zaměstnanci, obchodními partnery a dodavateli a napříč službami a aplikacemi jak místně, tak v cloudech:

- Řídit životní cyklus identity
- Řídit životní cyklus přístupu
- Zabezpečený privilegovaný přístup pro správu

Konkrétně je určena k tomu, aby pomohla organizacím řešit tyto čtyři klíčové otázky:

- Kteří uživatelé by měli mít přístup ke kterým prostředkům?
- Co dělají ti uživatelé s tímto přístupem?
- Existují účinné organizační kontroly pro správu přístupu?
- Mohou auditoři ověřit, zda ovládací prvky fungují?

## <a name="identity-lifecycle"></a>Životní cyklus identity

Zásady správného řízení identit pomáhá organizacím dosáhnout rovnováhy mezi *produktivitou* – Jak rychle může mít osoba přístup k prostředkům, které potřebuje, například když se připojí k mé organizaci? A *bezpečnost* - Jak by se měl jejich přístup v průběhu času měnit, například kvůli změnám v zaměstnaneckém statusu této osoby?  Správa životního cyklu identitje základem pro zásady správného řízení identit a efektivní zásady správného řízení ve velkém měřítku vyžadují modernizaci infrastruktury správy životního cyklu identit pro aplikace.

![Životní cyklus identity](./media/identity-governance-overview/identity-lifecycle.png)

Pro mnoho organizací je životní cyklus identity pro zaměstnance vázán na reprezentaci tohoto uživatele v systému HCM (human capital management).  Azure AD Premium automaticky udržuje identity uživatelů pro uživatele reprezentované v Workday ve službě Active Directory a Azure Active Directory, jak je popsáno v [kurzu zřizování příchozí pracovní den](../saas-apps/workday-inbound-tutorial.md).  Azure AD Premium také zahrnuje [Microsoft Identity Manager](/microsoft-identity-manager/), který můžete importovat záznamy z místních systémů HCM, jako je SAP, Oracle eBusiness a Oracle PeopleSoft.

Scénáře stále více vyžadují spolupráci s lidmi mimo vaši organizaci. [Spolupráce Azure AD B2B](/azure/active-directory/b2b/) umožňuje bezpečně sdílet aplikace a služby vaší organizace s uživateli typu Host a externími partnery z libovolné organizace při zachování kontroly nad vlastními podnikovými daty.  [Správa nároků Azure AD](entitlement-management-overview.md) umožňuje vybrat, kteří uživatelé organizace mohou požádat o přístup a být přidáni jako hosté B2B do adresáře vaší organizace, a zajistí, že tito hosté budou odebráni, když už přístup nepotřebují.

## <a name="access-lifecycle"></a>Životní cyklus přístupu

Organizace potřebují proces pro správu přístupu nad rámec toho, co bylo původně zřízeno pro uživatele, když byla vytvořena identita tohoto uživatele.  Kromě toho musí být podnikové organizace schopny efektivně škálovat, aby mohly průběžně vyvíjet a vynucovat zásady přístupu a ovládací prvky.

![Životní cyklus přístupu](./media/identity-governance-overview/access-lifecycle.png)

It obvykle deleguje rozhodnutí o schválení přístupu na osoby s rozhodovací pravomocí.  Kromě toho může IT zapojit samotné uživatele.  Například uživatelé, kteří mají přístup k důvěrným zákaznickým datům v marketingové aplikaci společnosti v Evropě, potřebují znát zásady společnosti. Uživatelé typu Host nemusí vědět o požadavcích na zpracování dat v organizaci, do které byli pozváni.

Organizace mohou automatizovat proces životního cyklu přístupu prostřednictvím technologií, jako jsou [dynamické skupiny](../users-groups-roles/groups-dynamic-membership.md), spolu s zřizováním uživatelů do [aplikací SaaS](../saas-apps/tutorial-list.md) nebo [aplikací integrovaných s SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md).  Organizace mohou také řídit, kteří [uživatelé typu Host mají přístup k místním aplikacím](../b2b/hybrid-cloud-to-on-premises.md).  Tato přístupová práva pak můžete pravidelně kontrolovat pomocí opakovaných [kontrol přístupu Azure AD](access-reviews-overview.md).   [Správa oprávnění Azure AD](entitlement-management-overview.md) také umožňuje definovat, jak uživatelé požadují přístup mezi balíčky členství ve skupinách a týmech, role mise a role SharePointu Online.

Když se uživatel pokusí o přístup k aplikacím, Azure AD vynucuje zásady [podmíněného přístupu.](/azure/active-directory/conditional-access/) Zásady podmíněného přístupu mohou například zahrnovat zobrazení [podmínek použití](../conditional-access/terms-of-use.md) a [zajištění toho, aby uživatel souhlasil s těmito podmínkami](../conditional-access/require-tou.md) před přístupem k aplikaci.

## <a name="privileged-access-lifecycle"></a>Životní cyklus privilegovaného přístupu

Historicky privilegovaný přístup byl popsán jinými dodavateli jako samostatnou funkci od zásad správného řízení identity. Ve společnosti Microsoft si však myslíme, že řízení privilegovaného přístupu je klíčovou součástí správy identit – zejména s ohledem na možnost zneužití spojeného s těmito právy správce může způsobit organizaci. Je třeba se řídit zaměstnanci, dodavateli a dodavateli, kteří přebírají práva na správu.

![Životní cyklus privilegovaného přístupu](./media/identity-governance-overview/privileged-access-lifecycle.png)

[Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) poskytuje další ovládací prvky přizpůsobené zabezpečení přístupových práv pro prostředky, napříč Azure AD, Azure a dalšími službami Microsoft Online Services.  Možnosti upozornění na just-in-time a změny rolí poskytované službou Azure AD PIM poskytují kromě vícefaktorového ověřování a podmíněného přístupu komplexní sadu ovládacích prvků zásad správného řízení, které pomáhají zabezpečit prostředky vaší společnosti (adresář, Role prostředků Office 365 a Azure). Stejně jako u jiných forem přístupu mohou organizace pomocí kontrol přístupu konfigurovat opakovanou opětovnou certifikaci přístupu pro všechny uživatele v rolích správce.

## <a name="getting-started"></a>Začínáme

Podívejte se na kartu Začínáme se **zásadami správného řízení identit** na webu Azure portal a začněte používat správu nároků, kontroly přístupu, správu privilegovaných identit a podmínky použití.

![Začínáme s řízením identity](./media/identity-governance-overview/getting-started.png)


Pokud máte nějakou zpětnou vazbu o funkcích zásad správného řízení identit, klikněte na **tlačítko Získat zpětnou vazbu?** na webu Azure Portal a odešlete svůj názor. Tým pravidelně kontroluje vaši zpětnou vazbu.

I když neexistuje žádné dokonalé řešení nebo doporučení pro každého zákazníka, následující konfigurační příručky také poskytují základní zásady, které společnost Microsoft doporučuje dodržovat, aby zajistila bezpečnější a produktivnější pracovní sílu.

- [Konfigurace identit a přístupu k zařízením](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Zabezpečení privilegovaného přístupu](../users-groups-roles/directory-admin-roles-secure.md)

## <a name="appendix---least-privileged-roles-for-managing-in-identity-governance-features"></a>Dodatek - nejméně privilegované role pro správu funkcí zásad správného řízení identity

Je osvědčeným postupem použít nejméně privilegovanou roli k provádění úloh správy v identity governance. Doporučujeme použít Azure AD PIM aktivovat roli podle potřeby k provedení těchto úkolů. Níže jsou uvedeny nejméně privilegované role adresáře pro konfiguraci funkcí zásad správného řízení identit:

| Funkce | Nejméně privilegovaná role |
| ------- | --------------------- |
| Správa nároků | Správce uživatelů (s výjimkou přidání webů SharePointu Online do katalogů, které vyžadují globálního správce) |
| Kontroly přístupu | Správce uživatelů (s výjimkou kontrol přístupu rolí Azure nebo Azure AD, který vyžaduje správce privilegovaných rolí) |
|Privileged Identity Management | Správce privilegovaných rolí |
| Podmínky použití | Správce zabezpečení nebo správce podmíněného přístupu |

## <a name="next-steps"></a>Další kroky

- [Co je správa nároků Azure AD?](entitlement-management-overview.md)
- [Co jsou kontroly přístupu Azure AD?](access-reviews-overview.md)
- [Co je Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)
- [Co můžu dělat s podmínkami použití?](active-directory-tou.md)


