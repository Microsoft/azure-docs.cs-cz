---
title: Připojení Azure Monitor pro virtuální počítače | Dokumentace Microsoftu
description: Tento článek popisuje, jak můžete připojit a nakonfigurovat monitorování Azure pro virtuální počítače, abyste je mohli začít, pochopení, jaký je výkon distribuované aplikace a jaké problémy se stavem se zjistily.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: magoedte
ms.openlocfilehash: 2f0568064eed556429675ffb34c84d588ac670d5
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47064352"
---
# <a name="how-to-onboard-the-azure-monitor-for-vms"></a>Jak připojit Azure monitorovat pro virtuální počítače 
Tento článek popisuje, jak nastavit monitorování Azure pro virtuální počítače, které monitoroval stav operačního systému vašich virtuálních počítačů Azure a zjišťuje a mapuje závislosti aplikací, které mohou být hostovány na ně.  

Povolení monitorování Azure pro virtuální počítače se provádí pomocí jedné z následujících metod a podrobnosti o použití každé metody jsou k dispozici později v tomto článku.  

* Jeden virtuální počítač Azure tak, že vyberete **Insights (preview)** přímo z virtuálního počítače.
* Více virtuálních počítačů Azure prostřednictvím Azure Policy, které zajistí stávající a nové virtuální počítače, vyhodnotí mít nainstalované požadované závislosti a jsou správně nakonfigurované.  Nekompatibilní virtuální počítače označené, abyste se mohli rozhodnout na co nedodržuje předpisy, jak chcete opravit závislosti.  
* Více virtuálních počítačů Azure nebo virtuálního počítače škálovací sady v zadané předplatné nebo skupinu prostředků pomocí Powershellu.

## <a name="prerequisites"></a>Požadavky
Než začnete, ujistěte se, že máte k dispozici následující, jak je popsáno v následující dílčí části.

### <a name="log-analytics"></a>Log Analytics 

Aktuálně jsou podporovány pracovnímu prostoru Log Analytics v těchto oblastech:

  - Západní střed USA  
  - USA – východ  
  - Západní Evropa  
  - Jihovýchodní Asie<sup>1</sup>  

<sup>1</sup> této oblasti pro virtuální počítače aktuálně nepodporuje funkci stavu služby Azure Monitor   

Pokud nemáte pracovní prostor, můžete si je můžete vytvořit pomocí [Azure Resource Manageru](../log-analytics/log-analytics-template-workspace-configuration.md), pomocí [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json), nebo [webu Azure portal](../log-analytics/log-analytics-quick-create-workspace.md).  

Pokud chcete řešení povolit, musíte být členem role Přispěvatel Log Analytics. Další informace o tom, jak řídit přístup k pracovnímu prostoru Log Analytics najdete v tématu [Správa pracovních prostorů](../log-analytics/log-analytics-manage-access.md).

