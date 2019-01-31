---
title: Monitorování služby Azure Batch | Dokumentace Microsoftu
description: Další informace o monitorovacích služeb Azure, metrik, diagnostické protokoly a další funkce monitorování pro služby Azure Batch.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: lahugh
ms.openlocfilehash: a04161d80a6dd3f9db2a95a8556eaac466407047
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55470072"
---
# <a name="monitor-batch-solutions"></a>Monitorování řešení Batch

Azure a službu Batch poskytují celou řadu služeb, nástroje a rozhraní API pro monitorování řešení Batch. V tomto článku Přehled umožňuje vybrat metodu monitorování, která nejlépe vyhovuje vašim potřebám.

Přehled Azure součástmi a službami, které jsou k dispozici pro monitorování prostředků Azure najdete v tématu [aplikací a prostředků Monitoring Azure](../monitoring-and-diagnostics/monitoring-overview.md).

## <a name="subscription-level-monitoring"></a>Monitorování na úrovni předplatného

Na úrovni předplatného, který obsahuje účty Batch, [protokolu aktivit Azure](../azure-monitor/platform/activity-logs-overview.md) shromažďuje provozní události v [několik kategorií](../azure-monitor/platform/activity-logs-overview.md#categories-in-the-activity-log).

Pro účty Batch konkrétně protokolu aktivit shromažďuje události související se správou vytváření a odstraňování a klíč účtu.

Jedním ze způsobů k načtení událostí z protokolu aktivit je použití webu Azure portal. Klikněte na tlačítko **všechny služby** > **protokolu aktivit**. Nebo zadat dotaz pro události pomocí rozhraní příkazového řádku Azure, rutin prostředí PowerShell nebo rozhraní REST API pro monitorování Azure. Můžete také exportovat protokol aktivit, nebo nakonfigurovat [upozornění protokolu aktivit](../monitoring-and-diagnostics/monitoring-activity-log-alerts-new-experience.md).

## <a name="batch-account-level-monitoring"></a>Monitorování úroveň účtu batch

Sledování každého účtu Batch pomocí funkce [Azure Monitor](../azure-monitor/overview.md). Azure Monitor provádí sběr [metriky](../azure-monitor/platform/data-collection.md#metrics) a volitelně [diagnostické protokoly](../azure-monitor/platform/diagnostic-logs-overview.md) pro prostředky v oboru na úrovni účtu Batch, jako jsou fondy, úlohy a úkoly. Shromažďování a tato data využívat ručně nebo prostřednictvím kódu programu k monitorování aktivit v účtu Batch a k diagnostice problémů. Podrobnosti najdete v tématu [Batch metriky, výstrahy a protokoly pro diagnostické hodnocení a monitorování](batch-diagnostics.md).
 
> [!NOTE]
> Metriky jsou k dispozici ve výchozím nastavení v účtu Batch bez další konfigurace a znají historii posledních 30 dní. Je nutné povolit protokolování diagnostiky pro účet Batch a může způsobit dodatečné náklady na uložení nebo zpracování dat diagnostický protokol. 

## <a name="batch-resource-monitoring"></a>Sledování prostředků služby batch

V aplikacích Batch pomocí rozhraní API služby Batch ke sledování nebo dotaz na stav svých prostředků, včetně úloh, úkolů, uzly a fondy. Příklad:

* [Počet úloh a výpočetní uzel podle stavu](batch-get-resource-counts.md)
* [Efektivně vytvářet dotazy do seznamu prostředků služby Batch](batch-efficient-list-queries.md)
* [Vytváření závislostí](batch-task-dependencies.md)
* Použití [úkol Správce úloh](/rest/api/batchservice/job/add#jobmanagertask)
* Monitorování [úloh stavu](/rest/api/batchservice/task/list#taskstate)
* Monitorování [stav uzlu](/rest/api/batchservice/computenode/list#computenodestate)
* Monitorování [fondu stavu](/rest/api/batchservice/pool/get#poolstate)
* Monitorování [fondu využití v účtu](/rest/api/batchservice/pool/listusagemetrics)
* [Počet uzlů fondu podle stavu](/rest/api/batchservice/account/listpoolnodecounts)

## <a name="vm-performance-counters-and-application-monitoring"></a>Čítače výkonu virtuálních počítačů a monitorování aplikací

* [Application Insights](../azure-monitor/app/app-insights-overview.md) je služba Azure můžete prostřednictvím kódu programu sledování dostupnosti, výkonu a využití dávkových úloh a úkolů. Snadno získat čítače výkonu z výpočetních uzlů (virtuálních počítačů) a vlastních informací pro úkoly z virtuálních počítačů. 

  Příklad najdete v tématu [monitorování a ladění aplikací Batch .NET pomocí nástroje Application Insights](monitor-application-insights.md) a souvisejícím [vzorový kód](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights).

  > [!NOTE]
  > Může způsobit dodatečné náklady, jak pomocí Application Insights. Zobrazit [cenové možnosti](https://azure.microsoft.com/pricing/details/application-insights/). 
  >

* [Batch Explorer](https://github.com/Azure/BatchExplorer) je zdarma, bohatými funkcemi, samostatný klientský nástroj umožňující vytvářet, ladit a monitorovat aplikace Azure Batch. Můžete si stáhnout [instalační balíček](https://azure.github.io/BatchExplorer/) pro Mac, Linux nebo Windows. Volitelně můžete nakonfigurovat řešení Batch k [zobrazit data Application Insights](https://github.com/Azure/batch-insights) jako jsou čítače výkonu virtuálních počítačů v Průzkumníkovi služby Batch.


## <a name="next-steps"></a>Další postup

* Další informace o dostupných [rozhraních API a nástrojích služby Batch](batch-apis-tools.md) pro sestavování řešení Batch.
* Další informace o [protokolování diagnostiky](batch-diagnostics.md) pomocí služby Batch.