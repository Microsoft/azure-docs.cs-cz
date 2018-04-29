---
title: Přehled Azure Diagnostics | Microsoft Docs
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
ms.date: 03/18/2017
ms.author: robb
ms.openlocfilehash: 0231a6c1d78818b948bb24d0c406fb2f2da17a0f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="what-is-azure-diagnostics"></a>Co je Azure Diagnostics
Azure Diagnostics je funkce v rámci Azure, která umožňuje shromažďování diagnostických dat na nasazené aplikace. Můžete použít rozšíření diagnostiky z mnoha různých zdrojů. Aktuálně podporované jsou cloudové služby Azure (klasický) Web a rolí pracovního procesu, virtuální počítače sady škálování virtuálního počítače a Service Fabric. Jinými službami Azure mají různé diagnostiky metody. V tématu [Přehled monitorování v Azure](monitoring-overview.md). 

## <a name="data-you-can-collect"></a>Data, která můžete shromáždit
Azure Diagnostics může shromažďovat následující typy dat:

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

Rozšíření diagnostiky Azure může přenést tato data do účtu úložiště Azure nebo odesílat jej do [Application Insights](../application-insights/app-insights-cloudservices.md). Můžete také Streamovat ho k [centra událostí](../event-hubs/event-hubs-what-is-event-hubs.md), který pak umožňuje poslat Centrum monitorování serveru mimo platformu Azure services. Data můžete použít pro ladění a řešení potíží s měření výkonu, sledování využití prostředků, analýza provozu a plánování kapacity a auditování.

## <a name="versioning"></a>Správa verzí
V tématu [historie Správa verzí Azure Diagnostics](azure-diagnostics-versioning-history.md).

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

## <a name="virtual-machines-using-azure-diagnostics"></a>Virtuální počítače pomocí Azure Diagnostics
* Pokud používáte Visual Studio, najdete v části [pomocí aplikace Visual Studio trasování virtuálních počítačích Azure](../vs-azure-tools-debug-cloud-services-virtual-machines.md) začít pracovat. Jinak naleznete v tématu
* [Nastavení Azure Diagnostics na virtuální počítač Azure](../virtual-machines-dotnet-diagnostics.md)

Pokročilejší témata naleznete v tématu

* [Nastavení diagnostiky ve virtuálních počítačích Azure pomocí prostředí PowerShell](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Vytvoření Windows virtuálního počítače s monitorování a Diagnostika pomocí šablony Azure Resource Manageru](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric-using-azure-diagnostics"></a>Service Fabric pomocí diagnostiky Azure
Začínáme v [monitorování aplikace Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Mnoho dalších článků diagnostiky Service Fabric jsou k dispozici v navigačním stromu vlevo po přechodu na krok v tomto článku.

## <a name="general-azure-diagnostics-articles"></a>Obecné Azure Diagnostics články
* [Schéma konfigurace Azure Diagnostics](https://msdn.microsoft.com/library/azure/mt634524.aspx) -informace o změně souboru schématu a shromažďovat diagnostická data trasy. Všimněte si, že můžete také můžete změnit souboru schématu pomocí sady Visual Studio.
* [Jak je Azure Diagnostics data uložená ve službě Azure Storage](../cloud-services/cloud-services-dotnet-diagnostics-storage.md) -znát názvy tabulek a umístění, kam je zapisován diagnostických dat objektů BLOB.
* Naučte se [pomocí čítače výkonu v Azure Diagnostics](../cloud-services/diagnostics-performance-counters.md).
* Naučte se [trasy Azure diagnostické informace do služby Application Insights](azure-diagnostics-configure-application-insights.md)
* Pokud máte potíže se spuštěním diagnostiky nebo hledání vaše data v tabulkách Azure Storage, najdete v části [řešení potíží s Azure Diagnostics](azure-diagnostics-troubleshooting.md)
