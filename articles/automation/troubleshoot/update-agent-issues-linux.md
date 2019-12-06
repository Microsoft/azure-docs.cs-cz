---
title: Vysvětlení stavu Linux Hybrid Runbook Worker v Azure Update Management
description: Naučte se řešit problémy s Hybrid Runbook Worker v systému Linux, která podporuje Update Management.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: 924c2fd176b5b8e45352d616d226f484e814450d
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849255"
---
# <a name="understand-the-linux-hybrid-runbook-worker-health-in-update-management"></a>Pochopení stavu Hybrid Runbook Worker pro Linux v Update Management

Příčinou může být to, že váš počítač není v Update Management **připravený** . V Update Management můžete zjistit příčinu problému tím, že zkontrolujete stav agenta Hybrid Runbook Worker. Tento článek popisuje, jak spustit Poradce při potížích pro počítače Azure z Azure Portal a počítačů mimo Azure v [offline scénáři](#troubleshoot-offline).

Následující seznam uvádí tři stavy připravenosti, na kterých počítač může být:

* **Připraveno** – Hybrid Runbook Worker je nasazená a naposledy se zobrazila před méně než 1 hodinou.
* **Odpojeno** – Hybrid Runbook Worker je nasazená a naposledy se zobrazila před 1 hodinou.
* **Nenakonfigurováno** – Hybrid Runbook Worker se nenašlo nebo nedokončilo registraci.

> [!NOTE]
> Mezi zobrazením Azure Portal a aktuálním stavem počítače může dojít k mírnému zpoždění.

## <a name="start-the-troubleshooter"></a>Spustit Poradce při potížích

V případě počítačů Azure se kliknutím na odkaz **Poradce při potížích** ve sloupci **připravenost agenta aktualizace** na portálu spustí stránka **Poradce při potížích s agentem** aktualizace. V případě počítačů mimo Azure se v tomto článku zobrazí odkaz. Přečtěte si pokyny offline k řešení potíží s počítačem mimo Azure.

![Stránka seznamu virtuálních počítačů](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> Kontroly vyžadovat přesun virtuálního počítače běžet. Pokud virtuální počítač není spuštěný, zobrazí se tlačítko **spusťte virtuální počítač**.

Na **řešení potíží s aktualizací agenta** klikněte na **spuštění zkontroluje**, spustí Poradce při potížích. Poradce při potížích používá [příkaz run](../../virtual-machines/linux/run-command.md) ke spuštění skriptu na počítači za účelem ověření závislostí. Po dokončení Průvodce při potížích se vrátí výsledek kontroly.

![Řešení potíží s stránky](../media/update-agent-issues-linux/troubleshoot-page.png)

Jakmile budete hotovi, výsledky se vrátí v okně. Kontrolní oddíly obsahují informace o tom, co jednotlivé kontroly hledají.

![Stránka pro aktualizaci agenta kontroly](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Kontroly splnění podmínek

### <a name="operating-system"></a>Operační systém

Kontroly operačního systému ověří, jestli Hybrid Runbook Worker používá jeden z následujících operačních systémů:

|Operační systém  |Poznámky  |
|---------|---------|
|CentOS 6 (x86/x64) a 7 (x64)      | Agenty Linux musí mít přístup k úložišti aktualizací. Oprava založená na klasifikaci vyžaduje, aby příkaz "Yumu" vracel data zabezpečení, která CentOS nejsou v poli.         |
|Red Hat Enterprise 6 (x86/x64) a 7 (x64)     | Agenty Linux musí mít přístup k úložišti aktualizací.        |
|SUSE Linux Enterprise Server 11 (x86/x64) a 12 (x64)     | Agenty Linux musí mít přístup k úložišti aktualizací.        |
|Ubuntu 14.04 LTS, 16.04 LTS a 18.04 LTS (x86/x64)      |Agenty Linux musí mít přístup k úložišti aktualizací.         |

## <a name="monitoring-agent-service-health-checks"></a>Monitorování kontroly stavu služby agent

### <a name="log-analytics-agent"></a>Agent Log Analytics

Tato kontrolu zajistí, že je nainstalován agent Log Analytics pro Linux. Pokyny k její instalaci najdete v tématu [instalace agenta pro Linux](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux
).

### <a name="log-analytics-agent-status"></a>Stav agenta Log Analytics

Tato kontrolu zajistí, že je spuštěný agent Log Analytics pro Linux. Pokud agent neběží, můžete spustit následující příkaz a pokusit se ho restartovat. Další informace o řešení potíží s agentem najdete v tématu [řešení potíží se systémem Linux Hybrid Runbook Worker](hybrid-runbook-worker.md#linux)

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>Multihoming

Tato kontrola Určuje, jestli se agent hlásí do několika pracovních prostorů. Vícenásobné navádění nepodporuje správu aktualizací.

### <a name="hybrid-runbook-worker"></a>Hybridní pracovní proces runbooku

Tato kontrolu ověří, zda má Log Analytics Agent pro Linux balíček Hybrid Runbook Worker. Tento balíček je nutná pro správu aktualizací pro práci.

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

Tato kontrolu určuje, zda Hybrid Runbook Worker může správně komunikovat s Azure Automationm pracovním prostorem Log Analytics.

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

## <a name="troubleshoot-offline"></a>Řešení potíží offline

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

## <a name="next-steps"></a>Další kroky

Pokud chcete řešit další problémy s procesy Hybrid Runbook Worker, přečtěte si téma [řešení potíží – hybridní pracovní procesy Runbooku](hybrid-runbook-worker.md).
