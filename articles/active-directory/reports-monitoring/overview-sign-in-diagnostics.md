---
title: Co je diagnostika přihlašování ve službě Azure AD? | Dokumentace Microsoftu
description: Poskytuje obecný přehled diagnostiky přihlášení ve službě Azure AD.
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
ms.openlocfilehash: d6aedf41fbf1ed0d70467a2efe97431fdecaa4fa
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97585904"
---
# <a name="what-is-sign-in-diagnostic-in-azure-ad"></a>Co je diagnostika přihlašování ve službě Azure AD?

Azure AD poskytuje flexibilní model zabezpečení, který umožňuje řídit, co můžou uživatelé dělat se spravovanými prostředky. Přístup k těmto prostředkům není řízen pouze tím **, kdo** jste, ale také **způsobem, jak** k nim přistupujete. Flexibilita se obvykle dodává spolu s určitou mírou složitosti z důvodu počtu možností konfigurace, které máte. Složitost má potenciál zvýšit riziko chyb.

Jako správce IT budete potřebovat řešení, které vám nabídne správnou úroveň přehledu o aktivitách v systému, abyste mohli snadno diagnostikovat a řešit problémy, když k nim dojde. Diagnostika přihlášení pro Azure AD je příkladem takového řešení. Pomocí diagnostiky můžete analyzovat, co se stalo během přihlašování, a jaké akce můžete provést k vyřešení problémů, aniž byste museli zahrnovat podporu Microsoftu.

Tento článek vám poskytne přehled o tom, co řešení dělá a jak ho můžete používat.


## <a name="requirements"></a>Požadavky

Diagnostika přihlášení je dostupná ve všech edicích služby Azure AD.<br> Abyste ho mohli používat, musíte být globálním správcem ve službě Azure AD.

## <a name="how-it-works"></a>Jak to funguje

V Azure AD se odpověď na pokus o přihlášení váže na to **, kdo** jste vy a **jak** máte přístup k vašemu tenantovi. Například jako správce můžete při přihlašování z podnikové sítě obvykle nakonfigurovat všechny aspekty vašeho tenanta. Pokud se přihlašujete pomocí stejného účtu z nedůvěryhodné sítě, může být dokonce i zablokovaný.
 
Vzhledem k větší flexibilitě systému, který reaguje na pokus o přihlášení, se můžete setkat ve scénářích, kdy potřebujete řešit problémy s přihlášením. Diagnostika přihlášení je funkce, která:

- Analyzuje data z přihlášení. 

- Zobrazuje, co se stalo, a doporučení k řešení problémů. 

Diagnostika přihlášení pro Azure AD je navržená tak, aby umožňovala samočinnou diagnostiku chyb přihlášení. K dokončení procesu diagnostiky musíte provést tyto kroky:

![Proces diagnostiky přihlašování](./media/overview-sign-in-diagnostics/process.png)
 
1. **Definujte** rozsah událostí přihlašování, které vás zajímají.

2. **Vyberte** přihlášení, které chcete zkontrolovat.

3. **Kontrola** výsledku diagnostiky

4. **Provést** akce

 
### <a name="define-scope"></a>Definovat obor

Cílem tohoto kroku je definovat rozsah přihlašovacích modulů, které chcete prozkoumat. Váš rozsah je založený na uživateli nebo identifikátoru (ID korelace, requestId) a časový rozsah. Pokud chcete rozšířit rozsah dalšího rozsahu, můžete také zadat název aplikace. Azure AD používá informace o oboru k vyhledání správných událostí.  

### <a name="select-sign-in"></a>Vybrat přihlášení  

Na základě vašich kritérií hledání Azure AD načte všechna vyhovující přihlášení a prezentuje je v zobrazení seznamu Shrnutí ověřování. 

![Souhrn ověřování](./media/overview-sign-in-diagnostics/authentication-summary.png)
 
Sloupce zobrazené v tomto zobrazení můžete přizpůsobit.

### <a name="review-diagnostic"></a>Zkontrolovat diagnostiku 

Pro vybranou událost přihlášení vám Azure AD poskytne výsledek diagnostiky. 

![Výsledky diagnostiky](./media/overview-sign-in-diagnostics/diagnostics-results.png)

 
Výsledek začíná posouzením. Posouzení vysvětluje několik vět, co se stalo. Vysvětlení vám pomůže pochopit chování systému. 

V dalším kroku získáte souhrnné informace o souvisejících zásadách podmíněného přístupu, které byly aplikovány na vybrané přihlášení. Tato část se dokončí doporučenými kroky pro nápravu problému. Vzhledem k tomu, že není vždy možné řešit problémy bez další pomoci, může být doporučený krok otevřít lístek podpory. 

### <a name="take-action"></a>Provést akci 
V tomto okamžiku byste měli mít k dispozici informace, které potřebujete k vyřešení vašeho problému.


