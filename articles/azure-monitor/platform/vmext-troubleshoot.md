---
title: Řešení potíží s rozšířením virtuálního počítače Azure Log Analytics v Azure Monitor | Microsoft Docs
description: Popisují příznaky, příčiny a řešení většiny běžných potíží s rozšířením Log Analytics VM pro Windows a virtuální počítače Azure s Linuxem.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/06/2019
ms.openlocfilehash: 88d76fc0c215653cf732ba7b827d82187d738fd9
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658468"
---
# <a name="troubleshooting-the-log-analytics-vm-extension-in-azure-monitor"></a>Řešení potíží s rozšířením Log Analytics VM v Azure Monitor
Tento článek poskytuje nápovědu řešení potíží s chybami setkat s rozšířením Log Analytics VM pro virtuální počítače Windows a Linuxu spuštěné na Microsoft Azure a navrhne řešení k jejich řešení.

Pokud chcete ověřit stav rozšíření, proveďte následující kroky na webu Azure Portal.

1. Přihlaste se k [Azure Portal](https://portal.azure.com).
2. Na webu Azure Portal klikněte na **Všechny služby**. V seznamu prostředků zadejte **virtuální počítače**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **virtuální počítače**.
3. V seznamu virtuálních počítačů vyhledejte a vyberte ji.
3. Na virtuálním počítači klikněte na **rozšíření**.
4. Ze seznamu zkontrolujte, jestli je povolené rozšíření Log Analytics, nebo ne.  Pro Linux je agent uveden jako **OMSAgentforLinux** a pro Windows, agent je uveden jako **MicrosoftMonitoringAgent**.

   ![Zobrazení rozšíření virtuálního počítače](./media/vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. Klikněte na rozšíření zobrazíte podrobnosti. 

   ![Podrobnosti o rozšíření virtuálního počítače](./media/vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Řešení potíží s rozšíření virtuálního počítače Windows Azure

Pokud rozšíření virtuálního počítače *Microsoft Monitoring Agent* neinstaluje nebo nevytváří sestavy, můžete tento problém vyřešit provedením následujících kroků.

1. Pomocí kroků v [článku KB 2965986](https://support.microsoft.com/kb/2965986#mt1)ověřte, zda je agent virtuálního počítače Azure nainstalovaný a funguje správně.
   * Můžete také zkontrolovat soubor protokolu agenta virtuálního počítače `C:\WindowsAzure\logs\WaAppAgent.log`
   * Pokud v protokolu neexistuje, není nainstalován agent virtuálního počítače.
   * [Instalace agenta virtuálního počítače Azure](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Zkontrolujte soubory protokolu rozšíření Microsoft Monitoring Agent VM v `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`
3. Ujistěte se, že virtuální počítač může spouštět skripty prostředí PowerShell
4. Ujistěte se, že oprávnění C:\Windows\temp nebyly změněny.
5. Zadáním následujícího příkazu v okně PowerShellu se zvýšenými oprávněními na virtuálním počítači zobrazte stav Microsoft Monitoring Agent `(New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
6. Zkontrolujte soubory protokolu instalace Microsoft Monitoring Agent v `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`

Další informace najdete v tématu [řešení potíží s rozšířeními systému Windows](../../virtual-machines/extensions/oms-windows.md).

## <a name="troubleshooting-linux-vm-extension"></a>Řešení potíží s rozšíření virtuálního počítače s Linuxem
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 
Pokud se neinstaluje nebo nehlásí rozšíření *Log Analytics Agent pro Linux* VM, můžete problém vyřešit provedením následujících kroků.

1. Pokud je stav rozšíření *neznámá* kontrola, jestli je agent virtuálního počítače Azure nainstalovaný a funguje správně, Projděte si soubor protokolu agenta virtuálního počítače `/var/log/waagent.log`
   * Pokud v protokolu neexistuje, není nainstalován agent virtuálního počítače.
   * [Instalace agenta virtuálního počítače Azure do virtuálních počítačů se systémem Linux](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. U dalších stavů, které nejsou v pořádku, zkontrolujte soubory protokolů rozšíření Log Analytics Agent pro Linux VM v `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` a `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`
3. Pokud je stav rozšíření v pořádku, ale data se neodesílají, zkontrolujte soubory protokolu Log Analytics agenta pro Linux v `/var/opt/microsoft/omsagent/log/omsagent.log`

Další informace najdete v tématu [řešení potíží s rozšířeními pro Linux](../../virtual-machines/extensions/oms-linux.md).

## <a name="next-steps"></a>Další kroky

Další pokyny k odstraňování potíží souvisejících s agentem Log Analytics pro Linux hostovanou na počítačích mimo Azure najdete v tématu [řešení potíží s agentem azure Log Analytics Linux](agent-linux-troubleshoot.md).  
