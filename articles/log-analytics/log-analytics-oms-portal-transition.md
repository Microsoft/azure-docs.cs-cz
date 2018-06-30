---
title: Portál OMS Přesun do Azure | Microsoft Docs
description: Na portálu OMS se sunsetted se všemi funkcemi přesouvá na portál Azure. Tento článek poskytuje podrobné informace o tento přechod.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: e47e8cbd209ea34317ca9b176a2c4b0fef10a2b2
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37132905"
---
# <a name="oms-portal-moving-to-azure"></a>Přesouvání do Azure portálu OMS
Děkujeme za používání portálu OMS. Jsme podporu doporučujeme a nadále výraznou investovat do našich monitorování a správu služeb. Jeden připomínek opakovaně naslouchali zákazníkům, je potřeba pro jednoho uživatele prostředí můžete sledovat a spravovat místní i Azure úlohy. Pravděpodobně víte, portál Azure je centrem pro všech služeb Azure a nabízí bohaté správu prostředí s funkcí, jako je řídicí panely pro Připnutí prostředky, inteligentního hledat prostředky hledání a označování pro správu prostředků. Konsolidovat a zjednodušit sledování a správu pracovního postupu, spuštění přidání funkcí portálu OMS do portálu Azure. Snažíme se radostí oznamujeme, že většina funkcí portálu OMS jsou teď součástí portálu Azure. Ve skutečnosti některé nové funkce, jako je například Traffic Manager jsou dostupné jenom na portálu Azure. Jsou pouze několik mezery zbývající, nejvíce impactful se pět řešení, které jsou stále v procesu se přesunul na portálu Azure. Pokud nepoužíváte tyto funkce, bude moci provést vše, co jste dělali na portálu OMS pomocí portálu Azure a další. Pokud jste tak již neučinili, doporučujeme, že abyste mohli začít používat portál Azure ještě dnes! 

Očekáváme, zavřete dolů zbývající mezery mezi dva portály podle 2018 srpnu. Na základě názorů zákazníků, jsme bude komunikovat časovou osu pro sunsetting na portálu OMS. Snažíme se nadšení přesunout do portálu Azure a očekávají, že bude snadno přechod. Ale chápeme změny obtížně a můžou narušovat běh produktu. Odesílat jakékoli dotazy, zpětnou vazbu nebo připomínky k LAUpgradeFeedback@microsoft.com. Zbývající část tohoto článku prochází přes klíčových scénářů, aktuální mezer a plán pro tento přechod. 


## <a name="what-will-change"></a>Co se změní? 
Tyto změny se odesílány zpět s vyřazení na portálu OMS. Každý z těchto změn je podrobně popsaná v další v následujících částech.

- Nové prostředí pro správu výstrah nahradí řešení pro správu výstrahu.
- Správa přístupu uživatelů bude provedeno v portálu Azure pomocí řízení přístupu Azure na základě rolí.
- Konektor služby Statistika aplikace se už nevyžadují, protože stejné funkce lze povolit prostřednictvím prostoru mezi dotazy.
- OMS mobilní aplikace bude zastaralá. 
- Skupina NSG řešení je nahrazován rozšířené funkce, které jsou k dispozici prostřednictvím řešení analýzy provozu.



