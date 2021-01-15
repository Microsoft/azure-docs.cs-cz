---
title: Povolit Azure Monitor pro virtuální počítače – přehled
description: Přečtěte si, jak nasadit a nakonfigurovat Azure Monitor pro virtuální počítače. Zjistěte požadavky na systém.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/22/2020
ms.custom: references_regions
ms.openlocfilehash: 655a146ccde9c75629d0a991a6a3aafa91f40764
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2021
ms.locfileid: "98233963"
---
# <a name="enable-azure-monitor-for-vms-overview"></a>Povolit Azure Monitor pro virtuální počítače – přehled

Tento článek poskytuje přehled možností, které jsou k dispozici pro povolení Azure Monitor pro virtuální počítače k monitorování stavu a výkonu následujících akcí:

- Virtuální počítače Azure 
- Škálovací sady virtuálních počítačů Azure
- Hybridní virtuální počítače připojené pomocí ARC Azure
- Místní virtuální počítače
- Virtuální počítače hostované v jiném cloudovém prostředí.  

Nastavení Azure Monitor pro virtuální počítače:

* Pokud chcete povolit jeden virtuální počítač Azure, sadu škálování virtuálního počítače Azure nebo počítač ARC Azure, vyberte **přehledy** přímo z nabídky v Azure Portal.
* Pomocí Azure Policy povolte více virtuálních počítačů Azure, virtuálních počítačů Azure nebo počítačů s obloukem Azure ARC. Tato metoda zajišťuje, že u stávajících a nových virtuálních počítačů a sad škálování jsou požadované závislosti nainstalovány a správně nakonfigurovány. Nahlásí se nekompatibilní virtuální počítače a sady škálování, takže se můžete rozhodnout, jestli je chcete povolit, a opravit je.
* Povolte více virtuálních počítačů Azure, virtuálních počítačů Azure ARC, služby Azure Virtual Machine Scale Sets nebo počítačů ARC Azure v rámci zadaného předplatného nebo skupiny prostředků pomocí PowerShellu.
* Povolte Azure Monitor pro virtuální počítače k monitorování virtuálních počítačů nebo fyzických počítačů hostovaných ve vaší podnikové síti nebo jiném cloudovém prostředí.

## <a name="supported-machines"></a>Podporované počítače
Azure Monitor pro virtuální počítače podporuje následující počítače:

- Virtuální počítač Azure
- Sada škálování virtuálních počítačů Azure
- Hybridní virtuální počítač připojený pomocí ARC Azure


## <a name="supported-azure-arc-machines"></a>Podporované počítače ARC Azure
Azure Monitor pro virtuální počítače je k dispozici pro servery s podporou ARC Azure v oblastech, kde je k dispozici služba rozšíření ARC. Je nutné, abyste spustili agenta ARC verze 0,9 nebo vyšší.

