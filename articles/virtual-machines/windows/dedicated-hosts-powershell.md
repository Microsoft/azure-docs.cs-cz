---
title: Nasazení vyhrazených hostitelů Azure pomocí Azure PowerShell
description: Nasaďte virtuální počítače na vyhrazené hostitele pomocí Azure PowerShell.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure
ms.date: 08/01/2019
ms.author: cynthn
ms.openlocfilehash: 30d15970b00a81ab85cdb85d2c0a27ee23ed1b92
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79130305"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-powershell"></a>Nasazení virtuálních počítačů na vyhrazené hostitele pomocí Azure PowerShell

Tento článek vás provede procesem vytvoření [vyhrazeného hostitele](dedicated-hosts.md) Azure pro hostování virtuálních počítačů. 

Ujistěte se, že máte nainstalovanou verzi Azure PowerShell 2.8.0 nebo novější a že jste k účtu Azure přihlášení pomocí `Connect-AzAccount`. 

## <a name="limitations"></a>Omezení

- Sady škálování virtuálních počítačů se na vyhrazených hostitelích aktuálně nepodporují.
- Typy velikosti a hardwaru, které jsou dostupné pro vyhrazené hostitele, se v jednotlivých oblastech liší. Další informace najdete na [stránce s cenami](https://aka.ms/ADHPricing) hostitele.

## <a name="create-a-host-group"></a>Vytvoření hostitelské skupiny

**Skupina hostitelů** je prostředek, který představuje kolekci vyhrazených hostitelů. Vytvoříte skupinu hostitelů v oblasti a zóně dostupnosti a přidáte do ní hostitele. Při plánování vysoké dostupnosti jsou k dispozici další možnosti. U vyhrazených hostitelů můžete použít jednu z následujících možností: 
- Rozsah napříč několika zónami dostupnosti. V takovém případě je nutné mít skupinu hostitelů v každé z zón, které chcete použít.
- Rozložit napříč několika doménami selhání, které jsou namapované na fyzické racky. 
 
V obou případech je nutné zadat počet domén selhání pro skupinu hostitelů. Pokud nechcete rozsah domén selhání ve skupině, použijte počet domén selhání 1. 

Můžete se také rozhodnout použít jak zóny dostupnosti, tak i domény selhání. Tento příklad vytvoří skupinu hostitelů v zóně 1 se dvěma doménami selhání. 


```powershell
$rgName = "myDHResourceGroup"
$location = "East US"

New-AzResourceGroup -Location $location -Name $rgName
$hostGroup = New-AzHostGroup `
   -Location $location `
   -Name myHostGroup `
   -PlatformFaultDomain 2 `
   -ResourceGroupName $rgName `
   -Zone 1
```

## <a name="create-a-host"></a>Vytvoření hostitele

Nyní vytvoříme vyhrazeného hostitele ve skupině hostitelů. Kromě názvu pro hostitele je nutné zadat SKU pro hostitele. SKU hostitele zachytí podporovanou řadu virtuálních počítačů a také generování hardwaru pro vyhrazeného hostitele.

Další informace o SKU a cenách hostitelů najdete v tématu [ceny za vyhrazené hostitele Azure](https://aka.ms/ADHPricing).

Pokud pro skupinu hostitelů nastavíte počet domén selhání, budete požádáni o zadání domény selhání pro hostitele. V tomto příkladu nastavíme doménu selhání pro hostitele na 1.


```powershell
$dHost = New-AzHost `
   -HostGroupName $hostGroup.Name `
   -Location $location -Name myHost `
   -ResourceGroupName $rgName `
   -Sku DSv3-Type1 `
   -AutoReplaceOnFailure 1 `
   -PlatformFaultDomain 1
```

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

Vytvořte virtuální počítač na vyhrazeném hostiteli. 

Pokud jste při vytváření skupiny hostitelů zadali zónu dostupnosti, budete při vytváření virtuálního počítače muset použít stejnou zónu. V tomto příkladu, protože je naše skupina hostitelů v zóně 1, musíme vytvořit virtuální počítač v zóně 1.  


```powershell
$cred = Get-Credential
New-AzVM `
   -Credential $cred `
   -ResourceGroupName $rgName `
   -Location $location `
   -Name myVM `
   -HostId $dhost.Id `
   -Image Win2016Datacenter `
   -Zone 1 `
   -Size Standard_D4s_v3
```

> [!WARNING]
> Pokud vytvoříte virtuální počítač na hostiteli, který nemá dostatek prostředků, vytvoří se virtuální počítač ve stavu selhání. 

## <a name="check-the-status-of-the-host"></a>Zkontroluje stav hostitele.

Můžete kontrolovat stav hostitele a počet virtuálních počítačů, které můžete nasadit na hostitele pomocí [GetAzHost](/powershell/module/az.compute/get-azhost) s parametrem `-InstanceView`.

```
Get-AzHost `
   -ResourceGroupName $rgName `
   -Name myHost `
   -HostGroupName $hostGroup.Name `
   -InstanceView
```

Výstup bude vypadat nějak takto:

```
ResourceGroupName      : myDHResourceGroup
PlatformFaultDomain    : 1
AutoReplaceOnFailure   : True
HostId                 : 12345678-1234-1234-abcd-abc123456789
ProvisioningTime       : 7/28/2019 5:31:01 PM
ProvisioningState      : Succeeded
InstanceView           : 
  AssetId              : abc45678-abcd-1234-abcd-123456789abc
  AvailableCapacity    : 
    AllocatableVMs[0]  : 
      VmSize           : Standard_D2s_v3
      Count            : 32
    AllocatableVMs[1]  : 
      VmSize           : Standard_D4s_v3
      Count            : 16
    AllocatableVMs[2]  : 
      VmSize           : Standard_D8s_v3
      Count            : 8
    AllocatableVMs[3]  : 
      VmSize           : Standard_D16s_v3
      Count            : 4
    AllocatableVMs[4]  : 
      VmSize           : Standard_D32-8s_v3
      Count            : 2
    AllocatableVMs[5]  : 
      VmSize           : Standard_D32-16s_v3
      Count            : 2
    AllocatableVMs[6]  : 
      VmSize           : Standard_D32s_v3
      Count            : 2
    AllocatableVMs[7]  : 
      VmSize           : Standard_D64-16s_v3
      Count            : 1
    AllocatableVMs[8]  : 
      VmSize           : Standard_D64-32s_v3
      Count            : 1
    AllocatableVMs[9]  : 
      VmSize           : Standard_D64s_v3
      Count            : 1
  Statuses[0]          : 
    Code               : ProvisioningState/succeeded
    Level              : Info
    DisplayStatus      : Provisioning succeeded
    Time               : 7/28/2019 5:31:01 PM
  Statuses[1]          : 
    Code               : HealthState/available
    Level              : Info
    DisplayStatus      : Host available
Sku                    : 
  Name                 : DSv3-Type1
Id                     : /subscriptions/10101010-1010-1010-1010-101010101010/re
sourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts
/myHost
Name                   : myHost
Location               : eastus
Tags                   : {}
```

## <a name="clean-up"></a>Vyčištění

Účtují se vám poplatky za vaše vyhrazené hostitele i v případě, že nejsou nasazené žádné virtuální počítače. Měli byste odstranit všechny hostitele, na které aktuálně nepoužíváte, abyste ušetřili náklady.  

Hostitele můžete odstranit jenom v případě, že ho nepoužívá žádný virtuální počítač. Odstraňte virtuální počítače pomocí [Remove-AzVM](/powershell/module/az.compute/remove-azvm).

```powershell
Remove-AzVM -ResourceGroupName $rgName -Name myVM
```

Po odstranění virtuálních počítačů můžete hostitele odstranit pomocí [Remove-AzHost](/powershell/module/az.compute/remove-azhost).

```powershell
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Po odstranění všech hostitelů můžete skupinu hostitelů odstranit pomocí [Remove-AzHostGroup](/powershell/module/az.compute/remove-azhostgroup). 

```powershell
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Celou skupinu prostředků můžete také odstranit v jednom příkazu pomocí příkazu [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). Tím se odstraní všechny prostředky vytvořené ve skupině včetně všech virtuálních počítačů, hostitelů a skupin hostitelů.
 
```azurepowershell-interactive
Remove-AzResourceGroup -Name $rgName
```


## <a name="next-steps"></a>Další kroky

- [Zde](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)najdete ukázkovou šablonu, která pro maximální odolnost v oblasti používá zóny i domény selhání.

- Můžete také nasadit vyhrazené hostitele pomocí [Azure Portal](dedicated-hosts-portal.md).
