---
title: Poradce při potížích s rozšířením virtuálních aplikací Azure Log Analytics
description: Popište příznaky, příčiny a řešení nejčastějších problémů s rozšířením virtuálního počítače Analýzy protokolů pro virtuální počítače Windows a Linux Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/06/2019
ms.openlocfilehash: e16531484505f055c1383aff5adb40518719d98a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80054580"
---
# <a name="troubleshooting-the-log-analytics-vm-extension-in-azure-monitor"></a>Řešení potíží s rozšířením Log Analytics pro virtuální počítače ve službě Azure Monitor
Tento článek poskytuje pomoc při řešení chyb, ke kterým může dojít s rozšířením virtuálního počítače analýzy protokolů pro virtuální počítače se systémem Windows a Linux, které běží v Microsoft Azure, a navrhuje možná řešení k jejich vyřešení.

Chcete-li ověřit stav rozšíření, proveďte následující kroky z webu Azure Portal.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Na webu Azure Portal klikněte na **Všechny služby**. Do seznamu prostředků zadejte **virtuální počítače**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Virtuální počítače**.
3. V seznamu virtuálních počítačů ho najděte a vyberte.
3. Ve virtuálním počítači klikněte na **Rozšíření**.
4. V seznamu zkontrolujte, jestli je povoleno rozšíření Log Analytics nebo ne.  Pro Linux je agent uveden jako **OMSAgentforLinux** a pro Windows je agent uveden jako **MicrosoftMonitoringAgent**.

   ![Zobrazení rozšíření virtuálního virtuálního virtuálního virtuálního pole](./media/vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. Kliknutím na rozšíření zobrazíte podrobnosti. 

   ![Podrobnosti o rozšíření virtuálního účtu](./media/vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Řešení potíží s rozšířením virtuálního počítače s Windows azure

Pokud rozšíření virtuálního zařízení *Microsoft Monitoring Agent* není instalace nebo vykazování, můžete provést následující kroky k řešení problému.

1. Zkontrolujte, jestli je agent virtuálního počítače Azure nainstalovaný a funguje správně pomocí kroků v [kb 2965986](https://support.microsoft.com/kb/2965986#mt1).
   * Můžete také zkontrolovat soubor protokolu agenta virtuálního aplikace`C:\WindowsAzure\logs\WaAppAgent.log`
   * Pokud protokol neexistuje, není nainstalován agent virtuálního zařízení.
   * [Instalace agenta virtuálních počítačových společností Azure](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Projděte si soubory protokolu rozšíření virtuálních aplikací Microsoft Monitoring Agent VM`C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`
3. Ujistěte se, že virtuální počítač může spouštět skripty PowerShellu
4. Zajištění, že oprávnění pro C:\Windows\temp nebyla změněna
5. Zobrazení stavu agenta monitorování společnosti Microsoft zadáním následujícího příkazu do okna prostředí PowerShell se zvýšenými oprávněními ve virtuálním počítači`(New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
6. Projděte si soubory protokolu instalace agenta monitorování společnosti Microsoft`C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`

Další informace naleznete v [tématu řešení potíží s rozšířeními systému Windows](../../virtual-machines/extensions/oms-windows.md).

## <a name="troubleshooting-linux-vm-extension"></a>Poradce při potížích s rozšířením virtuálního počítače s Linuxem
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 
Pokud *agent Log Analytics pro* rozšíření virtuálního počítače s Linuxem není instalace nebo vytváření sestav, můžete provést následující kroky k řešení problému.

1. Pokud je stav rozšíření *Neznámý zkontrolujte,* jestli je agent virtuálního počítače Azure nainstalovaný a funguje správně, kontrolou souboru protokolu agenta virtuálního počítače`/var/log/waagent.log`
   * Pokud protokol neexistuje, není nainstalován agent virtuálního zařízení.
   * [Instalace agenta virtuálních počítačích Azure na virtuální počítače s Linuxem](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Další stavy není v pořádku, zkontrolujte Agent Log Analytics `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` pro rozšíření virtuálního počítače s Linuxem protokoly soubory a`/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`
3. Pokud je stav rozšíření v pořádku, ale data nejsou odesílána zkontrolujte agenta Log Analytics pro soubory protokolu Linux`/var/opt/microsoft/omsagent/log/omsagent.log`

Další informace naleznete v [tématu řešení potíží s linuxovými rozšířeními](../../virtual-machines/extensions/oms-linux.md).

## <a name="next-steps"></a>Další kroky

Další pokyny pro řešení potíží související s agentem Log Analytics pro Linux hostovaným v počítačích mimo Azure najdete [v tématu Poradce při potížích s agentem Azure Log Analytics Linux .](agent-linux-troubleshoot.md)  