| Připojený zdroj | Podporováno | Popis |
|:--|:--|:--|
| Agenti systému Windows | Ano | Společně s [agentem Log Analytics pro Windows](../platform/log-analytics-agent.md)potřebují agenti pro Windows agenta závislostí. Další informace najdete v tématu [podporované operační systémy](../platform/agents-overview.md#supported-operating-systems). |
| Agenti systému Linux | Ano | Společně s [agentem Log Analytics pro Linux](../platform/log-analytics-agent.md)musí mít agenti pro Linux agenta závislostí. Další informace najdete v tématu [podporované operační systémy](#supported-operating-systems). |
| Skupina pro správu nástroje System Center Operations Manager | Ne | |

## <a name="supported-operating-systems"></a>Podporované operační systémy

Azure Monitor pro virtuální počítače podporuje všechny operační systémy, které podporují agenta Log Analytics a agenta závislostí. Úplný seznam najdete v tématu [přehled Azure Monitorch agentů ](../platform/agents-overview.md#supported-operating-systems) .

> [!IMPORTANT]
> Funkce Azure Monitor pro virtuální počítače stav hosta má více omezené podpory operačního systému, zatímco je ve verzi Public Preview. Podrobný seznam najdete v tématu [Povolení stavu hosta Azure monitor pro virtuální počítače (Preview)](vminsights-health-enable.md) .

Přečtěte si následující seznam důležitých informací o podpoře agenta závislostí pro Linux, který podporuje Azure Monitor pro virtuální počítače:

- Jsou podporované jen verze s výchozím a SMP jádrem Linuxu.
- Nestandardní verze jádra, jako třeba rozšíření fyzické adresy (PAE) a Xen, nejsou podporované pro žádnou distribuci systému Linux. Například systém s řetězcem vydání *2.6.16.21-0,8-Xen* není podporován.
- Vlastní jádra, včetně překompilování standardních jader, nejsou podporovaná.
- Pro Debian distribuce jiné než verze 9,4 není funkce map podporována a funkce Performance je k dispozici pouze z nabídky Azure Monitor. Není k dispozici přímo v levém podokně virtuálního počítače Azure.
- Jádro CentOSPlus je podporováno.
- Pro chybu zabezpečení Spectre je nutné opravit jádro systému Linux. Další podrobnosti najdete u dodavatele distribuce systému Linux.
## <a name="log-analytics-workspace"></a>Pracovní prostor služby Log Analytics
Azure Monitor pro virtuální počítače vyžaduje pracovní prostor Log Analytics. Podrobnosti a požadavky tohoto pracovního prostoru najdete v tématu [konfigurace log Analyticsho pracovního prostoru pro Azure monitor pro virtuální počítače](vminsights-configure-workspace.md) .
## <a name="agents"></a>Agenti
Azure Monitor pro virtuální počítače vyžaduje, aby byly na každém virtuálním počítači nebo sadě škálování virtuálních počítačů, které mají být monitorovány, nainstalovány následující dva agenty. Chcete-li připojit prostředek, nainstalujte tyto agenty a připojte je k pracovnímu prostoru.  Požadavky na síť pro tyto agenty najdete v tématu [požadavky na síť](../platform/log-analytics-agent.md#network-requirements) .

- [Agent Log Analytics](../platform/log-analytics-agent.md). Shromažďuje události a data o výkonu z virtuálního počítače nebo sady škálování virtuálních počítačů a doručuje je do pracovního prostoru Log Analytics. Metody nasazení pro agenta Log Analytics v prostředcích Azure používají rozšíření virtuálního počítače pro [Windows](../../virtual-machines/extensions/oms-windows.md) a [Linux](../../virtual-machines/extensions/oms-linux.md).
- Agent závislostí. Shromažďuje zjištěná data o procesech spuštěných na virtuálním počítači a o závislostech externích procesů, které používá [funkce map v Azure monitor pro virtuální počítače](vminsights-maps.md). Agent závislostí spoléhá na agenta Log Analytics k doručování svých dat do Azure Monitor. Metody nasazení pro agenta závislostí v prostředcích Azure používají rozšíření virtuálního počítače pro [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) a [Linux](../../virtual-machines/extensions/agent-dependency-linux.md).

> [!NOTE]
> Agent Log Analytics je stejný agent, kterého používá System Center Operations Manager. Azure Monitor pro virtuální počítače mohou monitorovat agenty, které jsou také sledovány pomocí Operations Manager Pokud jsou přímo připojeny, a nainstalovat na ně agenta závislostí. Agenty připojení k Azure Monitor prostřednictvím [připojení skupiny pro správu](../tform/../platform/om-agents.md) nelze monitorovat pomocí Azure monitor pro virtuální počítače.

Níže najdete několik metod pro nasazení těchto agentů. 

| Metoda | Popis |
|:---|:---|
| [Azure Portal](./vminsights-enable-portal.md) | Nainstalujte oba agenty na jeden virtuální počítač, sadu škálování virtuálního počítače nebo hybridní virtuální počítače připojené pomocí Azure ARC. |
| [Šablony Resource Manageru](vminsights-enable-resource-manager.md) | Nainstalujte oba agenty pomocí kterékoli z podporovaných metod pro nasazení Správce prostředků šablony, včetně CLI a PowerShellu. |
| [Azure Policy](./vminsights-enable-policy.md) | Pokud se vytvoří virtuální počítač nebo sada škálování virtuálních počítačů, přiřaďte Azure Policy iniciativu k automatické instalaci agentů. |
| [Ruční instalace](./vminsights-enable-hybrid.md) | Nainstalujte agenty v hostovaném operačním systému do počítačů hostovaných mimo Azure, včetně ve vašem datovém centru nebo v jiných cloudových prostředích. |




## <a name="management-packs"></a>Sady Management Pack
Pokud je pro Azure Monitor pro virtuální počítače nakonfigurovaný pracovní prostor Log Analytics, předají se dvě sady Management Pack na všechny počítače s Windows připojené k tomuto pracovnímu prostoru. Sady Management Pack jsou pojmenovány jako *Microsoft. IntelligencePacks. ApplicationDependencyMonitor* a *Microsoft. IntelligencePacks. VMInsights* a jsou zapisovány do *%ProgramFiles%\Microsoft monitoring Agent\Agent\Health Service State\Management Packs*. 

Zdroj dat používaný Management Pack *ApplicationDependencyMonitor* je **% Program Files%\Microsoft monitoring Agent\Agent\Health Service State\Resources \<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll*. Zdroj dat používaný Management Pack *VMInsights* je *% Program Files%\Microsoft monitoring Agent\Agent\Health Service State\Resources \<AutoGeneratedID> \ Microsoft.VirtualMachineMonitoringModule.dll*.

## <a name="diagnostic-and-usage-data"></a>Diagnostika a data o používání

Společnost Microsoft automaticky shromažďuje data o využití a výkonu prostřednictvím služby Azure Monitor. Společnost Microsoft používá tato data ke zlepšení kvality, zabezpečení a integrity služby. 

Aby funkce map poskytovala přesné a efektivní možnosti odstraňování potíží, zahrnuje data týkající se konfigurace softwaru. Data obsahují informace, jako je operační systém a verze, IP adresa, název DNS a název pracovní stanice. Microsoft neshromažďuje jména, adresy ani jiné kontaktní údaje.

Další informace o shromažďování a používání dat naleznete v tématu [prohlášení o zásadách ochrany osobních údajů služby Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Další kroky

Informace o tom, jak používat funkci monitorování výkonu, najdete v tématu [zobrazení výkonu Azure monitor pro virtuální počítače](vminsights-performance.md). Pokud chcete zobrazit zjištěné závislosti aplikací, přečtěte si téma [zobrazení Azure monitor pro virtuální počítače mapa](vminsights-maps.md).
