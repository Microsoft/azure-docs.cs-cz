---
title: Funkce Hybrid Runbook Worker služby Azure Automation v Linuxu
description: Tento článek poskytuje informace o instalaci Hybrid Runbook Worker Azure Automation, takže můžete spouštět Runbooky v počítačích se systémem Linux v místním datovém centru nebo v cloudovém prostředí.
services: automation
ms.subservice: process-automation
ms.date: 03/02/2020
ms.topic: conceptual
ms.openlocfilehash: 82f6d9e56e5d5745077ef512cb3392c16b95961f
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872183"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Nasazení Hybrid Runbook Worker pro Linux

Pomocí funkce Hybrid Runbook Worker služby Azure Automation můžete spouštět Runbooky přímo v počítači, který je hostitelem role, a k prostředkům v prostředí za účelem správy těchto místních prostředků. Linux Hybrid Runbook Worker spouští Runbooky jako speciálního uživatele, který může být zvýšen na spouštění příkazů, které vyžadují zvýšení úrovně oprávnění. Sady Runbook jsou uloženy a spravovány v Azure Automation a poté dodávány do jednoho nebo více určených počítačů.

Tento článek popisuje, jak nainstalovat Hybrid Runbook Worker do počítače se systémem Linux, jak odebrat pracovní proces a jak odebrat skupinu Hybrid Runbook Worker.

## <a name="supported-linux-operating-systems"></a>Podporované operační systémy Linux

Funkce Hybrid Runbook Worker podporuje následující distribuce:

* Amazon Linux 2012,09 až 2015,09 (x86/x64)
* CentOS Linux 5, 6 a 7 (x86/x64)
* Oracle Linux 5, 6 a 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 a 7 (x86/x64)
* Debian GNU/Linux 6, 7 a 8 (x86/x64)
* Ubuntu 12,04 LTS, 14,04 LTS, 16,04 LTS a 18,04 (x86/x64)
* SUSE Linux Enterprise Server 11 a 12 (x86/x64)

## <a name="supported-runbook-types"></a>Podporované typy runbooků

Procesy Hybrid Runbook Worker pro Linux nepodporují úplnou sadu Runbook typu v Azure Automation.

Následující typy runbooků fungují v Hybrid Worker pro Linux:

* Python 2
* PowerShell

  > [!NOTE]
  > Runbooky PowerShellu vyžadují, aby byl na počítači se systémem Linux nainstalovaný PowerShell Core. Další informace o tom, jak ji nainstalovat, najdete v tématu [instalace PowerShellu Core v systému Linux](/powershell/scripting/install/installing-powershell-core-on-linux) .

Následující typy runbooků nefungují na Hybrid Worker pro Linux:

* Pracovní postup PowerShellu
* Grafický
* Grafický pracovní postup PowerShellu

## <a name="deployment-requirements"></a>Požadavky nasazení

Minimální požadavky pro Hybrid Runbook Worker pro Linux jsou:

* Dvě jádra
* 4 GB RAM paměti
* Port 443 (odchozí)

### <a name="package-requirements"></a>Požadavky na balíčky

| **Požadovaný balíček** | **Popis** | **Minimální verze**|
|--------------------- | --------------------- | -------------------|
|Glibc |Knihovna GNU C| 2.5-12 |
|Openssl| Knihovny OpenSSL | 1,0 (podporované TLS 1,1 a TLS 1,2)|
|Curl | Webový klient s kudrlinkou | 7.15.5|
|Python – ctypes | Python 2. x je povinný. |
|PAM | Pluggable Authentication Modules|
| **Volitelný balíček** | **Popis** | **Minimální verze**|
| PowerShell Core | Pokud chcete spustit PowerShellové Runbooky, je potřeba nainstalovat PowerShell, viz [instalace PowerShellu Core v systému Linux](/powershell/scripting/install/installing-powershell-core-on-linux) , kde se dozvíte, jak ho nainstalovat.  | 6.0.0 |

## <a name="install-a-linux-hybrid-runbook-worker"></a>Instalace Hybrid Runbook Worker pro Linux

Chcete-li nainstalovat a nakonfigurovat Hybrid Runbook Worker v počítači se systémem Linux, postupujte podle jednoduchého ručního procesu. Je potřeba povolit řešení Automation Hybrid Worker v pracovním prostoru Azure Log Analytics a pak spuštěním sady příkazů zaregistrovat počítač jako pracovní proces a přidat ho do skupiny.

