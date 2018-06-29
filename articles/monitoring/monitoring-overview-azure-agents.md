---
title: Přehled služby Azure agenty nástroje monitorování | Microsoft Docs
description: Tento článek obsahuje podrobný přehled o Azure k dispozici agenty, kteří podporují monitorování virtuálních počítačích Azure.
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
ms.openlocfilehash: a399c3968e5ee1e2d1f6d623a68dbb1e15cef212
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2018
ms.locfileid: "37088609"
---
# <a name="overview-of-the-azure-agents-to-monitor-azure-virtual-machines"></a>Přehled Azure agentů k monitorování virtuálních počítačů Azure
Microsoft Azure poskytuje několik způsobů shromažďování různých typů dat z virtuálních počítačů hostovaných v Azure nebo jiných poskytovatelů cloudu systémem Microsoft Windows a Linux.  Tento článek vám pomůže popisují rozdíly a možnosti k dispozici s každého agenta v pořadí, abyste mohli určit, která bude podporovat vaší správy služby nebo obecné požadavky na monitorování.  

## <a name="comparing-azure-diagnostic-and-log-analytics-agent"></a>Porovnání agenta Azure diagnostiku a analýzy protokolů
Dnes ve službě Azure existují dva typy agentů k dispozici pro monitorování virtuální počítač Azure – rozšíření diagnostiky Azure a Agent analýzy protokolů pro Linux a Windows.  Zásadně tyto agenty slouží ke shromažďování metrik a protokoly a předávání do úložiště. Který je ale kde jejich podobnosti ukončení.  

[Rozšíření Azure Diagnostics](../monitoring-and-diagnostics/azure-diagnostics.md), které bylo zadáno pro Azure Cloud Services vzhledem k tomu, že jsou obvykle dostupné v 2010, je agenta, který poskytuje jednoduché shromažďování diagnostických dat z Azure IaaS prostředků jako virtuální počítač, a zachovat ho do úložiště Azure.  Jakmile se v úložišti, rozhodnete zobrazit s jedním z několika dostupných nástrojů, jako například [Průzkumníka serveru v sadě Visual Studio](../vs-azure-tools-storage-resources-server-explorer-browse-manage.md) a [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).

Můžete shromažďovat:

* Předdefinovanou sadu čítačů výkonu operačního systému a protokoly událostí nebo můžete určit, které se mají shromažďovat. 
* Všechny požadavky nebo neúspěšné požadavky na webový server služby IIS
* Aplikace .NET výstupní protokoly trasování
* Trasování událostí pro události trasování událostí pro Windows (Windows) 
* Shromažďovat události protokolu syslog  
* Výpisy stavu systému 

Data můžete případně předají do [Application Insights](../application-insights/app-insights-cloudservices.md), [analýzy protokolů](../log-analytics/log-analytics-overview.md), nebo mimo platformu Azure Services pomocí [centra událostí](../event-hubs/event-hubs-what-is-event-hubs.md). 

Pro pokročilé monitorování, které budete potřebovat více než shromažďování metrik a podmnožinu protokoly, se požaduje agent analýzy protokolů pro systém Windows a Linux.  S tímto agentem budete moci využívat služby Azure, jako je například automatizace a analýzy protokolů, včetně úplnou sadu funkcí, které nabízejí, k poskytování komplexní správu virtuální počítače Azure prostřednictvím jejich životního cyklu. To zahrnuje:

* [Správa Azure Automation aktualizací](../automation/automation-update-management.md) aktualizací operačního systému
* [Konfigurace Azure Automation žádaný stavu](../automation/automation-dsc-overview.md) pro zachování konzistentní konfigurace stavu
* Sledovat změny konfigurace s [inventáře a sledování změn automatizace Azure](../automation/automation-change-tracking.md)
* Kolekce vlastní protokoly z operačního systému a hostovaných aplikací, jako [FluentD](../log-analytics/log-analytics-data-sources-json.md), [vlastní protokoly](../log-analytics/log-analytics-data-sources-custom-logs.md), [MySQL a Apache](../log-analytics/log-analytics-data-sources-linux-applications.md) s analýzy protokolů
* Azure services, jako [Application Insights](https://docs.microsoft.com/azure/application-insights/) a [Azure Security Center](https://docs.microsoft.com/azure/security-center/) nativně ukládají data přímo v analýzy protokolů.  

## <a name="next-steps"></a>Další postup

- V tématu [shromažďovat data z počítačů ve vašem prostředí s analýzy protokolů](../log-analytics/log-analytics-concept-hybrid.md) zkontrolovat požadavky a dostupné metody nasazení agenta do počítače ve vašem datovém centru nebo jiném cloudové prostředí.
- V článku [Shromažďování dat o virtuálních počítačích Azure](../log-analytics/log-analytics-quick-collect-azurevm.md) najdete informace o konfiguraci shromažďování dat z virtuálních počítačů Azure. 