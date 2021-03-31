---
title: Zásady správného řízení identity – Azure Active Directory | Microsoft Docs
description: Azure Active Directory Identity Governance vám umožní rovnováhu mezi potřebou zabezpečení a produktivity zaměstnanců pomocí správných procesů a viditelnosti.
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: e02df83d4b7874a1d158aae45f1619eb543e0aec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92362446"
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

Pro mnoho organizací je životní cyklus identit zaměstnanců vázaný na zastoupení daného uživatele v systému HCM (lidské správy velkých písmen).  Azure AD Premium automaticky zachovává identity uživatelů pro lidi reprezentované v Workday a v SuccessFactors jak v rámci služby Active Directory, tak v Azure Active Directory, jak je popsáno v tématu  [aplikace cloudového HR pro Azure Active Directory Průvodce plánováním zřizování uživatelů](../app-provisioning/plan-cloud-hr-provision.md).  Azure AD Premium také zahrnuje [Microsoft Identity Manager](/microsoft-identity-manager/), které mohou importovat záznamy z místních systémů HCM, jako jsou SAP HCM, Oracle EBusiness a Oracle PeopleSoft spouštěných místně.

Stále více scénářů vyžaduje spolupráci s lidmi mimo vaši organizaci. Spolupráce [B2B Azure AD](/azure/active-directory/b2b/) umožňuje bezpečně sdílet aplikace a služby vaší organizace s uživateli typu Host a externími partnery z jakékoli organizace a přitom zachovat kontrolu nad vašimi podnikovými daty.  [Správa opravňujících k Azure AD](entitlement-management-overview.md) umožňuje vybrat uživatele organizace, kteří můžou požádat o přístup a přidat je jako hosty do adresáře vaší organizace a zajistit, aby se tito hosté odebrali, když už nepotřebují přístup.

## <a name="access-lifecycle"></a>Životní cyklus přístupu

Organizace potřebují proces pro správu přístupu nad rámec toho, co byl poprvé zřízen pro uživatele při vytvoření identity tohoto uživatele.  Kromě toho musí být organizace v rozlehlých sítích schopné efektivně škálovat, aby bylo možné průběžně vyvíjet a vymáhat zásady přístupu a ovládací prvky.

![Životní cyklus přístupu](./media/identity-governance-overview/access-lifecycle.png)

Obvykle IT deleguje rozhodnutí o schválení přístupu u tvůrců obchodních rozhodnutí.  Kromě toho může zahrnovat samotné uživatele.  Například uživatelé, kteří mají přístup k důvěrným zákaznickým datům v obchodní aplikaci společnosti v Evropě, potřebují znát zásady společnosti. Uživatelé typu Host mohou být nevědomi požadavků na zpracování dat v organizaci, které byly pozvány.

Organizace můžou proces životního cyklu přístupu automatizovat prostřednictvím technologií, jako jsou [dynamické skupiny](../enterprise-users/groups-dynamic-membership.md), společně s zřizováním uživatelů, aby [SaaS aplikace](../saas-apps/tutorial-list.md) nebo [aplikace integrované s SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md).  Organizace také mohou určovat, kteří [Uživatelé typu Host mají přístup k místním aplikacím](../external-identities/hybrid-cloud-to-on-premises.md).  Tato přístupová práva se pak dají pravidelně kontrolovat pomocí opakujících se kontrol [přístupu Azure AD](access-reviews-overview.md).   [Správa opravňujících k Azure AD](entitlement-management-overview.md) také umožňuje definovat, jak uživatelé požadují přístup mezi balíčky členství v rámci skupiny a týmu, aplikační role a role SharePointu Online.

Když se uživatel pokusí o přístup k aplikacím, Azure AD vynutil zásady [podmíněného přístupu](../conditional-access/index.yml) . Zásady podmíněného přístupu můžou například zahrnovat zobrazení [podmínek použití](../conditional-access/terms-of-use.md) a [zajištění souhlasu uživatele s těmito podmínkami](../conditional-access/require-tou.md) předtím, než bude moci získat přístup k aplikaci.

## <a name="privileged-access-lifecycle"></a>Životní cyklus privilegovaného přístupu

V minulosti byl privilegovaný přístup popsán jinými dodavateli jako samostatnou funkcí ze zásad správného řízení identity. V Microsoftu se ale domníváme, že pro privilegovaný přístup je klíčovou součástí zásad správného řízení identity – obzvláště vzhledem k možnosti nesprávného použití přidružené k těmto právům správce může dojít k organizaci. Zaměstnanci, dodavatelé a dodavatelé, kteří přijímají oprávnění správce, musí být řízeni.

![Životní cyklus privilegovaného přístupu](./media/identity-governance-overview/privileged-access-lifecycle.png)

[Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) poskytuje další ovládací prvky přizpůsobené zabezpečení přístupových práv k prostředkům, v Azure AD, Azure a dalších online službách Microsoftu.  Přístup za běhu a možnosti upozorňování při změně role poskytované službou Azure AD PIM, kromě služby Multi-Factor Authentication a podmíněného přístupu, poskytují komplexní sadu kontrolních mechanismů, které vám pomůžou zabezpečit firemní prostředky (adresáře, Microsoft 365 a role prostředků Azure). Stejně jako u jiných forem přístupu můžou organizace použít kontroly přístupu ke konfiguraci opakovaného ověření přístupu pro všechny uživatele v rolích správce.