Než budete pokračovat, poznamenejte si Log Analytics pracovní prostor, se kterým je váš účet Automation propojený. Také si poznamenejte primární klíč pro účet Automation. Oba z Azure Portal najdete tak, že vyberete účet Automation, vybíráte **pracovní prostor** pro ID pracovního prostoru a vyberete **klíče** pro primární klíč. Informace o portech a adresách, které potřebujete pro Hybrid Runbook Worker, najdete v tématu [Konfigurace sítě](automation-hybrid-runbook-worker.md#network-planning).

>[!NOTE]
> Během instalace Hybrid Worker pro Linux musí být přítomen [účet nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) s odpovídajícími oprávněními sudo. Pokud se pokusíte nainstalovat pracovní proces a účet není přítomen nebo nemáte příslušná oprávnění, instalace se nezdařila.

1. Pomocí jedné z následujících metod povolte řešení Automation Hybrid Worker v Azure:

   * Přidejte řešení Automation Hybrid Worker do svého předplatného pomocí postupu v části [přidání Azure monitor protokolů řešení do vašeho pracovního prostoru](../log-analytics/log-analytics-add-solutions.md).
   * Spusťte následující rutinu:

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. Pomocí následujícího příkazu nainstalujte agenta Log Analytics pro systém Linux. Hodnoty \<ID pracovního prostoru\> a \<WorkspaceKey\> nahraďte odpovídajícími hodnotami z vašeho pracovního prostoru.

   [!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. Spusťte následující příkaz a změňte hodnoty parametrů *-w*, *-k*, *-g*a *-e*. Pro parametr *-g* nahraďte hodnotu názvem Hybrid Runbook Worker skupiny, ke které se má nový hybrid Runbook Worker Linux připojit. Pokud název ve vašem účtu Automation neexistuje, vytvoří se nová skupina Hybrid Runbook Worker s tímto názvem.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. Po dokončení příkazu se na stránce Hybrid Worker skupiny v Azure Portal zobrazí nová skupina a počet členů. Pokud se jedná o existující skupinu, zvýší se počet členů. Skupinu můžete vybrat ze seznamu na stránce Hybrid Worker skupiny a vybrat dlaždici **hybridní pracovní procesy** . Na stránce hybridní procesy se zobrazí všichni členové skupiny v seznamu.

> [!NOTE]
> Pokud pro virtuální počítač Azure používáte rozšíření Azure Monitor virtuálních počítačů pro Linux, doporučujeme nastavit `autoUpgradeMinorVersion` na hodnotu NEPRAVDA, protože verze automatického upgradu můžou způsobit problémy Hybrid Runbook Worker. Informace o tom, jak rozšíření upgradovat ručně, najdete v tématu nasazení rozhraní příkazového [řádku Azure CLI](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment).

## <a name="turn-off-signature-validation"></a>Vypnout ověřování podpisů

Ve výchozím nastavení vyžadují procesy Hybrid Runbook Worker pro Linux ověření podpisu. Pokud spustíte nepodepsaný Runbook s pracovníkem, zobrazí se `Signature validation failed` chyba. Pokud chcete vypnout ověřování podpisů, spusťte následující příkaz. Nahraďte druhý parametr ID vašeho pracovního prostoru Log Analytics.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-linux-computer"></a><a name="remove-linux-hybrid-runbook-worker"></a>Odebrání Hybrid Runbook Worker z místního počítače se systémem Linux

K získání ID pracovního prostoru `ls /var/opt/microsoft/omsagent` můžete použít příkaz na Hybrid Runbook Worker. Vytvoří se složka s názvem ID pracovního prostoru.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Tento kód neodebere agenta Log Analytics pro Linux z počítače. Odebírá jenom funkce a konfiguraci role Hybrid Runbook Worker.

## <a name="remove-a-hybrid-worker-group"></a>Odebrání skupiny Hybrid Worker

Chcete-li odebrat Hybrid Runbook Worker skupinu počítačů se systémem Linux, použijte stejný postup jako u skupiny hybridních pracovních procesů systému Windows. Viz [Odebrání skupiny Hybrid Worker](automation-windows-hrw-install.md#remove-a-hybrid-worker-group).

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak konfigurovat Runbooky pro automatizaci procesů v místním datovém centru nebo v jiném cloudovém prostředí, najdete v tématu [spouštění Runbooků na Hybrid Runbook Worker](automation-hrw-run-runbooks.md).
* Informace o řešení potíží s procesy Hybrid Runbook Worker najdete v tématu [řešení potíží s procesy Hybrid Runbook Worker pro Linux](troubleshoot/hybrid-runbook-worker.md#linux) .
