---
title: Řešení potíží s agentem aktualizace pro Linux v Azure Automation Update Management
description: Naučte se řešit potíže s agentem Windows Update pro Linux pomocí Update Management řešení.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: 1f9c8d449fb060d5b1a5f810f9e387057eac3252
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2020
ms.locfileid: "82927968"
---
# <a name="troubleshoot-linux-update-agent-issues"></a>Řešení potíží s agentem aktualizace pro Linux

Může to mít spoustu důvodů, proč se Váš počítač v Update Management nezobrazuje jako připravený (v pořádku). Chcete-li zjistit základní problém, můžete ověřit stav agenta Hybrid Runbook Worker pro Linux. Níže jsou uvedené tři stavy připravenosti pro počítač:

* Připraveno: Hybrid Runbook Worker se nasadí a poslední se zobrazila před méně než jednou hodinou.
* Odpojeno: Hybrid Runbook Worker se nasazuje a naposledy se zobrazilo před jednou hodinou.
* Nenakonfigurováno: Hybrid Runbook Worker se nenašlo nebo nedokončilo registraci.

> [!NOTE]
> Mezi zobrazením Azure Portal a aktuálním stavem počítače může být mírné zpoždění.

Tento článek popisuje, jak spustit Poradce při potížích pro počítače Azure z Azure Portal a počítačů mimo Azure v [offline scénáři](#troubleshoot-offline). 

> [!NOTE]
> Skript Poradce při potížích aktuálně nesměruje provoz prostřednictvím proxy server, pokud je nakonfigurovaný.

## <a name="start-the-troubleshooter"></a>Spustit Poradce při potížích

V případě počítačů Azure vyberte odkaz **Poradce při potížích** pod sloupcem **připravenost agenta aktualizace** na portálu a otevřete stránku Poradce při potížích s aktualizací agenta. V případě počítačů mimo Azure se v tomto článku zobrazí odkaz. Informace o řešení potíží s počítačem mimo Azure najdete v pokynech v části věnované řešení potíží v režimu offline.

![Stránka seznamu virtuálních počítačů](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> Kontroly vyžadují, aby byl virtuální počítač spuštěný. Pokud virtuální počítač není spuštěný, **spustí se virtuální počítač** .

Na stránce Poradce při potížích s agentem aktualizace vyberte **Spustit kontroly** a spusťte Poradce při potížích. Poradce při potížích používá [příkaz run](../../virtual-machines/linux/run-command.md) ke spuštění skriptu na počítači za účelem ověření závislostí. Po dokončení Poradce při potížích vrátí výsledek kontrol.

![Stránka Poradce při potížích](../media/update-agent-issues-linux/troubleshoot-page.png)

Po dokončení kontrol jsou výsledky vráceny v okně. Kontrolní oddíly obsahují informace o tom, co jednotlivé kontroly hledají.

![Aktualizovat stránku kontroly agenta](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Kontroly požadovaných součástí

### <a name="operating-system"></a>Operační systém

Kontroly operačního systému ověří, zda Hybrid Runbook Worker používá jeden z následujících operačních systémů.

|Operační systém  |Poznámky  |
|---------|---------|
|CentOS 6 (x86/x64) a 7 (x64)      | Agenty Linux musí mít přístup k úložišti aktualizací. Oprava založená na klasifikaci vyžaduje, aby příkaz "Yumu" vracel data zabezpečení, která CentOS nejsou v poli.         |
|Red Hat Enterprise 6 (x86/x64) a 7 (x64)     | Agenty Linux musí mít přístup k úložišti aktualizací.        |
|SUSE Linux Enterprise Server 11 (x86/x64) a 12 (x64)     | Agenty Linux musí mít přístup k úložišti aktualizací.        |
|Ubuntu 14,04 LTS, 16,04 LTS a 18,04 LTS (x86/x64)      |Agenty Linux musí mít přístup k úložišti aktualizací.         |

## <a name="monitoring-agent-service-health-checks"></a>Kontroly stavu služby agenta monitorování

### <a name="log-analytics-agent"></a>Agent Log Analytics

Tato kontrolu zajistí, že je nainstalován agent Log Analytics pro Linux. Pokyny k instalaci najdete v tématu [instalace agenta pro Linux](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux).

### <a name="log-analytics-agent-status"></a>Stav agenta Log Analytics

Tato kontrolu zajistí, že je spuštěný agent Log Analytics pro Linux. Pokud agent neběží, můžete spustit následující příkaz a pokusit se ho restartovat. Další informace o řešení potíží s agentem najdete v tématu [řešení potíží se systémem Linux Hybrid Runbook Worker](hybrid-runbook-worker.md#linux).

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>Multihoming

Tato kontrolu určuje, zda agent hlásí do více pracovních prostorů. Update Management nepodporuje více domovských stránek.

### <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker

Tato kontrolu ověří, zda má Log Analytics Agent pro Linux balíček Hybrid Runbook Worker. Tento balíček je nutný, aby Update Management fungoval.

### <a name="hybrid-runbook-worker-status"></a>Stav Hybrid Runbook Worker

Tato kontrolu zajišťuje, že Hybrid Runbook Worker v počítači běží. V případě, že Hybrid Runbook Worker pracuje správně, by měly být k dispozici následující procesy. Další informace najdete v tématu [Poradce při potížích s agentem Log Analytics pro Linux](hybrid-runbook-worker.md#oms-agent-not-running).

> [!NOTE]
> Pokud Hybrid Runbook Worker neběží a koncový bod operací se nezdařil, aktualizace může selhat. Update Management stáhne balíčky hybridního pracovního procesu z koncového bodu operací.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>Kontroly připojení

### <a name="general-internet-connectivity"></a>Obecné připojení k Internetu

Tato kontrolu zajistí, že počítač má přístup k Internetu.

### <a name="registration-endpoint"></a>Registrační koncový bod

Tato kontrolu určuje, zda Hybrid Runbook Worker může správně komunikovat s Azure Automation v pracovním prostoru Log Analytics.

Konfigurace proxy serveru a brány firewall musí umožňovat, aby agent Hybrid Runbook Worker komunikoval s koncovým bodem registrace. Seznam adres a portů, které se mají otevřít, najdete v tématu [Plánování sítě pro hybridní pracovní procesy](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>Koncový bod operací

Tato kontrolu určuje, zda může agent správně komunikovat se službou data runtime úlohy.

Konfigurace proxy serveru a brány firewall musí umožňovat, aby agent Hybrid Runbook Worker komunikoval se službou data runtime úlohy. Seznam adres a portů, které se mají otevřít, najdete v tématu [Plánování sítě pro hybridní pracovní procesy](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="log-analytics-endpoint-1"></a>Log Analytics koncový bod 1

Tato kontrolu ověří, že váš počítač má přístup ke koncovým bodům, které vyžaduje agent Log Analytics.

### <a name="log-analytics-endpoint-2"></a>Log Analytics koncový bod 2

Tato kontrolu ověří, že váš počítač má přístup ke koncovým bodům, které vyžaduje agent Log Analytics.

### <a name="log-analytics-endpoint-3"></a>Log Analytics koncový bod 3

Tato kontrolu ověří, že váš počítač má přístup ke koncovým bodům, které vyžaduje agent Log Analytics.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Řešení potíží offline

Poradce při potížích můžete použít offline v Hybrid Runbook Worker spuštěním skriptu místně. Skript Pythonu, [update_mgmt_health_check. py](https://gallery.technet.microsoft.com/scriptcenter/Troubleshooting-utility-3bcbefe6), najdete v centru skriptů. Příklad výstupu tohoto skriptu je znázorněn v následujícím příkladu:

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

Pokud chcete řešit další problémy s procesy Hybrid Runbook Worker, přečtěte si téma [řešení potíží s hybridními procesy Runbooku](hybrid-runbook-worker.md).
