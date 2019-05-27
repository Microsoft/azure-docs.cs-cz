---
title: Povolit monitorování Azure pro virtuální počítače (preview) | Dokumentace Microsoftu
description: Tento článek popisuje, jak nasadit a nakonfigurovat Azure Monitor pro virtuální počítače a požadované systémové požadavky.
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
ms.date: 05/22/2019
ms.author: magoedte
ms.openlocfilehash: 76d18b6a942ed9b8c6871b0ff7cbc1c83917ada4
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66130469"
---
# <a name="enable-azure-monitor-for-vms-preview-overview"></a>Povolit monitorování Azure pro virtuální počítače (preview)

Tento článek obsahuje přehled dostupné možnosti a nastavení monitorování virtuálních počítačů určených k monitorování stavu, výkonu a zjištění závislosti aplikace běžící na virtuálních počítačích Azure a škálovací sady virtuálních počítačů Azure, místní virtuální počítače nebo virtuální počítače hostované v jiné cloudové prostředí.  

Azure Monitor povolíte pro virtuální počítače pomocí jedné z následujících metod:

* Povolit jednoho virtuálního počítače Azure nebo virtuálního počítače škálovací sady výběrem **Insights (preview)** přímo od virtuálního počítače nebo virtuálního počítače škálovací sady.
* Povolit dvě nebo více virtuálních počítačů Azure a virtuální počítače škálovací sady s využitím zásad Azure. Prostřednictvím této metody jsou požadované závislosti stávající i nové virtuální počítače a škálovací sady nainstalované a správně nakonfigurovaný. Nekompatibilní virtuální počítače a škálovací sady jsou hlášeny, abyste se mohli rozhodnout, jestli je chcete povolit, a způsob jejich řešení.
* Povolit dvě nebo více virtuálních počítačů Azure nebo virtuální počítač škálovací sady v zadané předplatné nebo skupinu prostředků pomocí prostředí PowerShell.
* Povolte monitorování virtuálních počítačů nebo fyzických počítačů, které jsou hostované ve vaší podnikové síti nebo jiné cloudové prostředí.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že rozumíte informace v následujících částech.

### <a name="log-analytics"></a>Log Analytics

Azure Monitor pro virtuální počítače podporuje pracovní prostor Log Analytics v těchto oblastech:

- Západní střed USA
- USA – východ
- Canada Central<sup>1</sup>
- Velká Británie – jih<sup>1</sup>
- Západní Evropa
- Jihovýchodní Asie<sup>1</sup>

<sup>1</sup> této oblasti nepodporuje aktuálně funkci stavu služby Azure Monitor pro virtuální počítače.

>[!NOTE]
>Azure virtual machines se dají nasadit z libovolné oblasti a nejsou omezené na podporovaných oblastí pro pracovní prostor Log Analytics.
>

