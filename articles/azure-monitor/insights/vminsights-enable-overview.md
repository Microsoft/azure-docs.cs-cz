---
title: Povolit Azure Monitor pro virtuální počítače – přehled
description: Přečtěte si, jak nasadit a nakonfigurovat Azure Monitor pro virtuální počítače. Zjistěte požadavky na systém.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 96783955eac6ade90a155236891307720616ed20
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87323940"
---
# <a name="enable-azure-monitor-for-vms-overview"></a>Povolit Azure Monitor pro virtuální počítače – přehled

Tento článek poskytuje přehled možností, které jsou k dispozici pro povolení Azure Monitor pro virtuální počítače k monitorování stavu a výkonu následujících akcí:

- Virtuální počítače Azure 
- Škálovací sady virtuálních počítačů Azure
- Hybridní virtuální počítače připojené pomocí ARC Azure
- Místní virtuální počítače
- Virtuální počítače hostované v jiném cloudovém prostředí.  

Nastavení Azure Monitor pro virtuální počítače:

* Pokud chcete povolit jeden virtuální počítač Azure, Azure VMSS nebo počítač ARC Azure, vyberte **přehledy** přímo z nabídky v Azure Portal.
* Pomocí Azure Policy povolte více virtuálních počítačů Azure, Azure VMSS nebo počítačů s obloukem Azure ARC. Tato metoda zajišťuje, že u stávajících a nových virtuálních počítačů a sad škálování jsou požadované závislosti nainstalovány a správně nakonfigurovány. Nahlásí se nekompatibilní virtuální počítače a sady škálování, takže se můžete rozhodnout, jestli je chcete povolit, a opravit je.
* Povolte několik virtuálních počítačů Azure, virtuálních počítačů ARC Azure, Azure VMSS nebo počítačů ARC Azure v rámci zadaného předplatného nebo skupiny prostředků pomocí PowerShellu.
* Povolte Azure Monitor pro virtuální počítače k monitorování virtuálních počítačů nebo fyzických počítačů hostovaných ve vaší podnikové síti nebo jiném cloudovém prostředí.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že rozumíte informacím v následujících částech. 

>[!NOTE]
>Následující informace popsané v této části se vztahují také na [řešení Service map](service-map.md).  

### <a name="log-analytics"></a>Log Analytics

Azure Monitor pro virtuální počítače podporuje pracovní prostor Log Analytics v následujících oblastech:

- USA – středozápad
- USA – západ
- Západní USA 2
- Středojižní USA
- East US
- USA – východ 2
- Střední USA
- USA – středosever
- US Gov AZ
- US Gov VA
- Střední Kanada
- Spojené království – jih
- Severní Evropa
- West Europe
- Východní Asie
- Southeast Asia
- Indie – střed
- Japan East
- Austrálie – východ
- Austrálie – jihovýchod

>[!NOTE]
>Virtuální počítače Azure můžete monitorovat v libovolné oblasti. Samotné virtuální počítače nejsou omezeny na oblasti podporované pracovním prostorem Log Analytics.
>

Pokud nemáte pracovní prostor Log Analytics, můžete ho vytvořit pomocí jednoho z prostředků:
* [Azure CLI](../learn/quick-create-workspace-cli.md)
* [PowerShell](../platform/powershell-workspace-configuration.md)
* [Azure Portal](../learn/quick-create-workspace.md)
* [Azure Resource Manager](../platform/template-workspace-configuration.md)

- Virtuální počítač Azure
- Sada škálování virtuálních počítačů Azure
- Hybridní virtuální počítač připojený pomocí ARC Azure

## <a name="supported-operating-systems"></a>Podporované operační systémy

Následující tabulka uvádí operační systémy Windows a Linux, které Azure Monitor pro virtuální počítače podporuje. Později v této části najdete úplný seznam, který podrobně popisuje hlavní a podverze operačního systému Linux a podporované verze jádra.

|Verze operačního systému |Výkon |Maps |
|-----------|------------|-----|
|Windows Server 2019 | X | X |
|Windows Server 2016 1803 | X | X |
|Windows Server 2016 | X | X |
|Windows Server 2012 R2 | X | X |
|Windows Server 2012 | X | X |
|Windows Server 2008 R2 | X | X|
|Systém Windows 10 1803 | X | X |
|Windows 8.1 | X | X |
|Windows 8 | X | X |
|Windows 7 SP1 | X | X |
|Red Hat Enterprise Linux (RHEL) 6, 7| X | X| 
|Ubuntu 18,04, 16,04 | X | X |
|CentOS Linux 7, 6 | X | X |
|SUSE Linux Enterprise Server (SLES) 12 | X | X |
|Debian 9,4, 8 | ×<sup>1</sup> | |

