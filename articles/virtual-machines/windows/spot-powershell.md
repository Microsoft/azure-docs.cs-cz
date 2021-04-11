---
title: Použití PowerShellu k nasazení Virtual Machines na místě Azure
description: Naučte se používat Azure PowerShell k nasazení Virtual Machines na místě Azure, abyste ušetřili náklady.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 9a2ad2eb197af613919efa4414da1759cd47e2e7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802739"
---
# <a name="deploy-azure-spot-virtual-machines-using-azure-powershell"></a>Nasazení služby Azure spot Virtual Machines pomocí Azure PowerShell


Použití [Azure Spot Virtual Machines](../spot-vms.md) umožňuje využít výhod naší nevyužité kapacity s významnou úsporou nákladů. V jakémkoli okamžiku, kdy Azure potřebuje kapacitu zpátky, vyřadí infrastruktura Azure místo Virtual Machines Azure. Proto jsou Azure spot Virtual Machines Skvělé pro úlohy, které mohou zpracovávat přerušení, jako jsou úlohy dávkového zpracování, vývojové a testovací prostředí, velké výpočetní úlohy a další.

Ceny za Azure na místě Virtual Machines jsou proměnné na základě oblastí a SKU. Další informace najdete v tématu ceny virtuálních počítačů pro [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) a [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Další informace o nastavení maximální ceny najdete v tématu [Azure Spot Virtual Machines – ceny](../spot-vms.md#pricing).

Máte možnost nastavit maximální cenu, kterou jste ochotni zaplatit za hodinu pro virtuální počítač. Maximální cena pro virtuální počítač se službou Azure se dá nastavit v amerických dolarech (USD), a to s využitím až 5 desetinných míst. Hodnota by měla být například `0.98765` maximální cena $0,98765 USD za hodinu. Pokud nastavíte maximální cenu `-1` , nebude se virtuální počítač vyřadit podle ceny. Cena za virtuální počítač bude aktuální cena za bod nebo cena za standardní virtuální počítač, který je stále menší, pokud je dostupná kapacita a kvóta.


## <a name="create-the-vm"></a>Vytvoření virtuálního počítače

Vytvořte spotVM pomocí [New-AzVmConfig](/powershell/module/az.compute/new-azvmconfig) pro vytvoření konfigurace. Zahrňte `-Priority Spot` a nastavte `-MaxPrice` buď na:
- `-1` virtuální počítač proto nebude vyřazení na základě ceny.
- hodnota dolaru, maximálně 5 číslic. Například `-MaxPrice .98765` znamená, že se virtuální počítač bude uvolnit, jakmile cena za spotVM bude přibližně o 98765 za hodinu.


Tento příklad vytvoří spotVM, který se nevrátí na základě cen (jenom v případě, že Azure potřebuje kapacitu zpátky). Zásada vyřazení je nastavená tak, aby se virtuální počítač nastavil jako neplatný, aby se mohl později restartovat. Pokud chcete odstranit virtuální počítač a příslušný disk, když je virtuální počítač vyřazený, nastavte `-EvictionPolicy` na `Delete` v `New-AzVMConfig` .


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

# Create a virtual machine configuration and set this to be an Azure Spot Virtual Machine

$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1 -Priority "Spot" -MaxPrice -1 -EvictionPolicy Deallocate | `
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

## <a name="simulate-an-eviction"></a>Simulace vyřazení

Můžete simulovat vyřazení virtuálních počítačů se systémem Azure pomocí REST, PowerShellu nebo rozhraní příkazového řádku a otestovat tak, jak dobře bude aplikace reagovat na náhlé vyřazení.

Ve většině případů budete chtít použít REST API [Virtual Machines-simulovat vyřazení](/rest/api/compute/virtualmachines/simulateeviction) , které vám pomůže s automatizovaným testováním aplikací. Pro REST `Response Code: 204` to znamená, že simulované vyřazení bylo úspěšné. Simulované vyřazení můžete kombinovat s [naplánovanou službou Event Service](scheduled-events.md), abyste mohli automatizovat, jak bude vaše aplikace reagovat po vyřazení virtuálního počítače.

Pokud chcete zobrazit naplánované události v akci, podívejte se na [Azure pátek – s využitím azure Scheduled Events pro přípravu údržby virtuálních počítačů](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance).


### <a name="quick-test"></a>Rychlý test

Pro rychlý test k zobrazení, jak bude simulovaná vyřazení fungovat, si projdeme dotazování naplánované služby událostí, abyste viděli, co vypadá při simulaci vyřazení pomocí prostředí PowerShell.

Naplánovaná služba událostí je pro vaši službu povolená, když poprvé vytvoříte žádost o události. 

Vzdáleně na svůj virtuální počítač a otevřete příkazový řádek. 

Do příkazového řádku na svém VIRTUÁLNÍm počítači zadejte:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Tato první odpověď může trvat až 2 minuty. Od této chvíle by se měly zobrazit výstup téměř okamžitě.

Z počítače s nainstalovanou modulem AZ PowerShell (jako je váš místní počítač) Simulujte vyřazení pomocí rutiny [set-AzVM](https://docs.microsoft.com/powershell/module/az.compute/set-azvm). Název skupiny prostředků a název virtuálního počítače nahraďte vlastními. 

```azurepowershell-interactive
Set-AzVM -ResourceGroupName "mySpotRG" -Name "mySpotVM" -SimulateEviction
```

Výstup odpovědi bude v `Status: Succeeded` případě úspěšného provedení žádosti.

Rychle se vraťte ke vzdálenému připojení k virtuálnímu počítači na místě a znovu spusťte dotaz na Scheduled Events koncový bod. Opakujte následující příkaz, dokud nezískáte výstup, který obsahuje další informace:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Když naplánovaná událost získá oznámení vyřazení, dostanete odpověď, která bude vypadat podobně jako v tomto příkladu:

```output
{"DocumentIncarnation":1,"Events":[{"EventId":"A123BC45-1234-5678-AB90-ABCDEF123456","EventStatus":"Scheduled","EventType":"Preempt","ResourceType":"VirtualMachine","Resources":["myspotvm"],"NotBefore":"Tue, 16 Mar 2021 00:58:46 GMT","Description":"","EventSource":"Platform"}]}
```

Můžete vidět, že `"EventType":"Preempt"` a prostředek je prostředek virtuálního počítače `"Resources":["myspotvm"]` . 

Můžete také zjistit, jestli se virtuální počítač vyřadí, a to tak, že zkontrolujete jeho `"NotBefore"` hodnotu. Virtuální počítač nebude vyřazený před uplynutím časového limitu `NotBefore` . to znamená, že vaše okno vaší aplikace bude řádně uzavřeno.


## <a name="next-steps"></a>Další kroky

Virtuální počítač se službou Azure se dá vytvořit také pomocí [Azure CLI](../linux/spot-cli.md), [portálu](../spot-portal.md) nebo [šablony](../linux/spot-template.md).

Dotaz na aktuální informace o cenách pomocí [API maloobchodních cen Azure](/rest/api/cost-management/retail-prices/azure-retail-prices) najdete v informacích o cenách virtuálních počítačů Azure na místě. `meterName`A `skuName` bude obsahovat oba `Spot` .

Pokud dojde k chybě, přečtěte si [kódy chyb](../error-codes-spot.md).