[!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

### <a name="supported-operating-systems"></a>Podporované operační systémy

Následující tabulka uvádí operační systémy Windows a Linuxem, které jsou podporovány službou Azure Monitor pro virtuální počítače.  Úplný seznam podrobnostmi vydání hlavní a podverze operačního systému Linux a podporované verze jádra kousek dál v této části.

|Verze operačního systému |Výkon |Maps |Stav |  
|-----------|------------|-----|-------|  
|Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |  
|Windows Server 2012 R2 | X | X | |  
|Windows Server 2012 | X | X | |  
|Windows Server 2008 R2 | X | X| |  
|RHEL 7, 6| X | X| X |  
|Ubuntu 18.04, 16.04, 14.04 | X | X | X |  
|Cent operačního systému Linux 7, 6 | X | X | X |  
|SLES 12 | X | X | X |  
|Oracle Linux 7 | X<sup>1</sup> | | X |  
|Oracle Linux 6 | X | X | X |  
|Debian 9.4, 8 | X<sup>1</sup> | | X | 

<sup>1</sup> the výkonu funkce služby Azure Monitor pro virtuální počítače dostupná pouze ze služby Azure Monitor, není k dispozici při přístupu z podokna vlevo virtuálního počítače Azure přímo.  

>[!NOTE]
>Následující informace platí pro podporu operačního systému Linux:  
> - Jsou podporované jen verze s výchozím a SMP jádrem Linuxu.  
> - Verze s nestandardním jádrem, jako jsou PAE a Xen, nejsou podporované v žádné distribuci Linuxu. Například systém s vydání řetězec "2.6.16.21-0.8-xen" není podporován.  
> - Vlastní jádra, včetně opětovně zkompilovaných standardních jader, nejsou podporovaná.  
> - Jádro CentOSPlus není podporované.  


#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Verze operačního systému | Verze jádra |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7.2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Verze operačního systému | Verze jádra |
|:--|:--|
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4 | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6.8 | 2.6.32-642 |
| 6.9 | 2.6.32-696 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| Verze operačního systému | Verze jádra |
|:--|:--|
| Ubuntu 18.04 | jádra 4.15. * |
| Ubuntu 16.04.3 | jádra 4.15. * |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="oracle-enterprise-linux-6-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux 6 s nedělitelné Enterprise jádra
| Verze operačního systému | Verze jádra
|:--|:--|
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4 | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-enterprise-linux-5-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux 5 s nedělitelné Enterprise jádra

| Verze operačního systému | Verze jádra
|:--|:--|
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Verze operačního systému | Verze jádra
|:--|:--|
|12 SP2 | 4.4. * |
|12 SP3 | 4.4. * |

### <a name="hybrid-environment-connected-sources"></a>Hybridní prostředí, které jsou připojené zdroje
Azure Monitor pro mapování virtuálních počítačů získává data od agenta Microsoft Dependency. Agent závislostí závisí na agenta Log Analytics pro připojení k Log Analytics. To znamená, že systém musí mít nainstalovaný a nakonfigurovaný pomocí agenta závislostí agenta Log Analytics.  Následující tabulka popisuje připojené zdroje, které podporuje funkce mapy v hybridním prostředí.

| Připojený zdroj | Podporováno | Popis |
|:--|:--|:--|
| Agenti systému Windows | Ano | Kromě [agenta Log Analytics pro Windows](../log-analytics/log-analytics-concept-hybrid.md), agenti Windows vyžadují agent služby Microsoft Dependency. Úplný seznam verzí operačních systémů najdete v [podporovaných operačních systémech](#supported-operating-systems). |
| Agenti systému Linux | Ano | Kromě [agenta Log Analytics pro Linux](../log-analytics/log-analytics-concept-hybrid.md), vyžadují agent služby Microsoft Dependency agenti systému Linux. Úplný seznam verzí operačních systémů najdete v [podporovaných operačních systémech](#supported-operating-systems). |
| Skupina pro správu nástroje System Center Operations Manager | Ne | |  

Na Windows, Microsoft Monitoring Agent (MMA) používá System Center Operations Manager a Log Analytics ke shromažďování a odesílání dat monitorování. System Center Operations Manager a Log Analytics poskytují různé out v poli verze agenta. Tyto verze dokážou podávat hlášení nástroji System Center Operations Manager, službě Log Analytics nebo oběma.  

V Linuxu, agenta Log Analytics pro Linux shromáždí a pošle monitorování dat do služby Log Analytics.   

Pokud počítače Windows nebo Linuxem nemůžete připojit přímo ke službě, budete muset nakonfigurovat agenta Log Analytics pro připojení ke službě Log Analytics pomocí brány OMS. Další informace o tom, jak nasadit a nakonfigurovat bránu OMS najdete v tématu [bez připojení k Internetu pomocí brány OMS připojit počítače](../log-analytics/log-analytics-oms-gateway.md).  

Agent závislostí lze stáhnout z následujícího umístění.

| File | Operační systém | Verze | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.1 | 55030ABF553693D8B5112569FB2F97D7C54B66E9990014FC8CC43EFB70DE56C6 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.1 | 43C75EF0D34471A0CBCE5E396FFEEF4329C9B5517266108FA5D6131A353D29FE |

## <a name="diagnostic-and-usage-data"></a>Diagnostická data a data použití
Microsoft automaticky shromažďuje data o využití a výkonu prostřednictvím používání služby Azure Monitor. Tato data Microsoft používá k poskytování a vylepšování kvality, zabezpečení a integrity služby. Poskytnout přesné a efektivní možnosti pro odstraňování potíží, data z mapování funkce obsahuje informace o konfiguraci vašeho softwaru, jako je operační systém a verze, IP adresu, název DNS a název pracovní stanice. Společnost Microsoft neshromažďuje jména, adresy ani jiné kontaktní údaje.

Další informace o shromažďování a používání dat najdete v článku [prohlášení o ochraně osobních údajů Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="performance-counters-enabled"></a>Čítače výkonu povolena
Azure Monitor pro virtuální počítače lze konfigurovat pracovní prostor Log Analytics ke shromažďování čítačů výkonu, které jsou používané řešení.  V následující tabulce jsou uvedeny objekty a čítače nakonfigurované řešení, které byly shromážděny každých 60 sekund.

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

## <a name="enable-from-the-azure-portal"></a>Povolit z portálu Azure portal
Pokud chcete povolit monitorování virtuálního počítače Azure na webu Azure Portal, postupujte takto:

1. Na webu Azure Portal, vyberte **virtuálních počítačů**. 
2. V seznamu vyberte virtuální počítač. 
3. Na stránce virtuální počítač v **monitorování** vyberte **Insights (preview)**.
4. Na **Insights (preview)** stránce **vyzkoušet**.

    ![Povolit monitorování Azure pro virtuální počítače pro virtuální počítač](./media/monitoring-vminsights-onboard/enable-vminsights-vm-portal-01.png)

5. Na **připojování Přehled monitorování Azure** stránky, pokud máte existující Log Analytics vyberte pracovní prostor v rámci stejného předplatného, v rozevíracím seznamu.  V seznamu vybrána hodnota výchozího pracovního prostoru a umístění, který se nasazuje virtuální počítač v rámci předplatného. 

    >[!NOTE]
    >Pokud chcete vytvořit nový pracovní prostor Log Analytics pro ukládání dat monitorování z virtuálního počítače, postupujte podle pokynů v [vytvořit pracovní prostor Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md) v jednom z podporovaných oblastech uvedených výše.   

Po povolení sledování, může trvat přibližně 10 minut, než můžete zobrazit stav metriky pro virtuální počítač. 

![Povolit Azure Monitor pro monitorování zpracování nasazení virtuálních počítačů](./media/monitoring-vminsights-onboard/onboard-vminsights-vm-portal-status.png)

## <a name="enable-using-azure-policy"></a>Povolení s využitím Azure Policy
Pokud chcete řešení povolit pro více virtuálních počítačů Azure, která zajišťuje konzistentní dodržování předpisů a automatické podpory pro nové virtuální počítače zřízené, [Azure Policy](../azure-policy/azure-policy-introduction.md) se doporučuje.  Pro nové virtuální počítače pomocí zásad Azure se zásadami, které jsou k dispozici nabízí následující výhody:

* Povolení monitorování Azure pro virtuální počítače pro každý virtuální počítač v zadaném rozsahu
* Nasadit agenta Log Analytics 
* Nasazení agenta služby Dependency zjištění závislosti aplikací a zobrazit na mapě
* Auditovat, jestli je image operačního systému virtuálního počítače Azure na seznam předem definovaných v definici zásad  
* Auditovat, jestli váš virtuální počítač Azure je protokolování do pracovního prostoru, než je zadáno
* Sestav o výsledcích dodržování předpisů 
* Opravy podpory pro nekompatibilní virtuální počítače

Aktivovat pro vašeho tenanta, tento proces vyžaduje:

- Konfigurovat pracovní prostor Log Analytics pomocí postupu uvedeného zde
- Import definice iniciativy do svého tenanta (na úrovni skupiny pro správu nebo předplatného)
- Přiřaďte zásady k požadovanému oboru
- Zobrazení výsledků dodržování předpisů

### <a name="add-the-policies-and-initiative-to-your-subscription"></a>Přidání zásad a iniciativy do vašeho předplatného
Pokud chcete použít zásady, můžete použít dodaný skript Powershellu – [přidat VMInsightsPolicy.ps1](https://www.powershellgallery.com/packages/Add-VMInsightsPolicy/1.2) k dispozici z Galerie prostředí PowerShell Azure a dokončete tuto úlohu. Skript přidá zásad a iniciativy do vašeho předplatného.  Proveďte následující postup pro konfiguraci Azure Policy ve vašem předplatném. 

1. Stáhněte Poweshellový skript do vašeho místního systému souborů.

2. Přidání zásady použijte následující příkaz prostředí PowerShell ve složce. Tento skript podporuje následující volitelné parametry: 

    ```powershell
    -UseLocalPolicies [<SwitchParameter>]
      <Optional> Load the policies from a local folder instead of https://raw.githubusercontent.com/dougbrad/OnBoardVMInsights/Policy/Policy/

    -SubscriptionId <String>
      <Optional> SubscriptionId to add the Policies/Initiatives to
    -ManagementGroupId <String>
      <Optional> Management Group Id to add the Policies/Initiatives to

    -Approve [<SwitchParameter>]
      <Optional> Gives the approval to add the Policies/Initiatives without any prompt
    ```  

    >[!NOTE]
    >Poznámka: Pokud máte v plánu přiřazení iniciativy nebo zásady pro více předplatných, definice musí uloženy ve skupině pro správu, který obsahuje odběry, které přiřadíte zásady k. Takže je třeba použít parametr - ManagementGroupID.
    >
   
    Příklad bez parametrů:  `.\Add-VMInsightsPolicy.ps1`

### <a name="create-a-policy-assignment"></a>Vytvoření přiřazení zásady
Po spuštění `Add-VMInsightsPolicy.ps1` skript prostředí PowerShell, následující iniciativy a zásady jsou přidány:

* **Nasadit agenta Log Analytics pro virtuální počítače s Windows – Preview**
* **Nasadit agenta Log Analytics pro virtuální počítače s Linuxem – Preview**
* **Nasazení agenta závislosti pro virtuální počítače s Windows – Preview**
* **Nasazení agenta závislosti pro virtuální počítače s Linuxem – Preview**
* **Auditovat Log Analytics agenta nasazení – virtuální počítač Image operačního systému neuvedené – ve verzi Preview**
* **Auditovat závislost Agent nasazení – virtuální počítač Image operačního systému neuvedené – Preview**

Následující parametr iniciativy se přidá:

- **Protokolovat Analytice prostoru** (je nutné zadat ID prostředku pracovního prostoru používáte přiřazení pomocí Powershellu nebo rozhraní příkazového řádku)

    Pro virtuální počítače najít nedodržující předpisy ze zásad auditu **virtuálních počítačů není v oboru operačního systému...**  kritéria zásady nasazení obsahuje jenom virtuální počítače, které jsou nasazeny z dobře známé imagí virtuálních počítačů Azure. Pokud se operační systém virtuálního počítače podporuje nebo Ne, najdete v dokumentaci.  Pokud není, pak je potřeba duplicitní zásady nasazení a aktualizace a upravte ho tak, aby image v oboru.

Tyto samostatné volitelné zásady se přidá:

- **Virtuální počítač je nakonfigurovaný pro neodpovídající pracovní prostor Log Analytics – Preview**

    To slouží k identifikaci virtuálních počítačů už má nakonfigurovanou [rozšíření Log Analytics pro virtuální počítač](../virtual-machines/extensions/oms-windows.md), ale nejsou nakonfigurovány s jiným pracovním prostorem, než bylo zamýšleno (jak je uvedeno v přiřazení zásady). Tato akce trvá parametr pro ID pracovního prostoru.

Tato počáteční verze můžete vytvořit pouze přiřazení zásady z portálu Azure portal. Jak provést tyto kroky najdete v tématu [vytvoření přiřazení zásady z portálu Azure portal](../azure-policy/assign-policy-definition.md).

## <a name="enable-with-powershell"></a>Povolit pomocí Powershellu
Chcete-li povolit monitorování Azure pro virtuální počítače pro více virtuálních počítačů nebo virtuální počítač škálovací sady, můžete použít dodaný skript Powershellu – [instalace VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0) k dispozici z Galerie prostředí PowerShell Azure a dokončete tuto úlohu.  Tento skript se iterovat přes každých virtuálních počítačů a virtuálních počítačů škálovací sady v rámci vašeho předplatného, ve skupině prostředků s vymezeným oborem určené *ResourceGroup*, nebo na jeden virtuální počítač nebo škálovací sadu podle *název*.  Pro každý virtuální počítač nebo virtuální počítač škálovací sady skript ověřuje, jestli už je nainstalovaná rozšíření virtuálního počítače a pokud není došlo k pokusu o ho znovu nainstalujte.  V opačném případě pokračuje k instalaci rozšíření Log Analytics a virtuálních počítačů agenta závislostí.   

Tento skript vyžaduje modul Azure PowerShell verze 5.7.0 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzureRmAccount` pro vytvoření připojení k Azure.

O tomto skriptu potřebujete pomoc, můžete spustit `Get-Help` k získání seznamu argumentů podrobnosti a příklady použití.   

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and Dependency Agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or re-configures following on VM's and VM Scale Sets:
    - Log Analytics VM Extension configured to supplied Log Analytics Workspace
    - Dependency Agent VM Extension

    Can be applied to:
    - Subscription
    - Resource Group in a Subscription
    - Specific VM/VM Scale Set
    - Compliance results of a policy for a VM or VM Extension

    Script will show you list of VM's/VM Scale Sets that will apply to and let you confirm to continue.
    Use -Approve switch to run without prompting, if all required parameters are provided.

    If the extensions are already installed will not install again.
    Use -ReInstall switch if you need to for example update the workspace.

    Use -WhatIf if you would like to see what would happen in terms of installs, what workspace configured to, and status of the extension.


PARAMETERS
    -WorkspaceId <String>
        Log Analytics WorkspaceID (GUID) for the data to be sent to

    -WorkspaceKey <String>
        Log Analytics Workspace primary or secondary key

    -SubscriptionId <String>
        SubscriptionId for the VMs/VM Scale Sets
        If using PolicyAssignmentName parameter, subscription that VM's are in

    -ResourceGroup <String>
        <Optional> Resource Group to which the VMs or VM Scale Sets belong to

    -Name <String>
        <Optional> To install to a single VM/VM Scale Set

    -PolicyAssignmentName <String>
        <Optional> Take the input VM's to operate on as the Compliance results from this Assignment
        If specified will only take from this source.

    -ReInstall [<SwitchParameter>]
        <Optional> If VM/VM Scale Set is already configured for a different workspace, set this to change to the new workspace

    -TriggerVmssManualVMUpdate [<SwitchParameter>]
        <Optional> Set this flag to trigger update of VM instances in a scale set whose upgrade policy is set to Manual

    -Approve [<SwitchParameter>]
        <Optional> Gives the approval for the installation to start with no confirmation prompt for the listed VM's/VM Scale Sets

    -WorkspaceRegion <String>
        Region the Log Analytics Workspace is in
        Suported values: "East US","eastus","Southeast Asia","southeastasia","West Central US","westcentralus","West Europe","westeurope"
        For Health supported is: "East US","eastus","West Central US","westcentralus"

    -WhatIf [<SwitchParameter>]
        <Optional> See what would happen in terms of installs.
        If extension is already installed will show what workspace is currently configured, and status of the VM extension

    -Confirm [<SwitchParameter>]
        <Optional> Confirm every action

    <CommonParameters>
        This cmdlet supports the common parameters: Verbose, Debug,
        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
        OutBuffer, PipelineVariable, and OutVariable. For more information, see
        about_CommonParameters (https:/go.microsoft.com/fwlink/?LinkID=113216).

    -------------------------- EXAMPLE 1 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup>

    Install for all VM's in a Resource Group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to ReInstall extensions even if already installed, for example to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source, and to ReInstall (move to a new workspace)
```

Následující příklad ukazuje pomocí příkazů Powershellu ve složce povolit monitorování Azure pro virtuální počítače a pochopení očekávaný výstup:

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VM's or VM ScaleSets matching criteria specified

VM's or VM ScaleSets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency Agent extensions on above 2 VM's or VM Scale Sets.
VM's in a non-running state will be skipped.
Extension will not be re-installed if already installed. Use -ReInstall if desired, for example to update workspace

Confirm
Continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

db-ws-1 : Deploying DependencyAgentWindows with name DAExtension
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Deploying DependencyAgentWindows with name DAExtension
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Summary:

Already Onboarded: (0)

Succeeded: (4)
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Connected to different workspace: (0)

Not running - start VM to configure: (0)

Failed: (0)
```

## <a name="enable-for-hybrid-environment"></a>Povolit pro hybridní prostředí
Tato část vysvětluje, jak připojit virtuální počítače nebo fyzického počítače, které se hostované ve vašem datovém centru nebo jiné cloudové prostředí pro monitorování, tak monitorování Azure pro virtuální počítače.  

Azure Monitor pro virtuální počítače mapu závislostí agenta nepřenáší vlastní data a nevyžaduje žádné změny brány firewall nebo porty. Data v mapě je vždy přenášených v rámci agenta Log Analytics ve službě Azure Monitor, buď přímo nebo prostřednictvím [bránu OMS](../log-analytics/log-analytics-oms-gateway.md) Pokud zásady zabezpečení IT neumožňují počítače v síti pro připojení k Internetu.

Seznamte se s požadavky a metody nasazení pro [agenta Log Analytics Linux a Windows](../log-analytics/log-analytics-concept-hybrid.md).

Souhrnná kroky:

1. Instalace agenta Log Analytics pro Windows nebo Linux
2. Instalace agenta závislostí mapování virtuálních počítačů Azure Monitor
3. Povolte shromažďování čítačů výkonu
4. Připojení Azure Monitor pro virtuální počítače

### <a name="install-the-dependency-agent-on-windows"></a>Instalace agenta závislostí na Windows 
Agent závislostí můžete nainstalovat ručně počítačích s Windows spuštěním `InstallDependencyAgent-Windows.exe`. Pokud spustíte tento spustitelný soubor bez jakýchkoli možností, spustí Průvodce instalací, který může sledovat instalace interaktivně.  

>[!NOTE]
>K instalaci a odinstalaci tohoto agenta se vyžadují oprávnění správce.

Následující tabulka obsahuje konkrétní parametry nepodporuje instalační program pro agenta z příkazového řádku.  

| Parametr | Popis |
|:--|:--|
| /? | Vrátí seznam možností příkazového řádku. |
| /S | Proveďte bezobslužnou instalaci bez zásahu uživatele. |

Například spusťte instalační program se `/?` parametr, typ `InstallDependencyAgent-Windows.exe /?`

Soubory agenta závislostí Windows jsou nainstalované v `C:\Program Files\Microsoft Dependency Agent` ve výchozím nastavení.  Pokud se nepodaří spustit po dokončení instalace agenta závislostí, zkontrolujte protokoly podrobné informace o chybě. Adresář protokolu je `%Programfiles%\Microsoft Dependency Agent\logs`. 

### <a name="install-the-dependency-agent-on-linux"></a>Instalace agenta závislostí v Linuxu
Agent závislostí je nainstalovaný na servery s Linuxem z `InstallDependencyAgent-Linux64.bin`, skript prostředí samorozbalovací binárním souborem. Soubor můžete spustit pomocí `sh` nebo přidejte oprávnění k souboru, samotné.

>[!NOTE]
> K instalaci nebo konfiguraci tohoto agenta se vyžaduje přístup uživatele root.
> 

| Parametr | Popis |
|:--|:--|
| – Nápověda | Získá seznam parametrů příkazového řádku. |
| -s | Provede tichou instalaci bez zobrazení výzev uživateli. |
| – Zkontrolujte | Zkontroluje oprávnění a operační systém bez instalace agenta. |

Například spusťte instalační program se `-help` parametr, typ `InstallDependencyAgent-Linux64.bin -help`.

Instalace agenta závislostí Linux jako uživatel root spuštěním následujícího příkazu `sh InstallDependencyAgent-Linux64.bin`
    
Pokud agenta závislostí nespustí, zkontrolujte protokoly podrobné informace o chybě. U agentů Linuxu adresáři protokolu není `/var/opt/microsoft/dependency-agent/log`.

Soubory pro agenta závislostí jsou umístěny v následujících adresářích:

| Soubory | Umístění |
|:--|:--|
| Základní soubory | /opt/microsoft/dependency-agent |
| Soubory protokolu | /var/opt/microsoft/dependency-agent/log |
| Konfigurační soubory | /etc/opt/microsoft/dependency-agent/config |
| Spustitelné soubory služby | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Binární soubory úložiště | /var/opt/microsoft/dependency-agent/storage |

### <a name="enable-performance-counters"></a>Povolit čítače výkonu
Pokud již získat čítače výkonu požadováno řešením není nakonfigurovaný pracovní prostor Log Analytics odkazuje řešení, se musí být povolené. Můžete to provést ručně, jak je popsáno [tady](../log-analytics/log-analytics-data-sources-performance-counters.md), nebo tak, že stažení a spuštění skriptu PowerShell, který je k dispozici z [Galerie prostředí Powershell Azure](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1).
 
### <a name="onboard-azure-monitor-for-vms"></a>Připojení Azure Monitor pro virtuální počítače
Tato metoda zahrnuje šablony JSON, který určuje konfiguraci, aby zajistilo komponenty řešení do pracovního prostoru Log Analytics.  

Pokud nejste obeznámeni s konceptem nasazení prostředků pomocí šablony, naleznete v tématu:
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../azure-resource-manager/resource-group-template-deploy.md)
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md) 

Pokud se rozhodnete používat rozhraní příkazového řádku Azure, musíte nejprve nainstalovat a používat rozhraní příkazového řádku místně. Musíte používat Azure CLI verze 2.0.27 nebo novější. Zjistěte verzi, spusťte `az --version`. Pokud potřebujete instalaci nebo upgrade rozhraní příkazového řádku Azure, najdete v článku [instalace rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

#### <a name="create-and-execute-a-template"></a>Vytvoření a provedení šablony

1. Zkopírujte a vložte do souboru následující syntaxi JSON:

    ```json
    {

    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "WorkspaceName": {
            "type": "string"
        },
        "WorkspaceLocation": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-03-15-preview",
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('WorkspaceName')]",
            "location": "[parameters('WorkspaceLocation')]",
            "resources": [
                {
                    "apiVersion": "2015-11-01-preview",
                    "location": "[parameters('WorkspaceLocation')]",
                    "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    },

                    "plan": {
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "publisher": "Microsoft",
                        "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                        "promotionCode": ""
                    }
                },
                {
                    "apiVersion": "2015-11-01-preview",
                    "location": "[parameters('WorkspaceLocation')]",
                    "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    },
                    "plan": {
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "publisher": "Microsoft",
                        "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
                        "promotionCode": ""
                    }
                }
            ]
        }
    ]
    ```

2. Uložte soubor jako **installsolutionsforvminsights.json** do místní složky.
3. Upravte hodnoty **WorkspaceName**, **ResourceGroupName**, a **WorkspaceLocation**.  Hodnota pro **WorkspaceName** je je úplné ID prostředku pracovního prostoru Log Analytics, která zahrnuje název pracovního prostoru a hodnotu **WorkspaceLocation** je oblast, která je definována pracovní prostor v.
4. Jste připraveni nasadit tuto šablonu pomocí následujícího příkazu Powershellu:

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    Změna konfigurace může trvat několik minut. Když se dokončí, zobrazí se zpráva, která je podobný následujícímu a zahrnuje výsledek:

    ```powershell
    provisioningState       : Succeeded
    ```
Po povolení sledování, může trvat přibližně 10 minut, než můžete zobrazit stav a metriky pro počítač hybridní. 

## <a name="next-steps"></a>Další postup

Pomocí monitorování povoleno pro váš virtuální počítač, tyto informace jsou dostupné pro analýzy a monitorování Azure pro virtuální počítače.  Zjistěte, jak použít funkci stavu, najdete v článku [zobrazení monitorování Azure pro virtuální počítače stavu](monitoring-vminsights-health.md), nebo chcete-li zobrazit závislosti zjištěných aplikací, najdete v článku [zobrazení monitorování Azure pro virtuální počítače mapu](monitoring-vminsights-maps.md).  