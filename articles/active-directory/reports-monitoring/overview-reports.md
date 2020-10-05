---
title: Co jsou sestavy Azure Active Directory? | Dokumentace Microsoftu
description: Poskytuje obecný přehled sestav Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 6141a333-38db-478a-927e-526f1e7614f4
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 09/30/2020
ms.author: markvi
ms.reviewer: sarbar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f9a51c10a4f390e5627bccf35ab5dc74689e9c6
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91566818"
---
# <a name="what-are-azure-active-directory-reports"></a>Co jsou sestavy Azure Active Directory?

Sestavy Azure Active Directory (Azure AD) poskytují ucelený přehled o aktivitě ve vašem prostředí. Poskytnutá data vám umožní:

- Určit, jak uživatelé využívají vaše aplikace a služby.
- Rozpoznat potenciální rizika ovlivňující stav vašeho prostředí.
- Řešit problémy, které brání uživatelům v práci.  

Architektura generování sestav se spoléhá na dva hlavní pilíře:

- [Sestavy zabezpečení](#security-reports)
- [Sestavy aktivit](#activity-reports)

![Generování sestav](./media/overview-reports/01.png)


## <a name="security-reports"></a>Sestavy zabezpečení

Sestavy zabezpečení vám pomůžou chránit identity vaší organizace. Existují dva typy sestav zabezpečení:

- **Uživatelé označení příznakem rizika** – Ze [sestavy zabezpečení uživatelů označených příznakem rizika](../identity-protection/overview-identity-protection.md) získáte přehled o uživatelských účtech, u kterých mohlo dojít k ohrožení zabezpečení.

- **Riziková přihlášení** – Se [sestavou zabezpečení rizikových přihlášení](../identity-protection/overview-identity-protection.md) získáte indikátor pokusů o přihlášení, které mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu. 

### <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Jaká licence Azure AD je potřeba pro přístup k sestavě zabezpečení?  

Všechny edice služby Azure AD poskytují uživatelům příznakem rizika pro rizikové a rizikové přihlašovací zprávy. Úroveň podrobností sestav se však mezi jednotlivými edicemi liší: 

- V **edicích Azure Active Directory Free a Basic**se zobrazí seznam uživatelů označených příznakem rizika a rizikových přihlášení. 

- Edice **Azure Active Directory Premium 1** rozšiřuje tento model tím, že umožňuje prověření některých základních zjištění rizik zjištěných pro každou sestavu. 

- Edice **Azure Active Directory Premium 2** poskytuje nejpodrobnější informace o základních detekcích rizik a umožňuje také konfigurovat zásady zabezpečení, které automaticky reagují na nakonfigurované úrovně rizika.


## <a name="activity-reports"></a>Sestavy aktivit

Sestavy aktivit vám pomohou pochopit chování uživatelů ve vaší organizaci. V Azure AD existují dva typy sestav aktivit:

- **Protokoly auditu** – [Sestava aktivit protokolů auditu](concept-audit-logs.md) poskytuje přístup k historii každé úlohy provedené ve vašem tenantovi.

- **Přihlášení** – Se [sestavou aktivit přihlašování](concept-sign-ins.md) můžete určit, kdo provedl úlohy hlášené sestavou protokolů auditu.



> [!VIDEO https://www.youtube.com/embed/ACVpH6C_NL8]




### <a name="audit-logs-report"></a>Sestava protokolů auditu 

[Sestava protokolů auditu](concept-audit-logs.md) poskytuje záznamy systémových aktivit pro zajištění dodržování předpisů. Tato data umožňují řešit běžné scénáře tohoto typu:

- Někdo v tenantovi získal přístup ke správcovské skupině. Kdo jim dal přístup? 

- Chcete znát seznam uživatelů, kteří se přihlašují ke konkrétní aplikaci, protože jste aplikaci nedávno zprovoznili a chcete vědět, jestli funguje v pořádku.

- Chcete vědět, ke kolika resetováním hesla ve vašem tenantovi dochází.


#### <a name="what-azure-ad-license-do-you-need-to-access-the-audit-logs-report"></a>Jaká licence Azure AD je potřeba pro přístup k sestavě protokolů auditu?  

Sestava protokolů auditu je dostupná pro funkce, ke kterým máte licence. Pokud máte licenci ke konkrétní funkci, máte u ní také přístup k informacím protokolu auditu. Porovnání funkcí deatiled podle [různých typů licencí](../fundamentals/active-directory-whatis.md#what-are-the-azure-ad-licenses) se dá zobrazit na stránce s cenami za [Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). Další podrobnosti najdete v tématu [Azure Active Directory funkce a možnosti](../fundamentals/active-directory-whatis.md#which-features-work-in-azure-ad).

### <a name="sign-ins-report"></a>Sestava přihlášení

[Sestava přihlášení](concept-sign-ins.md) vám umožní najít odpovědi na otázky, jako například:

- Jaký je vzorec přihlašování uživatele?
- Kolik uživatelů se přihlásilo za týden?
- Jaký je stav těchto přihlášení?

#### <a name="what-azure-ad-license-do-you-need-to-access-the-sign-ins-activity-report"></a>Jaká licence Azure AD je potřeba pro přístup k sestavě aktivit přihlašování?  

Pro přístup k sestavě aktivit přihlašování musí mít váš tenant přiřazenou licenci Azure AD Premium.

## <a name="programmatic-access"></a>Programový přístup

Kromě uživatelského rozhraní poskytuje Azure AD také [programový přístup](concept-reporting-api.md) k datům sestav prostřednictvím sady rozhraní API založených na REST. Tato rozhraní API můžete volat z nejrůznějších programovacích jazyků a nástrojů. 

## <a name="next-steps"></a>Další kroky

- [Sestava rizikových přihlášení](../identity-protection/overview-identity-protection.md)
- [Sestava protokolů auditu](concept-audit-logs.md)
- [Sestava protokolů přihlášení](concept-sign-ins.md)