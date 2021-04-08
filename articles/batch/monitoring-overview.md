---
title: Azure Batch monitorování
description: Přečtěte si o službě Azure Monitoring Services, metrikách, diagnostických protokolech a dalších funkcích monitorování pro Azure Batch.
ms.topic: how-to
ms.date: 04/05/2018
ms.openlocfilehash: b926f9c6d173cd0b8d886047eae490e4a151988f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100595433"
---
# <a name="monitor-batch-solutions"></a>Monitorování řešení Batch

Azure a služba Batch poskytují řadu služeb, nástrojů a rozhraní API pro monitorování vašich řešení Batch. Tento přehledový článek vám pomůže vybrat přístup pro monitorování, který vyhovuje vašim potřebám.

Přehled komponent a služeb Azure, které jsou k dispozici pro monitorování prostředků Azure, najdete v tématu [monitorování aplikací a prostředků Azure](../azure-monitor/overview.md).

## <a name="subscription-level-monitoring"></a>Monitorování na úrovni předplatného

Na úrovni předplatného, která zahrnuje účty Batch, shromažďuje [Protokol aktivit Azure](../azure-monitor/essentials/platform-logs-overview.md) data provozní události v [několika kategoriích](../azure-monitor/essentials/activity-log.md#view-the-activity-log).

Pro účty Batch konkrétně protokol aktivit shromažďuje události související se vytvářením a odstraňováním účtů a správou klíčů.

Jedním ze způsobů, jak načíst události z protokolu aktivit, je použít Azure Portal. Klikněte na **všechny služby**  >  **Protokol aktivit**. Nebo se můžete dotazovat na události pomocí rozhraní příkazového řádku Azure CLI, rutin PowerShellu nebo Azure Monitor REST API. Můžete také exportovat protokol aktivit nebo nakonfigurovat [výstrahy protokolu aktivit](../azure-monitor/alerts/alerts-activity-log.md).

## <a name="batch-account-level-monitoring"></a>Monitorování na úrovni účtu Batch

Monitorujte každý účet Batch pomocí funkcí [Azure monitor](../azure-monitor/overview.md). Azure Monitor shromažďuje [metriky](../azure-monitor/essentials/data-platform-metrics.md) a volitelně [diagnostické protokoly](../azure-monitor/essentials/platform-logs-overview.md) pro prostředky s rozsahem na úrovni účtu Batch, jako jsou fondy, úlohy a úkoly. Shromažďovat a spotřebovávat tato data ručně nebo programově, abyste mohli monitorovat aktivity v účtu Batch a diagnostikovat problémy. Podrobnosti najdete v tématu [metriky, výstrahy a protokoly služby Batch pro vyhodnocení a monitorování diagnostiky](batch-diagnostics.md).
 
> [!NOTE]
> Metriky jsou ve výchozím nastavení k dispozici ve vašem účtu Batch bez další konfigurace a mají 30denní průběžnou historii. Je nutné povolit protokolování diagnostiky pro účet Batch a za účelem uložení nebo zpracování dat protokolů diagnostiky můžete účtovat další náklady. 

## <a name="batch-resource-monitoring"></a>Sledování prostředků Batch

V aplikacích Batch pomocí rozhraní API služby Batch můžete monitorovat stav svých prostředků, včetně úloh, úkolů, uzlů a fondů, a dotazovat se na ně. Například:

* [Počet úloh a výpočetních uzlů podle stavu](batch-get-resource-counts.md)
* [Efektivní vytváření dotazů k vypsání prostředků Batch](batch-efficient-list-queries.md)
* [Vytváření závislostí úloh](batch-task-dependencies.md)
* Použití [úkolu správce úloh](/rest/api/batchservice/job/add#jobmanagertask)
* Monitorování [stavu úlohy](/rest/api/batchservice/task/list#taskstate)
* Monitorování [stavu uzlu](/rest/api/batchservice/computenode/list#computenodestate)
* Monitorování [stavu fondu](/rest/api/batchservice/pool/get#poolstate)
* Monitorovat [využití fondu v účtu](/rest/api/batchservice/pool/listusagemetrics)
* [Počet uzlů fondu podle stavu](/rest/api/batchservice/account/listpoolnodecounts)

## <a name="vm-performance-counters-and-application-monitoring"></a>Čítače výkonu virtuálních počítačů a monitorování aplikací

* [Application Insights](../azure-monitor/app/app-insights-overview.md) je služba Azure, kterou můžete použít k programovému monitorování dostupnosti, výkonu a využití úloh a úloh služby Batch. Snadno získáte čítače výkonu z výpočetních uzlů (virtuálních počítačů) a vlastní informace pro úlohy mimo virtuální počítače. 

  Příklad najdete v tématu [monitorování a ladění aplikace Batch .NET pomocí Application Insights](monitor-application-insights.md) a doprovodného [kódu](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights).

  > [!NOTE]
  > K používání Application Insights můžete účtovat další poplatky. Podívejte se na [cenové možnosti](https://azure.microsoft.com/pricing/details/application-insights/). 
  >

* [Batch Explorer](https://github.com/Azure/BatchExplorer) je bezplatný a samostatný klientský nástroj s bohatými funkcemi, který vám umožní vytvářet, ladit a monitorovat Azure Batch aplikace. Můžete si stáhnout [instalační balíček](https://azure.github.io/BatchExplorer/) pro Mac, Linux nebo Windows. Volitelně můžete nakonfigurovat řešení Batch tak, aby [zobrazovalo Application Insights data](https://github.com/Azure/batch-insights) , jako jsou čítače výkonu virtuálních počítačů v Batch Explorer.


## <a name="next-steps"></a>Další kroky

* Další informace o dostupných [rozhraních API a nástrojích služby Batch](batch-apis-tools.md) pro sestavování řešení Batch.
* Přečtěte si další informace o [protokolování diagnostiky](batch-diagnostics.md) pomocí dávky.