## <a name="governance-capabilities-in-other-azure-ad-features"></a>Možnosti zásad správného řízení v jiných funkcích služby Azure AD

Kromě výše uvedených funkcí další funkce služby Azure AD, které se často používají k poskytování scénářů zásad správného řízení identit, patří:

| Schopnost | Scenario |Funkce
| ------- | --------------------- |-----|
|Životní cyklus identity (zaměstnanci)|Správci můžou povolit zřizování uživatelských účtů z Workday nebo SuccessFactors cloudového HR nebo místního HR.|[cloudové zajištění pro zřizování uživatelů Azure AD](../app-provisioning/plan-cloud-hr-provision.md)|
|Životní cyklus identity (hosty)|Správci můžou povolit registraci samoobslužného uživatele hosta z jiného tenanta Azure AD, přímé federace, jednorázového hesla nebo účtů Google.  Uživatelé typu Host se automaticky zřídí a odúčtují v souladu se zásadami životního cyklu.|[Správa nároků](entitlement-management-overview.md) pomocí [B2B](../external-identities/what-is-b2b.md)|
|Správa nároků|Vlastníci prostředků můžou vytvářet balíčky přístupu obsahující aplikace, týmy, skupiny Azure AD a Microsoft 365 a weby SharePointu Online.|[Správa nároků](entitlement-management-overview.md)|
|Žádosti o přístup|Koncoví uživatelé můžou žádat o členství ve skupině nebo přístup k aplikacím. Koncoví uživatelé, včetně hostů z jiných organizací, můžou požádat o přístup k balíčkům přístupu.|[Správa nároků](entitlement-management-overview.md)|
|Pracovní postup|Vlastníci prostředků mohou definovat schvalovatele a schvalovatele eskalace pro žádosti o přístup a schvalovatele pro žádosti o aktivaci role.  |[Správa nároků](entitlement-management-overview.md) a [PIM](../privileged-identity-management/pim-configure.md)|
|Zásady a Správa rolí|Správce může definovat zásady podmíněného přístupu pro přístup k aplikacím v době běhu.  Vlastníci prostředků můžou definovat zásady přístupu uživatele prostřednictvím balíčků přístupu.|Zásady pro [podmíněný přístup](../conditional-access/overview.md) a [oprávnění pro správu](entitlement-management-overview.md)|
|Přístup k certifikaci|Správci můžou povolit opakovaný přístup k opětovné certifikaci pro: aplikace SaaS nebo členství ve skupinách cloudu, přiřazení rolí prostředků Azure AD nebo Azure. Automaticky odeberte přístup k prostředkům, zablokujte přístup hostů a odstraňte účty hostů.|Kontroly [přístupu](access-reviews-overview.md), také Surface v [PIM](../privileged-identity-management/pim-how-to-start-security-review.md)|
|Plnění a zřizování|Automatické zřizování a rušení zřizování v aplikacích připojených k Azure AD, včetně přes SCIM a na weby SharePointu Online. |[zřizování uživatelů](../app-provisioning/user-provisioning.md)|
|Vytváření sestav a analýza|Správci mohou načíst protokoly auditu nedávných uživatelských zřízení a přihlašovacích aktivit. Integrace s Azure Monitor a "kdo má přístup" prostřednictvím balíčků přístupu.|Sestavy a [monitorování](../reports-monitoring/overview-monitoring.md) [Azure AD](../reports-monitoring/overview-reports.md)|
|Privilegovaný přístup|Pracovní postupy schvalování pro role Azure AD (včetně vlastních rolí) a role prostředků Azure, a to za běhu a plánovaného přístupu.|[Azure AD PIM](../privileged-identity-management/pim-configure.md)|
|Auditování|Správci mohou být upozorňováni na vytváření účtů správce.|[Výstrahy Azure AD PIM](../privileged-identity-management/pim-how-to-configure-security-alerts.md)|

## <a name="getting-started"></a>Začínáme

Pokud chcete začít používat správu nároků, kontroly přístupu, Privileged Identity Management a Podmínky použití, přečtěte si na kartě Začínáme **řízení identit** v Azure Portal.

![Začínáme se zásadami správného řízení identity](./media/identity-governance-overview/getting-started.png)


Pokud máte zpětnou vazbu k funkcím zásad správného řízení identit, klikněte na získat **zpětnou vazbu?** v Azure Portal odešlete svůj názor. Tým pravidelně kontroluje vaše názory.

I když pro každého zákazníka není k dispozici žádné dokonalé řešení nebo doporučení, poskytují následující konfigurační příručky také základní zásady, které společnost Microsoft doporučuje, abyste zajistili bezpečnější a produktivní pracovní síly.

- [Konfigurace identit a přístupu k zařízením](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Zabezpečení privilegovaného přístupu](../roles/security-planning.md)

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
- [Co můžu dělat s podmínkami použití?](../conditional-access/terms-of-use.md)