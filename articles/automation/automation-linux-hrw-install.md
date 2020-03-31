---
title: Funkce Hybrid Runbook Worker služby Azure Automation v Linuxu
description: Tento článek obsahuje informace o instalaci hybridního pracovníka runbooku Azure Automation, abyste mohli spouštět runbooky v počítačích se systémem Linux v místním datovém centru nebo cloudovém prostředí.
services: automation
ms.subservice: process-automation
ms.date: 03/02/2020
ms.topic: conceptual
ms.openlocfilehash: 2579748d9c68512e51fe46ec70084c30d06953bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278763"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Nasazení hybridního pracovníka runbooku pro Linux

Pomocí funkce Hybridní runbook worker azure automation můžete spustit runbooky přímo v počítači, který je hostitelem role a proti prostředkům v prostředí ke správě těchto místních prostředků. Linux Hybrid Runbook Worker spustí runbookjako speciální uživatel, který může být zvýšena pro spouštění příkazů, které potřebují zvýšení oprávnění. Sady Runbook se ukládají a spravují v Azure Automation a pak se doručují do jednoho nebo více určených počítačů.

Tento článek popisuje, jak nainstalovat hybridní runbook worker na počítači s Linuxem.

## <a name="supported-linux-operating-systems"></a>Podporované operační systémy Linux

Funkce Hybridní pracovní prostředí runbooku podporuje následující distribuce:

* Amazon Linux 2012.09 až 2015.09 (x86/x64)
* CentOS Linux 5, 6 a 7 (x86/x64)
* Oracle Linux 5, 6 a 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 a 7 (x86/x64)
* Debian GNU/Linux 6, 7 a 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS a 18.04 (x86/x64)
* SUSE Linux Enterprise Server 11 a 12 (x86/x64)

## <a name="installing-a-linux-hybrid-runbook-worker"></a>Instalace hybridního pracovníka runbooku pro Linux

Chcete-li nainstalovat a nakonfigurovat hybridní pracovník runbooku v počítači s Linuxem, postupujte podle jednoduchého procesu ruční instalace a konfigurace role. Vyžaduje povolení **řešení Automation Hybrid Worker** v pracovním prostoru Azure Log Analytics a pak spuštění sady příkazů k registraci počítače jako pracovníka a jeho přidání do skupiny.

Minimální požadavky pro linuxového hybridního pracovníka runbooku jsou:

* Dvě jádra
* 4 GB RAM paměti
* Port 443 (odchozí)

### <a name="package-requirements"></a>Požadavky na balíček

