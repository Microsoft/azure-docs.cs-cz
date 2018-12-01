---
title: Azure Monitor v Azure stacku | Dokumentace Microsoftu
description: Přečtěte si o Azure monitoru v Azure stacku.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2018
ms.author: mabrigg
ms.openlocfilehash: 45c06afd0d298c467d8f0a3a42b68bf1948c6ed3
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52726742"
---
# <a name="azure-monitor-on-azure-stack"></a>Azure Monitor v Azure stacku

*Platí pro: integrované systémy Azure Stack*

Tento článek obsahuje přehled služby Azure Monitor ve službě Azure Stack. Popisuje operace služby Azure Monitor a další informace o tom, jak používat Azure Monitor ve službě Azure Stack. 

Úvod najdete přehled a tom, jak začít pracovat s Azure Monitor, najdete v článku na global Azure [Začínáme se službou Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started).

![Okno Azure Monitor zásobníku](./media/azure-stack-metrics-azure-data/azs-monitor.png)

Azure Monitor je služba platformy, která poskytuje jeden zdroj pro monitorování prostředků Azure. Prostřednictvím služby Azure Monitor můžete vizualizovat, dotazy, směrovat, archivace a jinak reagovat na metriky a protokoly pocházející z prostředků v Azure. S těmito daty můžete pracovat s použitím služby Azure Stack portálu pro správu, monitorování rutin prostředí PowerShell, Cross-Platform CLI nebo REST API služby Azure Monitor. Pro konkrétní připojení podporován ve službě Azure Stack, najdete v článku [jak využívat data monitorování z Azure Stack](azure-stack-metrics-monitor.md)

> [!Note]  
Diagnostické protokoly a metriky nejsou k dispozici pro Azure Stack Development Kit.

## <a name="prerequisites"></a>Požadavky

Zaregistrujte **Microsoft.insights** poskytovatele prostředků na nastavení poskytovatele prostředků nabídka vašeho předplatného. Můžete ověřit, zda je k dispozici ve vaší nabídce přidružených k vašemu předplatnému poskytovatele prostředků:

1. Otevřete portál pro správu služby Azure Stack.
2. Vyberte **nabízí**.
3. Vyberte nabídku služby přidružené k předplatnému.
4. Vyberte **poskytovatelů prostředků** pod **nastavení.** 
5. Najít **Microsoft.Insights** v seznamu a ověřte, zda je stav **registrovaná.**.

## <a name="overview"></a>Přehled

Jako je Azure Monitor v Azure Azure Monitor ve službě Azure Stack poskytuje základní infrastruktura metriky a protokoly pro většina služeb.

## <a name="azure-monitor-sources-compute-subset"></a>Azure monitor zdroje: výpočetní část