<sup>1</sup> funkce výkonu Azure monitor pro virtuální počítače je k dispozici pouze Azure monitor. Není k dispozici přímo v levém podokně virtuálního počítače Azure.

>[!NOTE]
>V operačním systému Linux:
> - Jsou podporované jen verze s výchozím a SMP jádrem Linuxu.
> - Nestandardní verze jádra, jako třeba rozšíření fyzické adresy (PAE) a Xen, nejsou podporované pro žádnou distribuci systému Linux. Například systém s řetězcem vydání *2.6.16.21-0,8-Xen* není podporován.
> - Vlastní jádra, včetně překompilování standardních jader, nejsou podporovaná.
> - Jádro CentOSPlus je podporováno.
> - Pro chybu zabezpečení Spectre je nutné opravit jádro systému Linux. Další podrobnosti najdete u dodavatele distribuce systému Linux.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Verze operačního systému | Verze jádra |
|:--|:--|
| 7.6 | 3.10.0-957 |
| 7,5 | 3.10.0-862 |
| 7.4 | 3.10.0-693 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Verze operačního systému | Verze jádra |
|:--|:--|
| 6,10 | 2.6.32 – 754 |
| 6.9 | 2.6.32 – 696 |

#### <a name="centosplus"></a>CentOSPlus

| Verze operačního systému | Verze jádra |
|:--|:--|
| 6,10 | 2.6.32-754.3.5<br>2.6.32-696.30.1 |
| 6.9 | 2.6.32-696.30.1<br>2.6.32-696.18.7 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| Verze operačního systému | Verze jádra |
|:--|:--|
| 18,04 | 5.3.0 – 1020<br>5,0 (zahrnuje jádro Azure vyladěné)<br>4,18* <br> 4,15* |
| 16.04.3 | 4,15. * |
| 16,04 | 4,13.\*<br>4,11.\*<br>4,10.\*<br>4,8.\*<br>4,4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Verze operačního systému | Verze jádra |
|:--|:--|
|12 SP4 | 4,12. * (zahrnuje jádro Azure s vyladěnými jádry) |
|12 SP3 | 4,4. * |
|12 SP2 | 4,4. * |

#### <a name="debian"></a>Debian 

| Verze operačního systému | Verze jádra |
|:--|:--|
| 9 | 4,9 | 

## <a name="supported-azure-arc-machines"></a>Podporované počítače ARC Azure
Azure Monitor pro virtuální počítače je k dispozici pro servery s podporou ARC Azure v oblastech, kde je k dispozici služba rozšíření ARC. Je nutné, abyste spustili agenta ARC verze 0,9 nebo vyšší.

