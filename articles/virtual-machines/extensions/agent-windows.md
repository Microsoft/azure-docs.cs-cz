---
title: Přehled agenta virtuálního počítače Azure
description: Přehled agenta virtuálního počítače Azure
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
ms.author: amjads
author: amjads1
ms.collection: windows
ms.date: 07/20/2019
ms.openlocfilehash: 33db214013111b0dd4540a1b1d2947b7d1854db9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104607479"
---
# <a name="azure-virtual-machine-agent-overview"></a>Přehled agenta virtuálního počítače Azure
Agent virtuálního počítače Microsoft Azure (agent virtuálního počítače) je zabezpečený a odlehčený proces, který spravuje interakci virtuálních počítačů s řadičem prostředků infrastruktury Azure. Agent virtuálního počítače má primární roli při povolování a provádění rozšíření virtuálních počítačů Azure. Rozšíření virtuálních počítačů umožňují konfiguraci po nasazení virtuálního počítače, jako je instalace a konfigurace softwaru. Rozšíření virtuálních počítačů také umožňují funkce pro obnovení, jako je resetování hesla pro správu virtuálního počítače. Bez agenta virtuálního počítače Azure nejde spustit rozšíření virtuálních počítačů.

Tento článek podrobně popisuje instalaci a detekci agenta virtuálního počítače Azure.

## <a name="install-the-vm-agent"></a>Instalace agenta virtuálního počítače

### <a name="azure-marketplace-image"></a>Obrázek Azure Marketplace

Agent virtuálního počítače Azure se ve výchozím nastavení instaluje na libovolný virtuální počítač s Windows nasazený z bitové kopie Azure Marketplace. Když nasadíte Azure Marketplace image z portálu, PowerShellu, rozhraní příkazového řádku nebo šablony Azure Resource Manager, nainstaluje se taky agent virtuálního počítače Azure.

Balíček agenta hosta systému Windows je rozdělen do dvou částí:

- Agent zřizování (PA)
- Agent hosta systému Windows (křídlo)

Pokud chcete spustit virtuální počítač, musíte mít na virtuálním počítači nainstalovanou službu PA, ale není potřeba ji instalovat. V době nasazení virtuálního počítače můžete vybrat možnost neinstalovat křídlo. Následující příklad ukazuje, jak vybrat možnost *provisionVmAgent* pomocí šablony Azure Resource Manager:

```json
"resources": [{
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2016-04-30-preview",
"location": "[parameters('location')]",
"dependsOn": ["[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"],
"properties": {
    "osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "adminPassword": "[parameters('adminPassword')]",
    "windowsConfiguration": {
        "provisionVmAgent": "false"
}
```

Pokud nemáte nainstalované agenty, nemůžete použít některé služby Azure, například Azure Backup nebo zabezpečení Azure. Tyto služby vyžadují instalaci rozšíření. Pokud jste virtuální počítač nasadili bez křídla, můžete nainstalovat nejnovější verzi agenta později.

