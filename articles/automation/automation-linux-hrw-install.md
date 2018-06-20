---
title: Funkce Hybrid Runbook Worker služby Azure Automation v Linuxu
description: Tento článek obsahuje informace o instalaci Azure Automation Hybrid Runbook Worker, sady runbook můžete spustit v počítačích se systémem Linux v místní datové centrum nebo cloudového prostředí.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8507cf99ea22b24aa3026565cb7c4139e4c3742d
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268119"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Nasazení Linux Hybrid Runbook Worker

Ke spuštění sady runbook přímo na počítači, který je hostitelem role a s prostředky v prostředí pro správu těchto místních prostředků, můžete použít funkci hybridní pracovní proces Runbooku automatizace Azure. Linux Hybrid Runbook Worker spustí sady runbook jako speciální uživatel, který může být zvýšena pro spouštění příkazů, které je třeba zvýšení oprávnění. Sady Runbook jsou uložené a spravované ve službě Azure Automation a pak doručí jeden nebo více určenými počítači.

Tento článek popisuje postup instalace hybridní pracovní proces Runbooku na počítač s Linuxem.

## <a name="supported-linux-operating-systems"></a>Podporované operační systémy Linux

Funkce hybridní pracovní proces Runbooku podporuje následující distribuce:

* Linux Amazon 2012.09 k 2015.09 (x86/x64)
* CentOS Linux 5, 6 a 7 (x86/x64)
* Oracle Linux 5, 6 a 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 a 7 (x86/x64)
* Debian GNU/Linux 6, 7 a 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS a 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 a 12 (x86/x64)

## <a name="installing-a-linux-hybrid-runbook-worker"></a>Instalace Linux Hybrid Runbook Worker

K instalaci a konfiguraci Hybrid Runbook Worker ve vašem počítači Linux, postupujte podle jednoduchý proces ručně nainstalovat a nakonfigurovat role. Vyžaduje povolení **Automation Hybrid Worker** řešení v pracovní prostor analýzy protokolů Azure a pak spustit sadu příkazů k registraci počítače jako pracovní proces a přidat do skupiny.

Jsou minimální požadavky pro Linux Hybrid Runbook Worker:

* Dvě jádra
* 4 GB paměti RAM
* Port 443 (odchozí)

### <a name="package-requirements"></a>Požadavky na balíček

| **Požadovaný balíček** | **Popis** | **Minimální verze**|
|--------------------- | --------------------- | -------------------|
|Glibc |Knihovny jazyka C GNU| 2.5-12 |
|OpenSSL| Knihovny OpenSSL | 0.9.8E nebo 1.0|
|Curl | cURL webového klienta | 7.15.5|
|Python ctypes | |
|PAM | Modulů PAM|
| **Volitelné balíčku** | **Popis** | **Minimální verze**|
| Základní prostředí PowerShell | Ke spuštění sad runbook prostředí PowerShell, najdete v prostředí PowerShell musí být nainstalovaný, [instalace jádra prostředí PowerShell v systému Linux](/powershell/scripting/setup/installing-powershell-core-on-linux) se dozvíte, jak ji nainstalovat.  | 6.0.0 |

### <a name="installation"></a>Instalace

