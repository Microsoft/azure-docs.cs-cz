---
title: Vysvětlení výsledky kontroly agenta Windows ve službě Azure Update Management
description: Informace o řešení potíží s agentem Update Management.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: automation
ms.component: update-management
manager: carmonm
ms.openlocfilehash: 68bac54de4f6a9f16f54032b10031bcf7222c676
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52969307"
---
# <a name="understand-the-windows-agent-check-results-in-update-management"></a>Vysvětlení výsledky kontroly agenta Windows v Update Management

Existuje mnoho důvodů, proč nemusí zobrazit počítač Azure **připravené** stav ve službě Azure Update Management. V Update Management můžete zkontrolovat stav agenta Hybrid Worker, chcete-li zjistit příčinu problému. Tento článek popisuje, jak spustit Poradce při potížích s správu aktualizací z webu Azure portal a v případě offline scénářů.

## <a name="start-the-troubleshooter"></a>Spustit Poradce při potížích

Na webu Azure Portal **řešení potíží s aktualizací agenta** stránky zobrazí problémy s agentem. Na stránce je odkaz na tomto článku vám pomůžou při řešení potíží. Přejděte na **řešení potíží s aktualizací agenta** stránky, vyberte **Poradce při potížích s** odkaz v **připravenost agenta aktualizací** sloupec.

![Aktualizovat seznam pro správu virtuálních počítačů](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> Zkontrolujte stav agenta, musí být spuštěná virtuálním počítači. Pokud virtuální počítač není spuštěný, **spusťte virtuální počítač** se zobrazí tlačítko.

Na **řešení potíží s aktualizací agenta** stránce **spuštění kontrol** spustí Poradce při potížích. Poradce při potížích se používá [spustit příkaz](../../virtual-machines/windows/run-command.md) ke spuštění skriptu v počítači k ověření agenta závislostí. Po dokončení Průvodce při potížích se vrátí výsledek kontroly.

![Řešení potíží s stránku aktualizace agenta](../media/update-agent-issues/troubleshoot-page.png)

Výsledky jsou zobrazeny na stránce, až budou připravené. [Kontroluje oddíly](#prerequisiste-checks) zobrazit, co je zahrnuto v každé kontrole.

![Řešení potíží s kontroly aktualizací agenta](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Kontroly splnění podmínek

### <a name="operating-system"></a>Operační systém

Operační systém kontrola ověří, zda procesu Hybrid Runbook Worker s některým z těchto operačních systémů:

|Operační systém  |Poznámky  |
|---------|---------|
|Windows Server 2008 R2 RTM, Windows Server 2008 | Podporuje pouze aktualizovat posouzení.         |
|Windows Server 2008 R2 SP1 a novější |Rozhraní .NET framework 4.5.1 nebo novější je povinný. ([Stáhnout rozhraní .NET Framework](/dotnet/framework/install/guide-for-developers))<br/> Vyžaduje se Windows PowerShell 4.0 nebo novější. ([Stáhnout Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855))<br/> Windows PowerShell 5.1 se doporučuje pro zvýšení spolehlivosti.  ([Stáhnout Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616))        |

### <a name="net-451"></a>.NET 4.5.1

Rozhraní .NET Framework kontrola ověří, zda má systém minimálně [rozhraní .NET Framework 4.5.1](https://www.microsoft.com/download/details.aspx?id=30653) nainstalované.

### <a name="wmf-51"></a>WMF 5.1

WMF kontrola ověří, zda má systém verzi Windows Management Frameworku (WMF). [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) je nejbližší podporovanou verzi. Doporučujeme, abyste nainstalovali [Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616) pro zvýšení spolehlivosti Hybrid Runbook Worker.

### <a name="tls-12"></a>TLS 1.2

Tato kontrola Určuje, zda používáte protokol TLS 1.2 pro šifrování komunikace. Protokol TLS 1.0 je již nejsou podporovány touto platformou. Doporučujeme vám, že klienti používat ke komunikaci s Update managementem TLS 1.2.

## <a name="connectivity-checks"></a>Kontrola připojení k

### <a name="registration-endpoint"></a>Koncový bod registrace

Tato kontrola Určuje, zda agent může správně komunikovat se službou agenta.

Konfigurace proxy serveru a brány firewall musí umožňovat Hybrid Runbook Worker agenta pro komunikaci s koncovým bodem registrace. Seznam adres a portech najdete v tématu [sítě plánování pro hybridní pracovní procesy](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>Operace koncového bodu

Tato kontrola Určuje, zda agent může správně komunikovat se službou Data modulu Runtime úlohy.

Konfigurace proxy serveru a brány firewall musí umožňovat Hybrid Runbook Worker agenta ke komunikaci se službou Data modulu Runtime úlohy. Seznam adres a portech najdete v tématu [sítě plánování pro hybridní pracovní procesy](../automation-hybrid-runbook-worker.md#network-planning).

## <a name="vm-service-health-checks"></a>Kontroly stavu služby virtuálního počítače

### <a name="monitoring-agent-service-status"></a>Monitorování stavu služby agent

Tato kontrola Určuje, zda `HealthService`, běží agenta Microsoft Monitoring Agent na počítači.

Další informace o odstraňování potíží se službou, naleznete v tématu [agenta Microsoft Monitoring Agent není spuštěna](hybrid-runbook-worker.md#mma-not-running).

Znovu nainstalujte agenta Microsoft Monitoring Agent, najdete v článku [instalace a konfigurace agenta Microsoft Monitoring Agent](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows).

### <a name="monitoring-agent-service-events"></a>Monitorování události služby agenta

Tato kontrola Určuje, zda se mají `4502` události se zobrazí v protokolu Azure Operations Manageru na počítače za posledních 24 hodin.

Další informace o této události, najdete v článku [Průvodce odstraňováním potíží](hybrid-runbook-worker.md#event-4502) pro tuto událost.

## <a name="access-permissions-checks"></a>Kontroly přístupu oprávnění

### <a name="machinekeys-folder-access"></a>Přístup ke složce MachineKeys

Kontrola přístupu kryptografických složky Určuje, zda místní systémový účet má přístup k C:\ProgramData\Microsoft\Crypto\RSA.

## <a name="troubleshoot-offline"></a>Řešení potíží s offline

Spuštěním skriptu místně, můžete použít Poradce při potížích s na Hybrid Runbook Worker v režimu offline. Skript, můžete získat [Poradce při potížích WindowsUpdateAgentRegistration](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration), v galerii prostředí PowerShell. Výstup tohoto skriptu by měl vypadat jako v následujícím příkladu:

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
RuleName                    : .Net Framework 4.5+
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

## <a name="next-steps"></a>Další postup

Řešení potíží s více problémů s procesy Hybrid Runbook Worker, naleznete v tématu [Poradce při potížích s procesy Hybrid Runbook Worker](hybrid-runbook-worker.md).
