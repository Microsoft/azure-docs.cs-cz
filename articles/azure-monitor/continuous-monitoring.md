---
title: Průběžné monitorování pomocí Azure Monitoru | Dokumenty společnosti Microsoft
description: Popisuje konkrétní kroky pro použití Azure Monitoru k povolení nepřetržitého monitorování v rámci vašich pracovních postupů.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/12/2018
ms.openlocfilehash: b9ca8a703ed8a84148abd23e90114402d8806bd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667189"
---
# <a name="continuous-monitoring-with-azure-monitor"></a>Průběžné monitorování pomocí Azure Monitoru

Průběžné monitorování označuje proces a technologii potřebnou k začlenění monitorování do každé fáze životního cyklu operací DevOps a IT. Pomáhá nepřetržitě zajistit zdraví, výkon a spolehlivost vaší aplikace a infrastruktury při přesunu z vývoje do výroby. Průběžné monitorování vychází z konceptů průběžné integrace a průběžného nasazování (CI/CD), které vám pomohou vyvíjet a dodávat software rychleji a spolehlivěji, aby uživatelům poskytovaly nepřetržitou hodnotu.

[Azure Monitor](overview.md) je jednotné řešení monitorování v Azure, které poskytuje viditelnost celého zásobníku napříč aplikacemi a infrastrukturou v cloudu i v místním prostředí. Během vývoje a testování bezproblémově spolupracuje s [Visual Studio a Visual Studio Code](https://visualstudio.microsoft.com/) a integruje se s Azure [DevOps](/azure/devops/user-guide/index) pro správu verzí a správu pracovních položek během nasazení a operací. Dokonce se integruje mezi nástroji ITSM a SIEM podle vašeho výběru, aby pomohla sledovat problémy a incidenty v rámci vašich stávajících it procesů.

Tento článek popisuje konkrétní kroky pro použití Azure Monitor povolit průběžné monitorování v rámci pracovních postupů. Obsahuje odkazy na další dokumentaci, která obsahuje podrobnosti o implementaci různých funkcí.


## <a name="enable-monitoring-for-all-your-applications"></a>Povolit monitorování pro všechny vaše aplikace
Chcete-li získat pozorovatelnost v celém prostředí, musíte povolit monitorování všech webových aplikací a služeb. To vám umožní snadno vizualizovat transakce a připojení mezi koncovými soubory ve všech součástech.

- [Azure DevOps Projects](../devops-project/overview.md) vám poskytne zjednodušené prostředí s existujícím kódem a úložištěm Git, nebo si vyberte jednu z ukázkových aplikací a vytvořte kanál průběžné integrace (CI) a průběžného doručování (CD) do Azure.
- [Průběžné monitorování v kanálu vydání DevOps](../azure-monitor/app/continuous-monitoring.md) umožňuje gate nebo rollback nasazení na základě dat monitorování.
- [Sledování stavu](../azure-monitor/app/monitor-performance-live-website-now.md) umožňuje instrumentovat živou aplikaci .NET ve Windows pomocí Azure Application Insights, aniž byste museli upravovat nebo znovu nasazovat kód.
- Pokud máte přístup ke kódu pro vaši aplikaci, povolte úplné monitorování pomocí [Application Insights](../azure-monitor/app/app-insights-overview.md) instalací sady Azure Monitor Application Insights SDK pro [rozhraní .NET](../azure-monitor/learn/quick-monitor-portal.md), [Java](../azure-monitor/app/java-get-started.md), [Node.js](../azure-monitor/learn/nodejs-quick-start.md)nebo [jiných programovacích jazyků](../azure-monitor/app/platforms.md). To umožňuje zadat vlastní události, metriky nebo zobrazení stránek, které jsou relevantní pro vaši aplikaci a vaši firmu.



## <a name="enable-monitoring-for-your-entire-infrastructure"></a>Povolte monitorování celé infrastruktury
Aplikace jsou pouze tak spolehlivé jako jejich základní infrastruktury. Povolení monitorování v celé infrastruktuře vám pomůže dosáhnout plné pozorovatelnosti a usnadní nalezení potenciální hlavní příčiny, když něco selže. Azure Monitor vám pomůže sledovat stav a výkon celé hybridní infrastruktury, včetně prostředků, jako jsou virtuální počítače, kontejnery, úložiště a síť.

- Automaticky získáte [metriky platformy, protokoly aktivit a protokoly diagnostiky](platform/data-sources.md) z většiny prostředků Azure bez konfigurace.
- Povolte hlubší monitorování virtuálních počítačů pomocí [Azure Monitor u virtuálních počítačů](insights/vminsights-overview.md).
-  Povolte hlubší monitorování clusterů AKS pomocí [Azure Monitoru pro kontejnery](insights/container-insights-overview.md).
- Přidejte [monitorovací řešení](insights/solutions-inventory.md) pro různé aplikace a služby ve vašem prostředí.


[Infrastruktura jako kód](/azure/devops/learn/what-is-infrastructure-as-code) je správa infrastruktury v popisném modelu, pomocí stejné správy verzí jako devOps týmy používají pro zdrojový kód. Dodává spolehlivost a škálovatelnost vašemu prostředí a umožňuje využít podobné procesy, které se používají ke správě vašich aplikací.

-  Pomocí [šablon Správce prostředků](platform/template-workspace-configuration.md) můžete povolit monitorování a konfigurovat výstrahy přes velkou sadu prostředků.
- Pomocí [zásad Azure](../governance/policy/overview.md) vynucujte různá pravidla nad prostředky. Tím zajistíte, že tyto prostředky zůstanou v souladu s vašimi podnikovými standardy a smlouvami o úrovni služeb. 


##  <a name="combine-resources-in-azure-resource-groups"></a>Kombinování prostředků ve skupinách prostředků Azure
Typická aplikace v Azure dnes zahrnuje více prostředků, jako jsou virtuální počítače a služby app nebo mikroslužeb hostovaných v cloudových službách, clusterech AKS nebo Service Fabric. Tyto aplikace často využívají závislosti, jako jsou centra událostí, úložiště, SQL a service bus.

- Kombinujte prostředky ve skupinách prostředků Azure, abyste získali úplný přehled o všech vašich prostředcích, které tvoří různé aplikace. [Azure Monitor pro skupiny prostředků](../azure-monitor/insights/resource-group-insights.md) poskytuje jednoduchý způsob, jak sledovat stav a výkon celé aplikace celého zásobníku a umožňuje procházení do příslušných součástí pro všechny šetření nebo ladění.

## <a name="ensure-quality-through-continuous-deployment"></a>Zajištění kvality díky nepřetržitému nasazování
Průběžná integrace / průběžné nasazování umožňuje automaticky integrovat a nasazovat změny kódu do vaší aplikace na základě výsledků automatizovaného testování. Zjednodušuje proces nasazení a zajišťuje kvalitu všech změn před jejich přechodem do produkčního prostředí.


- Pomocí [Azure Pipelines](/azure/devops/pipelines) implementujte průběžné nasazení a automatizujte celý proces od potvrzení kódu po produkční prostředí na základě testů CI/CD.
- Pomocí [brány kvality](/azure/devops/pipelines/release/approvals/gates) můžete integrovat monitorování do přednasazení nebo po nasazení. Tím zajistíte, že splňujete klíčové metriky stavu a výkonu (KU), protože vaše aplikace se přesouvají z vývojáře do produkčního prostředí a žádné rozdíly v prostředí infrastruktury nebo škálování nemají negativní dopad na klíčové ukazatele výkonu.
- [Udržujte samostatné instance monitorování](../azure-monitor/app/separate-resources.md) mezi různými prostředími nasazení, jako je Dev, Test, Canary a Prod. Tím je zajištěno, že shromážděná data jsou relevantní v rámci přidružených aplikací a infrastruktury. Pokud potřebujete korelovat data napříč prostředími, můžete použít [grafy s více prostředky v Průzkumníku metrik](../azure-monitor/platform/metrics-charts.md) nebo vytvářet [dotazy mezi prostředky v Azure Monitoru](log-query/cross-workspace-query.md).


## <a name="create-actionable-alerts-with-actions"></a>Vytváření upozornění s akcemi
Kritickým aspektem monitorování je proaktivní upozorňování správců na všechny aktuální a předpokládané problémy. 

- Vytvořte [výstrahy v Azure Monitoru](../azure-monitor/platform/alerts-overview.md) na základě protokolů a metrik k identifikaci předvídatelných stavů selhání. Měli byste mít za cíl, aby všechny výstrahy žalovatelné, což znamená, že představují skutečné kritické podmínky a snaží se snížit falešně pozitivních hodnot. Dynamické [prahové hodnoty](platform/alerts-dynamic-thresholds.md) slouží k automatickému výpočtu směrných plánů na metrická data, nikoli k definování vlastních statických prahových hodnot. 
- Definujte akce pro výstrahy, které mají používat nejúčinnější způsob upozorňování správců. Dostupné [akce pro oznámení](platform/action-groups.md#create-an-action-group-by-using-the-azure-portal) jsou SMS, e-maily, nabízená oznámení nebo hlasová volání.
- Pomocí pokročilejších akcí [se můžete připojit k nástroji ITSM](platform/itsmc-overview.md) nebo k jiným systémům pro správu výstrah prostřednictvím [webhooků](platform/activity-log-alerts-webhook.md).
- Navažte situace identifikované ve výstrahách i pomocí [runbooků Azure Automation](../automation/automation-webhooks.md) nebo [logic apps,](/connectors/custom-connectors/create-webhook-trigger) které se můžou spouštět z výstrahy pomocí webhooků. 
- Automatické [škálování](../azure-monitor/learn/tutorial-autoscale-performance-schedule.md) slouží k dynamickému zvyšování a snižování výpočetních prostředků na základě shromážděných metrik.

## <a name="prepare-dashboards-and-workbooks"></a>Příprava řídicích panelů a sešitů
Zajištění, že váš vývoj a operace mají přístup ke stejné telemetrii a nástrojům, jim umožní zobrazit vzory v celém prostředí a minimalizovat střední čas pro detekci (MTTD) a střední čas obnovení (MTTR).

- Připravte [vlastní řídicí panely](../azure-monitor/learn/tutorial-app-dashboards.md) na základě běžných metrik a protokolů pro různé role ve vaší organizaci. Řídicí panely můžou kombinovat data ze všech prostředků Azure.
- Připravte [sešity,](../azure-monitor/app/usage-workbooks.md) abyste zajistili sdílení znalostí mezi vývojem a provozem. Ty mohou být připraveny jako dynamické sestavy s metrickými grafy a dotazy protokolů, nebo dokonce jako průvodce odstraňováním potíží připravené vývojáři, kteří pomáhají zákaznické podpoře nebo operacím při řešení základních problémů.

## <a name="continuously-optimize"></a>Průběžně optimalizovat
 Monitorování je jedním ze základních aspektů populární filozofie Build-Measure-Learn, která doporučuje průběžné sledování vašich metrik klíčového principu a uživatelského chování a následné snahy o jejich optimalizaci prostřednictvím optimalizace optimalizace. Azure Monitor vám pomůže shromažďovat metriky a protokoly relevantní pro vaši firmu a podle potřeby přidat nové datové body v příštím nasazení.

- Pomocí nástrojů v application insights [můžete sledovat chování a zapojení koncových uživatelů](../azure-monitor/learn/tutorial-users.md).
- Pomocí [analýzy dopadů](../azure-monitor/app/usage-impact.md) můžete určit prioritu oblastí, na které se zaměřit, aby bylo třeba využít důležité ukazatele kukly.


## <a name="next-steps"></a>Další kroky

- Další informace o rozdílových součástech [služby Azure Monitor](overview.md).
- [Přidejte průběžné monitorování](../azure-monitor/app/continuous-monitoring.md) do kanálu vydání.
