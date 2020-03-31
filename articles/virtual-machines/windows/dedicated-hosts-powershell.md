---
title: Nasazení vyhrazených hostitelů Azure pomocí Azure PowerShellu
description: Nasazujte virtuální počítače do vyhrazených hostitelů pomocí Azure PowerShellu.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure
ms.date: 08/01/2019
ms.author: cynthn
ms.openlocfilehash: a228a83d711c84d2aa994e6de7d90af48cca7f28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530933"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-powershell"></a>Nasazení virtuálních počítačů do vyhrazených hostitelů pomocí Azure PowerShellu

Tento článek vás provede, jak vytvořit [vyhrazeného hostitele](dedicated-hosts.md) Azure pro hostování virtuálních počítačů.This article guides through you through how to create a Azure dedicated host to host your virtual machines (VMs). 

Ujistěte se, že jste nainstalovali Azure PowerShell verze 2.8.0 nebo novější `Connect-AzAccount`a jste přihlášení k účtu Azure v aplikaci . 

## <a name="limitations"></a>Omezení

- Škálovací sady virtuálních strojů nejsou aktuálně podporovány na vyhrazených hostitelích.
- Velikosti a typy hardwaru, které jsou k dispozici pro vyhrazené hostitele, se liší podle oblasti. Další informace najdete na [stránce s cenami](https://aka.ms/ADHPricing) hostitele.

## <a name="create-a-host-group"></a>Vytvoření skupiny hostitelů

**Skupina hostitelů** je prostředek, který představuje kolekci vyhrazených hostitelů. Vytvoříte skupinu hostitelů v oblasti a zóně dostupnosti a přidáte do ní hostitele. Při plánování vysoké dostupnosti, existují další možnosti. S vyhrazenými hostiteli můžete použít jednu nebo obě následující možnosti: 
- Rozsah mezi více zónami dostupnosti. V takovém případě musíte mít skupinu hostitelů v každé zóně, kterou chcete použít.
- Rozsah mezi více domén ách selhání, které jsou mapovány na fyzické regály. 
 
V obou případech je třeba zadat počet domén selhání pro vaši skupinu hostitelů. Pokud nechcete, aby se ve skupině promítají domény selhání, použijte počet domén selhání 1. 

Můžete se také rozhodnout použít zóny dostupnosti i domény selhání. Tento příklad vytvoří skupinu hostitelů v zóně 1 se 2 doménami selhání. 


```azurepowershell-interactive
$rgName = "myDHResourceGroup"
$location = "EastUS"

New-AzResourceGroup -Location $location -Name $rgName
$hostGroup = New-AzHostGroup `
   -Location $location `
   -Name myHostGroup `
   -PlatformFaultDomain 2 `
   -ResourceGroupName $rgName `
   -Zone 1
```

## <a name="create-a-host"></a>Vytvoření hostitele

Nyní vytvoříme vyhrazeného hostitele ve skupině hostitelů. Kromě názvu hostitele je nutné zadat skladovou položku pro hostitele. Skladová položka hostitele zachycuje podporovanou řadu virtuálních počítače a generaci hardwaru pro vašeho vyhrazeného hostitele.

Další informace o hostitelských skum a cenách najdete v [tématu Ceny vyhrazeného hostitele Azure](https://aka.ms/ADHPricing).

Pokud pro skupinu hostitelů nastavíte počet domén selhání, budete vyzváni k zadání domény selhání pro hostitele. V tomto příkladu nastavíme doménu selhání pro hostitele na 1.


```azurepowershell-interactive
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

Pokud jste při vytváření skupiny hostitelů zadali zónu dostupnosti, musíte při vytváření virtuálního počítače použít stejnou zónu. V tomto příkladu, protože naše skupina hostitelů je v zóně 1, musíme vytvořit virtuální ho v zóně 1.  


```azurepowershell-interactive
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
> Pokud vytvoříte virtuální počítač na hostiteli, který nemá dostatek prostředků, virtuální počítač se vytvoří ve stavu SELHÁNÍ. 

## <a name="check-the-status-of-the-host"></a>Kontrola stavu hostitele

Můžete zkontrolovat stav hostitele a kolik virtuálních počítačů můžete stále nasadit do `-InstanceView` hostitele pomocí [GetAzHost](/powershell/module/az.compute/get-azhost) s parametrem.

```azurepowershell-interactive
Get-AzHost `
   -ResourceGroupName $rgName `
   -Name myHost `
   -HostGroupName $hostGroup.Name `
   -InstanceView
```

Výstup bude vypadat podobně jako tento:

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

## <a name="add-an-existing-vm"></a>Přidání existujícího virtuálního virtuálního montovna 

Existující virtuální ho do vyhrazeného hostitele můžete přidat, ale virtuální modul musí být nejprve Stop\Deallocated. Než přesunete virtuální ho důťák na vyhrazeného hostitele, ujistěte se, že je podporovaná konfigurace virtuálního počítače:

- Velikost virtuálního počítače musí být ve stejné rodině velikosti jako vyhrazený hostitel. Například pokud váš vyhrazený hostitel je DSv3, pak velikost virtuálního počítače může být Standard_D4s_v3, ale nemůže to být Standard_A4_v2. 
- Virtuální modul musí být umístěn ve stejné oblasti jako vyhrazený hostitel.
- Virtuální virtuální ms nemůže být součástí skupiny umístění bezkontaktní. Před přesunutím do vyhrazeného hostitele odeberte virtuální ho ze skupiny umístění bezkontaktní komunikace. Další informace najdete [v tématu Přesunutí virtuálního virtuálního ms ze skupiny umístění bez kontaktních míst.](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups#move-an-existing-vm-out-of-a-proximity-placement-group)
- Virtuální virtuální měsíč nemůže být v sadě dostupnosti.
- Pokud je virtuální virtuální soud v zóně dostupnosti, musí se jednat o stejnou zónu dostupnosti jako hostitelská skupina. Nastavení zóny dostupnosti pro virtuální počítače a skupinu hostitelů se musí shodovat.

Nahraďte hodnoty proměnných vlastními informacemi.

```azurepowershell-interactive
$vmRGName = "movetohost"
$vmName = "myVMtoHost"
$dhRGName = "myDHResourceGroup"
$dhGroupName = "myHostGroup"
$dhName = "myHost"

$myDH = Get-AzHost `
   -HostGroupName $dhGroupName `
   -ResourceGroupName $dhRGName `
   -Name $dhName
   
$myVM = Get-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
   
$myVM.Host = New-Object Microsoft.Azure.Management.Compute.Models.SubResource

$myVM.Host.Id = "$myDH.Id"

Stop-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName -Force
   
Update-AzVM `
   -ResourceGroupName $vmRGName `
   -VM $myVM -Debug
   
Start-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
```


## <a name="clean-up"></a>Vyčištění

Poplatky za vyhrazené hostitele se vám účtují i v případě, že se nenasazují žádné virtuální počítače. Měli byste odstranit všechny hostitele, které právě nepoužíváte k úspoře nákladů.  

Hostitele můžete odstranit pouze v případě, že už nejsou virtuální počítače, které by ho používaly. Odstraňte virtuální mích pomocí [Remove-AzVM](/powershell/module/az.compute/remove-azvm).

```azurepowershell-interactive
Remove-AzVM -ResourceGroupName $rgName -Name myVM
```

Po odstranění virtuálních měn můžete odstranit hostitele pomocí [remove-AzHost](/powershell/module/az.compute/remove-azhost).

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Po odstranění všech hostitelů můžete skupinu hostitelů odstranit pomocí [skupiny Remove-AzHostGroup](/powershell/module/az.compute/remove-azhostgroup). 

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Můžete také odstranit celou skupinu prostředků v jednom příkazu pomocí [remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). Tím odstraníte všechny prostředky vytvořené ve skupině, včetně všech virtuálních uživatelů, hostitelů a skupin hostitelů.
 
```azurepowershell-interactive
Remove-AzResourceGroup -Name $rgName
```


## <a name="next-steps"></a>Další kroky

- Zde je [nalezena](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)ukázková šablona , která používá zóny i domény selhání pro maximální odolnost proti chybám v oblasti.

- Můžete také nasadit vyhrazené hostitele pomocí [portálu Azure](dedicated-hosts-portal.md).
