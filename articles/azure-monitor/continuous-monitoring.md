---
title: Nepřetržité monitorování pomocí Azure Monitor | Microsoft Docs
description: Popisuje konkrétní kroky pro použití Azure Monitor k povolení průběžného monitorování v rámci vašich pracovních postupů.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/12/2018
ms.openlocfilehash: 89ce214ef9686d2704878304b8b324871ad9473f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100579502"
---
# <a name="continuous-monitoring-with-azure-monitor"></a>Nepřetržité monitorování pomocí Azure Monitor

Nepřetržité monitorování se týká procesu a technologie potřebné k začlenění monitorování v každé fázi životního cyklu DevOps a provozu IT. Pomáhá nepřetržitě zajistit stav, výkon a spolehlivost vaší aplikace a infrastruktury při přesunu z vývoje do produkčního prostředí. Průběžné monitorování vychází z konceptů průběžné integrace a průběžného nasazování (CI/CD), které vám pomůžou při vývoji a poskytování softwaru rychleji a spolehlivě zajistit nepřetržitou hodnotu pro uživatele.

[Azure monitor](overview.md) je jednotné řešení monitorování v Azure, které poskytuje plně zabalení v rámci aplikací a infrastruktury v cloudu i v místním prostředí. Funguje bez problémů se sadou [Visual Studio a Visual Studio Code](https://visualstudio.microsoft.com/) během vývoje a testování a integruje se se službou [Azure DevOps](/azure/devops/user-guide/index) pro správu verzí a správu pracovních položek během nasazení a provozu. Tato možnost se dokonce integruje do ITSM a SIEM nástrojů podle vašeho výběru, aby bylo možné sledovat problémy a incidenty v rámci stávajících procesů IT.

Tento článek popisuje konkrétní kroky pro použití Azure Monitor k povolení průběžného monitorování v rámci vašich pracovních postupů. Obsahuje odkazy na další dokumentaci, která poskytuje podrobné informace o implementaci různých funkcí.


## <a name="enable-monitoring-for-all-your-applications"></a>Povolit monitorování pro všechny vaše aplikace
Aby bylo možné získat pozor v celém prostředí, je nutné povolit monitorování všech webových aplikací a služeb. To vám umožní snadno vizualizovat kompletní transakce a připojení napříč všemi komponentami.

- [Azure DevOps Projects](../devops-project/overview.md) poskytují zjednodušené prostředí s existujícím kódem a úložištěm Git nebo si můžete vybrat některou z ukázkových aplikací a vytvořit tak kanál průběžné integrace (CI) a průběžného doručování (CD) do Azure.
- [Nepřetržité monitorování kanálu vydání DevOps](./app/continuous-monitoring.md) vám umožní bránu nebo vrátit zpět nasazení na základě dat monitorování.
- [Monitorování stavu](./app/monitor-performance-live-website-now.md)  umožňuje instrumentovat živou aplikaci .NET ve Windows pomocí Azure Application Insights, aniž byste museli upravovat nebo znovu nasazovat kód.
- Pokud máte přístup k kódu pro vaši aplikaci, pak povolte úplné monitorování pomocí [Application Insights](./app/app-insights-overview.md) instalací sady Azure monitor Application Insights SDK pro [rozhraní .NET](./app/asp-net.md), [.net Core](./app/asp-net-core.md), [Java](./app/java-get-started.md), [Node.js](./app/nodejs-quick-start.md)nebo [jakékoli jiné programovací jazyky](./app/platforms.md). Díky tomu můžete zadat vlastní události, metriky nebo zobrazení stránek, které jsou relevantní pro vaši aplikaci a vaši firmu.



## <a name="enable-monitoring-for-your-entire-infrastructure"></a>Povolit monitorování pro celou infrastrukturu
Aplikace jsou stejně spolehlivé jako základní infrastruktura. Monitorování povolené v celé infrastruktuře vám pomůže dosáhnout plné viditelnosti a zjednodušit zjištění potenciální hlavní příčiny, když se něco nepodaří. Azure Monitor vám pomůže sledovat stav a výkon celé hybridní infrastruktury včetně prostředků, jako jsou virtuální počítače, kontejnery, úložiště a síť.

- Automaticky získáte [metriky platforem, protokoly aktivit a diagnostické protokoly](agents/data-sources.md) z většiny vašich prostředků Azure bez konfigurace.
- Povolte hlubší monitorování pro virtuální počítače s [Azure monitor pro virtuální počítače](vm/vminsights-overview.md).
-  Povolte hlubší monitorování pro clustery AKS s [Azure monitor pro kontejnery](containers/container-insights-overview.md).
- Přidejte do svého prostředí [řešení monitorování](./monitor-reference.md) pro různé aplikace a služby.


[Infrastruktura jako kód](/azure/devops/learn/what-is-infrastructure-as-code) je správa infrastruktury v popisném modelu s využitím stejné správy verzí, jakou DevOps týmy používají ke zdrojovému kódu. Zvyšuje spolehlivost a škálovatelnost vašeho prostředí a umožňuje využívat podobné procesy, které se používají ke správě vašich aplikací.

-  Pomocí [Správce prostředků šablon](./logs/resource-manager-workspace.md) můžete povolit monitorování a konfiguraci výstrah přes velkou sadu prostředků.
- Pomocí [Azure Policy](../governance/policy/overview.md) vynutili různá pravidla pro vaše prostředky. Tím se zajistí, že tyto prostředky budou vyhovovat vašim firemním standardům a smlouvám o úrovni služeb. 


##  <a name="combine-resources-in-azure-resource-groups"></a>Kombinování prostředků ve skupinách prostředků Azure
Typická aplikace v Azure dnes zahrnuje několik prostředků, jako jsou virtuální počítače a App Services nebo mikroslužby hostované v Cloud Services, clusterech AKS nebo Service Fabric. Tyto aplikace často využívají závislosti jako Event Hubs, úložiště, SQL a Service Bus.

- Kombinováním prostředků ve skupinách prostředků Azure získáte úplnou viditelnost všech prostředků, které tvoří vaše různé aplikace. [Azure monitor pro skupiny prostředků](./insights/resource-group-insights.md) nabízí jednoduchý způsob, jak sledovat stav a výkon celé aplikace v plném zásobníku a umožňuje přechod na příslušné komponenty při jakémkoli vyšetřování nebo ladění.

## <a name="ensure-quality-through-continuous-deployment"></a>Zajištění kvality prostřednictvím průběžného nasazování
Průběžná integrace a průběžné nasazování umožňují automaticky integrovat a nasazovat změny kódu do vaší aplikace na základě výsledků automatizovaného testování. Zjednodušuje proces nasazení a zajišťuje kvalitu všech změn před přechodem do produkčního prostředí.


- Použijte [Azure Pipelines](/azure/devops/pipelines) k implementaci průběžného nasazování a automatizaci celého procesu z potvrzení kódu do produkčního prostředí založeného na testech CI/CD.
- Pomocí [bran kvality](/azure/devops/pipelines/release/approvals/gates) můžete integrovat monitorování do předběžného nasazení nebo po nasazení. Tím zajistíte, že se budete dostavovat klíčové ukazatele výkonu (KPI), protože vaše aplikace přecházejí z vývoje na produkční prostředí a jakékoli rozdíly v prostředí infrastruktury nebo škálování nemají negativní vliv na klíčové ukazatele výkonu.
- [Udržujte oddělené instance monitorování](./app/separate-resources.md) mezi různými prostředími nasazení, jako je vývoj, testování, kanárskéie a výrobní oddělení. Tím se zajistí, aby shromážděná data byla v rámci přidružených aplikací a infrastruktury relevantní. Pokud potřebujete korelovat data napříč prostředími, můžete použít [grafy s více prostředky v Průzkumník metrik](./essentials/metrics-charts.md) nebo vytvářet [dotazy na více prostředků v Azure monitor](logs/cross-workspace-query.md).


## <a name="create-actionable-alerts-with-actions"></a>Vytváření výstrah s akcemi s akcemi
Důležitý aspekt monitorování je proaktivní informování správců všech aktuálních a předpokládaných problémů. 

- [V Azure monitor vytvořte výstrahy](./alerts/alerts-overview.md) na základě protokolů a metrik a Identifikujte předvídatelné stavy selhání. Měli byste mít na cíl, aby všechny výstrahy mohly reagovat na to, že představují skutečné kritické podmínky a snaží se snížit počet falešně pozitivních hodnot. Použijte [dynamické prahové hodnoty](alerts/alerts-dynamic-thresholds.md) k automatickému výpočtu standardních hodnot dat metriky, ale nemusíte definovat své vlastní statické prahové hodnoty. 
- Definujte akce pro výstrahy, které budou používat nejúčinnější způsob upozorňování správců. Dostupné [akce pro oznámení](alerts/action-groups.md#create-an-action-group-by-using-the-azure-portal) jsou zprávy SMS, e-maily, nabízená oznámení nebo hlasové hovory.
- Pomocí pokročilejších akcí se můžete [připojit k nástroji ITSM](alerts/itsmc-overview.md) nebo jiným systémům pro správu výstrah pomocí [webhooků](alerts/activity-log-alerts-webhook.md).
- Opravte situace zjištěné v výstrahách i [Azure Automation Runbooky](../automation/automation-webhooks.md) nebo [Logic Apps](/connectors/custom-connectors/create-webhook-trigger) , které lze spustit z výstrahy pomocí webhooků. 
- Pomocí automatického [škálování](./autoscale/tutorial-autoscale-performance-schedule.md) můžete dynamicky zvyšovat a snižovat výpočetní prostředky na základě shromážděných metrik.

## <a name="prepare-dashboards-and-workbooks"></a>Příprava řídicích panelů a sešitů
Zajistěte, aby měl váš vývoj a operace přístup ke stejné telemetrie a nástrojům, aby mohli zobrazit vzory napříč celým prostředím a minimalizovat střední čas ke zjištění (MTTD) a průměrný čas obnovení (MTTR).

- Připravte si [vlastní řídicí panely](./app/tutorial-app-dashboards.md) založené na běžných metrikách a protokolech pro různé role ve vaší organizaci. Řídicí panely můžou kombinovat data ze všech prostředků Azure.
- Připravte [sešity](./visualize/workbooks-overview.md) , abyste zajistili sdílení znalostí mezi vývojem a provozem. Ty by se daly připravit jako dynamické sestavy s metrickými grafy a dotazy protokolů, nebo i když Průvodci odstraňováním potíží připravené vývojáři, kteří pomáhají se zákaznickou podporou nebo operacím zvládnout základní problémy.

## <a name="continuously-optimize"></a>Neustálé optimalizace
 Monitorování je jedním ze základních aspektů oblíbených filozofie sestavení a měření, které doporučuje nepřetržitě sledovat metriky klíčových ukazatelů výkonu a chování uživatelů a pak se snaží jejich optimalizaci prostřednictvím iterací plánování. Azure Monitor vám pomůže shromáždit metriky a protokoly, které jsou relevantní pro vaši firmu, a v případě potřeby přidat nové datové body do dalšího nasazení.

- Pomocí nástrojů v Application Insights můžete [sledovat chování a zapojení koncových uživatelů](./app/tutorial-users.md).
- Použijte [analýzu dopadů](./app/usage-impact.md) , která vám umožní určit prioritu oblastí, na které se chcete zaměřit, na důležité klíčové ukazatele výkonu.


## <a name="next-steps"></a>Další kroky

- Přečtěte si o rozdílných součástech [Azure monitor](overview.md).
- [Přidejte nepřetržité monitorování](./app/continuous-monitoring.md) do vašeho kanálu pro vydávání verzí.