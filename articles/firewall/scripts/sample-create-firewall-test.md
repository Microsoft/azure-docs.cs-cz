---
title: Vytvoření testovacího prostředí brány Azure Firewall
description: Tento ukázkový skript vytvoří bránu firewall a testovací síťové prostředí. Síť má jednu virtuální síť se třemi podsítěmi.
services: virtual-network
author: vhorne
ms.service: firewall
ms.devlang: powershell
ms.topic: sample
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 4158bc07373a2d0aa6fb6ceaf2dce62b50bb6bd7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94658362"
---
# <a name="create-an-azure-firewall-test-environment"></a>Vytvoření testovacího prostředí brány Azure Firewall

Tento ukázkový skript vytvoří bránu firewall a testovací síťové prostředí. Tato síť má jednu virtuální síť se třemi podsítěmi: *AzureFirewallSubnet*, *ServersSubnet* a *JumpboxSubnet*. Podsítě ServersSubnet a JumpboxSubnet mají každá jeden dvoujádrový Windows Server.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Brána firewall je ve AzureFirewallSubnet a je nakonfigurovaná pomocí kolekce pravidel aplikace s jedním pravidlem, které umožňuje přístup k `www.microsoft.com` .

Vytvoří se trasa definovaná uživatelem, která odkazuje síťový provoz z podsítě ServersSubnet přes bránu firewall, kde se používají její pravidla.

Skript můžete spustit ve službě Azure [Cloud Shell](https://shell.azure.com/powershell) nebo v místně nainstalovaném PowerShellu. 

Pokud spustíte prostředí PowerShell místně, vyžaduje tento skript Azure PowerShell. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. 

Pokud potřebujete PowerShell upgradovat můžete použít modul `PowerShellGet`, který je integrovaný ve Windows 10 a Windows Serveru 2016.

> [!NOTE]
>V ostatních verzích Windows musíte modul `PowerShellGet` nejprve nainstalovat, než ho budete moct použít. Zda máte modul v systému nainstalovaný zjistíte příkazem `Get-Module -Name PowerShellGet -ListAvailable | Select-Object -Property Name,Version,Path`. Pokud je výstup prázdný, musíte nainstalovat nejnovější [Windows Management framework](https://www.microsoft.com/download/details.aspx?id=54616).

Další informace najdete v tématu [instalace Azure PowerShell](/powershell/azure/install-Az-ps) .

Každá přítomná instalace prostředí Azure PowerShell provedená instalačním programem webové platformy způsobí konflikt s instalací modulu PowerShellGet, takže ji musíte odstranit.

Pokud používáte PowerShell místně, nezapomeňte spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript


[!code-azurepowershell-interactive[main](../../../powershell_scripts/firewall/create-fw-test.ps1  "Create a firewall test environment")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky:

```powershell
Remove-AzResourceGroup -Name AzfwSampleScriptEastUS -Force
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků, virtuální sítě a skupin zabezpečení sítě používá následující příkazy. Každý příkaz v následující tabulce odkazuje na příslušnou část dokumentace:

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Vytvoří objekt konfigurace podsítě. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Vytvoří virtuální síť Azure a front-endovou podsíť. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | Vytvoří pravidla zabezpečení pro přiřazení ke skupině zabezpečení sítě. |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |Vytvoří pravidla NSG, která povolí nebo zablokují konkrétní porty v konkrétních podsítích. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Přidruží skupiny zabezpečení sítě k podsítím. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Vytvoří veřejnou IP adresu pro přístup k virtuálnímu počítači z internetu. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Vytvoří virtuální síťová rozhraní a připojí je k front-endové a back-endové podsíti virtuální sítě. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Vytvoří konfiguraci virtuálního počítače. Tato konfigurace zahrnuje informace, jako je název virtuálního počítače, operační systém a přihlašovací údaje pro správu. Tato konfigurace se použije při vytváření virtuálního počítače. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Vytvoří virtuální počítač. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odebere skupinu prostředků a všechny prostředky, které obsahuje. |
|[New-AzFirewall](/powershell/module/az.network/new-azfirewall)| Vytvoří novou službu Azure Firewall.|
|[Get-AzFirewall](/powershell/module/az.network/get-azfirewall)|Získá objekt služby Azure Firewall.|
|[New-AzFirewallApplicationRule](/powershell/module/az.network/new-azfirewallapplicationrule)|Vytvoří nové pravidlo aplikace Azure Firewall.|
|[Set-AzFirewall](/powershell/module/az.network/set-azfirewall)|Potvrdí změny objektu služby Azure Firewall.|

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/).