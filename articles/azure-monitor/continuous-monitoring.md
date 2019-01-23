---
title: Nepřetržité monitorování prostřednictvím služby Azure Monitor | Dokumentace Microsoftu
description: Popisuje konkrétní kroky pro používání Azure Monitor umožňuje nepřetržité monitorování v rámci vašich pracovních postupů.
author: bwren
manager: carmonm
editor: ''
services: azure-monitor
documentationcenter: azure-monitor
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: bwren
ms.openlocfilehash: fdccb4e94d9b9fd8b698575c890efad56a115a3e
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54452815"
---
# <a name="continuous-monitoring-with-azure-monitor"></a>Nepřetržité monitorování prostřednictvím služby Azure Monitor

Nepřetržité monitorování se vztahuje k procesu a technologie potřebné k začlenění pro monitorování v každé fázi vašeho DevOps a IT operace životního cyklu. To pomáhá zajistit průběžné stavu, výkonu a spolehlivosti vašich aplikací a infrastruktury při jejich přesunu z vývojového do produkčního prostředí. Průběžné monitorování sestavení na koncepci průběžné integrace a průběžného nasazování (CI/CD), které vám pomohou vyvíjet a dodávat software rychleji a spolehlivěji můžete svým uživatelům poskytnout hodnotu.