| Připojený zdroj | Podporováno | Popis |
|:--|:--|:--|
| Agenti systému Windows | Ano | Společně s [agentem Log Analytics pro Windows](../platform/log-analytics-agent.md)potřebují agenti pro Windows agenta závislostí. Další informace najdete v tématu [podporované operační systémy](#supported-operating-systems). |
| Agenti systému Linux | Ano | Společně s [agentem Log Analytics pro Linux](../platform/log-analytics-agent.md)musí mít agenti pro Linux agenta závislostí. Další informace najdete v tématu [podporované operační systémy](#supported-operating-systems). |
| Skupina pro správu nástroje System Center Operations Manager | Ne | |

## <a name="agents"></a>Agenti
Azure Monitor pro virtuální počítače vyžaduje, aby byly na každém virtuálním počítači nebo sadě škálování virtuálních počítačů, které mají být monitorovány, nainstalovány následující dva agenty. Jediným požadavkem k zaregistrování prostředků je instalace těchto agentů a jejich připojení k pracovnímu prostoru.

- [Agent Log Analytics](../platform/log-analytics-agent.md). Shromažďuje události a data o výkonu z virtuálního počítače nebo sady škálování virtuálních počítačů a doručuje je do pracovního prostoru Log Analytics. Metody nasazení pro agenta Log Analytics v prostředcích Azure používají rozšíření virtuálního počítače pro [Windows](../../virtual-machines/extensions/oms-windows.md) a [Linux](../../virtual-machines/extensions/oms-linux.md).
- Agent závislostí. Shromažďuje zjištěná data o procesech spuštěných na virtuálním počítači a o závislostech externích procesů, které používá [funkce map v Azure monitor pro virtuální počítače](vminsights-maps.md). Agent závislostí spoléhá na agenta Log Analytics k doručování svých dat do Azure Monitor. Metody nasazení pro agenta závislostí v prostředcích Azure používají rozšíření virtuálního počítače pro [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) a [Linux](../../virtual-machines/extensions/agent-dependency-linux.md).

> [!NOTE]
> Agent Log Analytics je stejný agent, kterého používá System Center Operations Manager. Azure Monitor pro virtuální počítače mohou monitorovat agenty, které jsou také sledovány pomocí Operations Manager Pokud jsou přímo připojeny, a nainstalovat na ně agenta závislostí. Agenty připojení k Azure Monitor prostřednictvím [připojení skupiny pro správu](../tform/../platform/om-agents.md) nelze monitorovat pomocí Azure monitor pro virtuální počítače.

Níže najdete několik metod pro nasazení těchto agentů. 

| Metoda | Popis |
|:---|:---|
| [Azure Portal](vminsights-enable-single-vm.md) | Nainstalujte oba agenty na jeden virtuální počítač, sadu škálování virtuálního počítače nebo hybridní virtuální počítače připojené pomocí Azure ARC. |
| [Šablony Resource Manageru](vminsights-enable-powershell.md) | Nainstalujte oba agenty pomocí kterékoli z podporovaných metod pro nasazení Správce prostředků šablony, včetně CLI a PowerShellu. |
| [Azure Policy](vminsights-enable-at-scale-policy.md) | Pokud se vytvoří virtuální počítač nebo sada škálování virtuálních počítačů, přiřaďte Azure Policy iniciativu k automatické instalaci agentů. |
| [Ruční instalace](vminsights-enable-hybrid-cloud.md) | Nainstalujte agenty v hostovaném operačním systému do počítačů hostovaných mimo Azure, včetně ve vašem datovém centru nebo v jiných cloudových prostředích. |




## <a name="management-packs"></a>Sady Management Pack
Pokud je pro Azure Monitor pro virtuální počítače nakonfigurovaný pracovní prostor Log Analytics, předají se dvě sady Management Pack na všechny počítače s Windows připojené k tomuto pracovnímu prostoru. Sady Management Pack jsou pojmenovány jako *Microsoft. IntelligencePacks. ApplicationDependencyMonitor* a *Microsoft. IntelligencePacks. VMInsights* a jsou zapisovány do *%ProgramFiles%\Microsoft monitoring Agent\Agent\Health Service State\Management Packs \* . 

Zdroj dat používaný Management Pack *ApplicationDependencyMonitor* je **% Program Files%\Microsoft monitoring Agent\Agent\Health Service State\Resources \<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll*. Zdroj dat používaný Management Pack *VMInsights* je *% Program Files%\Microsoft monitoring Agent\Agent\Health Service State\Resources \<AutoGeneratedID> \ Microsoft.VirtualMachineMonitoringModule.dll*.

## <a name="diagnostic-and-usage-data"></a>Diagnostika a data o používání

Společnost Microsoft automaticky shromažďuje data o využití a výkonu prostřednictvím služby Azure Monitor. Společnost Microsoft používá tato data ke zlepšení kvality, zabezpečení a integrity služby. 

Aby funkce map poskytovala přesné a efektivní možnosti odstraňování potíží, zahrnuje data týkající se konfigurace softwaru. Data obsahují informace, jako je operační systém a verze, IP adresa, název DNS a název pracovní stanice. Microsoft neshromažďuje jména, adresy ani jiné kontaktní údaje.

Další informace o shromažďování a používání dat naleznete v tématu [prohlášení o zásadách ochrany osobních údajů služby Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Další kroky

Informace o tom, jak používat funkci monitorování výkonu, najdete v tématu [zobrazení výkonu Azure monitor pro virtuální počítače](vminsights-performance.md). Pokud chcete zobrazit zjištěné závislosti aplikací, přečtěte si téma [zobrazení Azure monitor pro virtuální počítače mapa](vminsights-maps.md).