## <a name="current-known-gaps"></a>Aktuální známé mezery 
Aktuálně neexistují mezery některé funkce, které vyžadují, abyste dál používat portál OMS. Tyto mezery jsou se zavře a bude tento dokument správně aktualizován. Musí se také podívat na [Azure aktualizace](https://azure.microsoft.com/updates/?product=log-analytics) pro průběžné oznámení o rozšíření a změny.

- Následující řešení nejsou plně funkční na portálu Azure. By měly být nadále používat tato řešení na portálu classic, dokud se aktualizovány.

    - Řešení pro analýzu systému Windows ([připravenost upgradu](https://technet.microsoft.com/itpro/windows/deploy/upgrade-readiness-get-started), [stavu zařízení](https://docs.microsoft.com/windows/deployment/update/device-health-monitor), a [kompatibility aktualizací](https://technet.microsoft.com/itpro/windows/manage/update-compliance-get-started))
    - [Analýzy DNS](log-analytics-dns.md) 
    - [Surface Hub](log-analytics-surface-hubs.md)

-  Pro přístup k prostředku analýzy protokolů v Azure, uživatel musí mít udělen přístup prostřednictvím [přístupu na základě role Azure](#user-access-and-role-migration).
- Plány aktualizace, které byly vytvořeny na portálu OMS neprojeví v nasazeních plánované aktualizace nebo aktualizace historie úlohy aktualizace správu řídicího panelu portálu Azure. Během této poměrně musí vzít v úvahu na konci června 2018.
- Funkce ve verzi preview vlastní protokoly lze povolit pouze prostřednictvím portálu OMS. Na konci června 2018 to budou automaticky povolené pro všechny pracovní prostory.
 
## <a name="what-should-i-do-now"></a>Co je třeba udělat teď?  
Se seznamte s [běžné otázky pro přechod z portálu OMS na portálu Azure Log Analytics uživatelům](../log-analytics/log-analytics-oms-portal-faq.md) informace o tom, jak převést na portál Azure. Pokud [mezery popsané výše](#current-known-gaps) se nevztahují na vašem prostředí, pak byste měli zvážit spouštění pomocí portálu Azure jako primární prostředí. Odesílat jakékoli zpětnou vazbu, dotazy nebo připomínky k LAUpgradeFeedback@microsoft.com.

## <a name="changes-to-alerts"></a>Změny výstrah 

### <a name="alert-extension"></a>Výstrahy rozšíření  
Výstrahy jsou právě probíhá [rozšířené na portálu Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md). Po dokončení akce správy výstrah bude k dispozici pouze na portálu Azure. Existující výstrahy budou nadále uvedené na portálu OMS. Pokud přistupujete pomocí Log Analytics výstrahy REST API nebo protokolu analýzy výstrahy prostředku šablony výstrahy prostřednictvím kódu programu, budete muset použít skupiny akcí místo akce v voláními rozhraní API, šablony Azure Resource Manager a příkazy prostředí PowerShell.

### <a name="alert-management-solution"></a>Řešení pro správu výstrah
Místo [výstrahy řešení pro správu](log-analytics-solution-alert-management.md), můžete použít [Azure monitorování unified výstrah rozhraní](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) vizualizovat a spravovat oznámení. Toto nové prostředí agreguje výstrahy z více zdrojů v rámci Azure včetně protokolu výstrahy od analýzy protokolů. Najdete v části distribuce upozornění, využít výhod automatizované seskupení souvisejících výstrah pomocí inteligentní skupin a zobrazení výstrah napříč více předplatných při použití bohaté filtrů. Všechny tyto funkce jsou dostupné ve verzi preview od 4 června 2018. Řešení pro správu výstrah není k dispozici na portálu Azure. 

Data shromážděná řešením pro správu výstrah (záznamů s typem výstrahy) nadále v analýzy protokolů řešení je po instalaci pro pracovní prostor. Od srpna 2018, datové proudy upozornění z jednotná výstrahy do pracovních prostorů bude povolena, tato funkce nahrazuje. Některé změny schématu očekává a budou oznámeny později.

## <a name="user-access-and-role-migration"></a>Migrace přístup a role uživatele
Správa Azure přístup k portálu je bohatší a výkonnější než správu přístupu na portálu OMS, ale vyžaduje některé převody. V tématu [spravovat pracovní prostory](log-analytics-manage-access.md#manage-accounts-and-users) podrobnosti správy přístupu v analýzy protokolů.

Od června 25 a až července, automatický převod přístupu řízení oprávnění z portálu OMS na portál Azure, bude zahájena oprávnění. Po dokončení převodu části portálu OMS uživatele správy bude směrovat uživatelé k řízení přístupu (IAM) v Azure. 

Při převodu bude systém zkontrolujte každému uživateli nebo skupině zabezpečení, který má oprávnění na portálu OMS a určení, zda má stejnou úroveň nebo oprávnění v Azure. Pokud chybí oprávnění, přiřadí následující role pro relevantní pracovních prostorů a řešení.

| Oprávnění na portálu OMS | Azure Role |
|:---|:---|
| Jen pro čtení | Čtenář Log Analytics |
| Přispěvatel | Přispěvatel Log Analytics |
| Správce | Vlastník |

Abyste měli jistotu, že žádné nadměrné oprávnění se přiřazují uživatelům, nebude systém automaticky přiřadit tato oprávnění na úrovni skupiny prostředků. V důsledku toho správci prostoru musí přiřadit ručně sami _vlastníka_ nebo _Přispěvatel_ role na úrovni skupiny nebo předplatné prostředků můžete provádět následující akce.

- Přidat nebo odebrat řešení
- Definujte nové vlastní zobrazení
- Správa výstrah 

V některých případech automatický převod nemůže použít oprávnění a vyzve správce, ručně přiřadit oprávnění.

## <a name="oms-mobile-app"></a>OMS mobilní aplikace
OMS mobilní aplikace bude sunsetted společně s portálu OMS. Místo OMS mobilní aplikace přístup k informacím o vaší IT infrastruktury, řídicí panely a uložené dotazy, dostanete na portál Azure přímo z prohlížeče v mobilním zařízení. Chcete-li získat výstrahy, měli byste nakonfigurovat [skupinám akce Azure](../monitoring-and-diagnostics/monitoring-action-groups.md) pro příjem oznámení ve formě SMS nebo telefonní hovor

## <a name="application-insights-connector-and-solution"></a>Konektor služby Statistika aplikací a řešení
[Konektor služby Statistika aplikace](log-analytics-app-insights-connector.md) poskytuje způsob, jak přenést data Application Insights do pracovního prostoru analýzy protokolů. Tato odstranění duplicitních dat nebyla nutná povolit viditelnost v rámci dat infrastruktury a aplikací.

S podporou [prostředků mezi dotazy](log-analytics-cross-workspace-search.md), je už tento potřeba duplicitní data. Jako takový přestanou existující řešení Application Insights. Od července se nebude moci propojit pracovní prostory analýzy protokolů nové prostředky Application Insights. Existující odkazy a řídicí panely budou nadále fungovat, dokud listopadu 2018.


## <a name="azure-network-security-group-analytics"></a>Analýzy skupin zabezpečení sítě Azure
[Řešení analýzy skupiny zabezpečení sítě Azure](log-analytics-azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-log-analytics) se nahradí s naposledy spuštěný [Analýza provozu](https://azure.microsoft.com/en-in/blog/traffic-analytics-in-preview/) který poskytuje přehled o činnosti uživatelů a aplikací v cloudových sítích. Analýza provozu umožňuje audit síťové aktivity, zabezpečených aplikací a dat vaší organizace, optimalizace výkonu zatížení a zůstat kompatibilní. 

Toto řešení analyzuje NSG toku protokoly a poskytuje přehledy o následující.

- Přenosy dat mezi vaší sítí mezi Azure a Internet, oblastí veřejného cloudu, virtuálních sítí a podsítí.
- Aplikace a protokoly v síti, aniž by bylo nutné pro zjištění je snazší, nebo vyhrazený tok kolekce zařízení.
- Provoz TOP talkers, chatty aplikace konverzace virtuálních počítačů v cloudu, aktivní oblasti.
- Zdroje a cíle provozu mezi virtuálními sítěmi, vzájemných vztahů mezi nejdůležitější obchodní služby a aplikace.
- Zabezpečení, včetně škodlivý přenos porty otevřené pro Internet, aplikace nebo virtuální počítače pokusu o přístup k Internetu.
- Využití kapacity, které vám umožní eliminovat problémy přes zřizování nebo nedostatečné.

Můžete nadále závisí na nastavení diagnostiky pro odeslání protokolů NSG k analýze protokolů tak stávající uložené hledání, výstrahy, řídicí panely budou nadále fungovat. Zákazníci, kteří již nainstalovali řešení můžete nadále používat ji až do dalšího oznámení. Od června 20 řešení Analytics skupiny zabezpečení sítě budou odebrány z marketplace a prostřednictvím komunity jako dostupné [šablony rychlý start Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Operationalinsights).

## <a name="next-steps"></a>Další postup
- V tématu [běžné otázky pro přechod z portálu OMS na portálu Azure Log Analytics uživatelům](log-analytics-oms-portal-faq.md) pokyny k přesouvání z portálu OMS na portál Azure.