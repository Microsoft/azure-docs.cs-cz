---
title: Řešení potíží s agentem Windows Update v Azure Automation
description: Tento článek popisuje, jak řešit problémy s agentem Windows Update během Update Management a řešit z něj problémy.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/16/2020
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: 92020313fccf1b8be0add58a7bafab62b5daa4d5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86187128"
---
# <a name="troubleshoot-windows-update-agent-issues"></a>Řešení problémů s agentem Windows Update

Může to mít spoustu důvodů, proč se Váš počítač během nasazování Update Management nezobrazuje jako připravený (v pořádku). Chcete-li zjistit základní problém, můžete ověřit stav agenta Windows Hybrid Runbook Worker. Níže jsou uvedené tři stavy připravenosti pro počítač:

* Připraveno: Hybrid Runbook Worker se nasadí a poslední se zobrazila před méně než jednou hodinou.
* Odpojeno: Hybrid Runbook Worker se nasazuje a naposledy se zobrazilo před jednou hodinou.
* Nenakonfigurováno: Hybrid Runbook Worker se nenajde nebo nedokončilo nasazení.

> [!NOTE]
> Mezi zobrazením Azure Portal a aktuálním stavem počítače může být mírné zpoždění.

Tento článek popisuje, jak spustit Poradce při potížích pro počítače Azure z Azure Portal a počítačů mimo Azure ve [scénáři offline](#troubleshoot-offline). 

> [!NOTE]
> Skript Poradce při potížích teď obsahuje kontrolu Windows Server Update Services (WSUS) a pro klíče pro autostažení a instalaci. 

## <a name="start-the-troubleshooter"></a>Spustit Poradce při potížích

U počítačů Azure můžete spustit stránku Poradce při potížích s agentem aktualizace kliknutím na odkaz **Poradce při potížích** pod sloupcem **připravenosti agenta aktualizace** na portálu. V případě počítačů mimo Azure se v tomto článku zobrazí odkaz. Řešení potíží s počítačem mimo Azure najdete v tématu věnovaném [řešení potíží offline](#troubleshoot-offline) .

![Snímek obrazovky se seznamem Update Management virtuálních počítačů](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> Pokud chcete zjistit stav Hybrid Runbook Worker, musí být virtuální počítač spuštěný. Pokud virtuální počítač není spuštěný, zobrazí se tlačítko **Spustit virtuální počítač** .

Na stránce Poradce při potížích s agentem aktualizace vyberte **Spustit kontroly** a spusťte Poradce při potížích. Poradce při potížích používá [příkaz Spustit](../../virtual-machines/windows/run-command.md) ke spuštění skriptu na počítači, aby se ověřily závislosti. Po dokončení Poradce při potížích vrátí výsledek kontrol.

![Snímek obrazovky se stránkou aktualizace agenta pro řešení potíží](../media/update-agent-issues/troubleshoot-page.png)

Po dokončení se na stránce zobrazí výsledky. V sekcích kontroly se zobrazují informace o tom, co je zahrnuté v každé kontrole.

![Snímek obrazovky s kontrolami aktualizace agenta Poradce při potížích](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Kontroly požadovaných součástí

### <a name="operating-system"></a>Operační systém

Při kontrole operačního systému se ověří, zda Hybrid Runbook Worker používá jeden z operačních systémů zobrazených v následující tabulce.

|Operační systém  |Poznámky  |
|---------|---------|
|Windows Server 2012 a novější |Vyžaduje se .NET Framework 4,6 nebo novější. ([Stáhněte si .NET Framework](/dotnet/framework/install/guide-for-developers).)<br/> Je vyžadován Windows PowerShell 5,1.  ([Stáhnout Windows Management Framework 5,1](https://www.microsoft.com/download/details.aspx?id=54616).)        |

### <a name="net-462"></a>.NET 4.6.2

.NET Framework zkontroluje, jestli je v systému nainstalovaná [.NET Framework 4.6.2](https://www.microsoft.com/en-us/download/details.aspx?id=53345) nebo novější.

### <a name="wmf-51"></a>WMF 5.1

Kontroly WMF ověří, zda má systém požadovanou verzi rozhraní Windows Management Framework (WMF), což je rozhraní [Windows Management framework 5,1](https://www.microsoft.com/download/details.aspx?id=54616).

### <a name="tls-12"></a>TLS 1.2

Tato kontrolu určuje, jestli k šifrování komunikace používáte protokol TLS 1,2. Tato platforma už nepodporuje protokol TLS 1,0. K komunikaci s Update Management použijte protokol TLS 1,2.

## <a name="connectivity-checks"></a>Kontroly připojení

### <a name="registration-endpoint"></a>Registrační koncový bod

Tato kontrolu určuje, zda může agent správně komunikovat se službou agenta.

Konfigurace proxy serveru a brány firewall musí umožňovat, aby agent Hybrid Runbook Worker komunikoval s koncovým bodem registrace. Seznam adres a portů, které se mají otevřít, najdete v tématu [Plánování sítě](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>Koncový bod operací

Tato kontrolu určuje, zda může agent správně komunikovat se službou data runtime úlohy.

Konfigurace proxy serveru a brány firewall musí umožňovat, aby agent Hybrid Runbook Worker komunikoval se službou data runtime úlohy. Seznam adres a portů, které se mají otevřít, najdete v tématu [Plánování sítě](../automation-hybrid-runbook-worker.md#network-planning).

## <a name="vm-service-health-checks"></a>Kontroly stavu služby virtuálních počítačů

### <a name="monitoring-agent-service-status"></a>Stav služby agenta monitorování

Tato kontrolu určuje, zda je na počítači spuštěn Agent Log Analytics pro systém Windows ( `healthservice` ). Další informace o řešení potíží se službou najdete v tématu [agent Log Analytics pro Windows není spuštěný](hybrid-runbook-worker.md#mma-not-running).

Chcete-li přeinstalovat agenta Log Analytics pro systém Windows, přečtěte si téma [instalace agenta pro systém Windows](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows).

### <a name="monitoring-agent-service-events"></a>Události služby Monitoring Agent

Tato kontrolu určuje, jestli se v protokolu Azure Operations Manager v počítači během posledních 24 hodin objevily nějaké události 4502.

Další informace o této události naleznete [v události 4502 v protokolu Operations Manager](hybrid-runbook-worker.md#event-4502) pro tuto událost.

## <a name="access-permissions-checks"></a>Kontroly přístupových oprávnění

> [!NOTE]
> Poradce při potížích aktuálně nesměruje provoz prostřednictvím proxy server, pokud je nakonfigurovaný.

### <a name="crypto-folder-access"></a>Přístup ke složce kryptografické složky

Při kontrole přístupu ke složce kryptografických souborů se určuje, jestli má účet místního systému přístup k C:\ProgramData\Microsoft\Crypto\RSA..

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Řešení potíží offline

Poradce při potížích s Hybrid Runbook Worker můžete použít offline spuštěním skriptu místně. Získejte následující skript z Galerie prostředí PowerShell: [Troubleshooting-WindowsUpdateAgentRegistration](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration). Pokud chcete skript spustit, musíte mít nainstalovanou verzi WMF 4,0 nebo novější. Pokud si chcete stáhnout nejnovější verzi PowerShellu, přečtěte si téma [instalace různých verzí PowerShellu](/powershell/scripting/install/installing-powershell).

Výstup tohoto skriptu vypadá jako v následujícím příkladu:

```output
RuleId                      : OperatingSystemCheck
RuleGroupId                 : prerequisites
RuleName                    : Operating System
RuleGroupName               : Prerequisite Checks
RuleDescription             : The Windows Operating system must be version 6.2.9200 (Windows Server 2012) or higher
CheckResult                 : Passed
CheckResultMessage          : Operating System version is supported
CheckResultMessageId        : OperatingSystemCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : DotNetFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : .NET Framework 4.5+
RuleGroupName               : Prerequisite Checks
RuleDescription             : .NET Framework version 4.5 or higher is required
CheckResult                 : Passed
CheckResultMessage          : .NET Framework version 4.5+ is found
CheckResultMessageId        : DotNetFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : WindowsManagementFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : WMF 5.1
RuleGroupName               : Prerequisite Checks
RuleDescription             : Windows Management Framework version 4.0 or higher is required (version 5.1 or higher is preferable)
CheckResult                 : Passed
CheckResultMessage          : Detected Windows Management Framework version: 5.1.17763.1
CheckResultMessageId        : WindowsManagementFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {5.1.17763.1}

RuleId                      : AutomationAgentServiceConnectivityCheck1
RuleGroupId                 : connectivity
RuleName                    : Registration endpoint
RuleGroupName               : connectivity
RuleDescription             :
CheckResult                 : Failed
CheckResultMessage          : Unable to find Workspace registration information in registry
CheckResultMessageId        : AutomationAgentServiceConnectivityCheck1.Failed.NoRegistrationFound
CheckResultMessageArguments : {}

RuleId                      : AutomationJobRuntimeDataServiceConnectivityCheck
RuleGroupId                 : connectivity
RuleName                    : Operations endpoint
RuleGroupName               : connectivity
RuleDescription             : Proxy and firewall configuration must allow Automation Hybrid Worker agent to communicate with eus2-jobruntimedata-prod-su1.azure-automation.net
CheckResult                 : Passed
CheckResultMessage          : TCP Test for eus2-jobruntimedata-prod-su1.azure-automation.net (port 443) succeeded
CheckResultMessageId        : AutomationJobRuntimeDataServiceConnectivityCheck.Passed
CheckResultMessageArguments : {eus2-jobruntimedata-prod-su1.azure-automation.net}

RuleId                      : MonitoringAgentServiceRunningCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service status
RuleGroupName               : VM Service Health Checks
RuleDescription             : HealthService must be running on the machine
CheckResult                 : Failed
CheckResultMessage          : Log Analytics for Windows service (HealthService) is not running
CheckResultMessageId        : MonitoringAgentServiceRunningCheck.Failed
CheckResultMessageArguments : {Log Analytics agent for Windows, HealthService}

RuleId                      : MonitoringAgentServiceEventsCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service events
RuleGroupName               : VM Service Health Checks
RuleDescription             : Event Log must not have event 4502 logged in the past 24 hours
CheckResult                 : Failed
CheckResultMessage          : Log Analytics agent for Windows service Event Log (Operations Manager) does not exist on the machine
CheckResultMessageId        : MonitoringAgentServiceEventsCheck.Failed.NoLog
CheckResultMessageArguments : {Log Analytics agent for Windows, Operations Manager, 4502}

RuleId                      : CryptoRsaMachineKeysFolderAccessCheck
RuleGroupId                 : permissions
RuleName                    : Crypto RSA MachineKeys Folder Access
RuleGroupName               : Access Permission Checks
RuleDescription             : SYSTEM account must have WRITE and MODIFY access to 'C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys'
CheckResult                 : Passed
CheckResultMessage          : Have permissions to access C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys
CheckResultMessageId        : CryptoRsaMachineKeysFolderAccessCheck.Passed
CheckResultMessageArguments : {C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys}

RuleId                      : TlsVersionCheck
RuleGroupId                 : prerequisites
RuleName                    : TLS 1.2
RuleGroupName               : Prerequisite Checks
RuleDescription             : Client and Server connections must support TLS 1.2
CheckResult                 : Passed
CheckResultMessage          : TLS 1.2 is enabled by default on the Operating System.
CheckResultMessageId        : TlsVersionCheck.Passed.EnabledByDefault
CheckResultMessageArguments : {}
```

## <a name="next-steps"></a>Další kroky

[Řešení potíží s Hybrid Runbook Worker](hybrid-runbook-worker.md).
