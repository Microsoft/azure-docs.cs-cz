---
title: Přehled automatického škálování se škálovacími sadami virtuálních počítačů Azure | Dokumentace Microsoftu
description: Další informace o různých způsobech, jakými může automaticky škálovat škálovací sady založené na výkon nebo podle pevného plánu Azure virtuálních počítačů
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: d29a3385-179e-4331-a315-daa7ea5701df
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4c33466af6e57264e02a7d5c298f7f1f9fbf4ed8
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44717297"
---
# <a name="overview-of-autoscale-with-azure-virtual-machine-scale-sets"></a>Nastaví přehled automatického škálování díky škálování virtuálních počítačů Azure
Škálovací sady virtuálních počítačů Azure můžete automaticky zvýšit nebo snížit počet instancí virtuálních počítačů, na kterých běží vaše aplikace. Toto chování automatizované a elastické snižuje režie na správu pro monitorování a optimalizace výkonu vaší aplikace. Můžete vytvořit pravidla, která definují přijatelný výkon pro pozitivní zkušenosti. Pokud jsou splněny tyto definované prahové hodnoty, pravidla automatického škálování provést akci Upravit kapacitu škálovací sady. Můžete také naplánovat události pro automatické zvýšení nebo snížení kapacity škálovací sady na pevnou časy. Tento článek obsahuje základní informace o výkonu, které metriky jsou k dispozici a jaké akce automatického škálování můžete provádět.


## <a name="benefits-of-autoscale"></a>Výhody automatického škálování
Pokud se požadavky na vaši aplikaci zvýší, zvýší se i zatížení instancí virtuálních počítačů ve škálovací sadě. Pokud je toto zvýšené zatížení konzistentní, a nejedná se pouze o krátkou poptávku, můžete nakonfigurovat pravidla automatického škálování pro zvýšení počtu instancí virtuálních počítačů ve škálovací sadě.

Po vytvoření těchto instancí virtuálních počítačů a nasazení aplikací do nich začne škálovací sada distribuovat provoz prostřednictvím nástroje pro vyrovnávání zatížení. Můžete řídit, které metriky pro monitorování, jako je například využití procesoru nebo paměti, jak dlouho musí zatížení aplikace dosahovat dané prahové hodnoty a nastavte počet instancí virtuálních počítačů, které chcete přidat do škálovací.

Večer nebo o víkendu se požadavky na vaši aplikaci můžou snížit. Pokud je toto snížené zatížení po určitou dobu konzistentní, můžete nakonfigurovat pravidla automatického škálování pro snížení počtu instancí virtuálních počítačů ve škálovací sadě. Tato akce horizontálního snížení kapacity sníží náklady na provoz škálovací sady, protože budete spouštět pouze takový počet instancí, který je potřeba ke zpracování aktuálních požadavků.


## <a name="use-host-based-metrics"></a>Použít metriky hostitele
Dostupné metriky této integrované hostitele můžete vytvořit pravidla automatického škálování z vašich instancí virtuálních počítačů. Metriky hostitele vám poskytnou přehled o výkonu instancí virtuálních počítačů ve škálovací sadě, aniž byste museli nainstalovat nebo nakonfigurovat další agenty a data kolekce. Pravidla automatického škálování, které používají tyto metriky můžete horizontální navýšení kapacity nebo počtu instancí virtuálních počítačů v reakci na využití procesoru, požadavky na paměť nebo přístup k disku.

Pravidla automatického škálování využívající metriky hostitele je možné vytvořit pomocí některého z následujících nástrojů:

- [Azure Portal](virtual-machine-scale-sets-autoscale-portal.md)
- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Azure CLI 2.0](tutorial-autoscale-cli.md)
- [Šablony Azure](tutorial-autoscale-template.md)

