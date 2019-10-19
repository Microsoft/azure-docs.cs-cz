---
title: Přehled rozšíření Azure Diagnostics
description: Použití diagnostiky Azure pro ladění, měření výkonu, monitorování, analýzy provozu v cloudových službách, virtuálních počítačích a Service Fabric
ms.service: azure-monitor
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 02/13/2019
ms.openlocfilehash: d1721411b57fc3542af48fc5f48eca7e4a2d06c8
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72552143"
---
# <a name="what-is-azure-diagnostics-extension"></a>Co je rozšíření Azure Diagnostics
Rozšíření Azure Diagnostics je agentem v rámci Azure, který umožňuje shromažďování diagnostických dat v nasazené aplikaci. Diagnostické rozšíření můžete použít z řady různých zdrojů. V současné době jsou podporované webové a pracovní role Azure Cloud Service (Classic), Virtual Machines, sady škálování virtuálních počítačů a Service Fabric. Jiné služby Azure mají různé diagnostické metody. Podívejte [se na Přehled monitorování v Azure](../../azure-monitor/overview.md).

## <a name="linux-agent"></a>Linuxový agent
K dispozici je [verze Linux rozšíření](../../virtual-machines/extensions/diagnostics-linux.md) pro Virtual Machines se systémem Linux. Shromážděné statistiky a chování se liší od verze Windows.

## <a name="data-you-can-collect"></a>Data, která můžete shromažďovat
Rozšíření Azure Diagnostics může shromažďovat následující typy dat:

| Zdroj dat | Popis |
| --- | --- |
| Metriky čítače výkonu |Operační systém a vlastní čítače výkonu |
| Protokoly aplikací |Trasování zpráv zapsaných vaší aplikací |
| Protokoly událostí systému Windows |Informace odesílané systému protokolování událostí systému Windows |
| Protokoly EventSource .NET |Psaní kódu pro události pomocí třídy [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) .NET |
| Protokoly IIS |Informace o webech služby IIS |
| [Protokoly ETW založené na manifestech](https://docs.microsoft.com/windows/desktop/etw/about-event-tracing) |Trasování událostí pro události systému Windows generované jakýmkoli procesem. první |
| Výpisy stavu systému (protokoly) |Informace o stavu procesu, pokud dojde k chybě aplikace |
| Vlastní protokoly chyb |Protokoly vytvořené vaší aplikací nebo službou |
| Protokoly diagnostické infrastruktury Azure |Informace o samotné Azure Diagnostics |

(1) Chcete-li získat seznam zprostředkovatelů ETW, spusťte `c:\Windows\System32\logman.exe query providers` v okně konzoly na počítači, ze kterého chcete získat informace.

## <a name="data-storage"></a>Úložiště dat
Rozšíření ukládá svá data do vámi zadaného [Azure Storage účtu](diagnostics-extension-to-storage.md) .

Můžete ji také odeslat [Application Insights](../../azure-monitor/app/cloudservices.md). 

Další možností je streamování IT do [centra událostí](../../event-hubs/event-hubs-about.md), které pak umožňuje odeslat ho do služby monitorování mimo Azure.

Máte také možnost odesílat data do Azure Monitor databáze časových řad s metrikami. V tuto chvíli je tato jímka platná jenom pro čítače výkonu. Umožňuje posílat čítače výkonu v nástroji jako vlastní metriky. Tato funkce je ve verzi Preview. Jímka Azure Monitor podporuje:
* Načítání všech čítačů výkonu odeslaných do Azure Monitor prostřednictvím [rozhraní API pro Azure monitor metriky.](https://docs.microsoft.com/rest/api/monitor/)
* Výstrahy na všechny čítače výkonu odeslané do Azure Monitor prostřednictvím [výstrah metrik](../../azure-monitor/platform/alerts-overview.md) v Azure monitor
* Zpracovávání operátoru zástupného znaku v čítačích výkonu jako dimenze instance v rámci metriky.  Pokud jste například shromáždili čítač "logický disk (\*)/DiskWrites/sec", mohli byste filtrovat a rozdělit na dimenzi "instance", která vykreslí nebo upozorní na zápisy disku/s pro každý logický disk na virtuálním počítači (například C:)

Další informace o tom, jak nakonfigurovat tuto jímku, najdete v [dokumentaci ke schématu diagnostiky Azure.](diagnostics-extension-schema-1dot3.md)

## <a name="costs"></a>Náklady
U každé z výše uvedených možností se může účtovat náklady. Nezapomeňte je prozkoumat, aby nedocházelo k neočekávaným fakturám.  Application Insights, centrum událostí a Azure Storage mají samostatné náklady spojené s příjmem a uloženým časem. Konkrétně Azure Storage bude uchovávat všechna data navždy, takže možná budete chtít po uplynutí určité doby vyprázdnit starší data a snížit tak náklady.    

## <a name="versioning-and-configuration-schema"></a>Správa verzí a schéma konfigurace
Viz [Azure Diagnostics Historie verzí a schématu](diagnostics-extension-schema.md).


## <a name="next-steps"></a>Další kroky
Vyberte službu, na kterou se snažíte shromažďovat diagnostiku, a použijte následující články, které vám pomohou začít. Použijte obecné odkazy Azure Diagnostics pro referenční informace pro konkrétní úkoly.

## <a name="cloud-services-using-azure-diagnostics"></a>Cloud Services pomocí Azure Diagnostics
* Pokud používáte Visual Studio, přečtěte si téma [použití sady Visual Studio k trasování Cloud Services aplikace](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines) a začněte. V opačném případě viz
* [Jak monitorovat cloudové služby pomocí Azure Diagnostics](../../cloud-services/cloud-services-how-to-monitor.md)
* [Nastavení Azure Diagnostics v aplikaci Cloud Services](../../cloud-services/cloud-services-dotnet-diagnostics.md)

Pokročilejší témata najdete v tématu.

* [Použití Azure Diagnostics s Application Insights pro Cloud Services](../../azure-monitor/app/cloudservices.md)
* [Trasování toku Cloud Services aplikace pomocí Azure Diagnostics](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
* [Použití PowerShellu k nastavení diagnostiky na Cloud Services](../../virtual-machines/extensions/diagnostics-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="virtual-machines"></a>Služba Virtual Machines
* Pokud používáte Visual Studio, přečtěte si téma [použití sady Visual Studio k trasování služby Azure Virtual Machines](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines) , abyste mohli začít. V opačném případě viz
* [Nastavení Azure Diagnostics na virtuálním počítači Azure](/azure/virtual-machines/extensions/diagnostics-windows)

Pokročilejší témata najdete v tématu.

* [Použití PowerShellu k nastavení diagnostiky v Azure Virtual Machines](../../virtual-machines/extensions/diagnostics-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Vytvoření virtuálního počítače s Windows pomocí monitorování a diagnostiky pomocí šablony Azure Resource Manager](../../virtual-machines/extensions/diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric"></a>Service Fabric
Začněte [monitorovat Service Fabric aplikaci](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Mnoho dalších Service Fabric diagnostických článků je po obdržení tohoto článku k dispozici v navigační stromové struktuře vlevo.

## <a name="general-articles"></a>Obecné články
* Naučte se [používat čítače výkonu v Azure Diagnostics](../../cloud-services/diagnostics-performance-counters.md).
* Pokud máte potíže s diagnostikou spouštění nebo hledáním dat v tabulkách úložiště Azure, přečtěte si téma [řešení potíží Azure Diagnostics](diagnostics-extension-troubleshooting.md)

