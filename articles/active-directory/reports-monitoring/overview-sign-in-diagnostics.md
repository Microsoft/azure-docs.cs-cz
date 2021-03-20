---
title: Co je diagnostika přihlášení pro Azure Active Directory?
description: Poskytuje obecný přehled diagnostiky přihlášení v Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: e2b3d8ce-708a-46e4-b474-123792f35526
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 12/15/2020
ms.author: markvi
ms.reviewer: tspring
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdef3e1f1a60c9eb0c751855837e9cbe77e015e9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98572285"
---
# <a name="what-is-the-sign-in-diagnostic-in-azure-ad"></a>Jaká je diagnostika přihlášení ve službě Azure AD?

Azure Active Directory (Azure AD) poskytuje flexibilní model zabezpečení, který řídí, co můžou uživatelé dělat se spravovanými prostředky. Přístup k těmto prostředkům se řídí nejen tím, *kdo* jsou, ale také podle *toho, jak* k nim přistupuje. Flexibilní model se obvykle dodává s určitou mírou složitosti z důvodu počtu možností konfigurace, které máte. Složitost má potenciál zvýšit riziko chyb.

Jako správce IT budete potřebovat řešení, které vám poskytne přehled o aktivitách v systému. Tato viditelnost vám umožní diagnostikovat a řešit problémy, když k nim dojde. Diagnostika přihlášení pro Azure AD je příkladem takového řešení. Pomocí diagnostiky můžete analyzovat, co se stalo při pokusu o přihlášení, a získat doporučení pro řešení problémů, aniž byste museli zahrnovat podporu Microsoftu.

Tento článek vám poskytne přehled o tom, co řešení dělá a jak ho můžete používat.

## <a name="requirements"></a>Požadavky

Diagnostika přihlášení je dostupná ve všech edicích služby Azure AD.

Abyste ho mohli používat, musíte být globálním správcem ve službě Azure AD.

## <a name="how-it-works"></a>Jak to funguje

V Azure AD je odpověď na pokus o přihlášení vázaná na to *, kdo* se přihlašuje a *jak* přistupují k tenantovi. Správce může například během přihlašování z podnikové sítě obvykle nakonfigurovat všechny aspekty tenanta. Ale stejný uživatel může být zablokován, když se přihlásí pomocí stejného účtu z nedůvěryhodné sítě.

Vzhledem k větší flexibilitě systému, který reaguje na pokus o přihlášení, se můžete setkat ve scénářích, kdy potřebujete řešit problémy s přihlášením. Diagnostika přihlášení je funkce, která:

- Analyzuje data z událostí přihlášení.

- Zobrazuje, co se stalo.

- Poskytuje doporučení pro řešení problémů.

Diagnostika přihlášení pro Azure AD je navržená tak, aby umožňovala samočinnou diagnostiku chyb přihlášení. K dokončení procesu diagnostiky musíte provést tyto kroky:

![Diagram znázorňující diagnostiku přihlášení](./media/overview-sign-in-diagnostics/process.png)

1. Definujte rozsah událostí přihlášení, které vás zajímají.

2. Vyberte přihlášení, které chcete zkontrolovat.

3. Zkontrolujte výsledky diagnostiky.

4. Proveďte akci.

### <a name="define-scope"></a>Definovat obor

Cílem tohoto kroku je definovat rozsah událostí přihlášení, které se mají prozkoumat. Váš rozsah je založený na uživateli nebo identifikátoru (ID korelace, requestId) a časový rozsah. Pokud chcete rozšířit rozsah dalšího rozsahu, můžete zadat název aplikace. Azure AD používá informace o oboru k vyhledání správných událostí.  

### <a name="select-sign-in"></a>Vybrat přihlášení  

Na základě vašich kritérií hledání Azure AD načte všechny vyhovující přihlašovací události a prezentuje je v zobrazení seznamu Shrnutí ověřování.

![Částečný snímek obrazovky s oddílem souhrn ověřování](./media/overview-sign-in-diagnostics/authentication-summary.png)

Sloupce zobrazené v tomto zobrazení můžete přizpůsobit.

### <a name="review-diagnostic"></a>Zkontrolovat diagnostiku

Pro vybranou událost přihlášení vám Azure AD poskytne výsledky diagnostiky.

![Částečný snímek obrazovky, který ukazuje oddíl výsledků diagnostiky](./media/overview-sign-in-diagnostics/diagnostics-results.png)

Tyto výsledky začínají posouzením, což vysvětluje, co se stalo v několika větách. Vysvětlení vám pomůže pochopit chování systému.

V dalším kroku získáte souhrnné informace o souvisejících zásadách podmíněného přístupu, které byly aplikovány na vybranou událost přihlášení. Diagnostické výsledky také obsahují doporučené kroky nápravy, které vám pomohly problém vyřešit. Vzhledem k tomu, že není vždy možné problémy vyřešit bez další pomoci, může být Doporučeným krokem otevření lístku podpory.

### <a name="take-action"></a>Provést akci

