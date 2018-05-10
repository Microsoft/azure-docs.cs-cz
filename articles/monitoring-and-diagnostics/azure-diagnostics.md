---
title: Přehled rozšíření diagnostiky Azure | Microsoft Docs
description: Použití Azure diagnostics pro ladění, měření výkonu, monitorování, analýza provozu v cloudových služeb, virtuální počítače a služby infrastruktury
services: multiple
documentationcenter: .net
author: rboucher
manager: ''
editor: ''
ms.assetid: baad40d8-c915-4f93-b486-8b160bf33463
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/01/2018
ms.author: robb
ms.openlocfilehash: daeaddefa461e71fcc62af4efc4fb7084b237cf9
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="what-is-azure-diagnostics-extension"></a>Co je Azure Diagnostics rozšíření
Rozšíření Azure Diagnostics není agenta v rámci Azure, která umožňuje shromažďování diagnostických dat na nasazené aplikace. Můžete použít rozšíření diagnostiky z mnoha různých zdrojů. Aktuálně podporované jsou cloudové služby Azure (klasický) Web a rolí pracovního procesu, virtuální počítače sady škálování virtuálního počítače a Service Fabric. Jinými službami Azure mají různé diagnostiky metody. V tématu [Přehled monitorování v Azure](monitoring-overview.md). 

## <a name="linux-agent"></a>Agenta systému Linux
A [Linux verzi rozšíření](../virtual-machines/linux/diagnostic-extension.md) je k dispozici pro virtuální počítače s Linuxem. Shromažďovat statistiky a chování se liší od verze systému Windows. 

## <a name="data-you-can-collect"></a>Data, která můžete shromáždit
Rozšíření diagnostiky Azure může shromažďovat následující typy dat:

| Zdroj dat | Popis |
| --- | --- |
| Čítače výkonu |Operační systém a vlastní čítače výkonu |
| Protokoly aplikací |Trasování zpráv zapsaných správcem vaší aplikace |
| Protokoly událostí systému Windows |Informace odesílané do systému protokolování událostí Windows |
| Zdroj události rozhraní .NET |Kód zápisu událostí pomocí .NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) – třída |
| Protokoly služby IIS |Informace o webů služby IIS |
| Manifest na základě trasování událostí pro Windows |Události trasování pro Windows události vygenerované modulem jakýkoli proces |
| Výpisy stavu systému |Informace o stavu procesu v případě při selhání aplikace |
| Vlastní protokoly chyb |Protokoly vytvořené aplikace nebo služby |
| Infrastrukturu Azure diagnostické protokoly |Informace o diagnostiky sám sebe |

## <a name="data-storage"></a>Úložiště dat
Rozšíření ukládá data v [účet úložiště Azure](azure-diagnostics-storage.md) který určíte. 

Můžete také odeslat, aby [Application Insights](../application-insights/app-insights-cloudservices.md). Další možností je Streamovat ho k [centra událostí](../event-hubs/event-hubs-what-is-event-hubs.md), který pak umožňuje poslat Centrum monitorování serveru mimo platformu Azure services. 


## <a name="versioning-and-configuration-schema"></a>Správa verzí a konfigurace schématu
V tématu [historie verzí Azure Diagnostics a schéma](azure-diagnostics-versioning-history.md).


## <a name="next-steps"></a>Další postup
Vyberte služby, která chcete shromažďovat diagnostiky na a pomocí následujících článků začít pracovat. Pomocí odkazů obecné Azure diagnostics pro referenční informace pro konkrétní úlohy.

## <a name="cloud-services-using-azure-diagnostics"></a>Cloudové služby pomocí Azure Diagnostics
* Pokud používáte Visual Studio, najdete v části [použijte ke sledování aplikace cloudové služby Visual Studio](../vs-azure-tools-debug-cloud-services-virtual-machines.md) začít pracovat. Jinak naleznete v tématu
* [Monitorování cloudových služeb pomocí Azure Diagnostics](../cloud-services/cloud-services-how-to-monitor.md)
* [Nastavení Azure Diagnostics v aplikaci cloudové služby](../cloud-services/cloud-services-dotnet-diagnostics.md)

Pokročilejší témata naleznete v tématu

* [Pomocí Azure Diagnostics Application Insights pro cloudové služby](../application-insights/app-insights-cloudservices.md)
* [Trasování toku aplikace cloudových služeb s Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
* [Nastavení diagnostiky na cloudové služby pomocí prostředí PowerShell](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="virtual-machines"></a>Virtuální počítače
* Pokud používáte Visual Studio, najdete v části [pomocí aplikace Visual Studio trasování virtuálních počítačích Azure](../vs-azure-tools-debug-cloud-services-virtual-machines.md) začít pracovat. Jinak naleznete v tématu
* [Nastavení Azure Diagnostics na virtuální počítač Azure](../virtual-machines-dotnet-diagnostics.md)

Pokročilejší témata naleznete v tématu

* [Nastavení diagnostiky ve virtuálních počítačích Azure pomocí prostředí PowerShell](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Vytvoření Windows virtuálního počítače s monitorování a Diagnostika pomocí šablony Azure Resource Manageru](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric"></a>Service Fabric
Začínáme v [monitorování aplikace Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Mnoho dalších článků diagnostiky Service Fabric jsou k dispozici v navigačním stromu vlevo po přechodu na krok v tomto článku.

## <a name="general-articles"></a>Obecné články
* Naučte se [pomocí čítače výkonu v Azure Diagnostics](../cloud-services/diagnostics-performance-counters.md).
* Pokud máte potíže se spuštěním diagnostiky nebo hledání vaše data v tabulkách úložiště Azure, najdete v části [řešení potíží s Azure Diagnostics](azure-diagnostics-troubleshooting.md)