Chcete-li vytvořit pravidla automatického škálování použít podrobnější metriky výkonu, můžete [nainstalujte a nakonfigurujte rozšíření Azure diagnostics](#in-guest-vm-metrics-with-the-azure-diagnostics-extension) na instancích virtuálních počítačů nebo [konfigurace vaší aplikace pomocí App Insights](#application-level-metrics-with-app-insights).

Pravidla automatického škálování využívající metriky hostitele, metrik na hostovi virtuálního počítače se diagnostické rozšíření Azure a App Insights můžete použít následující nastavení konfigurace.

### <a name="metric-sources"></a>Metriky zdroje
Pravidla automatického škálování můžete použít metriky z jednoho z následujících zdrojů:

| Zdroj metriky        | Případ použití                                                                                                                     |
|----------------------|------------------------------------------------------------------------------------------------------------------------------|
| Aktuální škálovací sady    | Pro metriky hostitele, které nevyžadují žádné další agenty do nainstalovány nebo nakonfigurovány.                                  |
| Účet úložiště      | Diagnostické rozšíření Azure zapíše do úložiště Azure, který je pak zpracován k aktivaci pravidla automatického škálování metriky výkonu. |
| Fronta Service Bus    | Vaše aplikace ani jiné součásti může přenášet zprávy ve frontě služby Azure Service Bus na aktivační událost pravidla.                   |
| Application Insights | Instrumentační balíček, nainstalované ve vaší aplikaci, která jsou streamována metriky přímo z aplikace.                         |


### <a name="autoscale-rule-criteria"></a>Kritéria pravidla automatického škálování
Následující metriky hostitele jsou k dispozici pro použití při vytváření pravidel automatického škálování. Pokud používáte diagnostické rozšíření Azure nebo App Insights, můžete definovat, které metriky pro monitorování a pomocí pravidel automatického škálování.

| Název metriky               |
|---------------------------|
| Procento CPU            |
| Síťové vstupy                |
| Síťové výstupy               |
| Čtení z disku – bajty           |
| Zápis na disk – bajty          |
| Čtení z disku – operace/s  |
| Zápis na disk – operace/s |
| Zbývající kredity CPU     |
| Spotřebované kredity CPU      |

Při vytváření pravidel automatického škálování pro danou metriku monitorování, podívejte se na jednu z následujících akcí agregace metrik pravidla:

| Typ agregace |
|------------------|
| Průměr          |
| Minimální          |
| Maximum          |
| Celkem            |
| Poslední             |
| Počet            |

Pravidla automatického škálování se pak zobrazí, když metriky se porovná s definovanou prahovou hodnotu s jedním z následujících operátorů:

| Operátor                 |
|--------------------------|
| Větší než             |
| Je větší nebo rovno |
| Méně než                |
| Je menší nebo rovno    |
| Rovno                 |
| Není rovno             |


### <a name="actions-when-rules-trigger"></a>Akce při aktivaci pravidla
Když automatické škálování pravidlo aktivuje, může automaticky škálovat škálovací sady v jednom z následujících způsobů:

| Operace škálování     | Případ použití                                                                                                                               |
|---------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Zvýšit počet o   | Pevný počet instancí virtuálních počítačů k vytvoření. Užitečné ve škálovacích sadách s menší počet virtuálních počítačů.                                           |
| Zvýšit procento o | Založený na procentech zvýšení instancí virtuálních počítačů. Vhodné pro pracovat ve větším měřítku nastaví, pokud oprava zvýšení nemusí výkon výrazně. |
| Zvýšit počet na   | Vytvořte velký počet instancí virtuálního počítače jsou potřeba k dosažení požadované maximální velikost.                                                            |
| Snížit počet o   | Pevný počet instancí virtuálních počítačů odebrat. Užitečné ve škálovacích sadách s menší počet virtuálních počítačů.                                           |
| Snížit procento o | Založený na procentech snížení instancí virtuálních počítačů. Vhodné pro pracovat ve větším měřítku nastaví, pokud oprava zvýšení nemusí výrazně snížit náklady a spotřeba prostředků. |
| Snížit počet na   | Odeberte, protože velký počet instancí virtuálního počítače jsou potřeba k dosažení požadované minimální velikost.                                                            |


## <a name="in-guest-vm-metrics-with-the-azure-diagnostics-extension"></a>Metriky na hostovi virtuálního počítače pomocí rozšíření Azure diagnostics
Rozšíření Azure diagnostics je agenta, který běží v rámci instance virtuálního počítače. Agent sleduje a metriky výkonu uloží do úložiště Azure. Tyto metriky výkonu obsahují podrobnější informace o stavu virtuálního počítače, například *AverageReadTime* disků nebo *PercentIdleTime* pro procesor. Můžete vytvořit pravidla automatického škálování, které jsou založené na podrobnější povědomí o výkon virtuálního počítače, nikoli pouze procento vytížení procesoru využití nebo paměti.

Použití rozšíření diagnostiky Azure, musíte vytvořit účty úložiště Azure pro vaše instance virtuálních počítačů, nainstalujte agenta diagnostiky Azure a konfigurace virtuálních počítačů do datového proudu konkrétních čítačích výkonu do účtu úložiště.

Další informace najdete v článcích popisujících povolení diagnostického rozšíření Azure na [virtuálním počítači s Linuxem](../virtual-machines/extensions/diagnostics-linux.md) nebo [virtuálním počítači s Windows](../virtual-machines/extensions/diagnostics-windows.md).


## <a name="application-level-metrics-with-app-insights"></a>Metriky na úrovni aplikace pomocí App Insights
Pokud chcete získat další viditelnosti v výkon vašich aplikací, můžete použít Application Insights. Nainstalujete malý Instrumentační balíček ve vaší aplikaci, která monitoruje aplikace a odesílá telemetrická data do Azure. Můžete monitorovat metriky, jako je doba odezvy aplikace, stav zatížení stránky a počítá relace. Tyto metriky aplikace lze použít k vytvoření pravidla automatického škálování na úrovni detailní a vložené při aktivaci pravidla založená na užitečné přehledy, které může mít vliv na prostředí pro zákazníky.

Další informace o službě App Insights najdete v tématu [Co je Application Insights](../application-insights/app-insights-overview.md).


## <a name="scheduled-autoscale"></a>Plánovaným automatickým Škálováním
Můžete také vytvořit pravidla automatického škálování podle plánů. Tato pravidla založené na plánu umožňují automaticky škálovat počet instancí virtuálních počítačů na pevnou časy. Pomocí pravidel založených na výkonu aplikace před trigger pravidla automatického škálování může být dopad na výkon a nové instance virtuálních počítačů jsou zřízené. Pokud takový požadavek lze předvídat, dalších instancí virtuálních počítačů jsou zřízené a je připravený k vyžádání používání dalších zákazníků.

Následující příklady jsou scénáře, které můžou mít užitek použití pravidel automatického škálování na základě plánu:

- Automaticky škálujte počet instancí virtuálních počítačů na začátku pracovního dne, kdy se zvyšuje poptávky zákazníků. Na konci pracovního dne automatické škálování počtu instancí virtuálních počítačů při nízkém využívání aplikací přes noc minimalizovat náklady na prostředky.
- Pokud aplikace používá oddělení silně na určité části měsíc nebo fiskální cyklu, automatické škálování počtu instancí virtuálních počítačů tak, aby vyhovovaly jejich nárokům na další.
- Když je marketing události, propagační akce nebo svátku prodej, může automaticky škálovat počet instancí virtuálních počítačů předem očekávané zákazníka. 


## <a name="next-steps"></a>Další postup
Můžete vytvořit pravidla automatického škálování využívající metriky hostitele s jedním z následujících nástrojů:

- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Azure CLI 2.0](tutorial-autoscale-cli.md)
- [Šablony Azure](tutorial-autoscale-template.md)

Tento přehled podrobné pomocí pravidla automatického škálování horizontálně škálovat a zvýšit nebo snížit *číslo* instancí virtuálních počítačů ve škálovací nastavit. Můžete také škálovat vertikálně zvyšte nebo snižte instance virtuálního počítače *velikost*. Další informace najdete v tématu [vertikální automatické škálování s Virtual Machine Scale sets](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Informace o tom, jak spravovat vaše instance virtuálních počítačů najdete v tématu [Správa škálovacích sad virtuálních počítačů pomocí Azure Powershellu](virtual-machine-scale-sets-windows-manage.md).

Zjistěte, jak generovat výstrahy, když vaše automatické škálování pravidla aktivační událost, najdete v článku [pomocí akcí automatického škálování můžete poslat e-mail a webhook oznámení výstrah ve službě Azure Monitor](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md). Můžete také [pomocí protokolů auditu odesílat emailová a webhooková oznámení výstrah ve službě Azure Monitor](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
