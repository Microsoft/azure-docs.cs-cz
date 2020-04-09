---
title: Povolení přehledu nástroje Azure Monitor pro virtuální počítače
description: Zjistěte, jak nasadit a nakonfigurovat Azure Monitor pro virtuální počítače. Zjistěte systémové požadavky.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/08/2020
ms.openlocfilehash: 5bb5d5dd5110f176b59a99f6a3aa223184158da5
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982306"
---
# <a name="enable-azure-monitor-for-vms-overview"></a>Povolení přehledu nástroje Azure Monitor pro virtuální počítače

Tento článek obsahuje přehled možností, které jsou k dispozici pro povolení Azure Monitor pro virtuální počítače na virtuálních počítačích ke sledování stavu a výkonu. Objevte závislosti aplikací, které běží na virtuálních počítačích Azure (VM) a škálovacích sadách virtuálních počítačů, místních virtuálních počítačích nebo virtuálních počítačích hostovaných v jiném cloudovém prostředí.  

Nastavení Azure Monitoru pro virtuální počítače:

* Povolte jeden virtuální počítač Azure nebo škálovací sadu virtuálních počítačů výběrem **přehledů** přímo z škálovací sady virtuálních zařízení nebo virtuálních strojů.
* Povolte dva nebo více virtuálních počítačů Azure a škálovací sady virtuálních strojů pomocí zásad Azure. Tato metoda zajišťuje, že na stávajících a nových virtuálních počítačích a škálovacích sadách jsou požadované závislosti nainstalovány a správně nakonfigurovány. Hlásí se nekompatibilní virtuální aplikace a škálovací sady, takže se můžete rozhodnout, jestli je povolíte a nastavíte.
* Povolte dva nebo více virtuálních počítačů Azure nebo škálovací sady virtuálních počítačů napříč zadaným předplatným nebo skupinou prostředků pomocí PowerShellu.
* Povolte Azure Monitor pro virtuální počítače k monitorování virtuálních počítačů nebo fyzických počítačů hostovaných ve vaší podnikové síti nebo jiném cloudovém prostředí.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že rozumíte informacím v následujících částech. 

>[!NOTE]
>Následující informace popsané v této části platí také pro [řešení Mapa služeb](service-map.md).  

### <a name="log-analytics"></a>Log Analytics

Azure Monitor pro virtuální počítače podporuje pracovní prostor Analýzy protokolů v následujících oblastech:

- USA – středozápad
- USA – západ
- USA – západ 2
- USA – středojih
- USA – východ
- USA – východ 2
- USA – střed
- USA – středosever
- Střední Kanada
- Spojené království – jih
- Severní Evropa
- Západní Evropa
- Východní Asie
- Jihovýchodní Asie
- Indie – střed
- Japonsko – východ
- Austrálie – východ
- Austrálie – jihovýchod

>[!NOTE]
>Virtuální počítače Azure můžete monitorovat v libovolné oblasti. Samotné virtuální aplikace nejsou omezeny na oblasti podporované pracovní prostor Analýzy protokolů.
>

