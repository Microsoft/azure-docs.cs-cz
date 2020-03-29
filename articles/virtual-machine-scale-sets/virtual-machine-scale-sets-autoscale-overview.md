---
title: Přehled automatického škálování pomocí škálovacích sad virtuálních počítačů Azure
description: Seznamte se s různými způsoby, jak můžete automaticky škálovat škálovací sadu virtuálních strojů Azure na základě výkonu nebo podle pevného plánu.
author: cynthn
tags: azure-resource-manager
ms.assetid: d29a3385-179e-4331-a315-daa7ea5701df
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: eb96be187502afcccfd3fb2c88f709facfbc3b59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278141"
---
# <a name="overview-of-autoscale-with-azure-virtual-machine-scale-sets"></a>Přehled automatického škálování pomocí škálovacích sad virtuálních počítačů Azure
Škálovací sada virtuálního počítače Azure může automaticky zvýšit nebo snížit počet instancí virtuálních virtuálních zařízení, které spouštějí vaši aplikaci. Toto automatizované a elastické chování snižuje režii na správu pro monitorování a optimalizaci výkonu vaší aplikace. Vytvoříte pravidla, která definují přijatelný výkon pro pozitivní zákaznickou zkušenost. Pokud jsou tyto definované prahové hodnoty splněny, pravidla automatického škálování provést akci upravit kapacitu škálovací sady. Můžete také naplánovat události tak, aby automaticky zvyšovaly nebo snižovaly kapacitu škálovací sady v pevně stanovených časech. Tento článek obsahuje přehled metriky výkonu, které jsou k dispozici a jaké akce automatické škálování lze provádět.


## <a name="benefits-of-autoscale"></a>Výhody automatického škálování
Pokud se požadavky na vaši aplikaci zvýší, zvýší se i zatížení instancí virtuálních počítačů ve škálovací sadě. Pokud je toto zvýšené zatížení konzistentní, a nejedná se pouze o krátkou poptávku, můžete nakonfigurovat pravidla automatického škálování pro zvýšení počtu instancí virtuálních počítačů ve škálovací sadě.

Po vytvoření těchto instancí virtuálních počítačů a nasazení aplikací do nich začne škálovací sada distribuovat provoz prostřednictvím nástroje pro vyrovnávání zatížení. Řídíte, jaké metriky se mají monitorovat, jako je procesor nebo paměť, jak dlouho musí zatížení aplikace splňovat danou prahovou hodnotu a kolik instancí virtuálních počítače se má přidat do škálovací sady.

Večer nebo o víkendu se požadavky na vaši aplikaci můžou snížit. Pokud je toto snížené zatížení po určitou dobu konzistentní, můžete nakonfigurovat pravidla automatického škálování pro snížení počtu instancí virtuálních počítačů ve škálovací sadě. Tato akce horizontálního snížení kapacity sníží náklady na provoz škálovací sady, protože budete spouštět pouze takový počet instancí, který je potřeba ke zpracování aktuálních požadavků.


## <a name="use-host-based-metrics"></a>Použití metrik založených na hostiteli
Můžete vytvořit pravidla automatického škálování, která integrované metriky hostitele jsou k dispozici z instancí virtuálních počítače. Metriky hostitele poskytují přehled o výkonu instancí virtuálních počítače ve škálovací sadě bez nutnosti instalace nebo konfigurace dalších agentů a kolekcí dat. Pravidla automatického škálování, která používají tyto metriky můžete horizontální navýšení kapacity nebo v počtu instancí virtuálních počítačů v reakci na využití procesoru, požadavek na paměť nebo přístup k disku.

Pravidla automatického škálování využívající metriky hostitele je možné vytvořit pomocí některého z následujících nástrojů:

- [Portál Azure](virtual-machine-scale-sets-autoscale-portal.md)
- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Azure CLI](tutorial-autoscale-cli.md)
- [Šablony Azure](tutorial-autoscale-template.md)

