---
title: Řešení potíží s rozšíření virtuálního počítače Azure Log Analytics | Microsoft Docs
description: Popisují příznaky, příčiny a řešení nejběžnějších problémů s rozšířením VM analýzy protokolů pro systém Windows a Linux virtuálních počítačích Azure.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/08/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 700d6b2c3bcd39aed38bf75556bcdcb59d1ab78b
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128822"
---
# <a name="troubleshooting-the-log-analytics-vm-extension"></a>Řešení potíží s rozšíření virtuálního počítače analýzy protokolů
Tento článek obsahuje nápovědu k odstraňování potíží chyby setkat s rozšířením VM analýzy protokolů pro Windows a Linux virtuální počítače běžící v Microsoft Azure a navrhne možná řešení jejich řešení.

Pokud chcete ověřit stav rozšíření, proveďte následující kroky na portálu Azure.

1. Přihlaste se k webu [Azure Portal](http://portal.azure.com).
2. Na webu Azure Portal klikněte na **Všechny služby**. V seznamu prostředků zadejte **virtuální počítače**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **virtuální počítače**.
3. V seznamu virtuálních počítačů najděte a vyberte ho.
3. Na virtuálním počítači, klikněte na tlačítko **rozšíření**.
4. Ze seznamu zkontrolujte, pokud je povolené rozšíření analýzy protokolů, nebo ne.  Pro Linux, je agent uveden jako **OMSAgentforLinux** a pro Windows, je agent uveden jako **MicrosoftMonitoringAgent**.

   ![Zobrazení rozšíření virtuálního počítače](./media/log-analytics-azure-vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. Klikněte na rozšíření zobrazíte podrobnosti. 

   ![Podrobnosti o rozšíření virtuálního počítače](./media/log-analytics-azure-vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Řešení potíží s rozšíření virtuálního počítače Windows Azure

Pokud *agenta Microsoft Monitoring Agent* není instalace rozšíření virtuálního počítače nebo vytváření sestav, můžete provést následující kroky k odstranění problému.

1. Zkontrolujte, zda je nainstalován agent virtuálního počítače Azure a že fungují správně pomocí kroků v [KB 2965986](https://support.microsoft.com/kb/2965986#mt1).
   * Můžete také zkontrolovat soubor protokolu agenta virtuálního počítače `C:\WindowsAzure\logs\WaAppAgent.log`
   * Pokud v protokolu neexistuje, není nainstalován agent virtuálního počítače.
   * [Nainstalujte agenta virtuálního počítače Azure](log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Potvrďte, že je spuštěn úkol prezenčního signálu agenta Microsoft Monitoring Agent rozšíření, pomocí následujících kroků:
   * Přihlaste se k virtuálnímu počítači
   * Otevřete Plánovač úloh a najít `update_azureoperationalinsight_agent_heartbeat` úloh
   * Potvrďte úloha je povolená a běží každou minutu
   * Zkontrolujte v souboru protokolu prezenčního signálu `C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\heartbeat.log`
3. Zkontrolujte soubory protokolů rozšíření Microsoft Monitoring Agent virtuálního počítače v `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`
4. Zajistěte, aby byl že virtuální počítač můžete spouštět skripty prostředí PowerShell
5. Ujistěte se, že oprávnění C:\Windows\temp nezměnily
6. Zobrazit stav služby Microsoft Monitoring Agent pomocí následujícího příkazu v okně prostředí PowerShell se zvýšenými oprávněními na virtuálním počítači `  (New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
7. Zkontrolujte soubory protokolu instalace agenta Microsoft Monitoring Agent v `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`

Další informace najdete v tématu [řešení potíží s rozšířeními Windows](../virtual-machines/windows/extensions-oms.md).

## <a name="troubleshooting-linux-vm-extension"></a>Řešení potíží s rozšíření virtuálního počítače s Linuxem
Pokud *OMS agenta pro Linux* není instalace rozšíření virtuálního počítače nebo vytváření sestav, můžete provést následující kroky k odstranění problému.

1. Pokud je stav rozšíření *neznámé* zkontrolujte, zda je nainstalován agent virtuálního počítače Azure a že fungují správně, a to prohlédnutím souboru protokolu agenta virtuálního počítače `/var/log/waagent.log`
   * Pokud v protokolu neexistuje, není nainstalován agent virtuálního počítače.
   * [Nainstalujte agenta virtuálního počítače Azure na virtuální počítače s Linuxem](log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Pro ostatní stavy není v pořádku, zkontrolujte agenta OMS pro rozšíření virtuálního počítače s Linuxem soubory protokolů `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` a `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`
3. Pokud je v pořádku stav rozšíření, ale není odesílání dat zkontrolujte OMS agenta pro Linux soubory protokolu v `/var/opt/microsoft/omsagent/log/omsagent.log`

Další informace najdete v tématu [řešení potíží s rozšířeními Linux](../virtual-machines/linux/extensions-oms.md).

## <a name="next-steps"></a>Další postup

Další pokyny k odstraňování problémů související s agentem OMS pro Linux hostované na počítače mimo Azure, najdete v části [řešení potíží s Azure Log Analytics Linux Agent](log-analytics-agent-linux-support.md).  
