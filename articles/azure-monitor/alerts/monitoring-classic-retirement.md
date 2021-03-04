---
title: Aktualizace klasického upozorňování & monitorování v Azure Monitor
description: Popis vyřazení klasických monitorovacích služeb a funkcí, které se dříve zobrazovaly v Azure Portal v části výstrahy (Classic).
author: yanivlavi
services: azure-monitor
ms.topic: conceptual
ms.date: 02/14/2021
ms.author: yalavi
ms.openlocfilehash: bfa92a2fc58d479edd328dba9bf02d57ec66c0c9
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041089"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Jednotné upozorňování & monitorování v Azure Monitor nahrazuje klasické výstrahy & monitorování

Azure Monitor je sjednocený monitorovací zásobník, který podporuje jednu metriku a jednu výstrahu napříč prostředky Azure. Další informace najdete v tomto [blogovém příspěvku](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/). Nové platformy pro monitorování a upozorňování Azure jsou sestavené tak, aby byly rychlejší, inteligentnější a rozšiřitelné, a přitom dodržíte rostoucí expansei cloud computingu a online pomocí inteligentního cloudového filozofie Microsoftu.

S novou platformou monitorování a upozorňování Azure se pro uživatele veřejného cloudu vyřadí klasické výstrahy v Azure Monitor, i když jsou stále v omezeném rozsahu, až do **31. května 2021**. Klasické výstrahy pro Azure Government Cloud a Azure Čína 21Vianet vybudou od **29. února 2024**.

 ![Klasická výstraha v Azure Portal](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

Doporučujeme začít a znovu vytvořit upozornění v nové platformě.

> [!IMPORTANT]
> Pravidla pro klasické výstrahy vytvořená v protokolu aktivit nebudou zastaralá ani migrována. Všechna pravidla pro klasické výstrahy vytvořená v protokolu aktivit jsou k dispozici a budou se používat jako z nových Azure Monitor – výstrahy. Další informace najdete v tématu [Vytvoření, zobrazení a správa výstrah protokolu aktivit pomocí Azure monitor](./alerts-activity-log.md). Podobně jsou k dispozici výstrahy na Service Health, které jsou k dispozici v nové Service Health oddílu. Podrobnosti najdete v tématu [výstrahy týkající se oznámení o stavu služby](../../service-health/alerts-activity-log-service-notifications-portal.md).

## <a name="unified-metrics-and-alerts-for-azure-resources"></a>Sjednocené metriky a výstrahy pro prostředky Azure

V březnu 2018 jsme vydali novou generaci výstrah pro prostředky Azure. Novější metrika a upozornění jsou rychlejší a poskytují větší členitost pomocí dimenzí. Dimenze umožňují rozdělit a filtrovat konkrétní kombinaci hodnot, podmínky nebo operace. Novější výstrahy metriky používají skupiny akcí, které umožňují další upozornění a akce automatizace. Přečtěte si další informace o [správě upozornění na metriku pomocí Azure monitor](./alerts-metric.md).

Novější metriky pro prostředky Azure jsou k dispozici jako:

- **Azure monitor standardní metriky platforem** – což nabízí populární předem vyplněnou metriku z různých služeb a produktů Azure. Další informace najdete v tomto článku o [podporovaných metrikách na Azure monitor](./alerts-metric-near-real-time.md#metrics-and-dimensions-supported) a [podpoře upozornění na metriky v Azure monitor](./alerts-metric-overview.md#supported-resource-types-for-metric-alerts).
- **Azure monitor vlastní metriky** , které poskytují metriky ze zdrojů řízených uživatelem včetně agenta Azure Diagnostics. Další informace najdete v tomto článku o [vlastních metrikách v Azure monitor](../essentials/metrics-custom-overview.md). Pomocí vlastních metrik můžete také publikovat metriky shromážděné agentem [Windows Azure Diagnostics agentem](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md) a [agentem InfluxData telegraf](../essentials/collect-custom-metrics-linux-telegraf.md).

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>Vyřazení klasického monitorování a platformy pro výstrahy

Jak bylo uvedeno dříve, starší klasické monitorování a upozorňování je pro uživatele veřejného cloudu vyřazeno. Zahrnuje uzavírání souvisejících rozhraní API, Azure Portal rozhraní a služeb, i když je stále v omezeném rozsahu, do **31. května 2021**. Klasické výstrahy pro Azure Government Cloud a Azure Čína 21Vianet vybudou od **29. února 2024**.

Rozsah odchodu je konkrétně pro klasické metriky, které jsou aktuálně dostupné v [části výstrahy (Classic)](./alerts-classic.overview.md) Azure Portal a přístupné jako prostředky [Microsoft. Insights/alertrules](/rest/api/monitor/alertrules) .

To znamená, že:

- Služba Classic monitorování a výstrahy bude vyřazena a nebude již k dispozici pro vytváření nových pravidel upozornění.
- Všechna pravidla výstrahy, která dál existují v upozorněních (Classic), budou i nadále spouštět a vyvolávat oznámení.
- Budou migrována většina klasických pravidel upozornění. Proces bude bezproblémové bez výpadků a zákazníci nebudou mít žádné ztráty v pokrytí monitorování.
- Aktivovaná oznámení budou zahrnovat novou strukturu datových částí. Cíl bude muset být přizpůsoben pro práci s novou strukturou.
- Některá z [klasických pravidel upozornění, která se nedají migrovat automaticky](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts) a která budou vyžadovat ruční akci od uživatelů, aby mohli pokračovat v práci.

> [!IMPORTANT]
> Azure Monitor zavedl [nástroj, který dobrovolně migruje](alerts-using-migration-tool.md) svá klasická pravidla upozornění na novou platformu. Zbývající pravidla budou migrována automaticky po vyřazení služby. Zákazníci budou muset zajistit, aby datová část pravidla výstrahy Classic pro automatizaci byla pro zpracování nové datové části v rámci [sjednocených metrik a upozornění pro ostatní prostředky Azure](#unified-metrics-and-alerts-for-azure-resources)po migraci klasických pravidel výstrah přizpůsobená. Další informace najdete v tématu [Příprava na migraci klasického pravidla výstrahy](alerts-prepare-migration.md).

## <a name="pricing-for-migrated-alert-rules"></a>Ceny pro migrovaná pravidla výstrah

Zavádíme Nástroj pro migraci, který vám umožní migrovat [výstrahy Azure monitor Classic](./alerts-classic.overview.md) na nové prostředí výstrah. Migrovaná pravidla výstrah a odpovídající migrované skupiny akcí (e-mail, Webhook nebo LogicApp) zůstanou zadarmo. Funkce, které jste používali s klasickými výstrahami včetně možnosti upravit prahovou hodnotu, typ agregace a členitosti agregace, budou nadále k dispozici zdarma s vaším pravidlem upozornění na migraci. Pokud však pravidlo migrované výstrahy upravíte tak, aby používalo některou z nových funkcí a oznámení z nové platformy pro upozornění, budou platit odpovídající poplatky. Další informace o cenách pro pravidla a oznámení výstrah najdete v tématu [Azure monitor ceny](https://azure.microsoft.com/pricing/details/monitor/).

Následují příklady případů, kdy se vám za pravidlo výstrahy účtují poplatky:

- Jakékoli nové (nemigrováno) pravidlo výstrahy vytvořené nad rámec volných jednotek na nové platformě Azure Monitor
- Všechna data ingestovaná a zachovaná mimo volné jednotky, kterou zahrnuje Azure Monitor
- Všechny webové testy multi-test spouštěné Application Insights
- Všechny vlastní metriky uložené mimo volné jednotky, které jsou součástí Azure Monitor
- Všechna migrovaná pravidla výstrah, která jsou upravena pro použití novějších funkcí metriky, jako je frekvence, více prostředků nebo dimenzí, [dynamické prahové hodnoty](../alerts/alerts-dynamic-thresholds.md), změna prostředku nebo signálu atd.
- Všechny migrované skupiny akcí, které jsou upraveny pro použití novějších oznámení, nebo typů akcí, jako je SMS, hlasový hovor nebo integrace ITSM.

## <a name="next-steps"></a>Další kroky

* Seznamte se s [novými sjednocenými Azure monitor](../overview.md).
* Přečtěte si další informace o nových [výstrahách Azure](./alerts-overview.md).