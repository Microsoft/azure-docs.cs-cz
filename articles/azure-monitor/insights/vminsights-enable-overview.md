---
title: Povolit Azure Monitor pro virtuální počítače – přehled
description: Přečtěte si, jak nasadit a nakonfigurovat Azure Monitor pro virtuální počítače. Zjistěte požadavky na systém.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: 072f8fd44fa45648afd15cb40cba26bb427c7b56
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86539614"
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

## <a name="prerequisites"></a>Předpoklady

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
- Západní Evropa
- Východní Asie
- Jihovýchodní Asie
- Central India
- Japan East
- Austrálie – východ
- Austrálie – jihovýchod

>[!NOTE]
>Virtuální počítače Azure můžete monitorovat v libovolné oblasti. Samotné virtuální počítače nejsou omezeny na oblasti podporované pracovním prostorem Log Analytics.
>

Pokud nemáte pracovní prostor Log Analytics, můžete ho vytvořit pomocí jednoho z prostředků:
* [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../platform/powershell-workspace-configuration.md)
* [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Pracovní prostor můžete také vytvořit, když povolíte monitorování pro jeden virtuální počítač Azure nebo sadu škálování virtuálního počítače v Azure Portal.

Chcete-li nastavit scénář ve velkém měřítku, který používá Azure Policy, Azure PowerShell nebo šablony Azure Resource Manager, je nutné nainstalovat řešení *VMInsights* . Můžete to provést pomocí jedné z následujících metod:

* Použijte [Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace).
* Na stránce Azure Monitor pro virtuální počítače [**pokrytí zásad**](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) vyberte **Konfigurovat pracovní prostor**. 

### <a name="azure-arc-machines"></a>Počítače ARC Azure
Azure Monitor pro virtuální počítače je k dispozici pro servery s podporou ARC Azure v oblastech, kde je k dispozici služba rozšíření ARC. Aby bylo možné povolit Azure Monitor pro virtuální počítače na serverech s povoleným obloukem, musí uživatelé používat verzi 0,9 nebo vyšší agenta ARC.

### <a name="supported-operating-systems"></a>Podporované operační systémy

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

### <a name="the-microsoft-dependency-agent"></a>Microsoft Dependency Agent

Funkce map v Azure Monitor pro virtuální počítače získá svá data od agenta závislostí společnosti Microsoft. Agent závislostí spoléhá na Log Analytics agenta pro připojení k Log Analytics. Takže váš systém musí mít nainstalovaný agent Log Analytics a nakonfigurovaný s agentem závislostí.

Bez ohledu na to, jestli povolíte Azure Monitor pro virtuální počítače pro jeden virtuální počítač Azure nebo použijete metodu nasazení na úrovni služby, použijte rozšíření agenta závislostí virtuálních počítačů Azure pro [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) nebo [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) a nainstalujte agenta jako součást prostředí.

>[!NOTE]
>Následující informace popsané v této části se vztahují také na [řešení Service map](service-map.md).  

V hybridním prostředí můžete agenta závislostí stáhnout a nainstalovat ručně nebo pomocí automatizované metody.

Následující tabulka popisuje připojené zdroje, které funkce mapy podporuje v hybridním prostředí.

| Připojený zdroj | Podporováno | Popis |
|:--|:--|:--|
| Agenti systému Windows | Ano | Společně s [agentem Log Analytics pro Windows](../../azure-monitor/platform/log-analytics-agent.md)potřebují agenti pro Windows agenta závislostí. Další informace najdete v tématu [podporované operační systémy](#supported-operating-systems). |
| Agenti systému Linux | Ano | Společně s [agentem Log Analytics pro Linux](../../azure-monitor/platform/log-analytics-agent.md)musí mít agenti pro Linux agenta závislostí. Další informace najdete v tématu [podporované operační systémy](#supported-operating-systems). |
| Skupina pro správu nástroje System Center Operations Manager | No | |

Agenta závislostí si můžete stáhnout z těchto umístění:

| Soubor | Operační systém | Verze | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.10.4.10090 | B4E1FF9C1E5CD254AA709AEF9723A81F04EC0763C327567C582CE99C0C5A0BAE  |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.10.4.10090 | A56E310D297CE3B343AE8F4A6F72980F1C3173862D6169F1C713C2CA09660A9F |

## <a name="role-based-access-control"></a>Řízení přístupu na základě role

K povolení a přístupu k funkcím v Azure Monitor pro virtuální počítače musíte mít roli *přispěvatel Log Analytics* . Chcete-li zobrazit data o výkonu, stavu a mapování, musíte mít roli *Čtenář monitorování* pro virtuální počítač Azure. Pracovní prostor Log Analytics musí být nakonfigurován pro Azure Monitor pro virtuální počítače.

Další informace o tom, jak řídit přístup k pracovnímu prostoru Log Analytics, najdete v tématu [Správa pracovních prostorů](../../azure-monitor/platform/manage-access.md).

## <a name="how-to-enable-azure-monitor-for-vms"></a>Postup povolení Azure Monitor pro virtuální počítače

Povolte Azure Monitor pro virtuální počítače pomocí jedné z metod popsaných v této tabulce:

| Stav nasazení | Metoda | Popis |
|------------------|--------|-------------|
| Jeden virtuální počítač Azure, Azure VMSS nebo počítač ARC Azure | [Povolit z portálu](vminsights-enable-single-vm.md) | Vyberte si **přehledy** přímo z nabídky v Azure Portal. |
| Několik virtuálních počítačů Azure, Azure VMSS nebo počítače s obloukem Azure | [Povolit prostřednictvím Azure Policy](vminsights-enable-at-scale-policy.md) | Při vytvoření virtuálního počítače nebo VMSS použijte Azure Policy k automatickému povolení. |
| | [Povolit prostřednictvím šablon Azure PowerShell nebo Azure Resource Manager](vminsights-enable-at-scale-powershell.md) | Pomocí Azure PowerShell nebo Azure Resource Manager šablony můžete povolit více virtuálních počítačů Azure, virtuálního počítače Azure ARC nebo Azure VMSS v rámci zadaného předplatného nebo skupiny prostředků. |
| Hybridní cloud | [Povolit pro hybridní prostředí](vminsights-enable-hybrid-cloud.md) | Nasazení do virtuálních počítačů nebo fyzických počítačů hostovaných ve vašem datovém centru nebo v jiných cloudových prostředích. |

## <a name="management-packs"></a>Sady Management Pack

Pokud je Azure Monitor pro virtuální počítače povolená a nakonfigurovaná pomocí pracovního prostoru Log Analytics, Management Pack se přepošle do všech počítačů se systémem Windows, které do tohoto pracovního prostoru hlásí. Pokud jste [System Center Operations Manager skupinu pro správu](../../azure-monitor/platform/om-agents.md) s pracovním prostorem Log Analytics, Service map Management Pack je nasazen ze skupiny pro správu do počítačů se systémem Windows, které se hlásí do skupiny pro správu.  

Management Pack má název *Microsoft. IntelligencePacks. ApplicationDependencyMonitor*. Jeho zápis do `%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\` složky. Zdroj dat, který používá Management Pack, je `%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll` .

## <a name="diagnostic-and-usage-data"></a>Diagnostika a data o používání

Společnost Microsoft automaticky shromažďuje data o využití a výkonu prostřednictvím služby Azure Monitor. Společnost Microsoft používá tato data ke zlepšení kvality, zabezpečení a integrity služby. 

Aby funkce map poskytovala přesné a efektivní možnosti odstraňování potíží, zahrnuje data týkající se konfigurace softwaru. Data obsahují informace, jako je operační systém a verze, IP adresa, název DNS a název pracovní stanice. Microsoft neshromažďuje jména, adresy ani jiné kontaktní údaje.

Další informace o shromažďování a používání dat naleznete v tématu [prohlášení o zásadách ochrany osobních údajů služby Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

Nyní, když jste povolili monitorování pro váš virtuální počítač, jsou informace o monitorování k dispozici pro analýzu v Azure Monitor pro virtuální počítače.

## <a name="next-steps"></a>Další kroky

Informace o tom, jak používat funkci monitorování výkonu, najdete v tématu [zobrazení výkonu Azure monitor pro virtuální počítače](vminsights-performance.md). Pokud chcete zobrazit zjištěné závislosti aplikací, přečtěte si téma [zobrazení Azure monitor pro virtuální počítače mapa](vminsights-maps.md).