| **Povinný balíček** | **Popis** | **Minimální verze**|
|--------------------- | --------------------- | -------------------|
|Glibc |Knihovna GNU C| 2.5-12 |
|Openssl| Knihovny OpenSSL | 1.0 (TLS 1.1 a TLS 1.2 jsou podporovány|
|Curl | webový klient cURL | 7.15.5|
|Python-ctypes | Je vyžadován python 2.x |
|PAM | Pluggable Authentication Modules|
| **Volitelný balíček** | **Popis** | **Minimální verze**|
| PowerShell Core | Pokud chcete spustit runbooky prostředí PowerShell, je potřeba nainstalovat PowerShell, přečtěte si informace o tom, jak ho nainstalovat, v [tématu Instalace powershellového jádra na Linuxu.](/powershell/scripting/install/installing-powershell-core-on-linux)  | 6.0.0 |

### <a name="installation"></a>Instalace

Než budete pokračovat, poznamenejte si pracovní prostor Log Analytics, se kterým je váš účet Automation propojen. Všimněte si také primárního klíče pro váš účet Automation. Oba typy můžete najít na webu Azure Portal tak, že vyberete svůj účet Automation, vyberete **pracovní prostor** pro ID pracovního prostoru a vyberete **klíče** pro primární klíč. Informace o portech a adresách, které potřebujete pro pracovníka hybridního runbooku, naleznete [v tématu Konfigurace sítě](automation-hybrid-runbook-worker.md#network-planning).

1. Povolte řešení **Automation Hybrid Worker** v Azure pomocí jedné z následujících metod:

   * Přidejte řešení **Automation Hybrid Worker** do svého předplatného pomocí postupu na webu Přidat řešení [protokolů Azure Monitor do pracovního prostoru](../log-analytics/log-analytics-add-solutions.md).
   * Spusťte následující rutinu:

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. Nainstalujte agenta Log Analytics pro Linux spuštěním následujícího příkazu. Nahraďte \<\> ID \<pracovního\> prostoru a klíč pracovního prostoru příslušnými hodnotami z pracovního prostoru.

   [!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. Spusťte následující příkaz a měňte hodnoty parametrů *-w*, *-k*, *-g*a *-e*. Pro parametr *-g* nahraďte hodnotu názvem skupiny Hybrid Runbook Worker, ke které by se měl připojit nový pracovník hybridního runbooku Pro Linux. Pokud název ve vašem účtu Automation neexistuje, bude s tímto názvem provedena nová skupina Hybridní pracovní kniha runbooku.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. Po dokončení příkazu se na stránce **Hybridní pracovní skupiny** na portálu Azure zobrazí nová skupina a počet členů. Pokud se jedná o existující skupinu, počet členů se zintážceje. Skupinu můžete vybrat ze seznamu na stránce **Hybridní pracovní skupiny** a vybrat dlaždici **Hybridní pracovníci.** Na stránce **Hybridní pracovníci** se zobrazí každý člen skupiny uvedený.

> [!NOTE]
> Pokud používáte rozšíření virtuálního počítače Azure Monitor pro Linux `autoUpgradeMinorVersion` pro virtuální počítač Azure doporučujeme nastavit na false, protože verze automatické inovace může způsobit problémy hybrid Runbook Worker. Informace o ručním upgradu rozšíření najdete v [tématu nasazení příkazového příkazu k webu Azure ](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment).

## <a name="turning-off-signature-validation"></a>Vypnutí ověřování podpisu

Ve výchozím nastavení vyžadují pracovníci hybridního runbooku Linuxu ověření podpisu. Pokud spustíte nepodepsaný runbook proti pracovníkovi, zobrazí se chyba "Ověření podpisu se nezdařilo.". Chcete-li ověření podpisu vypnout, spusťte následující příkaz. Nahraďte druhý parametr ID pracovního prostoru analýzy protokolů.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="supported-runbook-types"></a>Podporované typy runbooků

Linux hybridní runbook pracovníků nepodporují úplnou sadu typů runbooků v Azure Automation.

Následující typy runbooků fungují na hybridním pracovním hardwaru Linuxu:

* Python 2
* PowerShell

  > [!NOTE]
  > Sady Runbook powershellu vyžadují, aby se powershellové jádro nainstalovalo do počítače s Linuxem. Informace o jeho instalaci najdete [v tématu Instalace powershellového jádra na Linuxu.](/powershell/scripting/install/installing-powershell-core-on-linux)

Následující typy runbooků nefungují na hybridním pracovním hardwaru Linuxu:

* Pracovní postup PowerShellu
* Grafický
* Grafický pracovní postup prostředí PowerShell

## <a name="next-steps"></a>Další kroky

* Informace o konfiguraci sad Runbook pro automatizaci procesů v místním datovém centru nebo jiném cloudovém prostředí najdete v [tématu Spuštění runbooků na hybridním pracovníkovi sady Runbook](automation-hrw-run-runbooks.md).
* Pokyny k odebrání hybridních pracovníků runbooku najdete v tématu [Odebrání hybridních pracovníků runbooku Azure Automation](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
* Informace o řešení potíží s hybridními pracovníky runbooku najdete [v tématu Poradce při potížích s hybridními pracovníky runbooku pro Linux](troubleshoot/hybrid-runbook-worker.md#linux)
