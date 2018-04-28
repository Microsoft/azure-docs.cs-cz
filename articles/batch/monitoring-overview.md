---
title: Monitorování Azure Batch | Microsoft Docs
description: Další informace o monitorování služby Azure, metriky, diagnostické protokoly a dalších funkcí monitorování pro Azure Batch.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: danlep
ms.openlocfilehash: 29ac86ed5c744d37150b0f1b2db17f60306fe77e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="monitor-batch-solutions"></a>Monitorování řešení Batch

Azure a služba Batch nabízí celou řadu služeb, nástroje a rozhraní API pro sledování vašich řešení Batch. Tento článek s přehledem umožňuje vybrat monitorování přístup, který vyhovuje vašim potřebám.

Přehled Azure součástmi a službami, které jsou k dispozici pro monitorování prostředků Azure najdete v tématu [monitorování Azure aplikacím a prostředkům](../monitoring-and-diagnostics/monitoring-overview.md).

## <a name="subscription-level-monitoring"></a>Sledování na úrovni předplatného

Na úrovni předplatného, která obsahuje účty Batch, [protokol činnosti Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) shromažďuje data provozních událostí [několika kategorií](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md#categories-in-the-activity-log).

Pro účty Batch konkrétně protokol aktivit shromažďuje události související se správou vytváření a odstraňování a klíč účtu.

Jeden způsob, jak načíst události z protokolu aktivit je použití portálu Azure. Klikněte na tlačítko **všechny služby** > **protokol aktivit**. Nebo dotazu pro události pomocí rozhraní příkazového řádku Azure, rutiny prostředí PowerShell nebo rozhraní REST API Azure monitorování. Můžete také exportovat protokol aktivit, nebo můžete nakonfigurovat [aktivity protokolu výstrahy](../monitoring-and-diagnostics/monitoring-activity-log-alerts-new-experience.md).

## <a name="batch-account-level-monitoring"></a>Monitorování úrovni účtu batch

Monitorování každého účtu Batch pomocí funkce [Azure monitorování](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md). Shromažďuje Azure monitorování [metriky](../monitoring-and-diagnostics/monitoring-overview-metrics.md) a volitelně [diagnostické protokoly](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) pro prostředky v oboru na úrovni účtu Batch, například fondy, úlohy a úkoly. Shromažďování a využívat tato data ručně nebo prostřednictvím kódu programu sledovat aktivity v účtu Batch a diagnostikovat problémy. Podrobnosti najdete v tématu [dávky metriky, výstrahy a protokoly pro monitorování a diagnostiky vyhodnocení](batch-diagnostics.md).
 
> [!NOTE]
> Metriky jsou dostupné ve výchozím nastavení v účtu Batch bez další konfigurace a mají postupnou historii 30 dnů. Je nutné povolit protokolování diagnostiky pro účet Batch a může způsobit dodatečné náklady na ukládání nebo zpracování dat protokolů diagnostiky. 

## <a name="batch-resource-monitoring"></a>Sledování prostředků batch

V aplikacích Batch pomocí rozhraní API služby Batch ke sledování nebo dotaz na stav svých prostředků, včetně úloh, úloh, uzly a fondy. Příklad:

* [Počet úloh podle stavu](batch-get-task-counts.md)
* [Efektivně vytvářet dotazy ke prostředky Batch seznamu](batch-efficient-list-queries.md)
* [Vytvoření závislosti úkolů](batch-task-dependencies.md)
* Použití [úkolu Správce úloh](/rest/api/batchservice/job/add#jobmanagertask)
* Monitorování [úloh stavu](/rest/api/batchservice/task/list#taskstate)
* Monitorování [stav uzlu](/rest/api/batchservice/computenode/list#computenodestate)
* Monitorování [fond stavu](/rest/api/batchservice/pool/get#poolstate)
* Monitorování [fond využití v účtu](/rest/api/batchservice/pool/listusagemetrics)
* [Počet uzlů fondu podle stavu](/rest/api/batchservice/account/listpoolnodecounts)

## <a name="vm-performance-counters-and-application-monitoring"></a>Čítače výkonu virtuálních počítačů a monitorování aplikací

* [Application Insights](../application-insights/app-insights-overview.md) je služba Azure, můžete prostřednictvím kódu programu sledování dostupnosti a výkonu a využití dávkové úlohy a úlohy. Snadno get čítače výkonu z výpočetních uzlů (virtuálních počítačů) a vlastní informace pro úlohy z virtuálních počítačů. 

  Příklad, naleznete v části [sledování a ladění aplikací Batch .NET pomocí nástroje Application Insights](monitor-application-insights.md) a doprovodných [ukázka kódu](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights).

  > [!NOTE]
  > Mohou být účtovány další poplatky za použití Application Insights. Najdete v článku [ceny možnosti](https://azure.microsoft.com/pricing/details/application-insights/). 
  >

* [BatchLabs](https://github.com/Azure/BatchLabs) je bezplatný a samostatný klientský nástroj s bohatými funkcemi, který pomáhá vytvářet, ladit a monitorovat aplikace Azure Batch. Můžete si stáhnout [instalační balíček](https://azure.github.io/BatchLabs/) pro Mac, Linux nebo Windows. Volitelně lze konfigurovat řešení Batch [zobrazit data Application Insights](https://github.com/Azure/batch-insights) například čítače výkonu virtuálních počítačů v BatchLabs.


## <a name="next-steps"></a>Další postup

* Další informace o dostupných [rozhraních API a nástrojích služby Batch](batch-apis-tools.md) pro sestavování řešení Batch.
* Další informace o [protokolování diagnostiky](batch-diagnostics.md) pomocí služby Batch.