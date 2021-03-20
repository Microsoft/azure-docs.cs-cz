---
title: Přehled automatického škálování pomocí škálovacích sad virtuálních počítačů Azure
description: Seznamte se s různými způsoby, jak můžete automaticky škálovat sadu škálování virtuálních počítačů Azure na základě výkonu nebo podle pevného plánu.
author: avirishuv
ms.author: avverma
ms.topic: overview
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 06/30/2020
ms.reviewer: jushiman
ms.custom: avverma
ms.openlocfilehash: dbd2d6dc3034ff64ef6b5d4d36d836ce8462b154
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100591403"
---
# <a name="overview-of-autoscale-with-azure-virtual-machine-scale-sets"></a>Přehled automatického škálování pomocí škálovacích sad virtuálních počítačů Azure
Sada škálování virtuálních počítačů Azure může automaticky zvýšit nebo snížit počet instancí virtuálních počítačů, které spouštějí vaši aplikaci. Toto automatizované a elastické chování omezuje režijní náklady na správu, které sledují a optimalizují výkon vaší aplikace. Vytvoříte pravidla, která definují přijatelný výkon pro pozitivní prostředí zákazníka. Když jsou splněné tyto prahové hodnoty, pravidla automatického škálování přijímají akci pro úpravu kapacity sady škálování. Můžete také naplánovat události pro automatické zvýšení nebo snížení kapacity sady škálování v pevně dané době. Tento článek poskytuje přehled o dostupných metrikách výkonu a o tom, jaké akce lze provádět pomocí automatického škálování.


## <a name="benefits-of-autoscale"></a>Výhody automatického škálování
Pokud se požadavky na vaši aplikaci zvýší, zvýší se i zatížení instancí virtuálních počítačů ve škálovací sadě. Pokud je toto zvýšené zatížení konzistentní, a nejedná se pouze o krátkou poptávku, můžete nakonfigurovat pravidla automatického škálování pro zvýšení počtu instancí virtuálních počítačů ve škálovací sadě.

> [!NOTE]
> Při použití automatických oprav instancí pro sadu škálování může být maximální počet instancí v sadě škálování 200. Přečtěte si další informace o [automatických opravách instancí](./virtual-machine-scale-sets-automatic-instance-repairs.md).

Po vytvoření těchto instancí virtuálních počítačů a nasazení aplikací do nich začne škálovací sada distribuovat provoz prostřednictvím nástroje pro vyrovnávání zatížení. Můžete řídit, jaké metriky se mají monitorovat, jako je například procesor nebo paměť, jak dlouho musí zatížení aplikace splňovat danou prahovou hodnotu, a kolik instancí virtuálních počítačů se má přidat do sady škálování.

Večer nebo o víkendu se požadavky na vaši aplikaci můžou snížit. Pokud je toto snížené zatížení po určitou dobu konzistentní, můžete nakonfigurovat pravidla automatického škálování pro snížení počtu instancí virtuálních počítačů ve škálovací sadě. Tato akce horizontálního snížení kapacity sníží náklady na provoz škálovací sady, protože budete spouštět pouze takový počet instancí, který je potřeba ke zpracování aktuálních požadavků.


## <a name="use-host-based-metrics"></a>Použít metriky založené na hostiteli
Můžete vytvořit pravidla automatického škálování, která zabudovanou metriky hostitele zpřístupní z instancí virtuálních počítačů. Metriky hostitele poskytují přehled o výkonu instancí virtuálních počítačů ve skupině škálování bez nutnosti instalovat nebo konfigurovat další agenty a kolekce dat. Pravidla automatického škálování, která používají tyto metriky, můžou škálovat nebo v počtu instancí virtuálních počítačů v reakci na využití procesoru, nároky na paměť nebo přístup k disku.

Pravidla automatického škálování využívající metriky hostitele je možné vytvořit pomocí některého z následujících nástrojů:

- [Azure Portal](virtual-machine-scale-sets-autoscale-portal.md)
- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Azure CLI](tutorial-autoscale-cli.md)
- [Šablony Azure](tutorial-autoscale-template.md)

