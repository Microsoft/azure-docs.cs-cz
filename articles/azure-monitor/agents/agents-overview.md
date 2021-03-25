---
title: Přehled agentů monitorování Azure | Microsoft Docs
description: Tento článek poskytuje podrobný přehled dostupných agentů Azure, které podporují monitorování virtuálních počítačů hostovaných v Azure nebo hybridním prostředí.
services: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/12/2021
ms.openlocfilehash: 4d1dd358c03d051be4be5733d9e729d1d7ef5b0c
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105026168"
---
# <a name="overview-of-azure-monitor-agents"></a>Přehled agentů Azure Monitor

Virtuální počítače a jiné výpočetní prostředky vyžadují od agenta shromažďování dat monitorování potřebných k měření výkonu a dostupnosti hostovaného operačního systému a úloh. Tento článek popisuje agenty používané nástrojem Azure Monitor a pomáhá určit, které požadavky je potřeba splnit pro konkrétní prostředí.

> [!NOTE]
> Azure Monitor v současné době má více agentů z důvodu nedávné konsolidace Azure Monitor a Log Analytics. I když se může ve svých funkcích překrývat, každá z nich má jedinečné možnosti. V závislosti na vašich požadavcích budete možná potřebovat jednoho nebo více agentů na vašich počítačích. 

Můžete mít konkrétní sadu požadavků, které nelze zcela splnit jediným agentem pro určitý počítač. Můžete například chtít použít výstrahy metriky, které vyžadují rozšíření Azure Diagnostics, ale také chcete využívat funkce přehledů virtuálních počítačů, které vyžadují agenta Log Analytics a agenta závislostí. V takových případech můžete použít více agentů a jedná se o běžný scénář pro zákazníky, kteří od nich vyžadují funkčnost.

## <a name="summary-of-agents"></a>Souhrn agentů

Následující tabulky poskytují rychlé porovnání Azure Monitor agentů pro systémy Windows a Linux. Další podrobnosti najdete v níže uvedené části.

> [!NOTE]
> Agent Azure Monitor je aktuálně ve verzi Preview s omezenými možnostmi. Tato tabulka bude aktualizována. 

### <a name="windows-agents"></a>Agenti systému Windows

| | Agent Azure Monitor (Preview) | Diagnostika<br>rozšíření (WAD) | Log Analytics<br>agent | Závislost<br>agent |
|:---|:---|:---|:---|:---|
| **Podporovaná prostředí** | Azure<br>Jiný Cloud (Azure ARC)<br>Místně (Azure ARC)  | Azure | Azure<br>Jiný Cloud<br>Lokálně | Azure<br>Jiný Cloud<br>Místní | 
| **Požadavky agenta**  | Žádné | Žádné | Žádné | Vyžaduje agenta Log Analytics |
| **Shromažďovaná data** | Protokoly událostí<br>Výkon | Protokoly událostí<br>Trasování událostí pro Windows – události<br>Výkon<br>Protokoly založené na souborech<br>Protokoly IIS<br>Protokoly aplikací .NET<br>Výpisy stavu systému<br>Protokoly diagnostiky agenta | Protokoly událostí<br>Výkon<br>Protokoly založené na souborech<br>Protokoly IIS<br>Přehledy a řešení<br>Další služby | Závislosti procesů<br>Metriky síťového připojení |
| **Data odesílaná do** | Protokoly služby Azure Monitor<br>Metriky Azure Monitoru | Azure Storage<br>Metriky Azure Monitoru<br>Centrum událostí | Protokoly služby Azure Monitor | Protokoly služby Azure Monitor<br>(prostřednictvím agenta Log Analytics) |
| **Služby a**<br>**funkce**<br>**doložen** | Log Analytics<br>Průzkumník metrik | Průzkumník metrik | Přehledy virtuálních počítačů<br>Log Analytics<br>Azure Automation<br>Azure Security Center<br>Azure Sentinel | Přehledy virtuálních počítačů<br>Mapa služeb |

### <a name="linux-agents"></a>Agenti systému Linux