Pokud nemáte pracovní prostor, můžete vytvořit jednu s jedním z následujících metod:
* [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Pokud aktivujete monitorování u jednoho virtuálního počítače Azure nebo virtuálního počítače škálovací sady na webu Azure Portal, můžete vytvořit pracovní prostor během tohoto procesu.

Pro scénář ve velkém měřítku pomocí zásad Azure, Azure Powershellu nebo šablony Azure Resource Manageru musí váš pracovní prostor Log Analytics nejprve nakonfigurované následující:

* Nainstalujte řešení ServiceMap a InfrastructureInsights. Pomocí šablony Azure Resource Manageru, která je k dispozici nebo se může dokončení této instalace **konfigurace pracovního prostoru** možnost najít na **Začínáme** kartu.
* Konfigurovat pracovní prostor Log Analytics ke shromažďování čítačů výkonu.

Proveďte konfiguraci pracovního prostoru pro scénář ve velkém měřítku, můžete nakonfigurovat pomocí jedné z následujících metod:

* [Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace)
* Z **konfigurace pracovního prostoru** možnost pro monitorování Azure pro virtuální počítače [pokrytí zásadami](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) stránky

### <a name="supported-operating-systems"></a>Podporované operační systémy

Následující tabulka uvádí operační systémy Windows a Linuxem, které jsou podporovány službou Azure Monitor pro virtuální počítače. Úplný seznam, který podrobně popisuje verze operačního systému Linux hlavní a dílčí a podporované verze jádra je v této části dozvíte později.

|Verze operačního systému |Výkon |Maps |Zdraví |
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

<sup>1</sup> the výkonu funkce služby Azure Monitor pro virtuální počítače je k dispozici pouze ze služby Azure Monitor. Není k dispozici, když se dostanete přímo z levého podokna virtuálního počítače Azure.

>[!NOTE]
>Následující informace platí pro podporu operačního systému Linux:
> - Jsou podporované jen verze s výchozím a SMP jádrem Linuxu.
> - Používá se nestandardní jádra vyjde nová verze, jako například rozšíření fyzické adresy (PAE) a Xen, nejsou podporovány pro libovolnou distribuci Linuxu. Například systém s řetězec verze *2.6.16.21-0.8-xen* se nepodporuje.
> - Vlastní jádrech, včetně překompilování standardní jádra, nejsou podporovány.
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

### <a name="centosplus"></a>CentOSPlus
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

Azure Monitor pro funkci mapování virtuálních počítačů získává data od agenta Microsoft Dependency. Agent závislostí závisí na agenta Log Analytics pro připojení k Log Analytics. Proto musí mít váš systém agenta Log Analytics, instalaci a konfiguraci agenta závislostí.

Povolit monitorování Azure pro virtuální počítače pro jeden virtuální počítač Azure nebo používat metodu nasazení ve velkém měřítku, budete muset použít rozšíření agenta závislosti virtuálních počítačů Azure jako součást možností instalace agenta.

V hybridním prostředí, můžete stáhnout a nainstalovat agenta závislostí v některém ze dvou způsobů: ručně nebo pomocí metody automatizované nasazení pro virtuální počítače, které jsou hostované mimo Azure.

Následující tabulka popisuje připojené zdroje, které podporuje funkce mapy v hybridním prostředí.

| Připojený zdroj | Podporováno | Popis |
|:--|:--|:--|
| Agenti systému Windows | Ano | Kromě [agenta Log Analytics pro Windows](../../azure-monitor/platform/log-analytics-agent.md), agenti Windows vyžadují agent služby Microsoft Dependency. Úplný seznam verzí operačního systému najdete v tématu [podporované operační systémy](#supported-operating-systems). |
| Agenti systému Linux | Ano | Kromě [agenta Log Analytics pro Linux](../../azure-monitor/platform/log-analytics-agent.md), vyžadují agent služby Microsoft Dependency agenti systému Linux. Úplný seznam verzí operačního systému najdete v tématu [podporované operační systémy](#supported-operating-systems). |
| Skupina pro správu nástroje System Center Operations Manager | Ne | |

Agent závislostí si můžete stáhnout z následujícího umístění:

| File | Operační systém | Verze | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.8.1 | 622C99924385CBF539988D759BCFDC9146BB157E7D577C997CDD2674E27E08DD |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.8.1 | 3037934A5D3FB7911D5840A9744AE9F980F87F620A7F7B407F05E276FE7AE4A8 |

## <a name="role-based-access-control"></a>Řízení přístupu na základě role

Pokud chcete povolit a přístup k funkcím ve službě Azure Monitor pro virtuální počítače, je potřeba přiřadit přístup následující role:

- Ho Pokud chcete povolit, musíte mít *Přispěvatel Log Analytics* role.

- Zobrazení výkonu, stavu, a mapování dat, musíte mít *Čtenář monitorování* role virtuálního počítače Azure. Pracovní prostor Log Analytics musí být nakonfigurovaný pro monitorování Azure pro virtuální počítače.

Další informace o tom, jak řídit přístup k pracovnímu prostoru Log Analytics najdete v tématu [Správa pracovních prostorů](../../azure-monitor/platform/manage-access.md).

## <a name="how-to-enable-azure-monitor-for-vms-preview"></a>Jak povolit Azure Monitor pro virtuální počítače (preview)

Azure Monitor je možné povolit pro virtuální počítače pomocí jedné z následujících metod popsaných v následující tabulce.

| Stav nasazení | Metoda | Popis |
|------------------|--------|-------------|
| Jeden virtuální počítač Azure nebo virtuálního počítače škálovací sady | [Přímo z virtuálního počítače](vminsights-enable-single-vm.md) | Jeden virtuální počítač Azure můžete povolit výběrem **Insights (preview)** přímo od virtuálního počítače nebo virtuálního počítače škálovací sady. |
| Více virtuálních počítačů Azure nebo škálovací sady virtuálních počítačů | [Azure Policy](vminsights-enable-at-scale-policy.md) | Můžete povolit více virtuálních počítačů Azure pomocí Azure Policy a dostupné definice zásad. |
| Více virtuálních počítačů Azure nebo škálovací sady virtuálních počítačů | [Šablony Azure Powershellu nebo Azure Resource Manageru](vminsights-enable-at-scale-powershell.md) | Více škálovacích sadách virtuálních počítačů Azure nebo na virtuálním počítači můžete povolit napříč zadané předplatné nebo skupinu prostředků pomocí šablon Azure Resource Manageru nebo Azure Powershellu. |
| Hybridní cloud | [Povolit pro hybridní prostředí](vminsights-enable-hybrid-cloud.md) | Můžete nasadit do virtuálních počítačů nebo fyzických počítačů, které jsou hostované ve vašem datovém centru nebo jiných cloudových prostředích. |

## <a name="performance-counters-enabled"></a>Čítače výkonu povolena

Azure Monitor pro virtuální počítače lze konfigurovat pracovní prostor Log Analytics ke shromažďování čítačů výkonu, které používá. V následující tabulce jsou uvedeny objekty a čítače, které byly shromážděny každých 60 sekund.

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

Microsoft automaticky shromažďuje data o využití a výkonu prostřednictvím používání služby Azure Monitor. Tato data Microsoft používá k poskytování a vylepšování kvality, zabezpečení a integrity služby. Poskytnout přesné a efektivní možnosti pro odstraňování potíží, data z mapování funkce obsahuje informace o konfiguraci vašeho softwaru, jako je operační systém a verze, IP adresu, název DNS a název pracovní stanice. Společnost Microsoft nebude shromažďovat jména, adresy ani jiné kontaktní údaje.

Další informace o shromažďování a používání dat najdete v článku [prohlášení o ochraně osobních údajů Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Další postup

Teď, když pro váš virtuální počítač je zapnuté monitorování, tyto informace jsou dostupné pro analýzy a monitorování Azure pro virtuální počítače. Další informace o použití funkce stavu, najdete v článku [zobrazení monitorování Azure pro virtuální počítače stav](vminsights-health.md). Chcete-li zobrazit závislosti zjištěných aplikací, najdete v článku [zobrazení monitorování Azure pro virtuální počítače mapu](vminsights-maps.md).
