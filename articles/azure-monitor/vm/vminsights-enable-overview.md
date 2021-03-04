---
title: Povolit přehled virtuálních počítačů Insights
description: Přečtěte si, jak nasadit a nakonfigurovat službu VM Insights. Zjistěte požadavky na systém.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/22/2020
ms.custom: references_regions
ms.openlocfilehash: 7aa8221c960685149a5d475665be105acaf7aa15
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102046665"
---
# <a name="enable-vm-insights-overview"></a>Povolit přehled virtuálních počítačů Insights

Tento článek poskytuje přehled možností, které jsou k dispozici pro povolení služby VM Insights k monitorování stavu a výkonu následujících akcí:

- Virtuální počítače Azure 
- Škálovací sady virtuálních počítačů Azure
- Hybridní virtuální počítače připojené pomocí ARC Azure
- Místní virtuální počítače
- Virtuální počítače hostované v jiném cloudovém prostředí.  

Postup nastavení virtuálních počítačů:

* Pokud chcete povolit jeden virtuální počítač Azure, sadu škálování virtuálního počítače Azure nebo počítač ARC Azure, vyberte **přehledy** přímo z nabídky v Azure Portal.
* Pomocí Azure Policy povolte více virtuálních počítačů Azure, virtuálních počítačů Azure nebo počítačů s obloukem Azure ARC. Tato metoda zajišťuje, že u stávajících a nových virtuálních počítačů a sad škálování jsou požadované závislosti nainstalovány a správně nakonfigurovány. Nahlásí se nekompatibilní virtuální počítače a sady škálování, takže se můžete rozhodnout, jestli je chcete povolit, a opravit je.
* Povolte více virtuálních počítačů Azure, virtuálních počítačů Azure ARC, služby Azure Virtual Machine Scale Sets nebo počítačů ARC Azure v rámci zadaného předplatného nebo skupiny prostředků pomocí PowerShellu.
* Umožněte službě VM Insights monitorovat virtuální počítače nebo fyzické počítače hostované ve vaší podnikové síti nebo jiném cloudovém prostředí.

## <a name="supported-machines"></a>Podporované počítače
Přehledy virtuálních počítačů podporují následující počítače:

- Virtuální počítač Azure
- Sada škálování virtuálních počítačů Azure
- Hybridní virtuální počítač připojený pomocí ARC Azure


## <a name="supported-azure-arc-machines"></a>Podporované počítače ARC Azure
Služba Azure Insights je k dispozici pro servery s podporou ARC Azure v oblastech, kde je k dispozici služba rozšíření ARC. Je nutné, abyste spustili agenta ARC verze 0,9 nebo vyšší.