## <a name="scenarios"></a>Scénáře

V této části najdete přehled zahrnutých diagnostických scénářů. Implementují se tyto scénáře: 
 
- Blokováno podmíněným přístupem

- Neúspěšný podmíněný přístup

- MFA z podmíněného přístupu

- MFA z jiných požadavků

- Vyžaduje se ověření MFA.

- Vyžaduje se ověření MFA, ale pokus o přihlášení uživatele není ze zabezpečeného umístění.

- Úspěšné přihlášení


### <a name="blocked-by-conditional-access"></a>Blokováno podmíněným přístupem

Tento scénář je založený na přihlášení, které zablokovala zásada podmíněného přístupu.

![Blokování přístupu](./media/overview-sign-in-diagnostics/block-access.png)

Část Diagnostika v tomto scénáři zobrazuje podrobnosti o přihlášení uživatele a použitých zásadách.


### <a name="failed-conditional-access"></a>Neúspěšný podmíněný přístup

Tento scénář je obvykle výsledkem přihlášení, které selhalo, protože nebyly splněny požadavky zásad podmíněného přístupu. Obvyklými příklady jsou:

![Vyžadovat ovládací prvky](./media/overview-sign-in-diagnostics/require-controls.png)

- Vyžadovat zařízení připojené k hybridní službě Azure AD

- Vyžadovat klientskou aplikaci schválenou

- Vyžadování zásad ochrany aplikací   


Část Diagnostika v tomto scénáři zobrazuje podrobnosti o přihlášení uživatele a použitých zásadách.


### <a name="mfa-from-conditional-access"></a>MFA z podmíněného přístupu

Tento scénář je založený na zásadách podmíněného přístupu, které mají požadavek na přihlášení pomocí sady Multi-Factor Authentication set.

![Vyžadovat Multi-Factor Authentication](./media/overview-sign-in-diagnostics/require-mfa.png)

Část Diagnostika v tomto scénáři zobrazuje podrobnosti o přihlášení uživatele a použitých zásadách.



### <a name="mfa-from-other-requirements"></a>MFA z jiných požadavků

Tento scénář je založený na požadavku vícefaktorového ověřování, který se vynutil zásadami podmíněného přístupu. Například Multi-Factor Authentication pro jednotlivé uživatele.


![Vyžadovat vícefaktorové ověřování na uživatele](./media/overview-sign-in-diagnostics/mfa-per-user.png)


Záměrem tohoto diagnostického scénáře je poskytnout další podrobnosti o:

- Zdroj přerušení Multi-Factor Authentication. 
- Výsledek interakce klienta.

V této části najdete taky všechny podrobnosti o pokusu o přihlášení uživatele. 


### <a name="mfa-proof-up-required"></a>Vyžaduje se ověření MFA.

Tento scénář je založený na přihlášeních, která byla přerušena požadavky na nastavení vícefaktorového ověřování. Tato instalace se označuje také jako "Kontrola pravopisu".

Ověření Multi-Factor Authentication prochází, pokud je uživatel nutný k použití vícefaktorového ověřování, ale ještě není nakonfigurovaný, nebo správce nakonfiguroval uživatele, aby ho nakonfiguroval.

Záměrem tohoto diagnostického scénáře je poskytnout přehled o tom, že přerušení Multi-Factor Authentication bylo nastaveno na stav a poskytnout doporučení, aby uživatel dokončil kontrolu.

### <a name="mfa-proof-up-required-from-a-risky-sign-in"></a>U rizikové přihlašování se vyžaduje ověření MFA.

Tento scénář je výsledkem přihlášení, která byla přerušena žádostí o nastavení služby Multi-Factor Authentication z rizikového přihlašování. 

Záměrem tohoto diagnostického scénáře je poskytnout přehled o tom, že přerušení služby Multi-Factor Authentication bylo nastaveno na stav, aby bylo možné poskytnout doporučení, aby uživatel dokončil kontrolu, ale provedl z umístění v síti, které se nejeví jako rizikové. Například pokud je podniková síť definovaná jako pojmenovaná lokalita, pokusí se provést zkušební místo v podnikové síti.


### <a name="successful-sign-in"></a>Úspěšné přihlášení

Tento scénář je založený na přihlášeních, která se nepřerušila pomocí podmíněného přístupu nebo vícefaktorového ověřování.

Účelem tohoto diagnostického scénáře je poskytnout přehled o tom, co uživatel zadal během přihlašování v případě, že existovaly zásady podmíněného přístupu nebo zásady, které se očekávaly, nebo nakonfigurovaná služba Multi-Factor Authentication, která se očekávala k přerušení přihlášení uživatele.



## <a name="next-steps"></a>Další kroky

* [Co jsou sestavy služby Azure Active Directory?](overview-reports.md)
* [Co je monitorování služby Azure Active Directory?](overview-monitoring.md)
