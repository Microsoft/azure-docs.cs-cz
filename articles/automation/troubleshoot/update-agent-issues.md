---
title: Řešení potíží s problémy s agentem aktualizace systému Windows ve správě aktualizací Azure Automation
description: Zjistěte, jak řešit a řešit problémy s agentem aktualizací systému Windows pomocí řešení správy aktualizací.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/16/2020
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: 6983a2ac7ab5fafcb00aee0b72221a8540ea1668
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678977"
---
# <a name="troubleshoot-windows-update-agent-issues"></a>Poradce při potížích s agentem aktualizace systému Windows

Může existovat mnoho důvodů, proč se váš počítač nezobrazuje jako připravený (v pořádku) ve správě aktualizací. Ve správě aktualizací můžete zkontrolovat stav agenta pracovníka hybridní sady Runbook a určit základní problém. Tento článek popisuje, jak spustit poradce při potížích pro počítače Azure z webu Azure Portal a počítačů, které nejsou Azure, ve [scénáři offline](#troubleshoot-offline).

Níže jsou uvedeny tři stavy připravenosti pro stroj:

* Připraveno – hybridní runbook worker je nasazen a naposledy byl viděn před méně než 1 hodinou.
* Odpojeno - Hybridní runbook worker je nasazen a byl naposledy viděn před více než 1 hodinou.
* Není nakonfigurováno – Hybridní pracovník runbooku nebyl nalezen nebo nedokončil registrace.

> [!NOTE]
> Může být mírné zpoždění mezi co se zobrazí portál Azure a aktuální stav počítače.

## <a name="start-the-troubleshooter"></a>Spuštění poradce při potížích

U počítačů Azure kliknutím na odkaz **Poradce při potížích** ve sloupci **Připravenost na agenta aktualizace** na portálu se spustí stránka Agent pro řešení potíží s aktualizací. Pro počítače než Azure odkaz přejdete na tento článek. Podívejte se na [offline pokyny](#troubleshoot-offline) k řešení potíží s počítačem, který není Azure.

![Aktualizovat seznam správy virtuálních počítačů](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> Chcete-li zkontrolovat stav hybridního pracovníka runbooku, musí být spuštěný virtuální ho diář. Pokud virtuální počítače není spuštěn, zobrazí **se tlačítko Spustit virtuální ho.**

Na stránce Poradce při potížích s agentem aktualizace vyberte **spustit kontroly** a spusťte poradce při potížích. Poradce při potížích používá [spustit příkaz](../../virtual-machines/windows/run-command.md) ke spuštění skriptu v počítači k ověření závislostí. Po dokončení poradce při potížích vrátí výsledek kontroly.

![Stránka Poradce při potížích s agentem aktualizace](../media/update-agent-issues/troubleshoot-page.png)

Výsledky se zobrazí na stránce, když jsou připraveny. V kontrolních částech jsou uvedeny, co je součástí každé kontroly.

![Poradce při potížích s kontrolami agenta aktualizace](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Kontroly požadovaných součástí

### <a name="operating-system"></a>Operační systém

Kontrola operačního systému ověřuje, zda hybridní pracovník runbooku používá jeden z následujících operačních systémů:

|Operační systém  |Poznámky  |
|---------|---------|
|Windows Server 2012 a novější |Rozhraní .NET Framework 4.6 nebo novější je povinné. ([Stáhněte si rozhraní .NET Framework](/dotnet/framework/install/guide-for-developers))<br/> Je vyžadováno prostředí Windows PowerShell 5.1.  ([Stáhnout rozhraní Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616))        |

### <a name="net-462"></a>.NET 4.6.2

Kontrola rozhraní .NET Framework ověří, zda má systém nainstalován minimálně [rozhraní .NET Framework 4.6.2.](https://www.microsoft.com/en-us/download/details.aspx?id=53345)

### <a name="wmf-51"></a>WMF 5.1

Kontrola WMF ověří, zda má systém požadovanou verzi rozhraní WMF (Windows Management Framework) – [Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616).

### <a name="tls-12"></a>TLS 1.2

Tato kontrola určuje, zda k šifrování komunikace používáte tls 1.2. TLS 1.0 již není podporován platformou. Doporučujeme klientům používat TLS 1.2 ke komunikaci se správou aktualizací.

## <a name="connectivity-checks"></a>Kontroly připojení

### <a name="registration-endpoint"></a>Koncový bod registrace

Tato kontrola určuje, zda agent může správně komunikovat se službou agenta.

Konfigurace proxy a brány firewall musí agentovi hybrid runbook worker umožnit komunikaci s koncovým bodem registrace. Seznam adres a portů, které chcete otevřít, naleznete v [tématu Plánování sítě pro hybridní pracovníky](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>Koncový bod operací

Tato kontrola určuje, zda agent může správně komunikovat se službou Job Runtime Data Service.

Konfigurace proxy a brány firewall musí agentovi hybrid runbook worker umožnit komunikaci se službou Job Runtime Data Service. Seznam adres a portů, které chcete otevřít, naleznete v [tématu Plánování sítě pro hybridní pracovníky](../automation-hybrid-runbook-worker.md#network-planning).

## <a name="vm-service-health-checks"></a>Kontroly stavu služby virtuálních montovna

### <a name="monitoring-agent-service-status"></a>Stav služby agenta monitorování

Tato kontrola určuje, zda je v`healthservice`počítači spuštěn agent Analýzy protokolů pro systém Windows ( ). Další informace o řešení potíží se službou najdete v [tématu Agent analýzy protokolů pro Systém Windows není spuštěn](hybrid-runbook-worker.md#mma-not-running).

Informace o přeinstalaci agenta Log Analytics pro Systém Windows naleznete v [tématu Instalace a konfigurace agenta Log Analytics pro systém Windows](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows).

### <a name="monitoring-agent-service-events"></a>Události služby agenta monitorování

Tato kontrola určuje, zda se v protokolu Nástroje pro provoz služby Azure Operations Manager v posledních 24 hodinách zobrazí všechny události 4502.

Další informace o této události naleznete v [průvodci řešením potíží](hybrid-runbook-worker.md#event-4502) pro tuto událost.

## <a name="access-permissions-checks"></a>Kontroly přístupových oprávnění

### <a name="machinekeys-folder-access"></a>Přístup ke složce MachineKeys

Kontrola přístupu ke složce Crypto určuje, zda má místní systémový účet přístup k c:\ProgramData\Microsoft\Crypto\RSA.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Poradce při potížích s offline

Poradce při potížích s hybridním pracovníkem runbooku můžete použít v offline spuštěním skriptu místně. Skript [Troubleshoot-WindowsUpdateAgentRegistration](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration)můžete získat v Galerii prostředí PowerShell. Chcete-li spustit skript, musíte mít nainstalován soubor WMF 4.0 nebo vyšší. Pokud chcete stáhnout nejnovější verzi PowerShellu, [přečtěte si informace o instalaci různých verzí PowerShellu](https://docs.microsoft.com/powershell/scripting/install/installing-powershell).

Výstup tohoto skriptu vypadá jako následující příklad:

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

Další problémy s hybridními pracovníky runbooku najdete [v tématu Poradce při potížích s hybridními pracovníky runbooku](hybrid-runbook-worker.md).
