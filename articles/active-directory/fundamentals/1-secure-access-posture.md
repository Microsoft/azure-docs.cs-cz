---
title: Určení stav zabezpečení pro externí spolupráci pomocí Azure Active Directory
description: Než budete moct spustit plán zabezpečení externího přístupu, musíte určit, co se snažíte dosáhnout.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37c27e84f15a01a2d8832baae137518685de59a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98725437"
---
# <a name="determine-your-security-posture-for-external-access"></a>Určení stav zabezpečení pro externí přístup 

V případě, že budete uvažovat o řízení přístupu k externím přístupům, budete muset posuzovat požadavky na zabezpečení a spolupráci pro celou organizaci a v rámci každého scénáře. Na úrovni organizace Vezměte v úvahu množství kontroly, které potřebujete, aby váš IT tým měl v průběhu každodenní spolupráce. Organizace v regulovaných odvětvích mohou vyžadovat více IT řízení. Například může být potřeba, aby dodavatel obrany jednoznačně identifikoval a zdokumentoval jednotlivé externí uživatele, jejich přístup a odebrání přístupu. Tento požadavek může být na všech přístupech nebo konkrétních scénářích nebo úlohách. Na druhém konci spektra může konzultační společnost obecně dovolit koncovým uživatelům určit externí uživatele, se kterými potřebují spolupracovat, v rámci určité kolejnice ochrany IT. 

![TÍM i koncovým uživatelům řídí spolupráci](media/secure-external-access/1-overall-control.png)

> [!NOTE]
> Vysoce těsné řízení spolupráce může vést k vyššímu počtu rozpočtů v IT, nižší produktivitě a opožděným obchodním výsledkům. Když jsou oficiální kanály pro spolupráci vnímané jako moc náročné, koncoví uživatelé se od těch uvedených systémů dostanou, aby získali své úlohy, a to například e-mailem nezabezpečených dokumentů.

## <a name="think-in-terms-of-scenarios"></a>Představte si, co se týče scénářů

V mnoha případech může delegovat přístup k partnerům aspoň v některých scénářích a zároveň zajistit bezpečnostní zábradlí pro zabezpečení. Zábradlí ochrany IT může pomoct zajistit, aby duševní vlastnictví zůstalo v bezpečí, zatímco zaměstnanci můžou spolupracovat na práci s partnery.

Při zvažování scénářů v rámci vaší organizace posuzujeme potřebu zaměstnanců v obchodní partnerovi přístup k prostředkům. Banka může mít požadavky na dodržování předpisů, které omezují přístup k určitým prostředkům, jako jsou informace o uživatelských účtech, do malých skupin interních zaměstnanců. Naopak stejná banka může povolit delegovaný přístup pro partnery pracující na marketingové kampani.

![Continuum zásad správného řízení na scénář](media\secure-external-access\1-scenarios.png)

V každém scénáři zvažte 

* Citlivost informací, které jsou ohroženy

* bez ohledu na to, jestli potřebujete omezit, co můžou partneři vidět na jiných uživatelích

* náklady na porušení a váhu centralizovaného řízení a tření koncového uživatele

 Můžete také začít s centrálně spravovanými ovládacími prvky pro splnění cílů dodržování předpisů a delegovat řízení koncovým uživatelům v průběhu času. Všechny modely správy přístupu můžou existovat současně v rámci organizace. 

Použití [spravovaných přihlašovacích údajů pro partnery](../external-identities/what-is-b2b.md) poskytuje vaší organizaci nezbytný signál, který ukončí přístup k prostředkům, jakmile externí uživatel ztratí přístup k prostředkům své vlastní společnosti.

## <a name="goals-of-securing-external-access"></a>Cíle zabezpečení externího přístupu

Cíle řízeného a delegovaného přístupu se liší.

**K hlavním cílům přístupu řízeným IT patří:**

* Splnění cílů zásad správného řízení, regulativního a dodržování předpisů (GRC). 

* Spolehlivě kontrolujte přístup k partnerům a informace o tom, co můžou partneři vidět na členských uživatelích, skupinách a dalších partnerech.

**Primárními cíli delegování přístupu je:**

* V rámci omezení IT umožníte vlastníkům obchodních aplikací řídit, se kterými budou spolupracovat.

* Umožněte obchodním partnerům požádat o přístup na základě pravidel definovaných obchodními vlastníky.

Podle toho, co jste přijali ve vaší organizaci a scénářích, budete potřebovat: 

* **Řízení přístupu k aplikacím, datům a obsahu**. To je možné dosáhnout různými způsoby v závislosti na vašich verzích [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) a [Microsoft 365](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans). 

* **Snižte plochu pro útok**. [Privileged Identity Management](../privileged-identity-management/pim-configure.md), [Ochrana před únikem informací (DLP)](/exchange/security-and-compliance/data-loss-prevention/data-loss-prevention) a [Možnosti šifrování](/exchange/security-and-compliance/data-loss-prevention/data-loss-prevention) snižují prostor pro útoky.

* **Pravidelně kontrolujte protokol aktivit a auditu a potvrďte dodržování předpisů**. Může delegovat rozhodování o přístupu pro vlastníky podniku prostřednictvím správy nároků, zatímco kontroly přístupu poskytují způsob, jak pravidelně potvrdit pokračování přístupu. Automatizovaná klasifikace dat s popisky citlivosti pomáhá automatizovat šifrování citlivého obsahu, aby mohli zaměstnanci koncoví uživatelé snadno dodržovat.

## <a name="next-steps"></a>Další kroky 

Další informace najdete v následujících článcích o zabezpečení externího přístupu k prostředkům. Doporučujeme, abyste provedli akce v uvedeném pořadí.

1. [Určete stav zabezpečení pro externí přístup](1-secure-access-posture.md) (jste tady.)

2. [Zjistit aktuální stav](2-secure-access-current-state.md)

3. [Vytvoření plánu zásad správného řízení](3-secure-access-plan.md)

4. [Použití skupin pro zabezpečení](4-secure-access-groups.md)

5. [Přechod do Azure AD B2B](5-secure-access-b2b.md)

6. [Zabezpečený přístup se správou oprávnění](6-secure-access-entitlement-managment.md)

7. [Zabezpečený přístup pomocí zásad podmíněného přístupu](7-secure-access-conditional-access.md)

8. [Zabezpečený přístup s popisky citlivosti](8-secure-access-sensitivity-labels.md)

9. [Zabezpečený přístup k Microsoft teams, OneDrivu a SharePointu](9-secure-access-teams-sharepoint.md)
 

