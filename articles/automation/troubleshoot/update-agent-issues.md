---
title: Vysvětlení výsledky kontroly agenta ve službě Azure Update Management
description: Informace o řešení potíží s agentem Update Management.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: automation
ms.component: update-management
manager: carmonm
ms.openlocfilehash: 20323afe79ad3de1e3dfccd4752c4f7e28d22266
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50095367"
---
# <a name="understand-the-agent-check-results-in-update-management"></a>Vysvětlení výsledky kontroly agenta v Update Management

Může být mnoho důvodů, proč počítač mimo Azure nezobrazuje **připravené** v Update Management. V Update Management můžete zkontrolovat stav agenta Hybrid Worker, chcete-li zjistit příčinu problému. Tento článek popisuje, jak spustit Poradce při potížích se z portálu Azure portal a v případě offline scénářů.

## <a name="start-the-troubleshooter"></a>Spustit Poradce při potížích

Kliknutím **Poradce při potížích** odkaz pod **připravenost agenta aktualizací** sloupce na portálu, spusťte **řešení potíží s aktualizací agenta** stránky. Tato stránka zobrazuje problémy s agentem a odkaz na tento článek vám pomůže vyřešit vaše potíže.

![Stránka seznamu virtuálních počítačů](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> Kontroly vyžadovat přesun virtuálního počítače běžet. Pokud virtuální počítač není spuštěný, zobrazí se tlačítko **spusťte virtuální počítač**.

Na **řešení potíží s aktualizací agenta** klikněte na **spuštění zkontroluje**, spustí Poradce při potížích. Poradce při potížích se používá [spusťte příkaz](../../virtual-machines/windows/run-command.md) ke spuštění skriptu v počítači ověřte závislosti, které má agent. Po dokončení Průvodce při potížích se vrátí výsledek kontroly.

![Řešení potíží s stránky](../media/update-agent-issues/troubleshoot-page.png)

Jakmile budete hotovi, výsledky se vrátí v okně. [Zkontrolujte oddíly](#pre-requisistes-checks) poskytují informace o každé kontrole hledání.

![Stránka pro aktualizaci agenta kontroly](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Kontroly splnění podmínek

### <a name="operating-system"></a>Operační systém

Kontrola operačního systému, ověří, pokud funkce Hybrid Runbook Worker s některým z následujících operačních systémů:

|Operační systém  |Poznámky  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Podporuje pouze aktualizovat posouzení.         |
|Windows Server 2008 R2 SP1 a novější     |Rozhraní .NET framework 4.5.1 nebo novější je povinný. ([Stáhnout rozhraní .NET Framework](/dotnet/framework/install/guide-for-developers))<br/> Vyžaduje se Windows PowerShell 4.0 nebo novější. ([Stáhnout WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855))<br/> Windows PowerShell 5.1 se doporučuje pro zvýšení spolehlivosti.  ([Stáhnout WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))        |
|CentOS 6 (x86/x64) a 7 (x64)      | Agenty Linux musí mít přístup k úložišti aktualizací. Podle klasifikace opravy vyžaduje "yumu" vrátit data zabezpečení, která CentOS nemá úprav.         |
|Red Hat Enterprise 6 (x86/x64) a 7 (x64)     | Agenty Linux musí mít přístup k úložišti aktualizací.        |
|SUSE Linux Enterprise Server 11 (x86/x64) a 12 (x64)     | Agenty Linux musí mít přístup k úložišti aktualizací.        |
|Ubuntu 14.04 LTS, 16.04 LTS a 18.04 LTS (x86/x64)      |Agenty Linux musí mít přístup k úložišti aktualizací.         |

### <a name="net-451"></a>.NET 4.5.1

Kontrola rozhraní .NET, ověří, zda má systém minimálně [rozhraní .NET Framework 4.5.1](https://www.microsoft.com/download/details.aspx?id=30653) k dispozici.

### <a name="wmf-51"></a>WMF 5.1

Kontrola WMF, ověří, zda má systém požadovaná verze rozhraní Windows Management Framework. [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) je nejnižší verze podporovaná. Doporučuje se, že nainstalujete [Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616) pro zvýšení spolehlivosti Hybrid Runbook Worker.

### <a name="tls-12"></a>TLS 1.2

Tato kontrola Určuje, pokud používáte TLS 1.2 pro šifrování komunikace. Protokol TLS 1.0 je již nejsou podporovány touto platformou a doporučuje se, že klienti používat ke komunikaci s Update managementem TLS 1.2.

## <a name="connectivity-checks"></a>Kontrola připojení k

### <a name="registration-endpoint"></a>Koncový bod registrace

Tato kontrola Určuje, pokud agent můžete správně komunikovat se službou agenta.

Konfigurace proxy serveru a brány firewall musí umožňovat Hybrid Runbook Worker agenta pro komunikaci s koncovým bodem registrace. Seznam adres a portech najdete v tématu [sítě plánování pro hybridní pracovní procesy](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="operations-endpoint"></a>Operace koncového bodu

Tato kontrola Určuje, pokud agent můžete správně komunikovat se službou Data modulu Runtime úlohy.

Konfigurace proxy serveru a brány firewall musí umožňovat Hybrid Runbook Worker agenta ke komunikaci se službou Data modulu Runtime úlohy. Seznam adres a portech najdete v tématu [sítě plánování pro hybridní pracovní procesy](../automation-hybrid-runbook-worker.md#network-planning)

## <a name="vm-service-health-checks"></a>Kontroly stavu služby virtuálního počítače

### <a name="monitoring-agent-service-status"></a>Monitorování stavu služby agent

Tato kontrola Určuje, zda agenta Microsoft Monitoring Agent `HealthService` běží na počítači.

Další informace o odstraňování potíží se službou, naleznete v tématu [agenta Microsoft Monitoring Agent není spuštěna](hybrid-runbook-worker.md#mma-not-running).

Znovu nainstalujte agenta Microsoft Monitoring Agent, najdete v článku [instalace a konfigurace agenta Microsoft Monitoring Agent](/log-analytics/log-analytics-concept-hybrid.md#install-and-configure-agent)

### <a name="monitoring-agent-service-events"></a>Monitorování události služby agenta

Tato kontrola Určuje, pokud byly některé `4502` protokolu událostí v nástroji Operations Manager na počítači za posledních 24 hodin.

Další informace o této události, najdete v článku [Průvodce odstraňováním potíží](hybrid-runbook-worker.md#event-4502) pro tuto událost.

## <a name="access-permissions-checks"></a>Kontroly přístupu oprávnění

### <a name="machinekeys-folder-access"></a>Přístup ke složce MachineKeys

Zkontrolujte přístup ke složkám Crypto určí, zda má účet místního systému přístup k `C:\ProgramData\Microsoft\Crypto\RSA`

## <a name="troubleshoot-offline"></a>Řešení potíží s offline

Spuštěním skriptu místně, můžete použít Poradce při potížích s offline v procesu Hybrid Runbook Worker. Skript, [Poradce při potížích WindowsUpdateAgentRegistration](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration) najdete v galerii prostředí PowerShell. V následujícím příkladu je uveden příklad výstupu tohoto skriptu:

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

Řešení dalších potíží s procesy Hybrid Runbook Worker, najdete v článku [řešení potíží – Hybrid Runbook Worker](hybrid-runbook-worker.md)
