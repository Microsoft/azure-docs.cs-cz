---
title: Povolit monitorování Azure pro virtuální počítače (preview) | Dokumentace Microsoftu
description: Zjistěte, jak nasadit a nakonfigurovat monitorování Azure pro virtuální počítače. Přečtěte si požadavky na systém.
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
ms.date: 06/19/2019
ms.author: magoedte
ms.openlocfilehash: 2d4e49b4f7c1aa244b59ef17716c90369a0d3339
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273377"
---
# <a name="enable-azure-monitor-for-vms-preview-overview"></a>Povolit monitorování Azure pro virtuální počítače (preview)

Tento článek obsahuje přehled dostupné možnosti a nastavení monitorování Azure pro virtuální počítače. Použití Azure monitoru pro virtuální počítače k monitorování stavu a výkonu. Zjištění závislosti aplikací, které běží na Azure virtual machines (VM) a nastaví, místní virtuální počítače nebo virtuální počítače hostované v jiné prostředí cloudové škálování virtuálního počítače.  

Nastavení monitorování Azure pro virtuální počítače:

* Povolit jeden virtuální počítač Azure nebo virtuální počítač škálovací sady výběrem **Insights (preview)** přímo od virtuálního počítače nebo virtuálního počítače škálovací sady.
* Povolit dvě nebo více virtuálních počítačů Azure a virtuální počítače škálovací sady s využitím zásad Azure. Tato metoda zajišťuje, že na stávající i nové virtuální počítače a škálovací sady, požadované závislosti nainstalované a správně nakonfigurovaný. Nekompatibilní virtuální počítače a škálovací sady jsou hlášeny, abyste se mohli rozhodnout, jestli je chcete povolit a se je opravit.
* Povolit dvě nebo více virtuálních počítačů Azure nebo virtuální počítač škálovací sady v zadané předplatné nebo skupinu prostředků pomocí prostředí PowerShell.
* Povolte monitorování Azure pro virtuální počítače k monitorování virtuálních počítačů nebo fyzických počítačů, které jsou hostované ve vaší podnikové síti nebo jiné cloudové prostředí.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že rozumíte informace v následujících částech.

### <a name="log-analytics"></a>Log Analytics

Azure Monitor pro virtuální počítače podporuje pracovní prostor Log Analytics v těchto oblastech:

- Západní střed USA
- USA – západ 2<sup>1</sup>
- USA – východ
- Kanada – střed
- Velká Británie – jih
- Západní Evropa
- Jihovýchodní Asie

<sup>1</sup> této oblasti nepodporuje aktuálně funkci stavu služby Azure Monitor pro virtuální počítače.

>[!NOTE]
>Můžete nasadit virtuální počítače Azure z libovolné oblasti. Tyto virtuální počítače nejsou omezená na oblasti, které podporuje v pracovním prostoru Log Analytics.
>

