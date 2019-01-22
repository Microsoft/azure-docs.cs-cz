---
title: Funkce Hybrid Runbook Worker služby Azure Automation v Linuxu
description: Tento článek obsahuje informace o instalaci Azure Automation Hybrid Runbook Worker, takže je možné spustit runbooky v počítačích se systémem Linux v místním datovém centru nebo v cloudovém prostředí.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 06/28/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e0aaddb841687718295e09e64b23d9cefa9246fd
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54436106"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Nasazení Linuxu Hybrid Runbook Worker

Funkce Hybrid Runbook Worker služby Azure Automation můžete použít ke spuštění sady runbook přímo na počítači, který je hostitelem role a s prostředky v prostředí ke správě místních prostředků. Linux Hybrid Runbook Worker spustí jako speciální uživatel, který může být zvýšena pro spuštění příkazů, které potřebujete ke zvýšení úrovně oprávnění sady runbook. Sady Runbook jsou uloženy a spravovaná ve službě Azure Automation a pak doručí jeden nebo více počítačů určené.

Tento článek popisuje postup instalace procesu Hybrid Runbook Worker na počítači s Linuxem.

## <a name="supported-linux-operating-systems"></a>Podporované operační systémy a Linux

Funkce Hybrid Runbook Worker podporuje následující distribuce:

* Linux Amazon 2012.09 k 2015.09 (x86/x64)
* Linux centOS 5, 6 a 7 (x86/x64)
* Oracle Linux 5, 6 a 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 a 7 (x86/x64)
* Debian GNU/Linux 6, 7 a 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS a 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 a 12 (x86/x64)

## <a name="installing-a-linux-hybrid-runbook-worker"></a>Instalace Linux Hybrid Runbook Worker

Instalace a konfigurace procesu Hybrid Runbook Worker na počítači s Linuxem, postupujte podle přímočarý proces ručně nainstalovat a nakonfigurovat role. To vyžaduje povolení **Automation Hybrid Worker** řešení ve vašem pracovním prostoru Azure Log Analytics a následné spuštění sady příkazů zaregistrovat počítač jako pracovní proces a přidat do skupiny.

Jsou minimální požadavky pro Linux Hybrid Runbook Worker:

* Dvě jádra
* 4 GB paměti RAM
* Port 443 (odchozí)

### <a name="package-requirements"></a>Požadavky na balíček

| **Požadovaný balíček** | **Popis** | **Minimální verze**|
|--------------------- | --------------------- | -------------------|
|Glibc |Knihovna GNU C| 2.5-12 |
|Openssl| Knihovny OpenSSL | 1.0 (TLS 1.1 a TLS 1.2 podporují|
|Curl | cURL webového klienta | 7.15.5|
|Python ctypes | |
|PAM | Moduly PAM|
| **Volitelný balíček** | **Popis** | **Minimální verze**|
| PowerShell Core | Spouštění sad runbook Powershellu, prostředí PowerShell je potřeba nainstalovat, najdete v článku [instalace Powershellu Core v Linuxu](/powershell/scripting/setup/installing-powershell-core-on-linux) Další informace o jeho instalaci.  | 6.0.0 |

### <a name="installation"></a>Instalace

Než budete pokračovat, Všimněte si pracovní prostor Log Analytics, která váš účet Automation je propojený s. Také poznamenejte si primární klíč pro svůj účet Automation. Můžete najít i na webu Azure Portal tak, že vyberete automatizace účtu, výběru **pracovní prostor** pro ID pracovního prostoru a vyberete **klíče** pro primární klíč. Informace o portech a adresy, které potřebujete pro Hybrid Runbook Worker, naleznete v tématu [konfiguraci sítě](automation-hybrid-runbook-worker.md#network-planning).

1. Povolit **Automation Hybrid Worker** řešení v Azure pomocí jedné z následujících metod:

   * Přidat **Automation Hybrid Worker** řešení do vašeho předplatného, pomocí postupu v [řešení pro správu přidat Log Analytics pro váš pracovní prostor](../log-analytics/log-analytics-add-solutions.md).
   * Spusťte následující rutinu:

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. Nainstalujte agenta Log Analytics pro Linux spuštěním následujícího příkazu. Nahraďte \<ID pracovního prostoru\> a \<WorkspaceKey\> příslušnými hodnotami z pracovního prostoru.

  [!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)] 

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. Spusťte následující příkaz, změna hodnot pro parametry *-w*, *-k*, *-g*, a *-e*. Pro *-g* parametr, nahraďte hodnotu názvem skupiny Hybrid Runbook Worker, které se připojit nový Linux Hybrid Runbook Worker. Pokud název neexistuje ve vašem účtu Automation, nové skupiny Hybrid Runbook Worker se provádí s tímto názvem.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. Po dokončení příkazu **skupiny hybridních pracovních procesů** stránka na webu Azure Portal zobrazuje nové skupiny a počet členů. Pokud je existující skupiny, se zvýší počet členů. Můžete vybrat skupinu ze seznamu na **skupiny hybridních pracovních procesů** stránku a vybrat **hybridní pracovní procesy** dlaždici. Na **hybridní pracovní procesy** stránku, uvidíte každého člena skupiny uvedeny.

## <a name="turning-off-signature-validation"></a>Vypnout ověřování podpisu

Ve výchozím nastavení Linux Hybrid Runbook Worker vyžadovat ověření podpisu. Pokud spouštíte skript pracovního procesu runbook služby bez znaménka, zobrazí chybová zpráva "ověření podpisu se nezdařilo." Chcete-li vypnout ověřování podpisu, spusťte následující příkaz. Nahraďte druhý parametr vaše ID pracovního prostoru Log Analytics.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="supported-runbook-types"></a>Typy podporované runbooků

Linux Hybrid Runbook Worker nepodporují úplnou sadu typy runbooků ve službě Azure Automation.

Následující typy runbooků pracovat na Hybrid Worker v Linuxu:

* Python 2
* PowerShell

  > [!NOTE]
  > Powershellové runbooky vyžadují PowerShell Core na počítači s Linuxem nainstalovat. Zobrazit [instalace Powershellu Core v Linuxu](/powershell/scripting/setup/installing-powershell-core-on-linux) Další informace o jeho instalaci.

Následující typy runbooků nefungují v procesu Hybrid Worker Linux:

* Pracovní postup prostředí PowerShell
* Grafické
* Grafický Powershellový pracovní postup

## <a name="troubleshoot"></a>Řešení potíží

Další řešení potíží s procesy Hybrid Runbook Worker, najdete v článku [řešení potíží s Linuxem Hybrid Runbook Worker](troubleshoot/hybrid-runbook-worker.md#linux)

## <a name="next-steps"></a>Další postup

* Další informace o konfiguraci runbooky pro automatizaci procesů do vašeho místního datového centra nebo jiné cloudové prostředí, najdete v článku [spouštění runbooků v procesu Hybrid Runbook Worker](automation-hrw-run-runbooks.md).
* Pokyny k odebrání funkce Hybrid Runbook Worker, naleznete v tématu [odebrat Azure Automation Hybrid Runbook Worker](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).

