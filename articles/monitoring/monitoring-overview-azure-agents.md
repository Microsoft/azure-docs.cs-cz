---
title: Přehled služby Azure agentů monitorování | Dokumentace Microsoftu
description: Tento článek obsahuje podrobný přehled o Azure k dispozici agenty, které podporují monitorování na virtuálních počítačích Azure.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: magoedte
ms.openlocfilehash: 81db6720422de111cc5b390c58e9020d7c19f90a
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51282029"
---
# <a name="overview-of-the-azure-agents-to-monitor-azure-virtual-machines"></a>Přehled Azure agentům monitorovat virtuální počítače Azure
Microsoft Azure poskytuje několik způsobů, jak shromažďovat různé druhy dat z virtuálních počítačů hostovaných v Azure nebo jiných poskytovatelů cloudových služeb s Microsoft Windows a Linux.  Tento článek vám pomůže popisují rozdíly a funkce přináší každého agenta v pořadí, abyste mohli určit, která bude podporovat správu služby nebo obecné požadavky na monitorování.  

## <a name="comparing-azure-diagnostic-and-log-analytics-agent"></a>Porovnání agenta diagnostiky Azure a Log Analytics
Dnes v Azure existují dva typy agentů k dispozici pro monitorování virtuálního počítače Azure – rozšíření Azure Diagnostics a agenta Log Analytics pro systémy Linux a Windows.  V podstatě Tito agenti jsou navržené ke shromažďování metrik a protokolů a předávání do úložiště. Který je však kde ukončit jejich podobnosti.  

[Rozšíření Azure Diagnostics](../monitoring-and-diagnostics/azure-diagnostics.md), který byl poskytnut pro Azure Cloud Services od stala všeobecně dostupná v roce 2010, je agent, který poskytuje simple shromažďování diagnostických dat z prostředku Azure IaaS jako virtuální počítač, a zachovat ho do úložiště Azure.  Jakmile se v úložišti, jste se rozhodli zobrazení s jedním z několika dostupných nástrojů, jako například [Průzkumníku serveru v sadě Visual Studio](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) a [Průzkumníka služby Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md).

Můžete shromažďovat:

* Předdefinované sady čítačů výkonu operačního systému a protokoly událostí nebo je můžete určit, které se mají shromažďovat. 
* Všechny požadavky a/nebo neúspěšné požadavky na webový server služby IIS
* Výstupní protokoly trasování aplikace .NET
* Trasování událostí pro Windows (ETW) 
* Shromažďovat události protokolu syslog  
* Výpisy stavu systému 

Data můžete také předávat [Application Insights](../application-insights/app-insights-cloudservices.md), [Log Analytics](../log-analytics/log-analytics-queries.md), nebo mimo Azure services pomocí [centra událostí](../event-hubs/event-hubs-about.md). 

U rozšířené monitorování, kdy potřebujete víc než shromažďování metrik a podmnožinu protokolů, se vyžaduje agenta Log Analytics pro Windows a Linux.  Pomocí tohoto agenta budete moct využívat služby Azure, například služby Automation a Log Analytics, včetně kompletní sadu funkcí, které nabízejí, k poskytování komplexní správu virtuálních počítačů Azure prostřednictvím jejich životního cyklu. To zahrnuje:

* [Azure Automation Update management](../automation/automation-update-management.md) aktualizací operačního systému
* [Azure Automation Desired State Configuration](../automation/automation-dsc-overview.md) udržovat konzistentní konfiguraci stavu
* Sledovat změny konfigurace s [Azure Automation Change Tracking a Inventory](../automation/automation-change-tracking.md)
* Kolekce vlastních protokolů z operačního systému a hostovaných aplikací, jako [FluentD](../log-analytics/log-analytics-data-sources-json.md), [vlastní protokoly](../log-analytics/log-analytics-data-sources-custom-logs.md), [MySQL a Apache](../log-analytics/log-analytics-data-sources-linux-applications.md) službou Log Analytics
* Služby Azure, jako [Application Insights](https://docs.microsoft.com/azure/application-insights/) a [Azure Security Center](https://docs.microsoft.com/azure/security-center/) nativně ukládají data přímo do Log Analytics.  

## <a name="next-steps"></a>Další postup

- Zobrazit [shromažďování dat z počítačů ve vašem prostředí s využitím Log Analytics](../log-analytics/log-analytics-concept-hybrid.md) ke kontrole požadavků a dostupné metody nasazení agenta na počítače ve vašem datovém centru nebo jiných cloudovém prostředí.
- V článku [Shromažďování dat o virtuálních počítačích Azure](../log-analytics/log-analytics-quick-collect-azurevm.md) najdete informace o konfiguraci shromažďování dat z virtuálních počítačů Azure. 