### <a name="manual-installation"></a>Ruční instalace
Agenta virtuálního počítače s Windows je možné ručně nainstalovat pomocí balíčku Instalační služby systému Windows. Ruční instalace může být nutná při vytváření vlastní image virtuálního počítače, která je nasazena do Azure. Chcete-li ručně nainstalovat agenta virtuálního počítače s Windows, [Stáhněte si instalační program agenta virtuálního počítače](https://go.microsoft.com/fwlink/?LinkID=394789). Můžete také vyhledat konkrétní verzi ve [verzích agentů virtuálních počítačů s Windows IaaS na GitHubu](https://github.com/Azure/WindowsVMAgent/releases). Agent virtuálního počítače je podporovaný v systému Windows Server 2008 (64 bitů) a novějším.

> [!NOTE]
> Možnost AllowExtensionOperations je důležité aktualizovat po ruční instalaci VMAgent na virtuální počítač, který byl nasazený z image bez povolení ProvisionVMAgent.

```powershell
$vm.OSProfile.AllowExtensionOperations = $true
$vm | Update-AzVM
```

### <a name="prerequisites"></a>Požadavky

- Agent virtuálního počítače s Windows potřebuje ke spuštění aspoň Windows Server 2008 SP2 (64-bit) a .NET Framework 4,0. Podívejte [se na podporu minimálních verzí pro agenty virtuálních počítačů v Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

- Ujistěte se, že váš virtuální počítač má přístup k IP adrese 168.63.129.16. Další informace najdete v tématu [co je IP adresa 168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md).

- Zajistěte, aby byl v hostovaném virtuálním počítači povolený protokol DHCP. Tato možnost je nutná k získání adresy hostitele nebo prostředků infrastruktury z protokolu DHCP pro agenta virtuálního počítače IaaS a rozšíření pro fungování. Pokud potřebujete statickou privátní IP adresu, měli byste ji nakonfigurovat přes Azure Portal nebo PowerShell a zajistěte, aby byla ve virtuálním počítači povolená možnost DHCP. [Přečtěte si další informace](../../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) o nastavení statické IP adresy pomocí PowerShellu.


## <a name="detect-the-vm-agent"></a>Zjištění agenta virtuálního počítače

### <a name="powershell"></a>PowerShell

Modul Azure Resource Manager PowerShellu se dá použít k načtení informací o virtuálních počítačích Azure. Pokud chcete zobrazit informace o virtuálním počítači, jako je stav zřizování agenta virtuálního počítače Azure, použijte [příkaz Get-AzVM](/powershell/module/az.compute/get-azvm):

```powershell
Get-AzVM
```

Následující zhuštěný příklad výstupu ukazuje vlastnost *ProvisionVMAgent* vnořenou v rámci `OSProfile` . Tato vlastnost slouží k určení, jestli je na virtuálním počítači nasazený agent virtuálního počítače:

```powershell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

Pomocí následujícího skriptu můžete vracet stručný seznam názvů virtuálních počítačů a stav agenta virtuálního počítače:

```powershell
$vms = Get-AzVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Ruční zjišťování

Když se přihlásíte k virtuálnímu počítači s Windows, můžete ke kontrole spuštěných procesů použít Správce úloh. Pokud chcete zkontrolovat agenta virtuálního počítače Azure, otevřete Správce úloh, klikněte na kartu *Podrobnosti* a vyhledejte název procesu **WindowsAzureGuestAgent.exe**. Přítomnost tohoto procesu indikuje, že je agent virtuálního počítače nainstalovaný.


## <a name="upgrade-the-vm-agent"></a>Upgrade agenta virtuálního počítače
Agent virtuálního počítače Azure pro Windows se automaticky upgraduje na Image nasazené z Azure Marketplace. Když se do Azure nasadí nové virtuální počítače, dostanou nejnovějšího agenta virtuálního počítače při zřizování virtuálních počítačů. Pokud jste agenta nainstalovali ručně nebo nasazujete vlastní image virtuálních počítačů, budete muset ručně aktualizovat, aby se při vytváření image zahrnul nový agent virtuálního počítače.

## <a name="windows-guest-agent-automatic-logs-collection"></a>Kolekce automatických protokolů agenta hosta systému Windows
Agent hosta systému Windows obsahuje funkci pro automatické shromáždění některých protokolů. Tato funkce je řadičem procesu CollectGuestLogs.exe. Existuje jak pro PaaS Cloud Services, tak pro IaaS Virtual Machines a jejím cílem je rychle & automaticky shromažďovat některé diagnostické protokoly z virtuálního počítače, aby se mohly použít pro offline analýzu. Shromážděné protokoly jsou protokoly událostí, protokoly operačního systému, protokoly Azure a některé klíče registru. Vytvoří soubor ZIP, který se přenese na hostitele virtuálního počítače. Tento soubor ZIP si pak můžete prověřit technickými týmy a odborníky na podporu a prozkoumat problémy na žádost zákazníka, který vlastní virtuální počítač.

## <a name="guest-agent-and-osprofile-certificates"></a>Agent hosta a certifikáty OSProfile
Agent virtuálního počítače Azure zodpovídá za instalaci certifikátů, na které odkazuje virtuální počítač `OSProfile` nebo sada škálování virtuálního počítače. Pokud tyto certifikáty z konzoly MMC certifikáty v rámci hostovaného virtuálního počítače odeberete ručně, očekává se, že Agent hosta je přidá zpátky.
Chcete-li certifikát trvale odebrat, bude nutné jej odebrat z `OSProfile` portálu a pak jej odebrat z hostovaného operačního systému.

U virtuálního počítače odeberte certifikáty z nástroje pomocí [Remove-AzVMSecret]() `OSProfile` .

Další informace o certifikátech sady škálování virtuálních počítačů najdete v tématu [Virtual Machine Scale Sets-návody odebrání zastaralých certifikátů?](../../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-remove-deprecated-certificates)


## <a name="next-steps"></a>Další kroky
Další informace o rozšíření virtuálních počítačů najdete v tématu [Přehled rozšíření a funkcí virtuálních počítačů Azure](overview.md).
