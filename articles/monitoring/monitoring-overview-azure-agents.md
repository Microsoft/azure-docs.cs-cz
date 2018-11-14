---
title: Přehled služby Azure agentů monitorování | Dokumentace Microsoftu
description: Tento článek obsahuje podrobný přehled o dostupných agentů služby Azure, které podporují monitorování virtuálních počítačů hostovaných v Azure nebo hybridního prostředí.
services: azure-monitor
documentationcenter: azure-monitor
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: magoedte
ms.openlocfilehash: ce6f40e580595e4f3fbf0519aa1ba8be0355ded1
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51621901"
---
# <a name="overview-of-the-azure-monitoring-agents"></a>Přehled služby Azure agentů monitorování 
Microsoft Azure poskytuje několik způsobů, jak shromažďovat různé druhy dat z virtuálních počítačů hostovaných v Azure nebo jiných poskytovatelů cloudových služeb s Microsoft Windows a Linux. Tento článek vám pomůže popisují rozdíly a funkce přináší každého agenta v pořadí, abyste mohli určit, která bude podporovat vaše správy služeb IT nebo obecné požadavky na monitorování.  

## <a name="comparing-agents"></a>Porovnání agentů
Dnes v Azure existují tři typy agentů k dispozici pro monitorování virtuálního počítače Azure – rozšíření Azure Diagnostics, závislost agenta a agenta Log Analytics pro systémy Linux a Windows.  V podstatě rozšíření Azure závislostí a agentů Log Analytics jsou navržené ke shromažďování metrik a protokolů a předávání do úložiště. Který je však kde ukončit jejich podobnosti.  

### <a name="azure-diagnostic-extension"></a>Diagnostické rozšíření Azure
[Rozšíření Azure Diagnostics](../monitoring-and-diagnostics/azure-diagnostics.md) (označované jako rozšíření Windows Azure diagnostiky (WAD) nebo Linux Azure Diagnostic (LAD)), který byl poskytnut pro Azure Cloud Services od stala všeobecně dostupná v 2010 je agent, který dodává simple shromažďování diagnostických dat z výpočetním prostředkem Azure jako virtuální počítač a zachována do služby Azure storage. Jakmile se v úložišti, jste se rozhodli zobrazení s jedním z několika dostupných nástrojů, jako například [Průzkumníku serveru v sadě Visual Studio](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) a [Průzkumníka služby Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md).

Můžete shromažďovat:

* Předdefinované sady čítačů výkonu operačního systému a protokoly událostí nebo je můžete určit, které se mají shromažďovat. 
* Všechny požadavky a/nebo neúspěšné požadavky na webový server služby IIS
* Výstupní protokoly trasování aplikace .NET
* Trasování událostí pro Windows (ETW) 
* Shromažďovat události protokolu syslog  
* Výpisy stavu systému 

Agenta diagnostiky Azure by měl být použít, pokud:

* Chcete archivovat protokoly a metriky do služby Azure storage
* Data monitorování integraci s nástroji třetích stran. Tyto nástroje používat různými metodami, včetně dotazování na účet úložiště předá [Event Hubs](../event-hubs/event-hubs-about.md), nebo dotazování pomocí [rozhraním API REST pro monitorování Azure](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md)
* Nahrání dat do služby Azure Monitor k vytvoření grafů metrik na webu Azure Portal nebo vytvořit téměř v reálném čase [upozornění na metriku](../monitoring-and-diagnostics/alert-metric-overview.md). 
* Škálovací sady virtuálních počítačů automatického škálování a cloudové služby Classic podle metrik operačního systému hosta.
* Prozkoumat problémy spouštění virtuálních počítačů s [Diagnostika spouštění](../virtual-machines/troubleshooting/boot-diagnostics.md).
* Porozumět, jaký výkon vašich aplikací a proaktivně identifikuje problémy neovlivňují pomocí [Application Insights](../azure-monitor/overview.md).
* Nakonfigurujte Log Analytics pro import metrik a protokolování data shromážděná ze služby Cloud Services, klasické virtuální počítače, a uzly Service Fabricu uložených v účtu služby Azure storage.