[Azure Monitor](overview.md) je jednotné řešení pro monitorování v Azure, které poskytuje kompletní observability napříč aplikacemi a infrastrukturou v cloudu i lokálně. Funguje bezproblémově se [sady Visual Studio a Visual Studio Code](https://visualstudio.microsoft.com/) během vývoje a testování a integruje se s [Azure DevOps](/azure/devops/user-guide/index) pro release management a správy pracovních položek během nasazení a operace. Se integruje i napříč nástroji ITSM a SIEM podle vašeho výběru ke sledování problémů a incidentů v rámci existující IT procesy.

Tento článek popisuje konkrétní kroky pro používání Azure Monitor umožňuje nepřetržité monitorování v rámci vašich pracovních postupů. Obsahuje odkazy na další dokumentaci, která poskytuje podrobné informace o provádění různých funkcí.


## <a name="enable-monitoring-for-all-your-applications"></a>Povolit monitorování pro všechny aplikace
Získalo observability napříč celým prostředím je potřeba povolit monitorování na všechny webové aplikace a služby. To vám umožní snadno vizualizovat transakce začátku do konce a připojení napříč všemi komponentami.

- [Projekty Azure DevOps](../devops-project/overview.md) umožňují zjednodušené prostředí s váš stávající kód a úložiště Git, nebo vyberte jednu z ukázkových aplikací k vytvoření kanálu kontinuální integrace (CI) a průběžné doručování (CD) do Azure.
- [Nepřetržité monitorování v kanálu pro vydávání verzí DevOps](../azure-monitor/app/continuous-monitoring.md) vám umožní brány nebo vrácení změn na základě monitorování dat nasazení.
- [Monitorování stavu](../azure-monitor/app/monitor-performance-live-website-now.md) umožňuje instrumentovat živou aplikaci .NET ve Windows pomocí Azure Application Insights, aniž byste museli upravovat nebo znovu nasazovat kód.
- Pokud máte přístup ke kódu pro vaši aplikaci, povolte úplné monitorování s využitím [Application Insights](../azure-monitor/app/app-insights-overview.md) nainstalováním sady Azure monitorování Application Insights SDK pro [.NET](../azure-monitor/learn/quick-monitor-portal.md), [Java ](../azure-monitor/learn/java-quick-start.md), [Node.js](../azure-monitor/learn/nodejs-quick-start.md), nebo [jiných programovacích jazycích](../azure-monitor/app/platforms.md). To umožňuje zadat vlastní události, metriky nebo zobrazení stránek, které jsou relevantní pro vaši aplikaci a vaší firmy.



## <a name="enable-monitoring-for-your-entire-infrastructure"></a>Povolit monitorování pro celou infrastrukturu
Aplikace jsou pouze stejně spolehlivá jako svoje základní infrastruktury. Máte povoleno napříč celou infrastrukturu monitorování vám pomůže dosáhnout úplné observability a usnadňují zjistit potenciální příčinu, když něco selže. Azure Monitor pomáhá sledovat stav a výkon celé hybridní infrastruktury včetně prostředky, jako jsou virtuální počítače, kontejnery, úložiště a síť.

- Automaticky získáte [platformy metriky, protokoly aktivit a diagnostické protokoly](platform/data-sources.md) od většiny vašich prostředků Azure bez konfigurace.
- Povolit hlubší monitorování virtuálních počítačů s [monitorování Azure pro virtuální počítače](insights/vminsights-overview.md).
-  Povolit podrobnější sledování clusterů se službou AKS [monitorování Azure pro kontejnery](insights/container-insights-overview.md).
- Přidat [řešení monitorování](insights/solutions-inventory.md) pro různé aplikace a služby ve vašem prostředí.


[Infrastruktura jako kód](/azure/devops/learn/what-is-infrastructure-as-code) je správa infrastruktury v popisném modelu, jak týmy DevOps používají pro zdrojový kód pomocí stejné správy verzí. Přidá spolehlivosti a škálovatelnosti pro vaše prostředí a umožňuje vám to využívat podobné procesy, které používá ke správě vašich aplikací.

-  Použití [šablon Resource Manageru](platform/template-workspace-configuration.md) k povolení monitorování a konfigurace upozornění přes velkou sadu prostředků.
- Použití [Azure Policy](../governance/policy/overview.md) do u vašich prostředků vynucují různá pravidla. Tím se zajistí, že tyto prostředky i nadále odpovídaly vašim firemním standardům a smlouvám o úrovni. 


##  <a name="combine-resources-in-azure-resource-groups"></a>Kombinovat prostředky ve skupinách prostředků Azure
Typická aplikace v Azure ještě dnes obsahuje několik prostředků, jako jsou virtuální počítače a služby App Services nebo mikroslužby hostované na cloudové služby, AKS clusterů nebo Service Fabric. Tyto aplikace se často využívají závislosti, jako jsou Event Hubs, Storage, SQL a Service Bus.

- Kombinovat inAzure prostředky skupiny prostředků se získat kompletní přehled napříč vašimi prostředky, které společně tvoří různé aplikace. [Azure Monitor pro skupiny prostředků](../azure-monitor/insights/resource-group-insights.md) poskytuje jednoduchý způsob, jak udržovat přehled o stavu a výkonu vaší aplikace celou předprodukčních a umožňuje procházení k podrobnostem příslušné komponenty pro všechny zkoušky nebo ladění.

## <a name="ensure-quality-through-continuous-deployment"></a>Zajištění kvality pomocí nepřetržité nasazování
Průběžná integrace / průběžné nasazování umožňuje automaticky integraci a nasazování změn kódu do vaší aplikace na základě výsledků automatizovaných testů. Zjednodušuje proces nasazení a zajišťuje kvality jakékoli změny před jejich přesun do produkčního prostředí.


- Použití [kanály Azure](/azure/devops/pipelines) implementovat průběžné nasazování a automatizovat celý proces od potvrzení změn kódu do produkčního prostředí založené na vaše testy CI/CD.
- Použití [brány kvality](/azure/devops/pipelines/release/approvals/gates) integrovat monitorování do před nasazením nebo po nasazení. Tím se zajistí, že vaše aplikace přesunout z dev do produkčního prostředí a případné rozdíly v prostředí infrastruktury nebo škálování není mít negativní vliv klíčových ukazatelů výkonu naplňujete metriky stavu a výkonu (KPI).
- [Udržovat samostatné monitorování instance](../azure-monitor/app/separate-resources.md) mezi vývojových, testovacích, testovací a produkční prostředí jiného nasazení. Tím se zajistí, že shromážděných dat relevantní napříč přidružené aplikace a infrastrukturu. Pokud budete potřebovat ke korelaci dat napříč prostředími, můžete použít [grafy více prostředků v Průzkumníku metrik](../azure-monitor/platform/metrics-charts.md) nebo vytvořit [dotazy napříč prostředky ve službě Log Analytics](log-query/cross-workspace-query.md).


## <a name="create-actionable-alerts-with-actions"></a>Vytváření užitečných výstrahy s akcemi
Důležité aspekty monitorování je proaktivnímu upozorňování na správci všech aktuálních a předpokládaných problémů. 

- Vytvoření [výstrah ve službě Azure Monitor](../azure-monitor/platform/alerts-overview.md) na základě protokolů a metrik k identifikaci předvídatelné selhání stavy. Měli byste mít cíl provádění všech výstrah užitečné, což znamená, že představují skutečné kritického stavu a které se snaží snížit počet falešně pozitivních výsledků. Použití [dynamickými prahovými hodnotami](platform/alerts-dynamic-thresholds.md) automaticky vypočítat směrné plány na data metriky místo definování vlastní statické prahové hodnoty. 
- Definování akcí pro oznámení používat nejúčinnějším způsobem oznámení vašim správcům. K dispozici [akcí pro oznámení](platform/action-groups.md#create-an-action-group-by-using-the-azure-portal) jsou SMS, e-mailů, nabízená oznámení nebo hlasových hovorů.
- Použití rozšířené akce, které [připojení k nástroji ITSM](platform/itsmc-overview.md) nebo jiným systémům pro správu výstrah prostřednictvím [webhooky](platform/activity-log-alerts-webhook.md).
- Napravit situacích identifikovat ve výstrahách taky [runbooků Azure Automation](../automation/automation-webhooks.md) nebo [Logic Apps](/connectors/custom-connectors/create-webhook-trigger) , který můžete spustit z výstrahy pomocí webhooků. 
- Použití [automatické škálování](../azure-monitor/learn/tutorial-autoscale-performance-schedule.md) dynamicky zvýšit a snížit vaše výpočetní prostředky na základě shromážděných metrik.

## <a name="prepare-dashboards-and-workbooks"></a>Příprava řídicí panely a sešity
Zajištění, že vývoje a provozu přístup pro stejnou telemetrii a nástroje umožňující zobrazit vzory napříč celým prostředím a minimalizovat průměrný čas potřebný k detekci (MTTD) a průměrný čas potřebný k obnovení (MTTR).

- Příprava [vlastní řídicí panely](../azure-monitor/learn/tutorial-app-dashboards.md) na základě běžné metriky a protokoly pro různé role ve vaší organizaci. Řídicí panely můžete kombinovat data ze všech prostředků Azure.
- Příprava [sešity](../azure-monitor/app/usage-workbooks.md) pro zajištění znalosti sdílení mezi vývojem a provozem. Tyto může být připravené jako dynamické sestavy s grafy metrik a dotazů na protokoly nebo dokonce odstraňování vodítka vývojáři pomoc zákaznické podpory nebo operace připravena ke zpracování základních problémů.

## <a name="continuously-optimize"></a>Průběžné optimalizaci je možné
 Monitorování patří mezi základní aspekty oblíbených filozofií sestavení-vyhodnocení-poučení, které doporučuje průběžně sledování klíčových ukazatelů výkonu a metriky chování uživatelů a potom se snaží optimalizovat prostřednictvím plánování iterací. Azure Monitor umožňuje shromažďovat metriky a protokoly, které jsou relevantní pro vaši firmu a přidání nových datových bodů v další nasazení podle potřeby.

- Pomocí nástrojů v Application Insights a [sledovat chování koncových uživatelů a zapojení](../azure-monitor/learn/tutorial-users.md).
- Použití [analýza dopadu](../azure-monitor/app/usage-impact.md) můžete určit prioritu které oblasti zaměřit se na disku, aby se důležité klíčové ukazatele výkonu.


## <a name="next-steps"></a>Další postup

- Další informace o komponentách rozdíl [Azure Monitor](overview.md).
- [Přidání průběžného monitorování](../azure-monitor/app/continuous-monitoring.md) do kanálu pro vydávání verzí.