| | Agent Azure Monitor (Preview) | Diagnostika<br>rozšíření (LAD) | Telegraf<br>agent | Log Analytics<br>agent | Závislost<br>agent |
|:---|:---|:---|:---|:---|:---|
| **Podporovaná prostředí** | Azure<br>Jiný Cloud (Azure ARC)<br>Místně (Azure ARC) | Azure | Azure<br>Jiný Cloud<br>Lokálně | Azure<br>Jiný Cloud<br>Lokálně | Azure<br>Jiný Cloud<br>Místní |
| **Požadavky agenta**  | Žádné | Žádné | Žádné | Žádné | Vyžaduje agenta Log Analytics |
| **Shromažďovaná data** | Syslog<br>Výkon | Syslog<br>Výkon | Výkon | Syslog<br>Výkon| Závislosti procesů<br>Metriky síťového připojení |
| **Data odesílaná do** | Protokoly služby Azure Monitor<br>Metriky Azure Monitoru | Azure Storage<br>Centrum událostí | Metriky Azure Monitoru | Protokoly služby Azure Monitor | Protokoly služby Azure Monitor<br>(prostřednictvím agenta Log Analytics) |
| **Služby a**<br>**funkce**<br>**doložen** | Log Analytics<br>Průzkumník metrik | | Průzkumník metrik | Přehledy virtuálních počítačů<br>Log Analytics<br>Azure Automation<br>Azure Security Center<br>Azure Sentinel | Přehledy virtuálních počítačů<br>Mapa služeb |


## <a name="azure-monitor-agent-preview"></a>Agent Azure Monitor (Preview)

[Agent Azure monitor](azure-monitor-agent-overview.md) je aktuálně ve verzi Preview a nahradí agenta Log Analytics a agenta telegraf pro počítače se systémem Windows i Linux. Může odesílat data do obou protokolů Azure Monitor a Azure Monitor metriky a používat [pravidla shromažďování dat (DCR)](data-collection-rule-overview.md) , která poskytují pružnější způsob konfigurace shromažďování a cíle dat pro každého agenta.

Agenta Azure Monitor použijte v případě, že potřebujete:

- Shromažďovat protokoly a metriky hostů z libovolného počítače v Azure, v jiných cloudech nebo v místním prostředí. ([Servery s podporou ARC Azure](../../azure-arc/servers/overview.md) vyžadované pro počítače mimo Azure.) 
- Odesílat data do protokolů Azure Monitor a Azure Monitor metriky pro analýzu pomocí Azure Monitor. 
- Odeslat data do Azure Storage k archivaci.
- Posílání dat do nástrojů třetích stran pomocí [Azure Event Hubs](./diagnostics-extension-stream-event-hubs.md).
- Spravujte zabezpečení vašich počítačů pomocí [Azure Security Center](../../security-center/security-center-introduction.md)  nebo [Azure Sentinel](../../sentinel/overview.md). (Není k dispozici ve verzi Preview.)

Mezi omezení agenta Azure Monitor patří:

- Aktuálně ve verzi Public Preview. V tématu [aktuální omezení](azure-monitor-agent-overview.md#current-limitations) pro seznam omezení během veřejné verze Preview.

## <a name="log-analytics-agent"></a>Agent Log Analytics

[Agent Log Analytics](./log-analytics-agent.md) shromažďuje data monitorování z hostovaného operačního systému a úloh virtuálních počítačů v Azure, jiných poskytovatelů cloudu a místních počítačů. Odesílá data do pracovního prostoru Log Analytics. Agent Log Analytics je stejný agent, kterého používá aplikace System Center Operations Manager, a můžete mít více domácích počítačů agentů ke komunikaci se skupinou pro správu a Azure Monitor současně. Tento agent je také vyžadován některými přehledy v Azure Monitor a dalších službách v Azure.

> [!NOTE]
> Agent Log Analytics pro systém Windows se často označuje jako Microsoft Monitoring Agent (MMA). Agent Log Analytics pro Linux se často označuje jako agent OMS.

Agenta Log Analytics použijte v případě, že potřebujete:

* Shromažďovat protokoly a data o výkonu z virtuálních počítačů Azure nebo hybridních počítačů hostovaných mimo Azure
* Odešlete data do pracovního prostoru Log Analytics, abyste mohli využívat funkce podporované [Azure monitor protokoly](../logs/data-platform-logs.md) , jako jsou například [dotazy protokolu](../logs/log-query-overview.md).
* Využijte [přehledy virtuálních počítačů](../vm/vminsights-overview.md) , které vám umožní monitorovat vaše počítače ve velkém měřítku a monitorovat jejich procesy a závislosti na dalších prostředcích a externích procesech.  
* Spravujte zabezpečení vašich počítačů pomocí [Azure Security Center](../../security-center/security-center-introduction.md)  nebo [Azure Sentinel](../../sentinel/overview.md).
* K zajištění komplexní správy počítačů s Azure a mimo Azure použijte [Azure Automation Update Management](../../automation/update-management/overview.md), [konfiguraci stavu Azure Automation](../../automation/automation-dsc-overview.md)nebo [Azure Automation Change Tracking a inventáře](../../automation/change-tracking/overview.md) .
* Pomocí různých [řešení](../monitor-reference.md#insights-and-core-solutions) můžete monitorovat konkrétní službu nebo aplikaci.

Mezi omezení agenta Log Analytics patří:

- Nejde odesílat data do Azure Monitor metrik, Azure Storage nebo Azure Event Hubs.
- Je obtížné nakonfigurovat jedinečné definice monitorování pro jednotlivé agenty.
- Nenáročná Správa se škálováním, protože každý virtuální počítač má jedinečnou konfiguraci.

## <a name="azure-diagnostics-extension"></a>Rozšíření diagnostiky Azure

[Rozšíření Azure Diagnostics](./diagnostics-extension-overview.md) shromažďuje data monitorování z hostovaného operačního systému a úloh virtuálních počítačů Azure a dalších výpočetních prostředků. Primárně shromažďuje data do Azure Storage, ale také umožňuje definovat datové páry k odesílání dat do jiných cílů, jako jsou Azure Monitor metriky a Azure Event Hubs.

Diagnostické rozšíření Azure použijte v případě, že potřebujete:

- Odeslat data do Azure Storage pro archivaci nebo ji analyzovat pomocí nástrojů, jako je [Průzkumník služby Azure Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md).
- Odešlete data do [Azure monitor metriky](../essentials/data-platform-metrics.md) , abyste je mohli analyzovat pomocí [Průzkumníka metrik](../essentials/metrics-getting-started.md) a mohli využívat funkce, jako jsou [výstrahy metriky](../alerts/alerts-metric-overview.md) téměř v reálném čase a [Automatické škálování](../autoscale/autoscale-overview.md) (jenom Windows).
- Posílání dat do nástrojů třetích stran pomocí [Azure Event Hubs](./diagnostics-extension-stream-event-hubs.md).
- Shromažďovat [diagnostiku spouštění](/troubleshoot/azure/virtual-machines/boot-diagnostics) a prozkoumat problémy se spouštěním virtuálního počítače

Mezi omezení Azure Diagnostics Extension patří:

- Dá se použít jenom s prostředky Azure.
- Omezená schopnost odesílat data do protokolů Azure Monitor.

## <a name="telegraf-agent"></a>Agent telegraf

[Agent InfluxData telegraf](../essentials/collect-custom-metrics-linux-telegraf.md) se používá ke shromažďování údajů o výkonu z počítačů se systémem Linux pro Azure monitor metrik.

Telegraf agenta použijte v případě, že potřebujete:

* Odešlete data do [Azure monitor metriky](../essentials/data-platform-metrics.md) , abyste je mohli analyzovat pomocí [Průzkumníka metrik](../essentials/metrics-getting-started.md) a mohli využívat funkce, jako jsou [výstrahy metriky](../alerts/alerts-metric-overview.md) téměř v reálném čase a [Automatické škálování](../autoscale/autoscale-overview.md) (jenom Linux).

## <a name="dependency-agent"></a>Agent závislostí

Agent závislostí shromažďuje zjištěná data o procesech spuštěných na počítači a závislostech externích procesů. 

Agenta závislostí použijte v případě, že potřebujete:

* Využijte možnost map [VM Insights](../vm/vminsights-overview.md) nebo řešení [Service map](../vm/service-map.md) .

Při použití agenta závislostí Vezměte v úvahu tyto skutečnosti:

- Agent závislostí vyžaduje, aby byl na stejném počítači nainstalován agent Log Analytics.
- V počítačích se systémem Linux musí být agent Log Analytics nainstalován před diagnostickým rozšířením Azure.

## <a name="virtual-machine-extensions"></a>Rozšíření virtuálních počítačů

Log Analytics rozšíření pro [Windows](../../virtual-machines/extensions/oms-windows.md) a [Linux](../../virtual-machines/extensions/oms-linux.md) nainstalujte agenta Log Analytics na virtuální počítače Azure. Rozšíření závislostí Azure Monitor pro [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) a [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) instalují agenta závislostí na virtuálních počítačích Azure. Jedná se o stejné výše popsané agenty, ale umožňují vám jejich správu prostřednictvím [rozšíření virtuálních počítačů](../../virtual-machines/extensions/overview.md). Pokud je to možné, měli byste použít rozšíření k instalaci a správě agentů.

Na hybridních počítačích použijte [servery s podporou ARC Azure](../../azure-arc/servers/manage-vm-extensions.md) k nasazení Log Analytics a Azure monitor rozšíření virtuálních počítačů závislostí.

## <a name="supported-operating-systems"></a>Podporované operační systémy

V následujících tabulkách jsou uvedeny operační systémy, které jsou podporovány agenty Azure Monitor. V dokumentaci pro každého agenta najdete jedinečné požadavky a proces instalace. V dokumentaci k telegraf najdete informace o podporovaných operačních systémech. Všechny operační systémy se považují za x64. Platforma x86 není podporována pro žádný operační systém.

### <a name="windows"></a>Windows

| Operační systém | Agent Azure Monitoru | Agent Log Analytics | Agent závislostí | Rozšíření diagnostiky | 
|:---|:---:|:---:|:---:|:---:|
| Windows Server 2019                                      | × | × | × | × |
| Windows Server 2016                                      | × | × | × | × |
| Windows Server 2016 Core                                 |   |   |   | × |
| Windows Server 2012 R2                                   | × | × | × | × |
| Windows Server 2012                                      | × | × | × | × |
| Windows Server 2008 R2 SP1                               | × | × | × | × |
| Windows Server 2008 R2                                   |   | × | × | × |
| Windows 10 Enterprise<br>(včetně více relací) a pro<br>(Jenom serverové scénáře)  | × | × | × | × |
| Windows 8 Enterprise a pro<br>(Jenom serverové scénáře)  |   | × | × |   |
| Windows 7 SP1<br>(Jenom serverové scénáře)                 |   | × | × |   |

### <a name="linux"></a>Linux

| Operační systém | Azure Monitor agenta <sup>1</sup> | Log Analytics agenta <sup>1</sup> | Agent závislostí | Diagnostické rozšíření <sup>2</sup>| 
|:---|:---:|:---:|:---:|:---:
| Amazon Linux 2017,09                                        |   | × |   |   |
| CentOS Linux 8                                              | × <sup>3</sup> | × | × |   |
| CentOS Linux 7                                              | × | × | × | × |
| CentOS Linux 6                                              |   | × |   |   |
| CentOS Linux 6.5 +                                           |   | × | × | × |
| Debian 10 <sup>1</sup>                                      | × |   |   |   |
| Debian 9                                                    | × | × | x | × |
| Debian 8                                                    |   | × | × |   |
| Debian 7                                                    |   |   |   | × |
| OpenSUSE 13.1 +                                              |   |   |   | × |
| Oracle Linux 8                                              | × <sup>3</sup> | × |   |   |
| Oracle Linux 7                                              | × | × |   | × |
| Oracle Linux 6                                              |   | × |   |   |
| Oracle Linux 6.4 +                                           |   | × |   | × |
| Server Red Hat Enterprise Linux 8                           | × <sup>3</sup> | × | × |   |
| Red Hat Enterprise Linux Server 7                           | × | × | × | × |
| Red Hat Enterprise Linux Server 6                           |   | × | × |   |
| Red Hat Enterprise Linux Server 6.7 +                        |   | × | × | × |
| SUSE Linux Enterprise Server 15,2                           | × <sup>3</sup> |   |   |   |
| SUSE Linux Enterprise Server 15,1                           | × <sup>3</sup> | × |   |   |
| SUSE Linux Enterprise Server 15                             | × | × | × |   |
| SUSE Linux Enterprise Server 12                             | × | × | × | × |
| Ubuntu 20,04 LTS                                            | × | × | × |   |
| Ubuntu 18.04 LTS                                            | × | × | × | × |
| Ubuntu 16.04 LTS                                            | × | × | × | × |
| Ubuntu 14,04 LTS                                            |   | × |   | × |

<sup>1</sup> vyžaduje instalaci Pythonu (2 nebo 3) do počítače.

<sup>2</sup> vyžaduje, aby byl na počítači nainstalován Python 2.

<sup>3</sup> známý problém shromažďující události syslog V tuto chvíli jsou podporovaná jenom data o výkonu.
#### <a name="dependency-agent-linux-kernel-support"></a>Podpora jádra agenta závislostí v systému Linux

Vzhledem k tomu, že agent závislostí funguje na úrovni jádra, je podpora také závislá na verzi jádra. V následující tabulce jsou uvedeny hlavní a dílčí verze operačního systému Linux a podporované verze jádra pro agenta závislostí.

| Distribuce | Verze operačního systému | Verze jádra |
|:---|:---|:---|
|  Red Hat Linux 8   | 8.2     | 4.18.0-193.\*el8_2.x86_64 |
|                    | 8.1     | 4.18.0-147.\*el8_1.x86_64 |
|                    | 8.0     | 4.18.0-80. \* el8.x86_64<br>4.18.0-80.\*el8_0.x86_64 |
|  Red Hat Linux 7   | 7.9     | 3.10.0-1160 |
|                    | 7,8     | 3.10.0-1136 |
|                    | 7.7     | 3.10.0-1062 |
|                    | 7.6     | 3.10.0-957  |
|                    | 7,5     | 3.10.0-862  |
|                    | 7,4     | 3.10.0-693  |
| Red Hat Linux 6    | 6,10    | 2.6.32 – 754 |
|                    | 6.9     | 2.6.32 – 696  |
| CentOS Linux 8     | 8.2     | 4.18.0-193.\*el8_2.x86_64 |
|                    | 8.1     | 4.18.0-147.\*el8_1.x86_64 |
|                    | 8.0     | 4.18.0-80. \* el8.x86_64<br>4.18.0-80.\*el8_0.x86_64 |
| CentOS Linux 7     | 7.9     | 3.10.0-1160 |
|                    | 7,8     | 3.10.0-1136 |
|                    | 7.7     | 3.10.0-1062 |
| CentOS Linux 6     | 6,10    | 2.6.32-754.3.5<br>2.6.32-696.30.1 |
|                    | 6.9     | 2.6.32-696.30.1<br>2.6.32-696.18.7 |
| Ubuntu Server      | 20,04   | 5.4\* |
|                    | 18,04   | 5.3.0 – 1020<br>5,0 (zahrnuje jádro Azure vyladěné)<br>4,18 *<br> 4,15* |
|                    | 16.04.3 | 4,15.\* |
|                    | 16,04   | 4,13.\*<br>4,11.\*<br>4,10.\*<br>4,8.\*<br>4,4.\* |
| SUSE Linux 12 Enterprise Server | 15     | 4.12.14 – 150\*
|                                 | 12 SP4 | 4,12. * (zahrnuje jádro Azure s vyladěnými jádry) |
|                                 | 12 SP3 | 4,4. * |
|                                 | 12 SP2 | 4,4. * |
| Debian                          | 9      | 4,9  | 

## <a name="next-steps"></a>Další kroky

Další podrobnosti o jednotlivých agentech získáte v následujících verzích:

- [Přehled agenta Log Analytics](./log-analytics-agent.md)
- [Přehled rozšíření Azure Diagnostics](./diagnostics-extension-overview.md)
- [Shromažďování vlastních metrik pro virtuální počítač se systémem Linux pomocí agenta InfluxData telegraf](../essentials/collect-custom-metrics-linux-telegraf.md)