---
title: Přehled rozšíření Azure Diagnostics
description: Použití diagnostiky Azure pro ladění, měření výkonu, monitorování, analýzu provozu v cloud services, virtual machines a service fabric
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 07/13/2018
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: b00d774ec59755288b8660d238c7b8dfc9a89eab
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39089889"
---
# <a name="what-is-azure-diagnostics-extension"></a>Co je rozšíření Azure Diagnostics
Rozšíření Azure Diagnostics je agent v Azure, která umožňuje shromažďování diagnostických dat v nasazené aplikaci. Rozšíření diagnostiky můžete z mnoha různých zdrojů. V tuto chvíli nepodporuje jsou cloudové služby Azure (klasické) webové a pracovní role, Virtual Machines, Virtual Machine Scale sets a Service Fabric. Další služby Azure mají různé diagnostiky metody. Zobrazit [Přehled monitorování v Azure](monitoring-overview.md). 

## <a name="linux-agent"></a>Agenta pro Linux
A [Linux verze rozšíření](../virtual-machines/linux/diagnostic-extension.md) je k dispozici pro virtuální počítače s Linuxem. Shromažďovat statistiky a chování se liší od verze Windows. 

## <a name="data-you-can-collect"></a>Data můžete shromažďovat
Rozšíření Azure Diagnostics můžete shromažďovat následující typy dat:

| Zdroj dat | Popis |
| --- | --- |
| Čítače výkonu |Operační systém a vlastní čítače výkonu |
| Protokoly aplikací |Trasovací zprávy, autorem aplikace |
| Protokoly událostí Windows |Informace odesílané do systému pro protokolování událostí Windows |
| Zdroj událostí .NET |Kód zápisu událostí pomocí rozhraní .NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) třídy |
| Protokoly IIS |Informace o webech služby IIS |
| Trasování událostí pro Windows na základě manifestu |Události trasování pro Windows události generované modulem nějaký proces. (1) |
| Výpisy stavu systému |Informace o stavu procesu v případě při selhání aplikace |
| Vlastní protokoly chyb |Protokoly vytvořené metodami vaše aplikace nebo služba |
| Protokoly infrastruktury diagnostiky Azure |Informace o diagnostice, samotný |

(1) Pokud chcete získat seznam zprostředkovatelů trasování událostí pro Windows, spusťte `c:\Windows\System32\logman.exe query providers` v okně konzoly na počítači, který chcete shromáždit informace z. 

## <a name="data-storage"></a>Úložiště dat
Rozšíření ukládá data do [účtu služby Azure Storage](azure-diagnostics-storage.md) , který zadáte. 

Můžete také odeslat ho do [Application Insights](../application-insights/app-insights-cloudservices.md). Další možností je na datový proud stream [centra událostí](../event-hubs/event-hubs-what-is-event-hubs.md), která pak můžete odeslat do služby pro monitorování mimo Azure. 


## <a name="versioning-and-configuration-schema"></a>Schéma vytváření verzí a konfigurace
Zobrazit [historie verzí diagnostiky Azure a schéma](azure-diagnostics-versioning-history.md).


## <a name="next-steps"></a>Další postup
Zvolte službu, které se pokoušíte shromažďování diagnostických údajů na a použijte tyto články, abyste mohli začít. Pomocí odkazů obecné diagnostiky Azure pro referenční informace pro konkrétní úlohy.

## <a name="cloud-services-using-azure-diagnostics"></a>Cloudové služby pomocí diagnostiky Azure
* Pokud používáte Visual Studio, přečtěte si téma [použijte sadu Visual Studio pro sledování aplikace Cloud Services](../vs-azure-tools-debug-cloud-services-virtual-machines.md) začít. V opačném případě naleznete v tématu
* [Monitorování cloudové služby pomocí diagnostiky Azure](../cloud-services/cloud-services-how-to-monitor.md)
* [Nastavení diagnostiky Azure do aplikace Cloud Services](../cloud-services/cloud-services-dotnet-diagnostics.md)

Pokročilejší témata naleznete v tématu

* [Pro cloudové služby pomocí diagnostiky Azure pomocí Application Insights](../application-insights/app-insights-cloudservices.md)
* [Trasování toku aplikace Cloud Services s využitím Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
* [Nastavení diagnostiky na Cloud Services pomocí Powershellu](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="virtual-machines"></a>Virtuální počítače
* Pokud používáte Visual Studio, přečtěte si téma [pomocí aplikace Visual Studio trasování Azure Virtual Machines](../vs-azure-tools-debug-cloud-services-virtual-machines.md) začít. V opačném případě naleznete v tématu
* [Nastavení Azure Diagnostics na virtuálním počítači Azure](../virtual-machines-dotnet-diagnostics.md)

Pokročilejší témata naleznete v tématu

* [Použití Powershellu k nastavení diagnostiky v Azure Virtual Machines](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Vytvoření Windows virtuální počítače s monitorováním a diagnostikou pomocí šablony Azure Resource Manageru](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric"></a>Service Fabric
Začínáme při [monitorovat aplikace Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Mnoho dalších článcích diagnostické nástroje Service Fabric jsou k dispozici v navigačním stromu na levé straně, jakmile získáte k tomuto článku.

## <a name="general-articles"></a>Obecné články související s
* Zjistěte, jak [pomocí čítačů výkonu v diagnostice Azure](../cloud-services/diagnostics-performance-counters.md).
* Pokud máte potíže se spuštěním diagnostiky nebo hledání vaše data do tabulky v úložišti Azure, najdete v článku [odstraňování potíží diagnostiky Azure](azure-diagnostics-troubleshooting.md)