Pokud nemáte pracovní prostor, můžete vytvořit pomocí jednoho z těchto prostředků:
* [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Můžete také vytvořit pracovní prostor, zatímco jste povolení sledování pro jeden virtuální počítač Azure nebo virtuálního počítače škálovací sady na webu Azure Portal.

Nastavení scénáři ve velkém měřítku, která používá šablony Azure Policy, Azure Powershellu nebo Azure Resource Manageru ve vašem pracovním prostoru Log Analytics:

* Nainstalujte řešení ServiceMap a InfrastructureInsights. Dokončení této instalace s použitím zadané šablony Azure Resource Manageru. Nebo **Začínáme** kartu, vyberte možnost **konfigurace pracovního prostoru**.
* Konfigurovat pracovní prostor Log Analytics ke shromažďování čítačů výkonu.

Proveďte konfiguraci pracovního prostoru pro scénář ve velkém měřítku, použijte jednu z následujících metod:

* Použití [prostředí Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace).
* Pro monitorování Azure pro virtuální počítače [ **pokrytí zásadami** ](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) stránce **konfigurace pracovního prostoru**. 

### <a name="supported-operating-systems"></a>Podporované operační systémy

Následující tabulka uvádí operační systémy Windows a Linuxem, které podporuje Azure Monitor pro virtuální počítače. Později v této části najdete úplný seznam, který popisuje hlavní a podverze operačního systému Linux verze a podporované verze jádra.

|Verze operačního systému |Výkon |Maps |Stav |
|-----------|------------|-----|-------|
|Windows Server 2019 | X | X | X |
|Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |
|Windows Server 2012 R2 | X | X | X |
|Windows Server 2012 | X | X | |
|Windows Server 2008 R2 | X | X| |
|Red Hat Enterprise Linux (RHEL) 6, 7| X | X| X |
|Ubuntu 14.04, 16.04, 18.04 | X | X | X |
|CentOS Linux 6, 7 | X | X | X |
|SUSE Linux Enterprise Server (SLES) 11, 12 | X | X | X |
|Debian 8, 9.4 | X<sup>1</sup> | | X |

<sup>1</sup> the výkonu funkce služby Azure Monitor pro virtuální počítače je k dispozici pouze ze služby Azure Monitor. Není k dispozici přímo z levého podokna virtuálního počítače Azure.

>[!NOTE]
>Stav funkce služby Azure Monitor pro virtuální počítače nepodporuje [vnořená virtualizace](../../virtual-machines/windows/nested-virtualization.md) ve Virtuálním počítači Azure.
>

>[!NOTE]
>V operačním systému Linux:
> - Jsou podporované jen verze s výchozím a SMP jádrem Linuxu.
> - Používá se nestandardní jádra vyjde nová verze, jako například rozšíření fyzické adresy (PAE) a Xen, nejsou podporovány pro libovolnou distribuci Linuxu. Například systém s řetězec verze *2.6.16.21-0.8-xen* se nepodporuje.
> - Vlastní jádrech, včetně předkompilací standardní jádra, nejsou podporovány.
> - Je podporován CentOSPlus jádra.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Verze operačního systému | Verze jádra |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

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
| Ubuntu 18.04 | jádra 4.15. * |
| Ubuntu 16.04.3 | jádra 4.15. * |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="suse-linux-11-enterprise-server"></a>SUSE Linux 11 Enterprise Server

| Verze operačního systému | Verze jádra
|:--|:--|
|11 SP4 | 3.0.* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Verze operačního systému | Verze jádra
|:--|:--|
|12 SP2 | 4.4. * |
|12 SP3 | 4.4. * |

### <a name="the-microsoft-dependency-agent"></a>Agent Microsoft Dependency

Funkce mapy ve službě Azure Monitor pro virtuální počítače získává data od agenta Microsoft Dependency. Agent závislostí závisí na agenta Log Analytics pro připojení k Log Analytics. Takže vašeho systému, musí mít nainstalovaný a nakonfigurovaný pomocí agenta závislostí agenta Log Analytics.

Povolit monitorování Azure pro virtuální počítače pro jeden virtuální počítač Azure nebo používat metodu nasazení ve velkém měřítku, instalace agenta v rámci prostředí pomocí rozšíření agenta závislosti virtuálních počítačů Azure.

V hybridním prostředí můžete stáhnout a ručně nainstalovat agenta závislostí. Pokud vaše virtuální počítače hostují mimo Azure, použijte metodu automatického nasazení.

Následující tabulka popisuje připojené zdroje, které podporuje funkce mapy v hybridním prostředí.

| Připojený zdroj | Podporováno | Popis |
|:--|:--|:--|
| Agenti systému Windows | Ano | Spolu s [agenta Log Analytics pro Windows](../../azure-monitor/platform/log-analytics-agent.md), agenti Windows musí agenta závislostí. Další informace najdete v tématu [podporované operační systémy](#supported-operating-systems). |
| Agenti systému Linux | Ano | Spolu s [agenta Log Analytics pro Linux](../../azure-monitor/platform/log-analytics-agent.md), agenty Linux potřebujete agenta závislostí. Další informace najdete v tématu [podporované operační systémy](#supported-operating-systems). |
| Skupina pro správu nástroje System Center Operations Manager | Ne | |

Agent závislostí si můžete stáhnout z těchto míst:

| File | Operační systém | Verze | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.8.1 | 622C99924385CBF539988D759BCFDC9146BB157E7D577C997CDD2674E27E08DD |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.8.1 | 3037934A5D3FB7911D5840A9744AE9F980F87F620A7F7B407F05E276FE7AE4A8 |

## <a name="role-based-access-control"></a>Řízení přístupu na základě role

Pokud chcete povolit a přístup k funkcím ve službě Azure Monitor pro virtuální počítače, musíte mít *Přispěvatel Log Analytics* role. Zobrazení výkonu, stavu, a mapování dat, musíte mít *Čtenář monitorování* role virtuálního počítače Azure. Pracovní prostor Log Analytics musí být nakonfigurovaný pro monitorování Azure pro virtuální počítače.

Další informace o tom, jak řídit přístup k pracovnímu prostoru Log Analytics najdete v tématu [Správa pracovních prostorů](../../azure-monitor/platform/manage-access.md).

## <a name="how-to-enable-azure-monitor-for-vms-preview"></a>Jak povolit Azure Monitor pro virtuální počítače (preview)

Povolte monitorování Azure pro virtuální počítače pomocí jedné z metod popsaných v této tabulce:

| Stav nasazení | Metoda | Popis |
|------------------|--------|-------------|
| Jeden virtuální počítač Azure nebo virtuálního počítače škálovací sady | [Povolit z virtuálního počítače](vminsights-enable-single-vm.md) | Jeden virtuální počítač Azure můžete povolit výběrem **Insights (preview)** přímo od virtuálního počítače nebo virtuálního počítače škálovací sady. |
| Více virtuálních počítačů Azure nebo škálovací sady virtuálních počítačů | [Povolit prostřednictvím Azure Policy](vminsights-enable-at-scale-policy.md) | Můžete povolit více virtuálních počítačů Azure pomocí Azure Policy a dostupné definice zásad. |
| Více virtuálních počítačů Azure nebo škálovací sady virtuálních počítačů | [Povolit prostřednictvím šablon Azure Resource Manageru nebo Azure Powershellu](vminsights-enable-at-scale-powershell.md) | Můžete povolit více škálovacích sadách virtuálních počítačů Azure nebo na virtuálním počítači v zadané předplatné nebo skupinu prostředků pomocí šablon Azure Resource Manageru nebo Azure Powershellu. |
| Hybridní cloud | [Povolit pro hybridní prostředí](vminsights-enable-hybrid-cloud.md) | Můžete nasadit do virtuálních počítačů nebo fyzických počítačů, které jsou hostované ve vašem datovém centru nebo jiných cloudových prostředích. |

## <a name="performance-counters-enabled"></a>Čítače výkonu povolena 

Azure Monitor pro virtuální počítače lze konfigurovat pracovní prostor Log Analytics ke shromažďování čítačů výkonu, které používá. Následující tabulky uvádějí objekty a čítače, které byly shromážděny každých 60 sekund.

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

## <a name="diagnostic-and-usage-data"></a>Diagnostická data a data použití

Microsoft automaticky shromažďuje data o využití a výkonu prostřednictvím používání služby Azure Monitor. Společnost Microsoft používá tato data ke zlepšení kvality, zabezpečení a integrity služby. 

Pokud chcete poskytnout přesné a efektivní možnosti pro odstraňování potíží, funkci Mapa obsahuje údaje o konfiguraci vašeho softwaru. Data poskytuje informace, jako je operační systém a verze, IP adresu, název DNS a název pracovní stanice. Společnost Microsoft nebude shromažďovat jména, adresy ani jiné kontaktní údaje.

Další informace o shromažďování a používání dat najdete v článku [prohlášení o ochraně osobních údajů Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

Teď, když jste povolili monitorování pro váš virtuální počítač, informace o sledování je k dispozici pro analýzy ve službě Azure Monitor pro virtuální počítače.

## <a name="next-steps"></a>Další postup

Další informace o použití funkce stavu, najdete v článku [zobrazení monitorování Azure pro virtuální počítače stav](vminsights-health.md). Chcete-li zobrazit závislosti zjištěných aplikací, najdete v článku [zobrazení monitorování Azure pro virtuální počítače mapu](vminsights-maps.md).
