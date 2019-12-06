---
title: Funkce Hybrid Runbook Worker služby Azure Automation v Linuxu
description: Tento článek poskytuje informace o instalaci Hybrid Runbook Worker Azure Automation, takže můžete spouštět Runbooky v počítačích se systémem Linux v místním datovém centru nebo v cloudovém prostředí.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 06/28/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 01dcb30fca2819bc4cf85ea624de5735cc84801b
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850818"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Nasazení Hybrid Runbook Worker pro Linux

Pomocí funkce Hybrid Runbook Worker služby Azure Automation můžete spouštět Runbooky přímo v počítači, který je hostitelem role, a k prostředkům v prostředí za účelem správy těchto místních prostředků. Linux Hybrid Runbook Worker spouští Runbooky jako speciálního uživatele, který může být zvýšen na spouštění příkazů, které vyžadují zvýšení úrovně oprávnění. Sady Runbook jsou uloženy a spravovány v Azure Automation a poté dodávány do jednoho nebo více určených počítačů.

Tento článek popisuje, jak nainstalovat Hybrid Runbook Worker do počítače se systémem Linux.

## <a name="supported-linux-operating-systems"></a>Podporované operační systémy a Linux

Funkce Hybrid Runbook Worker podporuje následující distribuce:

* Amazon Linux 2012,09 až 2015,09 (x86/x64)
* CentOS Linux 5, 6 a 7 (x86/x64)
* Oracle Linux 5, 6 a 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 a 7 (x86/x64)
* Debian GNU/Linux 6, 7 a 8 (x86/x64)
* Ubuntu 12,04 LTS, 14,04 LTS a 16,04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 a 12 (x86/x64)

## <a name="installing-a-linux-hybrid-runbook-worker"></a>Instalace Hybrid Runbook Worker pro Linux

Chcete-li nainstalovat a nakonfigurovat Hybrid Runbook Worker v počítači se systémem Linux, postupujte podle jednoduchého procesu ruční instalace a konfigurace role. Je potřeba povolit řešení **Automation Hybrid Worker** v pracovním prostoru Azure Log Analytics a pak spuštěním sady příkazů zaregistrovat počítač jako pracovní proces a přidat ho do skupiny.

Minimální požadavky pro Hybrid Runbook Worker pro Linux jsou:

* Dvě jádra
* 4 GB RAM paměti
* Port 443 (odchozí)

### <a name="package-requirements"></a>Požadavky na balíčky