### <a name="log-analytics-agent"></a>Agenta log Analytics
U rozšířené monitorování, kdy potřebujete víc než shromažďování metrik a podmnožinu protokolů, se vyžaduje agenta Log Analytics pro Windows a Linux. Agenta Log Analytics byla vyvinuta pro komplexní správu napříč místní fyzické a virtuální počítače, System Center Operations Manager monitoruje počítače a hostované virtuální počítače v jiných cloudech. Agenti Windows a Linux se připojit k pracovnímu prostoru Log Analytics ke shromažďování dat monitorování na základě řešení i vlastní zdroje dat, které nakonfigurujete.

[!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

Je nutné agenta Log Analytics nepoužívá, pokud:

* Máte počítače, které běží v místním nebo jiné cloudové prostředí
* Pomocí jednoho z Azure monitoru, například monitorování řešení [monitorování Azure pro virtuální počítače](../monitoring/monitoring-vminsights-overview.md?toc=%2fazure%2fmonitoring%2ftoc.json), [monitorování Azure pro kontejnery](../monitoring/monitoring-container-insights-overview.md?toc=%2fazure%2fmonitoring%2ftoc.json)atd.  
* Pomocí jedné z jiných služeb Azure pro správu, jako [Azure Security Center](../security-center/security-center-intro.md), [Azure Automation](../automation/automation-intro.md)atd.
* Nahrávání protokolu nebo metriky data do Azure monitoru.

Dříve byly spojeny několik služeb Azure jako *Operations Management Suite*, a v důsledku agenta Log Analytics je sdílen mezi službami, včetně Azure Security Center a Azure Automation.  To zahrnuje úplnou sadu funkcí, které nabízejí, poskytování komplexní správu virtuálních počítačů Azure prostřednictvím jejich životního cyklu. To zahrnuje:

* [Azure Automation Update management](../automation/automation-update-management.md) aktualizací operačního systému.
* [Azure Automation Desired State Configuration](../automation/automation-dsc-overview.md) udržovat konzistentní konfiguraci stavu.
* Sledovat změny konfigurace s [Azure Automation Change Tracking a Inventory](../automation/automation-change-tracking.md).
* Kolekce vlastních protokolů z operačního systému a hostovaných aplikací, jako [FluentD](../log-analytics/log-analytics-data-sources-json.md), [vlastní protokoly](../log-analytics/log-analytics-data-sources-custom-logs.md), a [MySQL a Apache](../log-analytics/log-analytics-data-sources-linux-applications.md) službou Log Analytics.
* Služby Azure, jako [Application Insights](https://docs.microsoft.com/azure/application-insights/) a [Azure Security Center](https://docs.microsoft.com/azure/security-center/) nativně ukládají data přímo do Log Analytics.  

### <a name="dependency-agent"></a>Agent závislostí
Agent závislostí vyvinula jako součást řešení Service Map, který byl původně vytvořen externě od Microsoftu. [Řešení Service Map](../monitoring/monitoring-service-map.md) a [monitorování Azure pro virtuální počítače](monitoring-vminsights-overview.md) vyžaduje, aby Agent závislostí ve Windows a Linuxu virtuálních počítačů a integruje do agenta Log Analytics shromažďuje zjištění dat o procesech spuštěných na virtuální počítače a procesu externí závislosti. Ukládá tato data do Log Analytics a vizualizuje zjištěných vzájemně propojených součástí.

Možná bude nutné určitou kombinaci těchto agentů monitorování virtuálního počítače. Agenty je možné nainstalovat vedle sebe jako rozšíření Azure, ale v Linuxu, agenta Log Analytics *musí* nainstalovat první, jinak se instalace nezdaří. 

## <a name="next-steps"></a>Další postup

- Zobrazit [přehled agenta Log Analytics](../log-analytics/log-analytics-agent-overview.md) zkontrolovat požadavky a podporované metody nasazení agenta do počítače hostované v Azure, do vašeho datového centra nebo jiné cloudové prostředí.