| Připojený zdroj | Podporováno | Popis |
|:--|:--|:--|
| Agenti systému Windows | Ano | Společně s [agentem Log Analytics pro Windows](../agents/log-analytics-agent.md)potřebují agenti pro Windows agenta závislostí. Další informace najdete v tématu [podporované operační systémy](../agents/agents-overview.md#supported-operating-systems). |
| Agenti systému Linux | Ano | Společně s [agentem Log Analytics pro Linux](../agents/log-analytics-agent.md)musí mít agenti pro Linux agenta závislostí. Další informace najdete v tématu [podporované operační systémy](#supported-operating-systems). |
| Skupina pro správu nástroje System Center Operations Manager | Ne | |

## <a name="supported-operating-systems"></a>Podporované operační systémy

Přehledy virtuálních počítačů podporují libovolný operační systém, který podporuje agenta Log Analytics a agenta závislostí. Úplný seznam najdete v tématu [přehled Azure Monitorch agentů ](../agents/agents-overview.md#supported-operating-systems) .

> [!IMPORTANT]
> Funkce stavu hosta virtuálních počítačů Insights má víc omezené podpory operačního systému, zatímco je ve verzi Public Preview. Podrobný seznam najdete v tématu [Povolení stavu hosta služby VM Insights (Preview)](../vm/vminsights-health-enable.md) .

Podívejte se na následující seznam důležitých informací o podpoře agenta závislostí pro Linux, který podporuje službu VM Insights:

- Jsou podporované jen verze s výchozím a SMP jádrem Linuxu.
- Nestandardní verze jádra, jako třeba rozšíření fyzické adresy (PAE) a Xen, nejsou podporované pro žádnou distribuci systému Linux. Například systém s řetězcem vydání *2.6.16.21-0,8-Xen* není podporován.
- Vlastní jádra, včetně překompilování standardních jader, nejsou podporovaná.
- Pro Debian distribuce jiné než verze 9,4 není funkce map podporována a funkce Performance je k dispozici pouze z nabídky Azure Monitor. Není k dispozici přímo v levém podokně virtuálního počítače Azure.
- Jádro CentOSPlus je podporováno.
- Pro chybu zabezpečení Spectre je nutné opravit jádro systému Linux. Další podrobnosti najdete u dodavatele distribuce systému Linux.
## <a name="log-analytics-workspace"></a>Pracovní prostor služby Log Analytics
Virtuální počítač Insights vyžaduje Log Analytics pracovní prostor. Podrobnosti a požadavky tohoto pracovního prostoru najdete v tématu [konfigurace Log Analytics pracovního prostoru pro službu VM Insights](vminsights-configure-workspace.md) .
## <a name="agents"></a>Agenti
K monitorování virtuálních počítačů na všech virtuálních počítačích nebo na škále virtuálních počítačů, které mají být monitorovány, je nutné, aby byly na virtuálním počítači nainstalovány následující dva Chcete-li připojit prostředek, nainstalujte tyto agenty a připojte je k pracovnímu prostoru.  Požadavky na síť pro tyto agenty najdete v tématu [požadavky na síť](../agents/log-analytics-agent.md#network-requirements) .

- [Agent Log Analytics](../agents/log-analytics-agent.md). Shromažďuje události a data o výkonu z virtuálního počítače nebo sady škálování virtuálních počítačů a doručuje je do pracovního prostoru Log Analytics. Metody nasazení pro agenta Log Analytics v prostředcích Azure používají rozšíření virtuálního počítače pro [Windows](../../virtual-machines/extensions/oms-windows.md) a [Linux](../../virtual-machines/extensions/oms-linux.md).
- Agent závislostí. Shromažďuje zjištěná data o procesech spuštěných na virtuálním počítači a o závislostech externích procesů, které používá [funkce map v rámci služby VM Insights](../vm/vminsights-maps.md). Agent závislostí spoléhá na agenta Log Analytics k doručování svých dat do Azure Monitor. Metody nasazení pro agenta závislostí v prostředcích Azure používají rozšíření virtuálního počítače pro [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) a [Linux](../../virtual-machines/extensions/agent-dependency-linux.md).

> [!NOTE]
> Agent Log Analytics je stejný agent, kterého používá System Center Operations Manager. Služba VM Insights může monitorovat agenty, kteří jsou také monitorované pomocí Operations Manager, pokud jsou přímo připojeni a vy na ně nainstalujete agenta závislostí. Agenty připojení k Azure Monitor prostřednictvím [připojení skupiny pro správu](../tform/../agents/om-agents.md) nelze monitorovat pomocí přehledů virtuálních počítačů.

Níže najdete několik metod pro nasazení těchto agentů. 

| Metoda | Popis |
|:---|:---|
| [Azure Portal](../vm/vminsights-enable-portal.md) | Nainstalujte oba agenty na jeden virtuální počítač, sadu škálování virtuálního počítače nebo hybridní virtuální počítače připojené pomocí Azure ARC. |
| [Šablony Resource Manageru](../vm/vminsights-enable-resource-manager.md) | Nainstalujte oba agenty pomocí kterékoli z podporovaných metod pro nasazení Správce prostředků šablony, včetně CLI a PowerShellu. |
| [Azure Policy](../vm/vminsights-enable-policy.md) | Pokud se vytvoří virtuální počítač nebo sada škálování virtuálních počítačů, přiřaďte Azure Policy iniciativu k automatické instalaci agentů. |
| [Ruční instalace](../vm/vminsights-enable-hybrid.md) | Nainstalujte agenty v hostovaném operačním systému do počítačů hostovaných mimo Azure, včetně ve vašem datovém centru nebo v jiných cloudových prostředích. |


## <a name="network-requirements"></a>Požadavky sítě

- Požadavky na síť pro agenta Log Analytics najdete v tématu [požadavky na síť](../agents/log-analytics-agent.md#network-requirements) .
- Agent závislostí vyžaduje připojení z virtuálního počítače k adrese 169.254.169.254. Toto je koncový bod služby Azure metadata Service. Zajistěte, aby nastavení brány firewall povolovala připojení k tomuto koncovému bodu.


## <a name="management-packs"></a>Sady Management Pack
Když je pro službu VM Insights nakonfigurovaný pracovní prostor Log Analytics, předají se do všech počítačů s Windows připojených k tomuto pracovnímu prostoru dvě sady Management Pack. Sady Management Pack jsou pojmenovány jako *Microsoft. IntelligencePacks. ApplicationDependencyMonitor* a *Microsoft. IntelligencePacks. VMInsights* a jsou zapisovány do *%ProgramFiles%\Microsoft monitoring Agent\Agent\Health Service State\Management Packs*. 

Zdroj dat používaný Management Pack *ApplicationDependencyMonitor* je **% Program Files%\Microsoft monitoring Agent\Agent\Health Service State\Resources \<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll*. Zdroj dat používaný Management Pack *VMInsights* je *% Program Files%\Microsoft monitoring Agent\Agent\Health Service State\Resources \<AutoGeneratedID> \ Microsoft.VirtualMachineMonitoringModule.dll*.

## <a name="diagnostic-and-usage-data"></a>Diagnostika a data o používání

Společnost Microsoft automaticky shromažďuje data o využití a výkonu prostřednictvím služby Azure Monitor. Společnost Microsoft používá tato data ke zlepšení kvality, zabezpečení a integrity služby. 

Aby funkce map poskytovala přesné a efektivní možnosti odstraňování potíží, zahrnuje data týkající se konfigurace softwaru. Data obsahují informace, jako je operační systém a verze, IP adresa, název DNS a název pracovní stanice. Microsoft neshromažďuje jména, adresy ani jiné kontaktní údaje.

Další informace o shromažďování a používání dat naleznete v tématu [prohlášení o zásadách ochrany osobních údajů služby Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Další kroky

Pokud chcete zjistit, jak používat funkci monitorování výkonu, přečtěte si téma [zobrazení výkonu virtuálního počítače s přehledem](../vm/vminsights-performance.md). Pokud si chcete zobrazit zjištěné závislosti aplikací, přečtěte si téma [zobrazení mapy virtuálních počítačů](../vm/vminsights-maps.md).