Pokud nemáte pracovní prostor Analýzy protokolů, můžete ho vytvořit pomocí jednoho z prostředků:
* [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Portál Azure](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Můžete také vytvořit pracovní prostor, když povolujete monitorování pro jeden virtuální počítač Azure nebo škálovací sadu virtuálních strojů na webu Azure Portal.

Pokud chcete nastavit scénář ve velkém měřítku, který používá Azure Policy, Azure PowerShell nebo Azure Resource Manager šablony, v pracovním prostoru Log Analytics:

* Nainstalujte řešení *ServiceMap* a *InfrastructureInsights.* Tuto instalaci můžete dokončit pomocí zařízené šablony Azure Resource Manager. Nebo na kartě **Začínáme** na webu Azure Portal vyberte **Konfigurovat pracovní prostor**.
* Nakonfigurujte pracovní prostor Log Analytics tak, aby shromažďoval čítače výkonu.

Chcete-li nakonfigurovat pracovní prostor pro scénář ve velkém měřítku, použijte jednu z následujících metod:

* Použijte [Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace).
* Na stránce [**Pokrytí zásad**](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) Azure Monitor pro virtuální počítače vyberte Konfigurovat **pracovní prostor**. 

### <a name="supported-operating-systems"></a>Podporované operační systémy

V následující tabulce jsou uvedeny operační systémy Windows a Linux, které azure monitor pro virtuální počítače podporuje. Dále v této části najdete úplný seznam, který podrobně popisuje hlavní a menší vydání operačního systému Linux a podporované verze jádra.

|Verze operačního systému |Výkon |Maps |
|-----------|------------|-----|
|Windows Server 2019 | × | × |
|Windows Server 2016 1803 | × | × |
|Windows Server 2016 | × | × |
|Windows Server 2012 R2 | × | × |
|Windows Server 2012 | × | × |
|Windows Server 2008 R2 | × | ×|
|Windows 10 1803 | × | × |
|Windows 8.1 | × | × |
|Windows 8 | × | × |
|Windows 7 SP1 | × | × |
|Red Hat Enterprise Linux (RHEL) 6, 7| × | ×| 
|Ubuntu 18.04, 16.04 | × | × |
|Centos Linux 7, 6 | × | × |
|SUSE Linux Enterprise Server (SLES) 12 | × | × |
|Debian 9.4, 8 | X<sup>1</sup> | |

<sup>1</sup> Funkce Výkonu Azure Monitor u virtuálních počítačů je dostupná jenom z Azure Monitoru. Není k dispozici přímo z levého podokna virtuálního počítače Azure.

>[!NOTE]
>V operačním systému Linux:
> - Jsou podporované jen verze s výchozím a SMP jádrem Linuxu.
> - Nestandardní verze jádra, jako je například Physical Address Extension (PAE) a Xen, nejsou podporovány pro žádnou distribuci Linuxu. Například systém s uvolňovacím řetězcem *2.6.16.21-0.8-xen* není podporován.
> - Vlastní jádra, včetně rekompilací standardních jader, nejsou podporována.
> - Jádro CentOSPlus je podporováno.
> - Linuxové jádro musí být opraveno pro zranitelnost Spectre. Další podrobnosti získáte od dodavatele distribuce Linuxu.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Verze operačního systému | Verze jádra |
|:--|:--|
| 7.6 | 3.10.0-957 |
| 7,5 | 3.10.0-862 |
| 7.4 | 3.10.0-693 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Verze operačního systému | Verze jádra |
|:--|:--|
| 6.10 | 2.6.32-754 |
| 6.9 | 2.6.32-696 |

#### <a name="centosplus"></a>CentosPlus

| Verze operačního systému | Verze jádra |
|:--|:--|
| 6.10 | 2.6.32-754.3.5<br>2.6.32-696.30.1 |
| 6.9 | 2.6.32-696.30.1<br>2.6.32-696.18.7 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| Verze operačního systému | Verze jádra |
|:--|:--|
| 18.04 | 5.0 (obsahuje jádro vyladěné v Azure)<br>4,18*<br>4,15* |
| 16.04.3 | 4.15.* |
| 16.04 | 4.13.\*<br>4.11.\*<br>4.10.\*<br>4.8.\*<br>4.4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Verze operačního systému | Verze jádra |
|:--|:--|
|12 AKTUALIZACE SP4 | 4.12.* (obsahuje jádro vyladěné v Azure) |
|12 AKTUALIZACE SP3 | 4.4.* |
|12 AKTUALIZACE SP2 | 4.4.* |

#### <a name="debian"></a>Debian 

| Verze operačního systému | Verze jádra |
|:--|:--|
| 9 | 4.9 | 

### <a name="the-microsoft-dependency-agent"></a>Agent závislostí společnosti Microsoft

Funkce Map v Azure Monitor u virtuálních počítačů získává svá data od agenta závislostí Microsoftu. Agent závislostí spoléhá na agenta Analýzy protokolů pro jeho připojení k Log Analytics. Takže váš systém musí mít agenta Analýzy protokolů nainstalovaný a nakonfigurovaný s agentem závislostí.

Ať už povolíte Azure Monitor pro virtuální počítače pro jeden virtuální počítač Azure nebo použijete metodu nasazení ve velkém měřítku, použijte rozšíření agenta závislostí virtuálních počítače Azure pro [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) nebo [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) k instalaci agenta jako součást prostředí.

>[!NOTE]
>Následující informace popsané v této části platí také pro [řešení Mapa služeb](service-map.md).  

V hybridním prostředí můžete stáhnout a nainstalovat agenta závislostí ručně nebo pomocí automatizované metody.

Následující tabulka popisuje připojené zdroje, které funkce Map podporuje v hybridním prostředí.

| Připojený zdroj | Podporuje se | Popis |
|:--|:--|:--|
| Agenti systému Windows | Ano | Spolu s [agentem Log Analytics pro systém Windows](../../azure-monitor/platform/log-analytics-agent.md)potřebují agenti systému Windows agenta závislostí. Další informace naleznete v [tématu podporované operační systémy](#supported-operating-systems). |
| Agenti systému Linux | Ano | Spolu s [agentem Log Analytics pro Linux](../../azure-monitor/platform/log-analytics-agent.md)potřebují agenti Linuxu agenta závislostí. Další informace naleznete v [tématu podporované operační systémy](#supported-operating-systems). |
| Skupina pro správu nástroje System Center Operations Manager | Ne | |

Agenta závislostí si můžete stáhnout z těchto umístění:

| File | Operační systém | Version | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.10.3.9380 | 40763BD0A5B60707DF3F9E7BCC17D917F5CE995F2F5A4633D8B733F3BE143921  |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.10.3.9380 | BB41BB59BDD293968F02A9EF821F9639406AA1BDF1F67925DB9E00D54AA7F0B |

## <a name="role-based-access-control"></a>Řízení přístupu na základě role

Chcete-li povolit a získat přístup k funkcím v Azure Monitoru pro virtuální počítače, musíte mít roli *přispěvatele Log Analytics.* Chcete-li zobrazit výkon, stav a data mapy, musíte mít roli *čtečky monitorování* pro virtuální počítač Azure. Pracovní prostor Log Analytics musí být nakonfigurovaný pro Azure Monitor pro virtuální počítače.

Další informace o řízení přístupu k pracovnímu prostoru Log Analytics naleznete v [tématu Správa pracovních prostorů](../../azure-monitor/platform/manage-access.md).

## <a name="how-to-enable-azure-monitor-for-vms"></a>Jak povolit Azure Monitor pro virtuální počítače

Povolte Azure Monitor pro virtuální počítače pomocí jedné z metod popsaných v této tabulce:

| Stav nasazení | Metoda | Popis |
|------------------|--------|-------------|
| Škálovací sada pro jeden virtuální počítač Azure nebo virtuální počítač | [Povolení z virtuálního virtuálního mísy](vminsights-enable-single-vm.md) | Můžete povolit jeden virtuální počítač Azure výběrem **Insights** přímo z virtuálního počítače nebo škálovací sady virtuálních počítačů. |
| Více virtuálních počítačů Azure nebo škálovacísady virtuálních strojů | [Povolit prostřednictvím zásad Azure](vminsights-enable-at-scale-policy.md) | Pomocí zásad Azure a dostupných definic zásad můžete povolit více virtuálních počítačích Azure. |
| Více virtuálních počítačů Azure nebo škálovacísady virtuálních strojů | [Povolení prostřednictvím šablon Azure PowerShell nebo Azure Resource Manager](vminsights-enable-at-scale-powershell.md) | Pomocí šablon Azure PowerShell nebo Azure Resource Manager můžete povolit více virtuálních počítačů Azure nebo škálovacísady virtuálních strojů v rámci zadaného předplatného nebo skupiny prostředků. |
| Hybridní cloud | [Povolit pro hybridní prostředí](vminsights-enable-hybrid-cloud.md) | Můžete nasadit do virtuálních počítačů nebo fyzických počítačů, které jsou hostované ve vašem datovém centru nebo jiných cloudových prostředích. |

## <a name="management-packs"></a>Sady Management Pack

Když je Azure Monitor pro virtuální počítače povolený a nakonfigurovaný s pracovním prostorem Analýzy protokolů, předává se sada Management Pack všem počítačům s Windows, které se hlásí do tohoto pracovního prostoru. Pokud jste [integrovali skupinu pro správu nástroje System Center Operations Manager](../../azure-monitor/platform/om-agents.md) s pracovním prostorem Analýzy protokolů, bude sada Management Pack pro mapu služeb nasazena ze skupiny pro správu do počítačů se systémem Windows, které se hlásí skupině pro správu.  

Sada Management Pack se nazývá *Microsoft.IntelligencePacks.ApplicationDependencyMonitor*. Jeho zapsándo `%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\` složky. Zdroj dat, který používá `%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll`sada Management Pack je .

## <a name="diagnostic-and-usage-data"></a>Diagnostika a data o používání

Microsoft automaticky shromažďuje údaje o využití a výkonu prostřednictvím vašeho používání služby Azure Monitor. Společnost Microsoft používá tato data ke zlepšení kvality, zabezpečení a integrity služby. 

Chcete-li poskytnout přesné a efektivní možnosti řešení potíží, funkce Mapa obsahuje data o konfiguraci softwaru. Data poskytují informace, jako je operační systém a verze, IP adresa, název DNS a název pracovní stanice. Společnost Microsoft neshromažďuje jména, adresy ani jiné kontaktní informace.

Další informace o shromažďování a používání dat naleznete v Prohlášení o [zásadách ochrany osobních údajů služeb Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

Teď, když jste povolili monitorování pro váš virtuální počítač, informace o monitorování je k dispozici pro analýzu ve Službě Azure Monitor pro virtuální počítače.

## <a name="next-steps"></a>Další kroky

Informace o tom, jak používat funkci sledování výkonu, najdete [v tématu Zobrazení Azure Monitor pro výkon virtuálních počítačů](vminsights-performance.md). Informace o zjištěných závislostech aplikací najdete [v tématu Zobrazení programu Azure Monitor for VMS .](vminsights-maps.md)
