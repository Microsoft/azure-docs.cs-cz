---
title: Použití PowerShellu k nasazení virtuálních počítačů Azure na místě
description: Naučte se používat Azure PowerShell k nasazení virtuálních počítačů na místě za účelem úspory nákladů.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/11/2020
ms.author: cynthn
ms.openlocfilehash: 17186d1d7b50ea872dc47eca8c2c4491787d2a38
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77158940"
---
# <a name="preview-deploy-spot-vms-using-azure-powershell"></a>Verze Preview: nasazení virtuálních počítačů s přímým použitím Azure PowerShell


Použití [přímých virtuálních počítačů](spot-vms.md) vám umožní využít výhod naší nevyužité kapacity s významnou úsporou nákladů. V jakémkoli okamžiku, kdy Azure potřebuje kapacitu zpátky, vyřadí infrastruktura Azure virtuální počítače na místě. Proto jsou virtuální počítače Skvělé pro úlohy, které mohou zpracovávat přerušení, jako jsou úlohy dávkového zpracování, vývojové a testovací prostředí, velké výpočetní úlohy a další.

Ceny pro virtuální počítače na místě jsou proměnné na základě oblastí a SKU. Další informace najdete v tématu ceny virtuálních počítačů pro [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) a [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Další informace o nastavení maximální ceny najdete v tématu [virtuální počítače – ceny](spot-vms.md#pricing).

Máte možnost nastavit maximální cenu, kterou jste ochotni zaplatit za hodinu pro virtuální počítač. Maximální cena za virtuální počítač na místě se dá nastavit v amerických dolarech (USD), a to s využitím až 5 desetinných míst. Například hodnota `0.98765`by byla maximální cena $0,98765 USD za hodinu. Pokud nastavíte maximální cenu, která se má `-1`, virtuální počítač se nevyřadí na základě ceny. Cena za virtuální počítač bude aktuální cena za bod nebo cena za standardní virtuální počítač, který je stále menší, pokud je dostupná kapacita a kvóta.

> [!IMPORTANT]
> Instance přímých instancí jsou momentálně ve verzi Public Preview.
> Tato verze Preview se nedoporučuje pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>



## <a name="create-the-vm"></a>Vytvořte virtuální počítač.

Vytvořte spotVM pomocí [New-AzVmConfig](/powershell/module/az.compute/new-azvmconfig) pro vytvoření konfigurace. Přidejte `-Priority Spot` a nastavte `-MaxPrice` buď na:
- `-1` tak, že se virtuální počítač nevyřadí na základě ceny.
- hodnota dolaru, maximálně 5 číslic. `-MaxPrice .98765` například znamená, že se virtuální počítač bude uvolnit, jakmile bude cena za spotVM přibližně $. 98765 za hodinu.


Tento příklad vytvoří spotVM, který se nevrátí na základě cen (jenom v případě, že Azure potřebuje kapacitu zpátky).

```azurepowershell-interactive
$resourceGroup = "mySpotRG"
$location = "eastus"
$vmName = "mySpotVM"
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."
New-AzResourceGroup -Name $resourceGroup -Location $location
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup `
   -Location $location -Name MYvNET -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration and set this to be a Spot VM

$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1 -Priority "Spot" -MaxPrice -1| `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzVMNetworkInterface -Id $nic.Id

New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

Po vytvoření virtuálního počítače se můžete dotazovat, aby se zobrazila maximální cena za všechny virtuální počítače ve skupině prostředků.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName $resourceGroup | `
   Select-Object Name,@{Name="maxPrice"; Expression={$_.BillingProfile.MaxPrice}}
```

## <a name="next-steps"></a>Další kroky

Můžete také vytvořit virtuální počítač s přímým použitím rozhraní příkazového [řádku Azure](../linux/spot-cli.md) nebo [šablony](../linux/spot-template.md).

Pokud dojde k chybě, přečtěte si [kódy chyb](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).