---
title: Povolit Azure Monitor pro virtuální počítače (Preview) – přehled | Microsoft Docs
description: Přečtěte si, jak nasadit a nakonfigurovat Azure Monitor pro virtuální počítače. Zjistěte požadavky na systém.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2019
ms.author: magoedte
ms.openlocfilehash: f395ba5d63463aa177b453d187d025a4461eff28
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "69905576"
---
# <a name="enable-azure-monitor-for-vms-preview-overview"></a>Přehled povolení Azure Monitor pro virtuální počítače (Preview)

Tento článek poskytuje přehled možností, které jsou k dispozici pro nastavení Azure Monitor pro virtuální počítače. Pomocí Azure Monitor pro virtuální počítače můžete monitorovat stav a výkon. Objevte závislosti aplikací, které běží na virtuálních počítačích Azure a službě Virtual Machine Scale Sets, místních virtuálních počítačích nebo virtuálních počítačích hostovaných v jiném cloudovém prostředí.  

Nastavení Azure Monitor pro virtuální počítače:

* Výběrem možnosti **Insights (Preview)** přímo z virtuálního počítače nebo sady škálování virtuálního počítače povolte jeden virtuální počítač Azure nebo sadu škálování virtuálního počítače.
* Pomocí Azure Policy povolte dva nebo víc virtuálních počítačů Azure a sady škálování virtuálních počítačů. Tato metoda zajišťuje, že u stávajících a nových virtuálních počítačů a sad škálování jsou požadované závislosti nainstalovány a správně nakonfigurovány. Nahlásí se nekompatibilní virtuální počítače a sady škálování, takže se můžete rozhodnout, jestli je chcete povolit, a opravit je.
* Povolit dvě nebo více virtuálních počítačů Azure nebo virtuální počítač škálovací sady v zadané předplatné nebo skupinu prostředků pomocí prostředí PowerShell.
* Povolte Azure Monitor pro virtuální počítače k monitorování virtuálních počítačů nebo fyzických počítačů hostovaných ve vaší podnikové síti nebo jiném cloudovém prostředí.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že rozumíte informace v následujících částech. 

>[!NOTE]
>Následující informace popsané v této části se vztahují také na [řešení Service map](service-map.md).  

### <a name="log-analytics"></a>Log Analytics

Azure Monitor pro virtuální počítače podporuje pracovní prostor Log Analytics v následujících oblastech:

- Západní střed USA
- Západní USA 2<sup>1</sup>
- East US
- Kanada – střed
- Velká Británie – jih
- Západní Evropa
- Jihovýchodní Asie
- Austrálie – východ<sup>1</sup>
- Austrálie – jihovýchod<sup>1</sup>

<sup>1</sup> této oblasti nepodporuje aktuálně funkci stavu služby Azure Monitor pro virtuální počítače.

>[!NOTE]
>Virtuální počítače Azure můžete nasadit z libovolné oblasti. Tyto virtuální počítače nejsou omezené na oblasti podporované Log Analytics pracovním prostorem.
>

