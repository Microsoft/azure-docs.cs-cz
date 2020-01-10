---
title: Diagnostika Windows Hybrid Runbook Worker – Azure Update Management
description: Naučte se řešit problémy s Azure Automation Hybrid Runbook Worker ve Windows, které podporují Update Management.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: bfc2c4a660afa4341a676fc79ab447c8cb86d5e1
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769808"
---
# <a name="understand-and-resolve-windows-hybrid-runbook-worker-health-in-update-management"></a>Pochopení a řešení stavu Windows Hybrid Runbook Worker v Update Management

Příčinou může být to, že váš počítač není v Update Management **připravený** . V Update Management můžete zjistit příčinu problému tím, že zkontrolujete stav agenta Hybrid Runbook Worker. Tento článek popisuje, jak spustit Poradce při potížích pro počítače Azure z Azure Portal a počítačů mimo Azure v [offline scénáři](#troubleshoot-offline).

Následující seznam uvádí tři stavy připravenosti, na kterých počítač může být:

* **Připraveno** – Hybrid Runbook Worker je nasazená a naposledy se zobrazila před méně než 1 hodinou.
* **Odpojeno** – Hybrid Runbook Worker je nasazená a naposledy se zobrazila před 1 hodinou.
* **Nenakonfigurováno** – Hybrid Runbook Worker se nenašlo nebo nedokončilo registraci.

> [!NOTE]
> Mezi zobrazením Azure Portal a aktuálním stavem počítače může dojít k mírnému zpoždění.

## <a name="start-the-troubleshooter"></a>Spustit Poradce při potížích

V případě počítačů Azure se kliknutím na odkaz **Poradce při potížích** ve sloupci **připravenost agenta aktualizace** na portálu spustí stránka **Poradce při potížích s agentem** aktualizace. V případě počítačů mimo Azure se v tomto článku zobrazí odkaz. Přečtěte si [pokyny offline](#troubleshoot-offline) k řešení potíží s počítačem mimo Azure.

![Aktualizovat seznam virtuálních počítačů pro správu](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> Pokud chcete zjistit stav Hybrid Runbook Worker, musí být virtuální počítač spuštěný. Pokud virtuální počítač není spuštěný, zobrazí se tlačítko **Spustit virtuální počítač** .

Na stránce **Poradce při potížích s agentem aktualizace** vyberte **Spustit kontroly** a spusťte Poradce při potížích. Poradce při potížích používá [příkaz run](../../virtual-machines/windows/run-command.md) ke spuštění skriptu na počítači za účelem ověření závislostí. Po dokončení Poradce při potížích vrátí výsledek kontrol.

![Řešení potíží s aktualizací stránky agenta](../media/update-agent-issues/troubleshoot-page.png)

Po dokončení se na stránce zobrazí výsledky. V sekcích kontroly se zobrazují informace o tom, co je zahrnuté v každé kontrole.

![Řešení potíží s aktualizacemi kontrol agenta](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Kontroly splnění podmínek

### <a name="operating-system"></a>Operační systém

Při kontrole operačního systému se ověří, zda Hybrid Runbook Worker používá jeden z následujících operačních systémů:

|Operační systém  |Poznámky  |
|---------|---------|
|Windows Server 2008 R2 RTM, Windows Server 2008 | Podporuje pouze posouzení aktualizací.         |
|Windows Server 2008 R2 SP1 a novější |Vyžaduje se .NET Framework 4,6 nebo novější. ([Stáhnout .NET Framework](/dotnet/framework/install/guide-for-developers))<br/> Je vyžadován Windows PowerShell 5,1.  ([Stáhnout Windows Management Framework 5,1](https://www.microsoft.com/download/details.aspx?id=54616))        |

### <a name="net-462"></a>.NET 4.6.2

.NET Framework zkontroluje, jestli je v systému minimálně nainstalovaná [.NET Framework 4.6.2](https://www.microsoft.com/en-us/download/details.aspx?id=53345) .

### <a name="wmf-51"></a>WMF 5.1

Po kontrole WMF se ověří, že systém má požadovanou verzi rozhraní Windows Management Framework (WMF) – [Windows Management framework 5,1](https://www.microsoft.com/download/details.aspx?id=54616).

### <a name="tls-12"></a>TLS 1.2

Tato kontrolu určuje, jestli k šifrování komunikace používáte protokol TLS 1,2. Tato platforma už nepodporuje protokol TLS 1,0. Doporučujeme, aby klienti používali protokol TLS 1,2 ke komunikaci s Update Management.

## <a name="connectivity-checks"></a>Kontrola připojení k

### <a name="registration-endpoint"></a>Koncový bod registrace

Tato kontrolu určuje, zda může agent správně komunikovat se službou agenta.

Konfigurace proxy serveru a brány firewall musí umožňovat Hybrid Runbook Worker agenta pro komunikaci s koncovým bodem registrace. Seznam adres a portů, které se mají otevřít, najdete v tématu [Plánování sítě pro hybridní pracovní procesy](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>Operace koncového bodu

Tato kontrolu určuje, zda může agent správně komunikovat se službou data runtime úlohy.

Konfigurace proxy serveru a brány firewall musí umožňovat Hybrid Runbook Worker agenta ke komunikaci se službou Data modulu Runtime úlohy. Seznam adres a portů, které se mají otevřít, najdete v tématu [Plánování sítě pro hybridní pracovní procesy](../automation-hybrid-runbook-worker.md#network-planning).

## <a name="vm-service-health-checks"></a>Kontroly stavu služby virtuálních počítačů

### <a name="monitoring-agent-service-status"></a>Stav služby agenta monitorování

Tato kontrolu určuje, zda je na počítači spuštěna `HealthService`Microsoft Monitoring Agent.

Další informace o řešení potíží se službou najdete v tématu [Microsoft Monitoring Agent neběží](hybrid-runbook-worker.md#mma-not-running).

Chcete-li přeinstalovat Microsoft Monitoring Agent, přečtěte si téma [instalace a konfigurace Microsoft Monitoring Agent](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows).

### <a name="monitoring-agent-service-events"></a>Události služby Monitoring Agent

Tato kontrolu určuje, jestli se v protokolu Azure Operations Manager na počítači během posledních 24 hodin objevily nějaké události `4502`.

Další informace o této události najdete v [Průvodci odstraňováním potíží](hybrid-runbook-worker.md#event-4502) pro tuto událost.

## <a name="access-permissions-checks"></a>Kontroly přístupových oprávnění

### <a name="machinekeys-folder-access"></a>Přístup ke složce MachineKeys

Při kontrole přístupu ke složce kryptografických souborů se určuje, jestli má účet místního systému přístup k C:\ProgramData\Microsoft\Crypto\RSA.

## <a name="troubleshoot-offline"></a>Řešení potíží offline

Poradce při potížích s Hybrid Runbook Worker můžete použít offline spuštěním skriptu místně. Skript můžete získat na stránce Galerie prostředí PowerShell [řešení potíží – WindowsUpdateAgentRegistration](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration). Pro spuštění skriptu musíte mít nainstalované WMF 4,0 nebo novější. Pokud si chcete stáhnout nejnovější verzi PowerShellu, přečtěte si téma [instalace různých verzí PowerShellu](https://docs.microsoft.com/powershell/scripting/install/installing-powershell).

Výstup tohoto skriptu vypadá jako v následujícím příkladu:

```output
RuleId                      : OperatingSystemCheck
RuleGroupId                 : prerequisites
RuleName                    : Operating System
RuleGroupName               : Prerequisite Checks
RuleDescription             : The Windows Operating system must be version 6.1.7601 (Windows Server 2008 R2 SP1) or higher
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
CheckResultMessage          : Microsoft Monitoring Agent service (HealthService) is not running
CheckResultMessageId        : MonitoringAgentServiceRunningCheck.Failed
CheckResultMessageArguments : {Microsoft Monitoring Agent, HealthService}

RuleId                      : MonitoringAgentServiceEventsCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service events
RuleGroupName               : VM Service Health Checks
RuleDescription             : Event Log must not have event 4502 logged in the past 24 hours
CheckResult                 : Failed
CheckResultMessage          : Microsoft Monitoring Agent service Event Log (Operations Manager) does not exist on the machine
CheckResultMessageId        : MonitoringAgentServiceEventsCheck.Failed.NoLog
CheckResultMessageArguments : {Microsoft Monitoring Agent, Operations Manager, 4502}

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

Pokud chcete řešit více problémů s procesy Hybrid Runbook Worker, přečtěte si téma [řešení potíží s Hybrid Runbook Worker](hybrid-runbook-worker.md).