Chcete-li vytvořit pravidla automatického škálování, která používají podrobnější metriky výkonu, můžete [nainstalovat a nakonfigurovat rozšíření diagnostiky Azure](#in-guest-vm-metrics-with-the-azure-diagnostics-extension) na instancích virtuálních počítače nebo [nakonfigurovat aplikaci pomocí App Insights](#application-level-metrics-with-app-insights).

Pravidla automatického škálování, která používají metriky založené na hostiteli, metriky virtuálních počítačů hosta s diagnostickým rozšířením Azure a App Insights, můžou použít následující nastavení konfigurace.

### <a name="metric-sources"></a>Zdroje metrik
Pravidla automatického škálování mohou používat metriky z jednoho z následujících zdrojů:

| Zdroj metriky        | Případ použití                                                                                                                     |
|----------------------|------------------------------------------------------------------------------------------------------------------------------|
| Aktuální škálovací sada    | Pro metriky založené na hostiteli, které nevyžadují další agenty, které mají být nainstalovány nebo nakonfigurovány.                                  |
| Účet úložiště      | Diagnostické rozšíření Azure zapisuje metriky výkonu do úložiště Azure, které se pak spotřebovává, aby se aktivovala pravidla automatického škálování. |
| Fronta služby Service Bus    | Vaše aplikace nebo jiné součásti můžete přenášet zprávy ve frontě Azure Service Bus aktivovat pravidla.                   |
| Application Insights | Balíček instrumentace nainstalovaný ve vaší aplikaci, který streamuje metriky přímo z aplikace.                         |


### <a name="autoscale-rule-criteria"></a>Kritéria pravidla automatického škálování
Následující metriky založené na hostiteli jsou k dispozici pro použití při vytváření pravidel automatického škálování. Pokud používáte diagnostické rozšíření Azure nebo App Insights, definujete, které metriky se mají monitorovat a používat s pravidly automatického škálování.

| Název metriky               |
|---------------------------|
| Procento CPU            |
| Síťové vstupy                |
| Síťové výstupy               |
| Bajty čtení disku           |
| Bajty zápisu disku          |
| Operace čtení disku/s  |
| Operace zápisu na disk/s |
| Zbývající kredity procesoru     |
| Spotřebované kredity procesoru      |

Když vytváříte pravidla automatického škálování pro sledování dané metriky, pravidla se podívají na jednu z následujících akcí agregace metrik:

| Typ agregace |
|------------------|
| Průměr          |
| Minimální          |
| Maximum          |
| Celkem            |
| Poslední             |
| Počet            |

Pravidla automatického škálování se pak aktivují, když jsou metriky porovnány s definovanou prahovou hodnotou s jedním z následujících operátorů:

| Operátor                 |
|--------------------------|
| Větší než             |
| Větší nebo rovno |
| Menší než                |
| Menší nebo rovno    |
| Je rovno                 |
| Není rovno             |


### <a name="actions-when-rules-trigger"></a>Akce při aktivaci pravidel
Když se aktivuje pravidlo automatického škálování, škálovací sada může automaticky škálovat jedním z následujících způsobů:

| Operace škálování     | Případ použití                                                                                                                               |
|---------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Zvýšit počet o   | Pevný počet instancí virtuálních virtuálních< Užitečné v škálovacích sadách s menším počtem virtuálních zařízení.                                           |
| Zvýšení o procento | Zvýšení počtu instancí virtuálních vzahonek na základě procenta. Vhodné pro větší škálovací sady, kde pevné zvýšení nemusí výrazně zlepšit výkon. |
| Zvýšit počet na   | Vytvořte tolik instancí virtuálních byl potřeba k dosažení požadované maximální částky.                                                            |
| Snížit počet o   | Pevný počet instancí virtuálních virtuálních< Užitečné v škálovacích sadách s menším počtem virtuálních zařízení.                                           |
| Snížení o procento | Snížení počtu instancí virtuálních vzahonek na základě procenta. Vhodné pro větší škálovací sady, kde pevné zvýšení nemusí výrazně snížit spotřebu zdrojů a náklady. |
| Snížit počet na   | Odeberte, protože k dosažení požadované minimální částky je potřeba mnoho instancí virtuálních virtuálních<s.                                                            |


## <a name="in-guest-vm-metrics-with-the-azure-diagnostics-extension"></a>Metriky virtuálních počítačů hosta s rozšířením diagnostiky Azure
Rozšíření diagnostiky Azure je agent, který běží uvnitř instance virtuálního počítače. Agent monitoruje a ukládá metriky výkonu do úložiště Azure. Tyto metriky výkonu obsahují podrobnější informace o stavu virtuálního počítače, jako je *například AverageReadTime* pro disky nebo *PercentIdleTime* pro procesor. Můžete vytvořit pravidla automatického škálování na základě podrobnější povědomí o výkonu virtuálního počítače, nikoli pouze procento využití procesoru nebo využití paměti.

Pokud chcete použít rozšíření diagnostiky Azure, musíte vytvořit účty úložiště Azure pro vaše instance virtuálních počítačů, nainstalovat agenta diagnostiky Azure a pak nakonfigurovat virtuální počítače tak, aby streamovaly konkrétní čítače výkonu do účtu úložiště.

Další informace najdete v článcích popisujících povolení diagnostického rozšíření Azure na [virtuálním počítači s Linuxem](../virtual-machines/extensions/diagnostics-linux.md) nebo [virtuálním počítači s Windows](../virtual-machines/extensions/diagnostics-windows.md).


## <a name="application-level-metrics-with-app-insights"></a>Metriky na úrovni aplikace s přehledy aplikací
Chcete-li získat větší přehled o výkonu vašich aplikací, můžete použít Application Insights. Nainstalujete malý balíček instrumentace ve vaší aplikaci, která monitoruje aplikaci a odesílá telemetrická data do Azure. Můžete sledovat metriky, jako jsou doby odezvy aplikace, výkon načítání stránky a počty relací. Tyto metriky aplikace lze použít k vytvoření pravidel automatického škálování na podrobné a vložené úrovni, když aktivujete pravidla na základě užitečných přehledů, které mohou mít vliv na prostředí zákazníků.

Další informace o službě App Insights najdete v tématu [Co je Application Insights](../azure-monitor/app/app-insights-overview.md).


## <a name="scheduled-autoscale"></a>Plánované automatické škálování
Můžete také vytvořit pravidla automatického škálování na základě plánů. Tato pravidla založená na plánu umožňují automaticky škálovat počet instancí virtuálních virtuálních bylv pevných časech. S pravidly založenými na výkonu může mít vliv na výkon na aplikaci před aktivační události pravidla automatického škálování a nové instance virtuálních počítačů jsou zřízeny. Pokud můžete předvídat takové poptávky, další instance virtuálních aplikací jsou zřízeny a připravené pro další použití zákazníka a poptávky po aplikacích.

Následující příklady jsou scénáře, které mohou být přínosem pro použití pravidel automatického škálování založených na plánu:

- Automaticky škálujte počet instancí virtuálních virtuálních společností na začátku pracovního dne, kdy se zvýší poptávka zákazníků. Na konci pracovního dne automaticky škálujte v počtu instancí virtuálních aplikací, abyste minimalizovali náklady na prostředky přes noc, když je nízké použití aplikace.
- Pokud oddělení používá aplikaci silně v určitých částech měsíce nebo fiskálního cyklu, automaticky škálovat počet instancí virtuálních zařízení tak, aby vyhovovaly jejich další požadavky.
- Pokud se jedná o marketingovou událost, propagaci nebo prodej svátků, můžete automaticky škálovat počet instancí virtuálních virtuálních< 


## <a name="next-steps"></a>Další kroky
Pravidla automatického škálování, která používají metriky založené na hostiteli, můžete vytvořit pomocí jednoho z následujících nástrojů:

- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Azure CLI](tutorial-autoscale-cli.md)
- [Šablony Azure](tutorial-autoscale-template.md)

Tento přehled podrobně popisuje, jak pomocí pravidel automatického škálování škálovat vodorovně a zvýšit nebo snížit *počet* instancí virtuálních zařízení ve vaší škálovací sadě. Můžete také škálovat svisle a zvětšit nebo zmenšit *velikost*instance virtuálního počítače . Další informace najdete [v tématu Vertikální automatické škálování pomocí sad škálování virtuálních strojů](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Informace o tom, jak spravovat instance virtuálních počítačů, najdete v [tématu Správa škálovacích sad virtuálních strojů pomocí Azure PowerShellu](virtual-machine-scale-sets-windows-manage.md).

Informace o tom, jak generovat výstrahy při aktivaci pravidel automatického škálování, najdete v [tématu Odesílání e-mailů a upozornění webhooku v Azure Monitoru pomocí akcí automatického škálování.](../azure-monitor/platform/autoscale-webhook-email.md) Protokoly auditování můžete taky [použít k odesílání e-mailů a oznámení upozornění webhooku v Azure Monitoru](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