![Azure monitor zdroje – výpočetní část](media//azure-stack-metrics-azure-data/azs-monitor-computersubset.png)

**Microsoft.Compute** poskytovatele prostředků ve službě Azure Stack zahrnuje:
 - Virtuální počítače 
 - Škálovací sady virtuálních počítačů

### <a name="application---diagnostics-logs-application-logs-and-metrics"></a>Aplikace – diagnostické protokoly a protokoly aplikací a metriky

Aplikace můžete spustit v operačním systému virtuálního počítače s **Microsoft.Compute** poskytovatele prostředků. Tyto aplikace a virtuální počítače vygenerovat vlastní sadu protokolů a metrik. Azure Monitor používá rozšíření Azure Diagnostics (Windows nebo Linux) ke shromažďování většiny protokolů a metrik na úrovni aplikace. 

Mezi typy opatření patří:
 - Čítače výkonu
 - Protokoly aplikací
 - Protokoly událostí Windows
 - Zdroj události .NET
 - Protokoly IIS
 - Trasování událostí pro Windows na základě manifestu
 - Výpisy stavu systému
 - Protokoly chyb zákazníka

> [!Note]  
> Rozšíření diagnostiky Linuxu ve službě Azure Stack se nepodporují.

### <a name="host-and-guest-vm-metrics"></a>Metriky virtuálního počítače hostitele a hosta

Dříve uvedených výpočetní prostředky, které mají vyhrazený virtuální počítač hostitele a hostovaného operačního systému. Virtuální počítač hostitele a hostovaného operačního systému jsou ekvivalentem kořenový virtuální počítač a hostovaného virtuálního počítače v hypervisoru Hyper-V. Můžete shromažďovat metriky pro virtuální počítač hostitele a hostovaného operačního systému. Kromě toho můžete shromažďovat diagnostické protokoly pro hostovaný operační systém. Seznam kolekční metrik pro metriky hostitele a hostovaného virtuálního počítače ve službě Azure Stack najdete na adrese [podporované metriky ve službě Azure Monitor ve službě Azure Stack](azure-stack-metrics-supported.md). 

### <a name="activity-log"></a>Protokol aktivit

Můžete hledat protokolů aktivit pro informace o výpočetní prostředky, jak je vidět infrastrukturou Azure Stack. Tento protokol obsahuje informace, jako jsou třeba časy, kdy se prostředky vytvořily nebo zničily. Protokoly aktivit ve službě Azure Stack je konzistentní s Azure. Další informace najdete v popisu [přehled protokolu aktivit v Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). 


## <a name="azure-monitor-sources-everything-else"></a>Azure monitor zdroje: všechno ostatní

![Azure monitor zdroje – všechno ostatní](media//azure-stack-metrics-azure-data/azs-monitor-othersubset.png)

### <a name="resources---metrics-and-diagnostics-logs"></a>Prostředky - metriky a diagnostické protokoly

Kolekční metriky a diagnostické protokoly se liší v závislosti na typu prostředku. Seznam kolekční metrik pro jednotlivé prostředky ve službě Azure Stack je k dispozici na podporované metriky. Další informace najdete v tématu [podporované metriky ve službě Azure Monitor ve službě Azure Stack](azure-stack-metrics-supported.md).

### <a name="activity-log"></a>Protokol aktivit

Protokol aktivit je že stejný pro výpočetní prostředky. 

### <a name="uses-for-monitoring-data"></a>Používá pro monitorování dat.

**Store a archivace**  

Některá data monitorování se už v Azure Monitoru uchovávají a jsou po určitou dobu dostupná. 
 - Metriky se uchovávají po dobu 90 dnů. 
 - Položky protokolu aktivit se uchovávají po dobu 90 dnů. 
 - Diagnostické protokoly nejsou uložené.
 - Archivace dat do účtu úložiště pro delší dobu uchování.

**Dotaz**  

REST API služby Azure Monitor, příkazů rozhraní příkazového řádku (CLI) napříč platformami, rutin prostředí PowerShell nebo sady .NET SDK můžete použít pro přístup k datům v systému nebo úložiště Azure. 

**Vizualizace**

Vizualizace dat monitorování pomocí grafů a diagramů vám umožňuje rozpoznat trendy rychleji, než když prohlížíte samotná data. 

Mezi možné způsoby vizualizace patří:
 - Použití portálu Azure Stack uživatelů a správců
 - Data trasy pro Microsoft Power BI
 - Směrování dat do vizualizačního nástroje jiného výrobce pomocí živého streamování nebo tak, že necháte daný nástroj načítat data z archivu v úložišti Azure

## <a name="methods-of-accessing-azure-monitor-on-azure-stack"></a>Monitorování metod přístupu k Azure ve službě Azure Stack

Obecně platí, že k manipulaci se sledováním, směrováním a načítáním dat se dá použít některý z následujících způsobů. Některé způsoby ale nemusí být dostupné pro všechny akce nebo typy dat.

 - [Portál Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-use-portal)
 - [PowerShell](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples)
 - [Interface(CLI) příkazového řádku pro různé platformy](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-cli-samples)
 - [REST API](https://docs.microsoft.com/rest/api/monitor)
 - [.NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor)

## <a name="next-steps"></a>Další postup

Další informace o možnostech monitorování využití dat ve službě Azure Stack v článku [spotřebovat data monitorování z Azure Stack](azure-stack-metrics-monitor.md).