Než budete pokračovat, Všimněte si pracovní prostor analýzy protokolů, propojeném účtu Automation. Všimněte si také primární klíč pro svůj účet Automation. Můžete najít z portálu Azure tak, že vyberete vaší automatizace i účtu, výběru **prostoru** pro dané ID pracovního prostoru a výběr **klíče** pro primární klíč. Informace o porty a adresy, které potřebujete pro hybridní pracovní proces Runbooku, najdete v části [konfigurace vaší sítě](automation-hybrid-runbook-worker.md#network-planning).

1. Povolit **Automation Hybrid Worker** řešení v Azure pomocí jedné z následujících metod:

   * Přidat **Automation Hybrid Worker** řešení k předplatnému pomocí postupu v [řešení pro správu přidat analýzy protokolů do pracovního prostoru](../log-analytics/log-analytics-add-solutions.md).
   * Spusťte následující rutinu:

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. Nainstalujte agenta OMS pro Linux tak, že spustíte následující příkaz. Nahraďte \<WorkspaceID\> a \<WorkspaceKey\> s příslušnými hodnotami z pracovního prostoru.

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. Spusťte následující příkaz, změna hodnot pro parametry *-w*, *-k*, *-g*, a *-e*. Pro *-g* parametr, nahraďte název skupiny hybridní pracovní proces Runbooku, které se musí připojit nový Linux Hybrid Runbook Worker hodnotu. Pokud název neexistuje v účtu Automation, nové skupiny hybridní pracovní proces Runbooku se provádí s tímto názvem.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. Po dokončení příkazu **skupinám Hybrid Worker** stránky na webu Azure portal zobrazuje nové skupiny a počet členů. Pokud je existující skupiny, se zvýší počet členů. Můžete vybrat skupinu ze seznamu na **skupinám Hybrid Worker** a vyberte **hybridní pracovní procesy** dlaždici. Na **hybridní pracovní procesy** stránky, uvidíte každého člena skupiny uvedené.

## <a name="turning-off-signature-validation"></a>Vypnutí ověřování podpisu

Ve výchozím nastavení Linux hybridní pracovní procesy Runbooku vyžadovat ověření podpisu. Pokud spustíte nepodepsaný runbook proti pracovní, zobrazí chybu, která říká "podpis ověření se nezdařilo." Chcete-li vypnout ověřování podpisu, spusťte následující příkaz. Druhý parametr nahraďte vaše ID pracovního prostoru analýzy protokolů

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="supported-runbook-types"></a>Typy podporované runbooků

Linux hybridní pracovní procesy Runbooku nepodporují kompletní typy runbooků ve službě Azure Automation.

Následující typy runbook pracovat na hybridní pracovní proces Linux:

* Python 2
* PowerShell

  > [!NOTE]
  > Powershellové runbooky vyžadují základní prostředí PowerShell k instalaci na počítač s Linuxem. V tématu [instalace jádra prostředí PowerShell v systému Linux](/powershell/scripting/setup/installing-powershell-core-on-linux) se dozvíte, jak ji nainstalovat.

Linux hybridního pracovního procesu nepodporují následující typy sady runbook:

* Pracovní postup prostředí PowerShell
* Grafické
* Pracovní postup grafické prostředí PowerShell

## <a name="troubleshooting"></a>Řešení potíží

Linux hybridní pracovní proces Runbook závisí na OMS agenta pro Linux komunikovat s vaším účtem Automation registraci pracovního procesu, přijímat úlohy sady runbook a zprávy o stavu. Pokud pracovní proces registrace selže, tady jsou některé možné příčiny chyby.

### <a name="the-oms-agent-for-linux-isnt-running"></a>Není spuštěn Agent OMS pro Linux

Pokud není spuštěn Agent OMS pro Linux, Linux Hybrid Runbook Worker nemůže komunikovat s Azure Automation. Ověřte, zda je spuštěna agenta zadáním příkazu `ps -ef | grep python`. 

Měli byste vidět výstup podobný následujícímu (Python zpracovává s **nxautomation** uživatelský účet). Pokud řešení správy aktualizací nebo Azure Automation není povolena, žádný z těchto procesů bude spuštěna.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

Následující procesy jsou spuštěny pro Linux Hybrid Runbook Worker. Všechny se nachází v `/var/opt/microsoft/omsagent/state/automationworker/` adresáře.

* **OMS.conf**: Toto je správce pracovního procesu. Je spuštěno přímo z požadovaného stavu konfigurace (DSC).

* **Worker.conf**: Toto je automaticky zaregistrován hybridní pracovní proces. Je spuštěno pomocí Správce pracovního procesu. Tento proces slouží pomocí správy aktualizací a je pro uživatele transparentní. Tento proces není k dispozici pouze v případě, že je na počítači povolená řešení pro správu aktualizací.

* **diy/Worker.conf**: Jedná se o DIY hybridní pracovní proces. DIY hybridní pracovní proces se používá ke spouštění sad runbook uživatele na hybridní pracovní proces Runbooku. Se liší od automaticky zaregistrovat hybridní pracovní proces pouze v tom, že používá jinou konfiguraci. Tento proces je přítomen pouze v případě, že je povolená řešení služby Azure Automation a je zaregistrován DIY Linux hybridní pracovní proces.

Pokud agenta OMS pro Linux neběží, spusťte následující příkaz pro spuštění služby: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="the-specified-class-doesnt-exist"></a>Pro zadanou třídu neexistuje.

Pokud se zobrazí chyba "Zadaná třída neexistuje" v `/var/opt/microsoft/omsconfig/omsconfig.log`, OMS agenta pro Linux je nutné aktualizovat. Spusťte následující příkaz, který znovu nainstalujte agenta OMS:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

Další pokyny o tom, jak vyřešit problémy s správy aktualizací najdete v tématu [správy aktualizací: řešení potíží s](automation-update-management.md#troubleshooting).

## <a name="next-steps"></a>Další postup

* Naučte se konfigurovat své sady runbook k automatizaci procesů ve své místní datové centrum nebo jiné cloudové prostředí, najdete v tématu [spuštění sad runbook na hybridní pracovní proces Runbooku](automation-hrw-run-runbooks.md).
* Pokyny o tom, jak odebrat procesy Hybrid Runbook Worker najdete v tématu [odebrat Azure automatizace procesů Hybrid Runbook Worker](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
