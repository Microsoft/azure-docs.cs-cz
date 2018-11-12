---
title: Ukázkový skript Azure PowerShellu – Vytvoření testovacího prostředí brány Azure Firewall
description: Ukázkový skript Azure PowerShellu – Vytvoření testovacího prostředí služby Azure Firewall
services: virtual-network
author: vhorne
ms.service: firewall
ms.devlang: powershell
ms.topic: sample
ms.date: 8/13/2018
ms.author: victorh
ms.openlocfilehash: 5100c5723cf3442520d969e3db6aa65439855551
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230875"
---
# <a name="create-an-azure-firewall-test-environment"></a>Vytvoření testovacího prostředí brány Azure Firewall

Tento ukázkový skript vytvoří bránu firewall a testovací síťové prostředí. Tato síť má jednu virtuální síť se třemi podsítěmi: *AzureFirewallSubnet*, *ServersSubnet* a *JumpboxSubnet*. Podsítě ServersSubnet a JumpboxSubnet mají každá jeden dvoujádrový Windows Server.

Brána firewall je v podsíti AzureFirewallSubnet a je nakonfigurovaná kolekcí pravidel aplikace s jediným pravidlem, které umožňuje přístup k www.microsoft.com.

Vytvoří se trasa definovaná uživatelem, která odkazuje síťový provoz z podsítě ServersSubnet přes bránu firewall, kde se používají její pravidla.

Skript můžete spustit ve službě Azure [Cloud Shell](https://shell.azure.com/powershell) nebo v místně nainstalovaném PowerShellu. 

Pokud používáte PowerShell místně, vyžaduje tento skript nejnovější verzi modulu AzureRM PowerShell (6.9.0 nebo novější). Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. 

Pokud potřebujete PowerShell upgradovat můžete použít modul `PowerShellGet`, který je integrovaný ve Windows 10 a Windows Serveru 2016.

> [!NOTE]
>V ostatních verzích Windows musíte modul `PowerShellGet` nejprve nainstalovat, než ho budete moct použít. Zda máte modul v systému nainstalovaný zjistíte příkazem `Get-Module -Name PowerShellGet -ListAvailable | Select-Object -Property Name,Version,Path`. Pokud je výstup prázdný, musíte nainstalovat nejnovější [Windows Management framework](https://www.microsoft.com/download/details.aspx?id=54616).

Další informace najdete v tématu [Instalace Azure PowerShell ve Windows pomocí PowerShellGet](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.4.0).

Každá přítomná instalace prostředí Azure PowerShell provedená instalačním programem webové platformy způsobí konflikt s instalací modulu PowerShellGet, takže ji musíte odstranit.

Pokud používáte PowerShell místně, nezapomeňte spustit příkaz `Connect-AzureRmAccount` pro vytvoření připojení k Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript


[!code-azurepowershell-interactive[main](../../../powershell_scripts/firewall/create-fw-test.ps1  "Create a firewall test environment")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky:

```powershell
Remove-AzureRmResourceGroup -Name AzfwSampleScriptEastUS -Force
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků, virtuální sítě a skupin zabezpečení sítě používá následující příkazy. Každý příkaz v následující tabulce odkazuje na příslušnou část dokumentace:

| Příkaz | Poznámky |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Vytvoří objekt konfigurace podsítě. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Vytvoří virtuální síť Azure a front-endovou podsíť. |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | Vytvoří pravidla zabezpečení pro přiřazení ke skupině zabezpečení sítě. |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) |Vytvoří pravidla NSG, která povolí nebo zablokují konkrétní porty v konkrétních podsítích. |
| [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) | Přidruží skupiny zabezpečení sítě k podsítím. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Vytvoří veřejnou IP adresu pro přístup k virtuálnímu počítači z internetu. |
| [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Vytvoří virtuální síťová rozhraní a připojí je k front-endové a back-endové podsíti virtuální sítě. |
| [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | Vytvoří konfiguraci virtuálního počítače. Tato konfigurace zahrnuje informace, jako je název virtuálního počítače, operační systém a přihlašovací údaje pro správu. Tato konfigurace se použije při vytváření virtuálního počítače. |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Vytvoří virtuální počítač. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Odebere skupinu prostředků a všechny prostředky, které obsahuje. |
|[New-AzureRmFirewall](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermfirewall?view=azurermps-6.9.0)| Vytvoří novou službu Azure Firewall.|
|[Get-AzureRmFirewall](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermfirewall?view=azurermps-6.9.0)|Získá objekt služby Azure Firewall.|
|[New-AzureRmFirewallApplicationRule](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermfirewallapplicationrule?view=azurermps-6.9.0)|Vytvoří nové pravidlo aplikace Azure Firewall.|
|[Set-AzureRmFirewall](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermfirewall?view=azurermps-6.9.0)|Potvrdí změny objektu služby Azure Firewall.|


## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