V tomto okamžiku byste měli mít k dispozici informace, které potřebujete k vyřešení vašeho problému.

## <a name="scenarios"></a>Scénáře

Následující scénáře jsou pokryté diagnostikou přihlášení:

- Blokováno podmíněným přístupem

- Neúspěšný podmíněný přístup

- Vícefaktorové ověřování (MFA) z podmíněného přístupu

- MFA z jiných požadavků

- Vyžaduje se ověření MFA.

- Požadováno ověření MFA (rizikové přihlašovací umístění)

- Úspěšné přihlášení

### <a name="blocked-by-conditional-access"></a>Blokováno podmíněným přístupem

V tomto scénáři byl pokus o přihlášení zablokován zásadou podmíněného přístupu.

![Snímek obrazovky zobrazující konfiguraci přístupu s vybraným přístupovým blokem](./media/overview-sign-in-diagnostics/block-access.png)

Část Diagnostika v tomto scénáři zobrazuje podrobnosti o události přihlášení uživatele a použitých zásadách.

### <a name="failed-conditional-access"></a>Neúspěšný podmíněný přístup

Tento scénář je obvykle důsledkem pokusu o přihlášení, které selhalo, protože nebyly splněny požadavky zásad podmíněného přístupu. Obvyklými příklady jsou:

![Snímek obrazovky se zobrazením konfigurace přístupu s příklady běžných zásad a vybraným oprávněním pro udělení přístupu](./media/overview-sign-in-diagnostics/require-controls.png)

- Vyžadovat zařízení připojené k hybridní službě Azure AD

- Vyžadovat klientskou aplikaci schválenou

- Vyžadování zásad ochrany aplikací

Část Diagnostika pro tento scénář zobrazuje podrobnosti o pokusu o přihlášení uživatele a použitých zásadách.

### <a name="mfa-from-conditional-access"></a>MFA z podmíněného přístupu

V tomto scénáři mají zásady podmíněného přístupu požadavek na přihlášení pomocí vícefaktorového ověřování sady.

![Snímek obrazovky se zobrazenou konfigurací přístupu s použitím vícefaktorového ověřování](./media/overview-sign-in-diagnostics/require-mfa.png)

Část Diagnostika pro tento scénář zobrazuje podrobnosti o pokusu o přihlášení uživatele a použitých zásadách.

### <a name="mfa-from-other-requirements"></a>MFA z jiných požadavků

V tomto scénáři se zásady podmíněného přístupu nenakonfigurovaly na požadavek vícefaktorového ověřování. Například vícefaktorové ověřování na základě jednotlivých uživatelů.

![Snímek obrazovky s vícefaktorového ověřováním na konfiguraci uživatele](./media/overview-sign-in-diagnostics/mfa-per-user.png)

Záměrem tohoto diagnostického scénáře je poskytnout další podrobnosti o:

- Zdroj přerušení vícefaktorového ověřování
- Výsledek interakce klienta

Můžete si také zobrazit všechny podrobnosti o pokusu o přihlášení uživatele.

### <a name="mfa-proof-up-required"></a>Vyžaduje se ověření MFA.

V tomto scénáři se pokusy o přihlášení přerušily požadavky na nastavení vícefaktorového ověřování. Tato instalace se označuje také jako prokazování.

K prokázání vícefaktorového ověřování dojde, když je uživatel nutný k použití vícefaktorového ověřování, ale ještě ho ještě nenakonfiguroval, nebo Správce požadoval, aby ji nakonfigurovali uživatel.

Účelem tohoto diagnostického scénáře je odhalit, že přerušení vícefaktorového ověřování bylo způsobeno nedostatkem konfigurace uživatele. Doporučené řešení je pro uživatele k dokončení kontroly.

### <a name="mfa-proof-up-required-risky-sign-in-location"></a>Požadováno ověření MFA (rizikové přihlašovací umístění)

V tomto scénáři se pokusy o přihlášení přerušily požadavkem na nastavení vícefaktorového ověřování z rizikového přihlašování.

Účelem tohoto diagnostického scénáře je odhalit, že přerušení vícefaktorového ověřování bylo způsobeno nedostatkem konfigurace uživatele. Doporučené řešení je pro uživatele, aby dokončí zkušební kontrolu, konkrétně ze síťového umístění, které se nejeví jako rizikové.

Pokud je například podniková síť definovaná jako pojmenované umístění, měl by se uživatel pokusit provést kontrolu v podnikové síti místo toho.

### <a name="successful-sign-in"></a>Úspěšné přihlášení

V tomto scénáři se události přihlášení nepřerušily pomocí podmíněného přístupu nebo vícefaktorového ověřování.

Tento diagnostický scénář poskytuje podrobné informace o událostech přihlašování uživatelů, u kterých se očekává, že budou přerušeny kvůli zásadám podmíněného přístupu nebo vícefaktorového ověřování.

## <a name="next-steps"></a>Další kroky

- [Co jsou sestavy služby Azure Active Directory?](overview-reports.md)
- [Co je monitorování služby Azure Active Directory?](overview-monitoring.md)
