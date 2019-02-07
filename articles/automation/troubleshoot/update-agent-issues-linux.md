---
title: Vysvětlení výsledky kontroly systému Linux agenta ve službě Azure Update Management
description: Informace o řešení potíží s agentem Update Management.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 12/14/2018
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: aafed492e83066be20b4728a2617527351291a5b
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55813105"
---
# <a name="understand-the-linux-agent-check-results-in-update-management"></a>Vysvětlení výsledky kontroly systému Linux agenta v Update Management

Může být mnoho důvodů, proč počítač nezobrazuje **připravené** v Update Management. V Update Management můžete zkontrolovat stav agenta Hybrid Worker, chcete-li zjistit příčinu problému. Tento článek popisuje, jak spustit Poradce při potížích pro počítače Azure z webu Azure portal a počítače mimo Azure v [offline scénáři](#troubleshoot-offline).

V následujícím seznamu jsou tři připravenosti stavy, které počítač může být v:

* **Připraveno** – aktualizace agenta nasazení a byla posledního kontaktu s méně než hodinou.
* **Odpojení** – nasazení aktualizace agenta a byl naposledy zobrazeno více než 1 hodinou.
* **Není nakonfigurováno** – aktualizace agenta nebyl nalezen nebo nebyl dokončen registrace.

## <a name="start-the-troubleshooter"></a>Spustit Poradce při potížích

Pro počítače Azure, kliknutím **Poradce při potížích** odkaz v části **připravenost agenta aktualizací** sloupec v portálu spustí **řešení potíží s aktualizací agenta** stránky. Pro počítače mimo Azure přináší na odkaz můžete k tomuto článku. Offline pokyny k řešení potíží s počítači mimo Azure.

![Stránka seznamu virtuálních počítačů](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> Kontroly vyžadovat přesun virtuálního počítače běžet. Pokud virtuální počítač není spuštěný, zobrazí se tlačítko **spusťte virtuální počítač**.

Na **řešení potíží s aktualizací agenta** klikněte na **spuštění zkontroluje**, spustí Poradce při potížích. Poradce při potížích se používá [spusťte příkaz](../../virtual-machines/linux/run-command.md) ke spuštění skriptu v počítači ověřte závislosti, které má agent. Po dokončení Průvodce při potížích se vrátí výsledek kontroly.

![Řešení potíží s stránky](../media/update-agent-issues-linux/troubleshoot-page.png)

Jakmile budete hotovi, výsledky se vrátí v okně. Vrácení části poskytují informace o každé kontrole hledání.

![Stránka pro aktualizaci agenta kontroly](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Kontroly splnění podmínek

### <a name="operating-system"></a>Operační systém

Kontrola operačního systému, ověří, pokud funkce Hybrid Runbook Worker s některým z následujících operačních systémů:

|Operační systém  |Poznámky  |
|---------|---------|
|CentOS 6 (x86/x64) a 7 (x64)      | Agenty Linux musí mít přístup k úložišti aktualizací. Podle klasifikace opravy vyžaduje "yumu" vrátit data zabezpečení, které CentOS nemá úprav.         |
|Red Hat Enterprise 6 (x86/x64) a 7 (x64)     | Agenty Linux musí mít přístup k úložišti aktualizací.        |
|SUSE Linux Enterprise Server 11 (x86/x64) a 12 (x64)     | Agenty Linux musí mít přístup k úložišti aktualizací.        |
|Ubuntu 14.04 LTS, 16.04 LTS a 18.04 LTS (x86/x64)      |Agenty Linux musí mít přístup k úložišti aktualizací.         |

## <a name="monitoring-agent-service-health-checks"></a>Monitorování kontroly stavu služby agent

### <a name="oms-agent"></a>Agenta OMS

Tato kontrola se zajistí, že je nainstalovaný Agent OMS pro Linux. Pokyny k její instalaci najdete v tématu [instalace agenta pro Linux](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux
).

### <a name="oms-agent-status"></a>Stav agenta OMS

Tato kontrola se zajistí, že je spuštěn Agent OMS pro Linux. Pokud agent není spuštěn, můžete spustit následující příkaz, který se pokusí o restartování. Další informace o řešení potíží s agenta najdete v tématu [Linux Hybrid Runbook worker Poradce při potížích](hybrid-runbook-worker.md#linux)

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>Multihoming

Tato kontrola Určuje, jestli se agent hlásí do několika pracovních prostorů. Vícenásobné navádění nepodporuje správu aktualizací.

### <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker

Tato kontrola ověřuje, jestli má agenta OMS pro Linux balíček Hybrid Runbook Worker. Tento balíček je nutná pro správu aktualizací pro práci.

### <a name="hybrid-runbook-worker-status"></a>Stav hybrid Runbook Worker

Tato kontrola je zajištěno, že na počítači je spuštěná Hybrid Runbook Worker. Následující procesy by měla být k dispozici, pokud funkce Hybrid Runbook Worker běží správně. Další informace najdete v tématu [řešení potíží s agenta Log Analytics pro Linux](hybrid-runbook-worker.md#oms-agent-not-running).

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>Kontrola připojení k

### <a name="general-internet-connectivity"></a>Obecné připojení k Internetu

Tato kontrola je zajištěno, že počítač má přístup k Internetu.

### <a name="registration-endpoint"></a>Koncový bod registrace

Tato kontrola Určuje, pokud agent můžete správně komunikovat se službou agenta.

Konfigurace proxy serveru a brány firewall musí umožňovat Hybrid Runbook Worker agenta pro komunikaci s koncovým bodem registrace. Seznam adres a portech najdete v tématu [sítě plánování pro hybridní pracovní procesy](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="operations-endpoint"></a>Operace koncového bodu

Tato kontrola Určuje, pokud agent můžete správně komunikovat se službou Data modulu Runtime úlohy.

Konfigurace proxy serveru a brány firewall musí umožňovat Hybrid Runbook Worker agenta ke komunikaci se službou Data modulu Runtime úlohy. Seznam adres a portech najdete v tématu [sítě plánování pro hybridní pracovní procesy](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="log-analytics-endpoint-1"></a>Log Analytics koncový bod 1

Tato kontrola ověřuje, že váš počítač má přístup ke koncovým bodům vyžadované agenta Log Analytics.

### <a name="log-analytics-endpoint-2"></a>Log Analytics koncový bod 2

Tato kontrola ověřuje, že váš počítač má přístup ke koncovým bodům vyžadované agenta Log Analytics.

### <a name="log-analytics-endpoint-3"></a>Log Analytics endpoint 3

Tato kontrola ověřuje, že váš počítač má přístup ke koncovým bodům vyžadované agenta Log Analytics.

## <a name="troubleshoot-offline"></a>Řešení potíží s offline

Spuštěním skriptu místně, můžete použít Poradce při potížích s offline v procesu Hybrid Runbook Worker. Skript v jazyce python, [update_mgmt_health_check.py](https://gallery.technet.microsoft.com/scriptcenter/Troubleshooting-utility-3bcbefe6) najdete v Centru skriptů. V následujícím příkladu je uveden příklad výstupu tohoto skriptu:

```output
Debug: Machine Information:   Static hostname: LinuxVM2
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 00000000000000000000000000000000
           Boot ID: 00000000000000000000000000000000
    Virtualization: microsoft
  Operating System: Ubuntu 16.04.5 LTS
            Kernel: Linux 4.15.0-1025-azure
      Architecture: x86-64


Passed: Operating system version is supported

Passed: Microsoft Monitoring agent is installed

Debug: omsadmin.conf file contents:
        WORKSPACE_ID=00000000-0000-0000-0000-000000000000
        AGENT_GUID=00000000-0000-0000-0000-000000000000
        LOG_FACILITY=local0
        CERTIFICATE_UPDATE_ENDPOINT=https://00000000-0000-0000-0000-000000000000.oms.opinsights.azure.com/ConfigurationService.Svc/RenewCertificate
        URL_TLD=opinsights.azure.com
        DSC_ENDPOINT=https://scus-agentservice-prod-1.azure-automation.net/Accou            nts/00000000-0000-0000-0000-000000000000/Nodes\(AgentId='00000000-0000-0000-0000-000000000000'\)
        OMS_ENDPOINT=https://00000000-0000-0000-0000-000000000000.ods.opinsights            .azure.com/OperationalData.svc/PostJsonDataItems
        AZURE_RESOURCE_ID=/subscriptions/00000000-0000-0000-0000-000000000000/re            sourcegroups/myresourcegroup/providers/microsoft.compute/virtualmachines/linuxvm            2
        OMSCLOUD_ID=0000-0000-0000-0000-0000-0000-00
        UUID=00000000-0000-0000-0000-000000000000


Passed: Microsoft Monitoring agent is running

Passed: Machine registered with log analytics workspace:['00000000-0000-0000-0000-000000000000']

Passed: Hybrid worker package is present

Passed: Hybrid worker is running

Passed: Machine is connected to internet

Passed: TCP test for {scus-agentservice-prod-1.azure-automation.net} (port 443)             succeeded

Passed: TCP test for {eus2-jobruntimedata-prod-su1.azure-automation.net} (port 4            43) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.ods.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.oms.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {ods.systemcenteradvisor.com} (port 443) succeeded

```

## <a name="next-steps"></a>Další postup

Řešení dalších potíží s procesy Hybrid Runbook Worker, najdete v článku [řešení potíží – Hybrid Runbook Worker](hybrid-runbook-worker.md)

