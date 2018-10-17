---
title: Co jsou sestavy Azure Active Directory? | Microsoft Docs
description: Poskytuje obecný přehled sestav Azure Active Directory.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 6141a333-38db-478a-927e-526f1e7614f4
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 01/15/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 220f27ccf2d3eaefd8347e1d52824be2d601d9c9
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46364377"
---
# <a name="what-are-azure-active-directory-reports"></a>Co jsou sestavy Azure Active Directory?

Pomocí sestav Azure Active Directory můžete získat přehled o stavu vašeho prostředí.  
Poskytnutá data vám umožní:

- Určit, jak uživatelé využívají vaše aplikace a služby.
- Rozpoznat potenciální rizika ovlivňující stav vašeho prostředí.
- Řešit problémy, které brání uživatelům v práci.  

Architektura generování sestav se spoléhá na dva hlavní pilíře:

- Sestavy zabezpečení
- Sestavy aktivit

![Vytváření sestav](./media/overview-reports/01.png)


## <a name="security-reports"></a>Sestavy zabezpečení

Sestavy zabezpečení v Azure Active Directory pomáhají chránit identity vaší organizace.  
V Azure Active Directory existují dva typy sestav zabezpečení:

- **Uživatelé označení příznakem rizika** – Ze [sestavy zabezpečení uživatelů označených příznakem rizika](concept-user-at-risk.md) získáte přehled o uživatelských účtech, u kterých mohlo dojít k ohrožení zabezpečení.

- **Riziková přihlášení** – Se [sestavou zabezpečení rizikových přihlášení](concept-risky-sign-ins.md) získáte indikátor pokusů o přihlášení, které mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu. 

**Jaká licence Azure AD je potřeba pro přístup k sestavě zabezpečení?**  

Sestavy uživatelů označených příznakem rizika a rizikových přihlášení nabízí všechny edice Azure Active Directory.  
Úroveň podrobností sestav se však mezi jednotlivými edicemi liší: 

- **Edice Azure Active Directory Free a Basic** již nabízí seznam uživatelů označených příznakem rizika a rizikových přihlášení. 

- Edice **Azure Active Directory Premium 1** tento model rozšiřuje tím, že umožňuje také prozkoumávat některé ze základních rizikových událostí, které byly v každé sestavě rozpoznány. 

- Edice **Azure Active Directory Premium 2** poskytuje nejpodrobnější informace o základních rizikových událostech a umožňuje také konfigurovat zásady zabezpečení, které automaticky reagují na nakonfigurované úrovně rizika.


## <a name="activity-reports"></a>Sestavy aktivit

V Azure Active Directory existují dva typy sestav aktivit:

- **Protokoly auditu** – [Sestava aktivit protokolů auditu](concept-audit-logs.md) poskytuje přístup k historii každé úlohy provedené ve vašem tenantovi.

- **Přihlášení** – Se [sestavou aktivit přihlašování](concept-sign-ins.md) můžete určit, kdo provedl úlohy hlášené sestavou protokolů auditu.


**Sestava protokolů auditu** poskytuje záznamy systémových aktivit pro zajištění dodržování předpisů. Tato data umožňují řešit běžné scénáře tohoto typu:

- Někdo v tenantovi získal přístup ke správcovské skupině. Kdo jim dal přístup? 

- Chcete znát seznam uživatelů, kteří se přihlašují ke konkrétní aplikaci, protože jste aplikaci nedávno zprovoznili a chcete vědět, jestli funguje v pořádku.

- Chcete vědět, ke kolika resetováním hesla ve vašem tenantovi dochází.


**Jaká licence Azure AD je potřeba pro přístup k sestavě protokolů auditu?**  

Sestava protokolů auditu je dostupná pro funkce, ke kterým máte licence. Pokud máte licenci ke konkrétní funkci, máte u ní také přístup k informacím protokolu auditu.

Další podrobnosti najdete v části **Porovnání všeobecně dostupných funkcí v edicích Free, Basic a Premium** v tématu [Funkce a možnosti v Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features).   


**Sestava aktivit přihlašování** umožňuje najít odpovědi na otázky tohoto typu:

- Jaký je vzorec přihlašování uživatele?
- Kolik uživatelů se přihlásilo za týden?
- Jaký je stav těchto přihlášení?


**Jaká licence Azure AD je potřeba pro přístup k sestavě aktivit přihlašování?**  

Pro přístup k sestavě aktivit přihlašování musí mít váš tenant přiřazenou licenci Azure AD Premium.

## <a name="programmatic-access"></a>Programový přístup

Kromě uživatelského rozhraní nabízí generování sestav v Azure Active Directory také [programový přístup](concept-reporting-api.md) k datům sestav. Data z těchto sestav můžou být velmi užitečná pro vaše aplikace, jako jsou systémy SIEM nebo nástroje pro auditování a business intelligence. Rozhraní API pro generování sestav v Azure AD poskytují programový přístup k těmto datům prostřednictvím sady rozhraní API založených na REST. Tato rozhraní API můžete volat z nejrůznějších programovacích jazyků a nástrojů. 


## <a name="next-steps"></a>Další kroky

- [Sestava rizikových přihlášení](concept-risky-sign-ins.md)
- [Sestava protokolů auditu](concept-audit-logs.md)
- [Sestava protokolů přihlášení](concept-sign-ins.md)