Pokud nemáte pracovní prostor, můžete ho vytvořit pomocí jednoho z těchto prostředků:
* [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Pracovní prostor můžete také vytvořit, když povolíte monitorování pro jeden virtuální počítač Azure nebo sadu škálování virtuálního počítače v Azure Portal.

Pokud chcete nastavit scénář ve velkém měřítku, který používá Azure Policy, Azure PowerShell nebo šablony Azure Resource Manager, v pracovním prostoru Log Analytics:

* Nainstalujte řešení ServiceMap a InfrastructureInsights. Tuto instalaci můžete dokončit pomocí zadané Azure Resource Manager šablony. Nebo **na kartě Začínáme** vyberte **Konfigurovat pracovní prostor**.
* Konfigurovat pracovní prostor Log Analytics ke shromažďování čítačů výkonu.

Pokud chcete nakonfigurovat pracovní prostor pro scénář ve velkém měřítku, použijte jednu z následujících metod:

* Použijte [Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace).
* Na stránce Azure Monitor pro virtuální počítače [**pokrytí zásad**](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) vyberte **Konfigurovat pracovní prostor**. 

### <a name="supported-operating-systems"></a>Podporované operační systémy

Následující tabulka uvádí operační systémy Windows a Linux, které Azure Monitor pro virtuální počítače podporuje. Později v této části najdete úplný seznam, který podrobně popisuje hlavní a podverze operačního systému Linux a podporované verze jádra.

|Verze operačního systému |Výkon |Maps |Stav |
|-----------|------------|-----|-------|
|Windows Server. 2019 | X | X | X |
|Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |
|Windows Server 2012 R2 | X | X | X |
|Windows Server 2012 | X | X | |
|Windows Server 2008 R2 | X | X|  |
|Windows 10 1803 | X | X | |
|Windows 8.1 | X | X | |
|Windows 8 | X | X | |
|Windows 7 SP1 | X | X | |
|Red Hat Enterprise Linux (RHEL) 6, 7| X | X| X |
|Ubuntu 14,04, 16,04, 18,04 | X | X | X |
|CentOS Linux 6, 7 | X | X | X |
|SUSE Linux Enterprise Server (SLES) 12 | X | X | X |
|Debian 8, 9,4 | X<sup>1</sup> | | X |

<sup>1</sup> the výkonu funkce služby Azure Monitor pro virtuální počítače je k dispozici pouze ze služby Azure Monitor. Není k dispozici přímo v levém podokně virtuálního počítače Azure.

>[!NOTE]
>Funkce stavu Azure Monitor pro virtuální počítače nepodporuje [vnořenou virtualizaci](../../virtual-machines/windows/nested-virtualization.md) na virtuálním počítači Azure.
>

>[!NOTE]
>V operačním systému Linux:
> - Jsou podporované jen verze s výchozím a SMP jádrem Linuxu.
> - Používá se nestandardní jádra vyjde nová verze, jako například rozšíření fyzické adresy (PAE) a Xen, nejsou podporovány pro libovolnou distribuci Linuxu. Například systém s řetězec verze *2.6.16.21-0.8-xen* se nepodporuje.
> - Vlastní jádra, včetně překompilování standardních jader, nejsou podporovaná.
> - Jádro CentOSPlus je podporováno.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Verze operačního systému | Verze jádra |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |
| 7,6 | 3.10.0-957 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Verze operačního systému | Verze jádra |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

#### <a name="centosplus"></a>CentOSPlus
| Verze operačního systému | Verze jádra |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| Verze operačního systému | Verze jádra |
|:--|:--|
| Ubuntu 18.04 | jádro 4,15.\*<br>4,18 * |
| Ubuntu 16.04.3 | jádra 4.15. * |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Verze operačního systému | Verze jádra
|:--|:--|
|12 SP2 | 4.4. * |
|12 SP3 | 4.4. * |
|12 SP4 | 4.4. * |
|12 SP4 | Jádro Azure – vyladěné |

### <a name="the-microsoft-dependency-agent"></a>Agent Microsoft Dependency

Funkce map v Azure Monitor pro virtuální počítače získá svá data od agenta závislostí společnosti Microsoft. Agent závislostí závisí na agenta Log Analytics pro připojení k Log Analytics. Takže váš systém musí mít nainstalovaný agent Log Analytics a nakonfigurovaný s agentem závislostí.

Bez ohledu na to, jestli povolíte Azure Monitor pro virtuální počítače pro jeden virtuální počítač Azure, nebo použijete metodu nasazení v rámci škálování, nainstalujte agenta jako součást prostředí pomocí rozšíření Azure VM Dependency agent.

>[!NOTE]
>Následující informace popsané v této části se vztahují také na [řešení Service map](service-map.md).  

V hybridním prostředí můžete ručně stáhnout a nainstalovat agenta závislostí. Pokud jsou vaše virtuální počítače hostované mimo Azure, použijte metodu automatizovaného nasazení.

Následující tabulka popisuje připojené zdroje, které podporuje funkce mapy v hybridním prostředí.

| Připojený zdroj | Podporováno | Popis |
|:--|:--|:--|
| Agenti systému Windows | Ano | Společně s [agentem Log Analytics pro Windows](../../azure-monitor/platform/log-analytics-agent.md)potřebují agenti pro Windows agenta závislostí. Další informace najdete v tématu [podporované operační systémy](#supported-operating-systems). |
| Agenti systému Linux | Ano | Společně s [agentem Log Analytics pro Linux](../../azure-monitor/platform/log-analytics-agent.md)musí mít agenti pro Linux agenta závislostí. Další informace najdete v tématu [podporované operační systémy](#supported-operating-systems). |
| Skupina pro správu nástroje System Center Operations Manager | Ne | |

Agenta závislostí si můžete stáhnout z těchto umístění:

| File | Operační systém | Verze | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.9.1 | FCF9C1D9B20AD414051B49EE79144E595CCC411EB6D444D6D5B5A7B1874DCDEC |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.9.1 | 1CB447EF30FC042FE7499A686638F3F9B4F449692FB9D80096820F8024BE4D7C |

## <a name="role-based-access-control"></a>Řízení přístupu na základě role

K povolení a přístupu k funkcím v Azure Monitor pro virtuální počítače musíte mít roli *přispěvatel Log Analytics* . Chcete-li zobrazit data o výkonu, stavu a mapování, musíte mít roli *Čtenář monitorování* pro virtuální počítač Azure. Pracovní prostor Log Analytics musí být nakonfigurovaný pro monitorování Azure pro virtuální počítače.

Další informace o tom, jak řídit přístup k pracovnímu prostoru Log Analytics najdete v tématu [Správa pracovních prostorů](../../azure-monitor/platform/manage-access.md).

## <a name="how-to-enable-azure-monitor-for-vms-preview"></a>Postup povolení Azure Monitor pro virtuální počítače (Preview)

Povolte Azure Monitor pro virtuální počítače pomocí jedné z metod popsaných v této tabulce:

| Stav nasazení | Metoda | Popis |
|------------------|--------|-------------|
| Jeden virtuální počítač Azure nebo sada škálování virtuálního počítače | [Povolení z virtuálního počítače](vminsights-enable-single-vm.md) | Jeden virtuální počítač Azure můžete povolit tak, že vyberete **přehledy (Preview)** přímo z virtuálního počítače nebo sady škálování virtuálních počítačů. |
| Několik virtuálních počítačů Azure nebo sady škálování virtuálních počítačů | [Povolit prostřednictvím Azure Policy](vminsights-enable-at-scale-policy.md) | Pomocí Azure Policy a dostupných definic zásad můžete povolit víc virtuálních počítačů Azure. |
| Několik virtuálních počítačů Azure nebo sady škálování virtuálních počítačů | [Povolit prostřednictvím šablon Azure PowerShell nebo Azure Resource Manager](vminsights-enable-at-scale-powershell.md) | V rámci zadaného předplatného nebo skupiny prostředků můžete povolit víc virtuálních počítačů Azure nebo virtuálních počítačů pomocí šablon Azure PowerShell nebo Azure Resource Manager. |
| Hybridní cloud | [Povolit pro hybridní prostředí](vminsights-enable-hybrid-cloud.md) | Můžete ho nasadit do virtuálních počítačů nebo fyzických počítačů hostovaných ve vašem datovém centru nebo v jiných cloudových prostředích. |

## <a name="performance-counters-enabled"></a>Čítače výkonu povolena 

Azure Monitor pro virtuální počítače nakonfiguruje pracovní prostor Log Analytics pro shromažďování čítačů výkonu, které používá. V následujících tabulkách jsou uvedeny objekty a čítače, které se shromažďují každých 60 sekund.

### <a name="windows-performance-counters"></a>Čítače výkonu Windows

|Název objektu |Název čítače |
|------------|-------------|
|Logický disk |% Volného místa |
|Logický disk |Střední Doba disku/čtení |
|Logický disk |Střední Doba disku/přenos |
|Logický disk |Střední Doby disku/zápis |
|Logický disk |Bajty disku/s |
|Logický disk |Bajty čtení z disku/s |
|Logický disk |Čtení disku/s |
|Logický disk |Přenosy disku/s |
|Logický disk |Bajty zapisování na disk/s |
|Logický disk |Zápis disku/s |
|Logický disk |Volné megabajty |
|Memory (Paměť) |Počet MB k dispozici |
|Síťový adaptér |Přijaté bajty/s |
|Síťový adaptér |Odeslané bajty/s |
|Procesor |% Času procesoru |

### <a name="linux-performance-counters"></a>Čítače výkonu Linuxu

|Název objektu |Název čítače |
|------------|-------------|
|Logický Disk |% Využitého místa |
|Logický Disk |Bajty čtení z disku/s |
|Logický Disk |Čtení disku/s |
|Logický Disk |Přenosy disku/s |
|Logický Disk |Bajty zapisování na disk/s |
|Logický Disk |Zápis disku/s |
|Logický Disk |Volné megabajty |
|Logický Disk |Bajtů logického disku/s |
|Memory (Paměť) |Dostupná paměť v MB |
|Síť |Celkový počet přijatých bajtů |
|Síť |Celkový počet bajtů přenesených |
|Procesor |% Času procesoru |

## <a name="management-packs"></a>Sady Management Pack

Pokud je Azure Monitor pro virtuální počítače povolená a nakonfigurovaná pomocí pracovního prostoru Log Analytics, Management Pack se přepošle do všech počítačů se systémem Windows, které do tohoto pracovního prostoru hlásí. Pokud jste [System Center Operations Manager skupinu pro správu](../../azure-monitor/platform/om-agents.md) s pracovním prostorem Log Analytics, Service map Management Pack je nasazen ze skupiny pro správu do počítačů se systémem Windows, které se hlásí do skupiny pro správu.  

Management Pack má název *Microsoft. IntelligencePacks. ApplicationDependencyMonitor*. Je zapsán do složky%Programfiles%\Microsoft monitoring Agent\Agent\Health Service State\Management Packs \. Zdroj dat, která používá sadu management pack je % Program files%\Microsoft monitorování Agent\Agent\Health služby State\Resources\<AutoGeneratedID > \ Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="diagnostic-and-usage-data"></a>Diagnostická data a data použití

Microsoft automaticky shromažďuje data o využití a výkonu prostřednictvím používání služby Azure Monitor. Společnost Microsoft používá tato data ke zlepšení kvality, zabezpečení a integrity služby. 

Aby funkce map poskytovala přesné a efektivní možnosti odstraňování potíží, zahrnuje data týkající se konfigurace softwaru. Data obsahují informace, jako je operační systém a verze, IP adresa, název DNS a název pracovní stanice. Společnost Microsoft nebude shromažďovat jména, adresy ani jiné kontaktní údaje.

Další informace o shromažďování a používání dat najdete v článku [prohlášení o ochraně osobních údajů Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

Nyní, když jste povolili monitorování pro váš virtuální počítač, jsou informace o monitorování k dispozici pro analýzu v Azure Monitor pro virtuální počítače.

## <a name="next-steps"></a>Další postup

Další informace o použití funkce stavu, najdete v článku [zobrazení monitorování Azure pro virtuální počítače stav](vminsights-health.md). Chcete-li zobrazit závislosti zjištěných aplikací, najdete v článku [zobrazení monitorování Azure pro virtuální počítače mapu](vminsights-maps.md).