Pokud chcete vytvořit pravidla automatického škálování, která používají podrobnější metriky výkonu, můžete [nainstalovat a nakonfigurovat rozšíření diagnostiky Azure](#in-guest-vm-metrics-with-the-azure-diagnostics-extension) na INSTANCÍCH virtuálních počítačů nebo [nakonfigurovat svoji aplikaci pomocí App Insights](#application-level-metrics-with-app-insights).

Pravidla automatického škálování, která používají metriky založené na hostiteli, metriky virtuálního počítače hosta s diagnostickým rozšířením Azure a App Insights můžou používat následující nastavení konfigurace.

### <a name="metric-sources"></a>Zdroje metriky
Pravidla automatického škálování můžou používat metriky z jednoho z následujících zdrojů:

| Zdroj metriky        | Případ použití                                                                                                                     |
|----------------------|------------------------------------------------------------------------------------------------------------------------------|
| Aktuální sada škálování    | Pro metriky založené na hostiteli, které nevyžadují instalaci nebo konfiguraci dalších agentů.                                  |
| Účet úložiště      | Diagnostické rozšíření Azure zapisuje metriky výkonu do úložiště Azure, které se pak spotřebují pro aktivaci pravidel automatického škálování. |
| Fronta služby Service Bus    | Aplikace nebo jiné komponenty mohou přenášet zprávy ve frontě Azure Service Bus pro aktivaci pravidel.                   |
| Application Insights | Balíček instrumentace nainstalovaný ve vaší aplikaci, který streamuje metriky přímo z aplikace.                         |


### <a name="autoscale-rule-criteria"></a>Kritéria pravidla automatického škálování
Následující metriky založené na hostiteli jsou k dispozici pro použití při vytváření pravidel automatického škálování. Pokud používáte diagnostické rozšíření Azure nebo App Insights, definujete, které metriky se mají monitorovat a používat s pravidly automatického škálování.

| Název metriky               |
|---------------------------|
| Procento CPU            |
| Síťové vstupy                |
| Síťové výstupy               |
| Bajty čtení z disku           |
| Bajty zápisu na disk          |
| Operace čtení z disku/s  |
| Operace zápisu na disk/s |
| Zbývající kredity procesoru     |
| Spotřebované kredity procesoru      |

Když vytváříte pravidla automatického škálování pro monitorování dané metriky, pravidla se budou pohlížet na jednu z následujících akcí agregace metrik:

| Typ agregace |
|------------------|
| Průměr          |
| Minimum          |
| Maximum          |
| Celkem            |
| Poslední             |
| Počet            |

Pravidla automatického škálování se pak aktivují, když se metriky porovnají s definovanou prahovou hodnotou pomocí jednoho z následujících operátorů:

| Operátor                 |
|--------------------------|
| Větší než             |
| Větší než nebo rovno |
| Menší než                |
| Menší než nebo rovno    |
| Je rovno                 |
| Není rovno             |


### <a name="actions-when-rules-trigger"></a>Akce při aktivaci pravidel
Když se pravidlo automatického škálování aktivuje, vaše sada škálování se může automaticky škálovat jedním z následujících způsobů:

| Operace škálování     | Případ použití                                                                                                                               |
|---------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Zvýšit počet o   | Pevný počet instancí virtuálních počítačů, které se mají vytvořit Užitečné v sadě škálování s menším počtem virtuálních počítačů.                                           |
| Zvýšit procento o | Nárůst počtu instancí virtuálních počítačů založených na procentech. Dobrá pro škálované sady škálování, kde pevně navýšení kapacity nemusí výrazně zlepšit výkon. |
| Zvýšit počet na   | Vytvořte tolik instancí virtuálních počítačů potřebných k dosažení požadovaného maximálního množství.                                                            |
| Snížit počet o   | Pevný počet instancí virtuálních počítačů, které se mají odebrat Užitečné v sadě škálování s menším počtem virtuálních počítačů.                                           |
| Snížit procento o | Snížení počtu instancí virtuálních počítačů založených na procentech. Dobrá pro škálované sady škálování, kde pevně navýšení kapacity nemusí výrazně snížit spotřebu prostředků a náklady. |
| Snížit počet na   | Odebrat tolik instancí virtuálních počítačů potřebných k dosažení požadované minimální velikosti.                                                            |


## <a name="in-guest-vm-metrics-with-the-azure-diagnostics-extension"></a>Metriky virtuálního počítače hosta s rozšířením Azure Diagnostics
Rozšíření Azure Diagnostics je agent, který běží v instanci virtuálního počítače. Agent monitoruje a ukládá metriky výkonu do služby Azure Storage. Tyto metriky výkonu obsahují podrobnější informace o stavu virtuálního počítače, například *AverageReadTime* pro disky nebo *PercentIdleTime* pro procesor. Pravidla automatického škálování můžete vytvořit na základě podrobnějšího povědomí o výkonu virtuálních počítačů, a to nejen procento využití procesoru nebo spotřeby paměti.

Pokud chcete používat rozšíření Azure Diagnostics, musíte pro své instance virtuálních počítačů vytvořit účty úložiště Azure, nainstalovat agenta diagnostiky Azure a potom nakonfigurovat virtuální počítače tak, aby streamoval konkrétní čítače výkonu na účet úložiště.

Další informace najdete v článcích popisujících povolení diagnostického rozšíření Azure na [virtuálním počítači s Linuxem](../virtual-machines/extensions/diagnostics-linux.md) nebo [virtuálním počítači s Windows](../virtual-machines/extensions/diagnostics-windows.md).


## <a name="application-level-metrics-with-app-insights"></a>Metriky na úrovni aplikace s využitím App Insights
Chcete-li získat větší přehled o výkonu aplikací, můžete použít Application Insights. Do své aplikace nainstalujete malý balíček instrumentace, který monitoruje aplikaci a pošle telemetrii do Azure. Můžete monitorovat metriky, jako jsou doby odezvy vaší aplikace, výkon načtení stránky a počet relací. Tyto metriky aplikací se dají použít k vytvoření pravidel automatického škálování na detailní a vložené úrovni, když jste aktivovali pravidla na základě užitečných přehledů, které by mohly mít dopad na činnost zákazníků.

Další informace o službě App Insights najdete v tématu [Co je Application Insights](../azure-monitor/app/app-insights-overview.md).


## <a name="scheduled-autoscale"></a>Naplánované automatické škálování
Můžete také vytvořit pravidla automatického škálování na základě plánů. Tato pravidla založená na plánech umožňují automaticky škálovat počet instancí virtuálních počítačů v pevně uvedených časech. V případě pravidel založených na výkonu může být aplikace ovlivněna výkonem před triggerem pravidel automatického škálování a jsou zřízeny nové instance virtuálních počítačů. Pokud očekáváte takovou poptávku, budou dodatečné instance virtuálních počítačů zřízené a připravené na další zákaznická použití a požadavky na aplikaci.

V následujících příkladech jsou scénáře, které můžou využívat pravidla automatického škálování na základě plánu:

- Automatické horizontální navýšení kapacity počtu instancí virtuálních počítačů na začátku pracovního dne, když se zvyšuje poptávka zákazníka Na konci pracovního dne automaticky škáluje počet instancí virtuálních počítačů, aby se minimalizovaly náklady na prostředky v případě nízkého využití aplikace.
- Pokud oddělení používá aplikaci silně v určitých částech měsíce nebo fiskálního cyklu, automaticky škáluje počet instancí virtuálních počítačů tak, aby vyhovovaly jejich dalším požadavkům.
- V případě, že existuje marketingová událost, povýšení nebo prázdninový prodej, můžete automaticky škálovat počet instancí virtuálních počítačů před předpokládanou zákaznickou poptávkou. 


## <a name="next-steps"></a>Další kroky
Můžete vytvořit pravidla automatického škálování, která používají metriky založené na hostiteli s jedním z následujících nástrojů:

- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Azure CLI](tutorial-autoscale-cli.md)
- [Šablony Azure](tutorial-autoscale-template.md)

Tento přehled podrobně popisuje, jak používat pravidla automatického škálování pro horizontální škálování a zvýšení nebo snížení *počtu* instancí virtuálních počítačů ve vaší sadě škálování. *Velikost* instance virtuálního počítače můžete zvýšit nebo snížit také vertikálně. Další informace najdete v tématu [vertikální automatické škálování pomocí služby Virtual Machine Scale Sets](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Informace o tom, jak spravovat instance virtuálních počítačů, najdete v tématu [Správa služby Virtual Machine Scale Sets pomocí Azure PowerShell](./virtual-machine-scale-sets-manage-powershell.md).

Informace o tom, jak generovat výstrahy při aktivaci pravidel automatického škálování, najdete [v tématu použití akcí automatického škálování k odesílání oznámení o výstrahách e-mailu a Webhooku v Azure monitor](../azure-monitor/autoscale/autoscale-webhook-email.md). [Protokoly auditu můžete použít také k posílání oznámení o výstrahách e-mailu a Webhooku v Azure monitor](../azure-monitor/alerts/alerts-log-webhook.md).
