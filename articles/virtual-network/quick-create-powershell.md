---
title: "Vytvoření virtuální sítě v Azure – prostředí PowerShell | Microsoft Docs"
description: "Rychle se Naučte se vytvořit virtuální síť pomocí prostředí PowerShell. Virtuální síť umožňuje mnoho typů prostředků Azure, aby soukromě vzájemně komunikovat."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: 
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/25/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 22fcdbda85f3ea336c3926e04d408935ed069c25
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="create-a-virtual-network-using-powershell"></a>Vytvoření virtuální sítě pomocí prostředí PowerShell

V tomto článku zjistěte, jak vytvořit virtuální síť. Po vytvoření virtuální sítě, nasadíte dva virtuální počítače do virtuální sítě k otestování privátní síti komunikace mezi nimi.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

Pokud si zvolíte instalaci a použití prostředí PowerShell místně, v tomto článku vyžaduje prostředí AzureRM PowerShell verze modulu 5.1.1 nebo novější. Chcete-li najít nainstalovanou verzi, spusťte ` Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzureRmAccount` pro vytvoření připojení k Azure.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků Azure pomocí příkazu [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*. Všechny prostředky Azure jsou vytvořeny v rámci Azure umístění (nebo oblasti).

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť pomocí rutiny [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). Následující příklad vytvoří výchozí virtuální síť s názvem *myVirtualNetwork* v *EastUS* umístění:

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/24
```

Všechny virtuální sítě mít jeden nebo více předpony adres, které jsou jim přiřazeny. Adresní prostor je zadat v notaci CIDR. Místo adres 10.0.0.0/24 zahrnuje 10.0.0.0-10.0.0.254. Virtuální sítě má nula nebo více podsítí v nich. Prostředky jsou nasazeny do podsíť ve virtuální síti. 

Vytvoření konfigurací podsítě s [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Všechny podsítě mají předponu adresy, která existuje v rámci předponu adresy virtuální sítě. V tomto příkladu se vytvoří konfigurace podsítě se stejnou předponou adresy jako předponu adresy virtuální sítě:

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Když předpona adresy podsítě zahrnuje 10.0.0.0-10.0.0.254, jsou k dispozici, pouze 10.0.0.4-10.0.0.254 adresy, protože první čtyři adresy (0-3) a poslední adresa v jednotlivých podsítích si vyhrazuje Azure. Vzhledem k tomu, že předpona adresy podsítě je stejný jako předponu adresy virtuální sítě, může existovat jenom jednu podsíť v této virtuální síti.

Zapsat konfiguraci podsítě virtuální sítě s [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), která vytvoří podsíť:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="test-network-communication"></a>Test síťové komunikace

Virtuální síť umožňuje několik typů prostředků Azure, aby soukromě vzájemně komunikovat. Virtuální počítač je jeden typ prostředku, který můžete nasadit do virtuální sítě. Vytvořte dva virtuální počítače ve virtuální síti, abyste mohli ověřit privátní komunikaci mezi nimi později.

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvoření virtuálního počítače s [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Při spuštění tohoto kroku se zobrazí výzva k zadání přihlašovacích údajů. Hodnoty, které zadáte, se nakonfigurují jako uživatelské jméno a heslo pro virtuální počítač. Umístění, které virtuální počítač je vytvořen v musí být stejné umístění, které virtuální sítě v existuje. Virtuální počítač nemusí být ve stejné skupině prostředků jako virtuální počítač, když je v tomto článku. `-AsJob` Parametr umožňuje příkaz ke spuštění na pozadí, takže můžete pokračovat v dalším úkolem.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
```

Výstup podobné výstupu v následujícím příkladu se vrátí, a Azure spustí vytvoření virtuálního počítače na pozadí.

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmVM     
```

Azure DHCP automaticky přiřadí 10.0.0.4 k virtuálnímu počítači během vytváření, protože se jedná o první dostupnou adresu v *výchozí* podsítě.

Vytvořte druhý virtuální počítač. 

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```
Virtuální počítač trvá několik minut pro vytvoření. Po vytvoření vrátí výstupní o vytvořený virtuální počítač Azure. I když není ve vrácené výstupu Azure přiřazené *10.0.0.5* k *Můjvp2* virtuální počítač, protože se jedná o další dostupnou adresu v podsíti.

### <a name="connect-to-a-virtual-machine"></a>Připojit k virtuálnímu počítači

Použití [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) příkaz vrátí veřejnou IP adresu virtuálního počítače. Azure přiřadí veřejné, Internet směrovatelné IP adresy pro každý virtuální počítač, ve výchozím nastavení. Veřejná IP adresa je přiřazena k virtuálnímu počítači z [fondu adres přiřazených každé oblasti Azure](https://www.microsoft.com/download/details.aspx?id=41653). Sice Azure nezná, která veřejná IP adresa je přiřazena k virtuálnímu počítači, ale operační systém spuštěný ve virtuálním počítači nemá žádné informace o tom všechny veřejné IP adresy přiřazené. Následující příklad vrací veřejnou IP adresu *myVm1* virtuálního počítače:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -Name myVm1 -ResourceGroupName myResourceGroup | Select IpAddress
```

Chcete-li vytvořit relaci vzdálené plochy s použijte následující příkaz *myVm1* virtuálního počítače z místního počítače. Nahraďte `<publicIpAddress>` s IP adresou vrácená z předchozí příkaz.

```
mstsc /v:<publicIpAddress>
```

Soubor Remote Desktop Protocol (.rdp) je vytvořen, stažena do počítače a otevřít. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače a pak klikněte na tlačítko **OK**. Během procesu přihlášení se může zobrazit upozornění certifikátu. Klikněte na **Ano** nebo **Pokračovat** a pokračujte v připojení.

### <a name="validate-communication"></a>Ověření komunikace

Probíhá pokus o příkaz ping systému Windows virtuálního počítače selže, protože ping není přes bránu Windows firewall povolena ve výchozím nastavení. Chcete-li povolit příkaz ping na *myVm1*, zadejte následující příkaz z příkazového řádku:

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

K ověření komunikace s *Můjvp2*, zadejte následující příkaz z příkazového řádku *myVm1* virtuálního počítače. Zadejte přihlašovací údaje, které jste použili při vytvoření virtuálního počítače a pak dokončete připojení:

```
mstsc /v:myVm2
```

Připojení ke vzdálené ploše je úspěšné, protože oba virtuální počítače mají privátní IP adresy přiřazené z *výchozí* podsítě a protože je otevřeno přes bránu Windows firewall ve výchozím nastavení vzdálené plochy. Budete moci připojit k *Můjvp2* podle názvu hostitele protože Azure automaticky poskytuje překlad názvů DNS pro všechny hostitele v rámci virtuální sítě. Z příkazového řádku příkaz ping Moje *myVm1*, z *Můjvp2*.

```
ping myvm1
```

Příkaz ping je úspěšné, protože je povolené prostřednictvím brány Windows firewall na *myVm1* virtuálního počítače v předchozím kroku. Pokud chcete potvrdit odchozí komunikaci k Internetu, zadejte následující příkaz:

```
ping bing.com
```

Obdržíte čtyři odpovědi od vyhledávače bing.com. Ve výchozím nastavení může komunikovat jakéhokoli virtuálního počítače ve virtuální síti odchozí k Internetu.

Ukončete relaci vzdálené plochy. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete, můžete použít [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) příkaz k odebrání skupiny prostředků a všechny prostředky, které obsahuje:

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další postup

V tomto článku jste nasadili výchozí virtuální síť s jednou podsítí. Naučte se vytvářet vlastní virtuální síť s více podsítěmi, nadále kurz pro vytvoření vlastní virtuální sítě.

> [!div class="nextstepaction"]
> [Vytvoření vlastní virtuální sítě](virtual-networks-create-vnet-arm-ps.md)
