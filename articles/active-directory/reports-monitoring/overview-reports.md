---
title: Co jsou sestavy Azure Active Directory? | Dokumenty Microsoft
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
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 0c3c9b23ac3383eb7e2eda256af4fb92f7f5e0b5
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51622155"
---
# <a name="what-are-azure-active-directory-reports"></a>Co jsou sestavy Azure Active Directory?

Azure Active Directory (Azure AD) sestavy poskytují komplexní přehled aktivit ve vašem prostředí. Poskytnutá data vám umožní:

- Určit, jak uživatelé využívají vaše aplikace a služby.
- Rozpoznat potenciální rizika ovlivňující stav vašeho prostředí.
- Řešit problémy, které brání uživatelům v práci.  

Architektura generování sestav se spoléhá na dva hlavní pilíře:

- [Sestavy zabezpečení](#security-reports)
- [Sestavy aktivit](#activity-reports)

![Vytváření sestav](./media/overview-reports/01.png)


## <a name="security-reports"></a>Sestavy zabezpečení

Zprávy o zabezpečení pomáhají chránit identity vaší organizace. Existují dva typy sestav zabezpečení:

- **Uživatelé označení příznakem rizika** – Ze [sestavy zabezpečení uživatelů označených příznakem rizika](concept-user-at-risk.md) získáte přehled o uživatelských účtech, u kterých mohlo dojít k ohrožení zabezpečení.

- **Riziková přihlášení** – Se [sestavou zabezpečení rizikových přihlášení](concept-risky-sign-ins.md) získáte indikátor pokusů o přihlášení, které mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu. 

### <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Jaká licence Azure AD je potřeba pro přístup k sestavě zabezpečení?  

Nabízí všechny edice Azure AD uživatelů označených příznakem rizika a rizikových přihlášení sestavy. Úroveň podrobností sestav se však mezi jednotlivými edicemi liší: 

- V **edice Azure Active Directory Free a Basic**, získat seznam uživatelů označených příznakem rizika a rizikových přihlášení. 

- Edice **Azure Active Directory Premium 1** tento model rozšiřuje tím, že umožňuje také prozkoumávat některé ze základních rizikových událostí, které byly v každé sestavě rozpoznány. 

- Edice **Azure Active Directory Premium 2** poskytuje nejpodrobnější informace o základních rizikových událostech a umožňuje také konfigurovat zásady zabezpečení, které automaticky reagují na nakonfigurované úrovně rizika.


## <a name="activity-reports"></a>Sestavy aktivit

Sestavy aktivit vám pomůže porozumět chování uživatelů ve vaší organizaci. Existují dva typy sestav aktivit ve službě Azure AD:

- **Protokoly auditu** – [Sestava aktivit protokolů auditu](concept-audit-logs.md) poskytuje přístup k historii každé úlohy provedené ve vašem tenantovi.

- **Přihlášení** – Se [sestavou aktivit přihlašování](concept-sign-ins.md) můžete určit, kdo provedl úlohy hlášené sestavou protokolů auditu.


### <a name="audit-logs-report"></a>Sestava protokolů auditu 

[Sestava protokolů auditu](concept-audit-logs.md) poskytuje záznamy systémových aktivit pro zajištění dodržování předpisů. Tato data umožňují řešit běžné scénáře tohoto typu:

- Někdo v tenantovi získal přístup ke správcovské skupině. Kdo jim dal přístup? 

- Chcete znát seznam uživatelů, kteří se přihlašují ke konkrétní aplikaci, protože jste aplikaci nedávno zprovoznili a chcete vědět, jestli funguje v pořádku.

- Chcete vědět, ke kolika resetováním hesla ve vašem tenantovi dochází.


#### <a name="what-azure-ad-license-do-you-need-to-access-the-audit-logs-report"></a>Jaká licence Azure AD je potřeba pro přístup k sestavě protokolů auditu?  

Sestava protokolů auditu je dostupná pro funkce, ke kterým máte licence. Pokud máte licenci ke konkrétní funkci, máte u ní také přístup k informacím protokolu auditu. Další podrobnosti najdete v tématu [funkce Azure Active Directory a možnosti](https://www.microsoft.com/cloud-platform/azure-active-directory-features).   

### <a name="sign-ins-report"></a>Sestava přihlášení

[Sestavy přihlášení](concept-sign-ins.md) můžete nalézt odpovědi na otázky jako:

- Jaký je vzorec přihlašování uživatele?
- Kolik uživatelů se přihlásilo za týden?
- Jaký je stav těchto přihlášení?

#### <a name="what-azure-ad-license-do-you-need-to-access-the-sign-ins-activity-report"></a>Jaká licence Azure AD je potřeba pro přístup k sestavě aktivit přihlašování?  

Pro přístup k sestavě aktivit přihlašování musí mít váš tenant přiřazenou licenci Azure AD Premium.

## <a name="programmatic-access"></a>Programový přístup

Kromě uživatelského rozhraní služby Azure AD také poskytuje [programový přístup](concept-reporting-api.md) k datům sestav pomocí sady založené na protokolu REST API. Tato rozhraní API můžete volat z nejrůznějších programovacích jazyků a nástrojů. 

## <a name="next-steps"></a>Další postup

- [Sestava rizikových přihlášení](concept-risky-sign-ins.md)
- [Sestava protokolů auditu](concept-audit-logs.md)
- [Sestava protokolů přihlášení](concept-sign-ins.md)