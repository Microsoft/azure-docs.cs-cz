---
title: Řešení potíží s problémy s agentem aktualizací Linuxu ve správě aktualizací Azure Automation
description: Zjistěte, jak řešit a řešit problémy s agentem aktualizací Systému Windows pro Linux pomocí řešení správy aktualizací.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: bba1c7e89a9c3bb1c9aa1567e36dd71a40f14636
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679063"
---
# <a name="troubleshoot-linux-update-agent-issues"></a>Poradce při potížích s agentem aktualizace Linuxu

Může existovat mnoho důvodů, proč se váš počítač nezobrazuje jako připravený (v pořádku) ve správě aktualizací. Ve správě aktualizací můžete zkontrolovat stav agenta pracovníka hybridní sady Runbook a určit základní problém. Tento článek popisuje, jak spustit poradce při potížích pro počítače Azure z webu Azure Portal a počítačů, které nejsou Azure, ve [scénáři offline](#troubleshoot-offline). 

Následující seznam jsou tři stavy připravenosti, ve kterých může být počítač:

* Připraveno – hybridní runbook worker je nasazen a naposledy byl viděn před méně než 1 hodinou.
* Odpojeno - Hybridní runbook worker je nasazen a byl naposledy viděn před více než 1 hodinou.
* Není nakonfigurováno – Hybridní pracovník runbooku nebyl nalezen nebo nedokončil registrace.

> [!NOTE]
> Může být mírné zpoždění mezi co se zobrazí portál Azure a aktuální stav počítače.

## <a name="start-the-troubleshooter"></a>Spuštění poradce při potížích

U počítačů Azure kliknutím na odkaz **Poradce při potížích** ve sloupci **Připravenost na agenta aktualizace** na portálu se spustí stránka Agent pro řešení potíží s aktualizací. Pro počítače než Azure odkaz přejdete na tento článek. Podívejte se na offline pokyny k řešení potíží s počítačem, který není Azure.

![stránka se seznamem virtuálního videa](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> Kontroly vyžadují spuštění virtuálního soudu. Pokud virtuální hod není spuštěn, zobrazí se tlačítko **Spustit virtuální ho.**

Na stránce Poradce při potížích s agentem aktualizace spusťte poradce při potížích klepnutím na **tlačítko Spustit kontroly**. Poradce při potížích používá [příkaz Spustit](../../virtual-machines/linux/run-command.md) ke spuštění skriptu v počítači k ověření závislostí. Po dokončení poradce při potížích vrátí výsledek kontroly.

![Stránka Poradce při potížích](../media/update-agent-issues-linux/troubleshoot-page.png)

Po dokončení jsou vráceny výsledky v okně. Kontrolní sekce poskytují informace o tom, co každá kontrola hledá.

![Stránka Kontroly agenta aktualizace](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Kontroly požadovaných součástí

### <a name="operating-system"></a>Operační systém

Kontrola operačního systému ověří, zda hybridní pracovník runbooku používá jeden z následujících operačních systémů:

|Operační systém  |Poznámky  |
|---------|---------|
|CentOS 6 (x86/x64) a 7 (x64)      | Agenty Linux musí mít přístup k úložišti aktualizací. Oprava založená na klasifikaci vyžaduje , že "yum" vrátí bezpečnostní data, která CentOS nemá po vybalení z krabice.         |
|Red Hat Enterprise 6 (x86/x64) a 7 (x64)     | Agenty Linux musí mít přístup k úložišti aktualizací.        |
|SUSE Linux Enterprise Server 11 (x86/x64) a 12 (x64)     | Agenty Linux musí mít přístup k úložišti aktualizací.        |
|Ubuntu 14.04 LTS, 16.04 LTS a 18.04 LTS (x86/x64)      |Agenty Linux musí mít přístup k úložišti aktualizací.         |

## <a name="monitoring-agent-service-health-checks"></a>Kontroly stavu služby monitorovacího agenta

### <a name="log-analytics-agent"></a>Agent Log Analytics

Tato kontrola zajišťuje, že je nainstalován agent Analýzy protokolů pro Linux. Pokyny k jeho instalaci najdete [v tématu Instalace agenta pro Linux](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux
).

### <a name="log-analytics-agent-status"></a>Stav agenta služby Log Analytics

Tato kontrola zajišťuje, že je spuštěn agent Analýzy protokolů pro Linux. Pokud agent není spuštěn, můžete spustit následující příkaz a pokusit se jej restartovat. Další informace o řešení potíží s agentem najdete v tématu [Řešení potíží s pracovníkem hybridního runbooku Linuxu](hybrid-runbook-worker.md#linux)

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>Multihoming

Tato kontrola určuje, zda agent hlásí do více pracovních prostorů. Správa více násobné aktualizace není podporována službou Update Management.

### <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker

Tato kontrola ověří, zda agent Analýzy protokolů pro Linux má balíček Hybridní runbook worker. Tento balíček je vyžadován pro správu aktualizací.

### <a name="hybrid-runbook-worker-status"></a>Stav pracovníka hybridního runbooku

Tato kontrola zajišťuje, že hybridní pracovník runbooku běží v počítači. Následující procesy by měly být k dispozici, pokud hybridní runbook worker běží správně. Další informace najdete [v tématu řešení potíží s agentem analýzy protokolů pro Linux](hybrid-runbook-worker.md#oms-agent-not-running).

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>Kontroly připojení

### <a name="general-internet-connectivity"></a>Obecné připojení k internetu

Tato kontrola zajišťuje, že zařízení má přístup k internetu.

### <a name="registration-endpoint"></a>Koncový bod registrace

Tato kontrola určuje, pokud pracovník hybridní sady Runbook může správně komunikovat s azure automatizace pracovního prostoru Log Analytics.

Konfigurace proxy a brány firewall musí agentovi hybrid runbook worker umožnit komunikaci s koncovým bodem registrace. Seznam adres a portů, které chcete otevřít, najdete v [tématu Plánování sítě pro hybridní pracovníky](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="operations-endpoint"></a>Koncový bod operací

Tato kontrola určuje, zda agent může správně komunikovat se službou Job Runtime Data Service.

Konfigurace proxy a brány firewall musí agentovi hybrid runbook worker umožnit komunikaci se službou Job Runtime Data Service. Seznam adres a portů, které chcete otevřít, najdete v [tématu Plánování sítě pro hybridní pracovníky](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="log-analytics-endpoint-1"></a>Koncový bod analýzy protokolů 1

Tato kontrola ověří, že váš počítač má přístup ke koncovým bodům, které agent Analýzy protokolů potřebuje.

### <a name="log-analytics-endpoint-2"></a>Koncový bod analýzy protokolů 2

Tato kontrola ověří, že váš počítač má přístup ke koncovým bodům, které agent Analýzy protokolů potřebuje.

### <a name="log-analytics-endpoint-3"></a>Koncový bod analýzy protokolů 3

Tato kontrola ověří, že váš počítač má přístup ke koncovým bodům, které agent Analýzy protokolů potřebuje.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Poradce při potížích s offline

Poradce při potížích můžete použít offline u hybridního pracovníka runbooku spuštěním skriptu místně. Skript [pythonu, update_mgmt_health_check.py,](https://gallery.technet.microsoft.com/scriptcenter/Troubleshooting-utility-3bcbefe6) najdete v Centru skriptů. Příklad výstupu tohoto skriptu je uveden v následujícím příkladu:

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

Informace o řešení dalších problémů s hybridními pracovníky runbooku najdete [v tématu Poradce při potížích s hybridními pracovníky runbooku](hybrid-runbook-worker.md).