| **Požadovaný balíček** | **Popis** | **Minimální verze**|
|--------------------- | --------------------- | -------------------|
|Glibc |Knihovna GNU C| 2.5-12 |
|Openssl| Knihovny OpenSSL | 1,0 (podpora TLS 1,1 a TLS 1,2|
|Curl | Webový klient s kudrlinkou | 7.15.5|
|Python – ctypes | Python 2. x je povinný. |
|PAM | Pluggable Authentication Modules|
| **Volitelný balíček** | **Popis** | **Minimální verze**|
| PowerShell Core | Pokud chcete spustit PowerShellové Runbooky, je potřeba nainstalovat PowerShell, viz [instalace PowerShellu Core v systému Linux](/powershell/scripting/install/installing-powershell-core-on-linux) , kde se dozvíte, jak ho nainstalovat.  | 6.0.0 |

### <a name="installation"></a>Instalace

Než budete pokračovat, poznamenejte si Log Analytics pracovní prostor, se kterým je váš účet Automation propojený. Také si poznamenejte primární klíč pro účet Automation. Oba z Azure Portal najdete tak, že vyberete účet Automation, vybíráte **pracovní prostor** pro ID pracovního prostoru a vyberete **klíče** pro primární klíč. Informace o portech a adresách, které potřebujete pro Hybrid Runbook Worker, najdete v tématu [Konfigurace sítě](automation-hybrid-runbook-worker.md#network-planning).

1. Pomocí jedné z následujících metod povolte řešení **Automation Hybrid Worker** v Azure:

   * Přidejte řešení **Automation Hybrid Worker** do svého předplatného pomocí postupu v části [Přidání Azure monitor protokoluje řešení do vašeho pracovního prostoru](../log-analytics/log-analytics-add-solutions.md).
   * Spusťte následující rutinu:

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. Pomocí následujícího příkazu nainstalujte agenta Log Analytics pro systém Linux. Nahraďte \<ID pracovního prostoru\> a \<WorkspaceKey\> odpovídajícími hodnotami z vašeho pracovního prostoru.

   [!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. Spusťte následující příkaz a změňte hodnoty parametrů *-w*, *-k*, *-g*a *-e*. Pro parametr *-g* nahraďte hodnotu názvem Hybrid Runbook Worker skupiny, ke které se má nový hybrid Runbook Worker Linux připojit. Pokud název ve vašem účtu Automation neexistuje, vytvoří se nová skupina Hybrid Runbook Worker s tímto názvem.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. Po dokončení příkazu se na stránce **Hybrid Worker skupiny** v Azure Portal zobrazí nová skupina a počet členů. Pokud se jedná o existující skupinu, zvýší se počet členů. Skupinu můžete vybrat ze seznamu na stránce **Hybrid Worker skupiny** a vybrat dlaždici **hybridní pracovní procesy** . Na stránce **hybridní procesy** se zobrazí všichni členové skupiny v seznamu.

> [!NOTE]
> Pokud pro virtuální počítač Azure používáte rozšíření Azure Monitor virtuálních počítačů pro Linux, doporučujeme nastavit `autoUpgradeMinorVersion` na hodnotu false, protože verze automatického upgradu můžou způsobit problémy Hybrid Runbook Worker. Informace o tom, jak rozšíření upgradovat ručně, najdete v tématu nasazení rozhraní příkazového [řádku Azure CLI ](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment).

## <a name="turning-off-signature-validation"></a>Vypnutí ověřování podpisů

Ve výchozím nastavení vyžadují procesy Hybrid Runbook Worker pro Linux ověření podpisu. Pokud spustíte nepodepsaný Runbook v rámci pracovního procesu, zobrazí se chyba s oznámením, že se nepodařilo ověřit podpis. Pokud chcete vypnout ověřování podpisů, spusťte následující příkaz. Nahraďte druhý parametr ID vašeho pracovního prostoru Log Analytics.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="supported-runbook-types"></a>Podporované typy runbooků

Procesy Hybrid Runbook Worker pro Linux nepodporují úplnou sadu Runbook typu v Azure Automation.

Následující typy runbooků fungují v Hybrid Worker pro Linux:

* Python 2
* PowerShell

  > [!NOTE]
  > Runbooky PowerShellu vyžadují, aby byl na počítači se systémem Linux nainstalovaný PowerShell Core. Další informace o tom, jak ji nainstalovat, najdete v tématu [instalace PowerShellu Core v systému Linux](/powershell/scripting/install/installing-powershell-core-on-linux) .

Následující typy runbooků nefungují na Hybrid Worker pro Linux:

* Pracovní postup prostředí PowerShell
* Graphic
* Grafický pracovní postup PowerShellu

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak konfigurovat Runbooky pro automatizaci procesů v místním datovém centru nebo v jiném cloudovém prostředí, najdete v tématu [spouštění Runbooků na Hybrid Runbook Worker](automation-hrw-run-runbooks.md).
* Pokyny, jak odebrat procesy Hybrid Runbook Worker, najdete v tématu [odebrání Azure Automationch procesů Hybrid Runbook Worker](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
* Informace o řešení potíží s procesy Hybrid Runbook Worker najdete v tématu [řešení potíží s procesy Hybrid Runbook Worker pro Linux](troubleshoot/hybrid-runbook-worker.md#linux) .
