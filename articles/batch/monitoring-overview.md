---
title: Monitorování azure batch | Dokumenty společnosti Microsoft
description: Přečtěte si o službách monitorování Azure, metrikách, diagnostických protokolech a dalších funkcích monitorování pro Azure Batch.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: labrenne
ms.openlocfilehash: d251229c522bd4d6daca894513eaae14d244d8a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025856"
---
# <a name="monitor-batch-solutions"></a>Monitorování řešení Batch

Azure a služba Batch poskytují řadu služeb, nástrojů a api pro monitorování vašich dávkových řešení. Tento přehled článku vám pomůže zvolit přístup monitorování, který vyhovuje vašim potřebám.

Přehled součástí a služeb Azure dostupných ke sledování prostředků Azure najdete v [tématu Monitorování aplikací a prostředků Azure](../monitoring-and-diagnostics/monitoring-overview.md).

## <a name="subscription-level-monitoring"></a>Monitorování na úrovni předplatného

Na úrovni předplatného, která zahrnuje účty Batch, [protokol aktivit Azure](../azure-monitor/platform/platform-logs-overview.md) shromažďuje data provozních událostí v několika [kategoriích](../azure-monitor/platform/activity-log-view.md#categories-in-the-activity-log).

Pro dávkové účty konkrétně protokol aktivit shromažďuje události související s vytvořením a odstraněním účtu a správou klíčů.

Jedním ze způsobů, jak načíst události z vašeho protokolu aktivit, je použití portálu Azure. Klepněte na**položku Protokol aktivit** **všech služeb** > . Nebo dotaz na události pomocí Azure CLI, Rutiny PowerShell nebo rozhraní API Azure Monitor REST. Můžete také exportovat protokol aktivit nebo nakonfigurovat [výstrahy protokolu aktivit](../monitoring-and-diagnostics/monitoring-activity-log-alerts-new-experience.md).

## <a name="batch-account-level-monitoring"></a>Dávkové monitorování na úrovni účtu

Sledujte každý účet Batch pomocí funkcí [Azure Monitoru](../azure-monitor/overview.md). Azure Monitor shromažďuje [metriky](../azure-monitor/platform/data-platform-metrics.md) a volitelně [diagnostické protokoly](../azure-monitor/platform/platform-logs-overview.md) pro prostředky vymezené na úrovni účtu Batch, jako jsou fondy, úlohy a úkoly. Shromažďujte a konzumujte tato data ručně nebo programově, abyste sledovali aktivity ve vašem účtu Batch a diagnostikovali problémy. Podrobnosti naleznete v [tématu Dávkové metriky, výstrahy a protokoly pro diagnostické vyhodnocení a monitorování](batch-diagnostics.md).
 
> [!NOTE]
> Metriky jsou ve výchozím nastavení k dispozici ve vašem účtu Batch bez další konfigurace a mají 30denní klouzavou historii. Je nutné povolit protokolování diagnostiky pro účet Batch a může vám vzniknout další náklady na ukládání nebo zpracování diagnostických dat protokolu. 

## <a name="batch-resource-monitoring"></a>Monitorování dávkových prostředků

V dávkových aplikacích použijte dávková api ke sledování nebo dotazování na stav prostředků, včetně úloh, úkolů, uzlů a fondů. Například:

* [Počítání úloh a výpočet uzlů podle stavu](batch-get-resource-counts.md)
* [Vytvoření dotazů pro efektivní seznam dávkových prostředků](batch-efficient-list-queries.md)
* [Vytvoření závislostí mezi úkoly](batch-task-dependencies.md)
* Použití [úlohy správce úloh](/rest/api/batchservice/job/add#jobmanagertask)
* Sledování [stavu úlohy](/rest/api/batchservice/task/list#taskstate)
* Sledování [stavu uzlu](/rest/api/batchservice/computenode/list#computenodestate)
* Sledování [stavu fondu](/rest/api/batchservice/pool/get#poolstate)
* Sledování [využití fondu v účtu](/rest/api/batchservice/pool/listusagemetrics)
* [Počítat uzly fondu podle stavu](/rest/api/batchservice/account/listpoolnodecounts)

## <a name="vm-performance-counters-and-application-monitoring"></a>Čítače výkonu virtuálních počítačů a monitorování aplikací

* [Application Insights](../azure-monitor/app/app-insights-overview.md) je služba Azure, kterou můžete použít k programovému sledování dostupnosti, výkonu a využití dávkových úloh a úloh. Snadno získat čítače výkonu z výpočetních uzlů (VM) a vlastní informace pro úkoly mimo virtuální počítače. 

  Příklad najdete v tématu [Sledování a ladění aplikace Batch .NET s application insights](monitor-application-insights.md) a ukázkou doprovodného [kódu](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights).

  > [!NOTE]
  > Za používání Application Insights vám mohou vzniknout dodatečné náklady. Podívejte se na [cenové možnosti](https://azure.microsoft.com/pricing/details/application-insights/). 
  >

* [Batch Explorer](https://github.com/Azure/BatchExplorer) je bezplatný, bohatý, samostatný klientský nástroj, který pomáhá vytvářet, ladit a monitorovat aplikace Azure Batch. Můžete si stáhnout [instalační balíček](https://azure.github.io/BatchExplorer/) pro Mac, Linux nebo Windows. Volitelně nakonfigurujte dávkové řešení tak, aby [zobrazovalo data Application Insights,](https://github.com/Azure/batch-insights) jako jsou čítače výkonu virtuálních her v Průzkumníku dávek.


## <a name="next-steps"></a>Další kroky

* Další informace o dostupných [rozhraních API a nástrojích služby Batch](batch-apis-tools.md) pro sestavování řešení Batch.
* Přečtěte si další informace o [diagnostickém protokolování](batch-diagnostics.md) pomocí služby Batch.