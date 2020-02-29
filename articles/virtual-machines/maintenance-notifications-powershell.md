---
title: Získání oznámení o údržbě pro virtuální počítače Azure pomocí PowerShellu
description: Zobrazení oznámení o údržbě pro virtuální počítače běžící v Azure a spuštění samoobslužné údržby pomocí PowerShellu
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: b23c210d7c8a9f1d42e6e1b46e0f7f81bda857b2
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "77916078"
---
# <a name="handling-planned-maintenance-using-powershell"></a>Zpracování plánované údržby pomocí PowerShellu

**Tento článek se týká virtuálních počítačů, na kterých běží Linux i Windows.**

Pomocí Azure PowerShell můžete zjistit, kdy se virtuální počítače naplánovaly na [údržbu](maintenance-notifications.md). Informace o plánované údržbě jsou k dispozici pomocí rutiny [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) , když použijete parametr `-status`.
  
Informace o údržbě jsou vráceny pouze v případě, že je naplánována údržba. Pokud není naplánována žádná údržba, která by měla vliv na virtuální počítač, rutina nevrátí žádné informace o údržbě. 


```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Name myVM -Status
```

V MaintenanceRedeployStatus se vrátí následující vlastnosti: 

| Hodnota | Popis   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Určuje, jestli můžete v tomto okamžiku spustit údržbu virtuálního počítače. |
| PreMaintenanceWindowStartTime         | Začátek samoobslužného okna údržby, když můžete na svém VIRTUÁLNÍm počítači iniciovat údržbu |
| PreMaintenanceWindowEndTime           | Konec samoobslužného okna údržby, když můžete na svém VIRTUÁLNÍm počítači iniciovat údržbu |
| MaintenanceWindowStartTime            | Začátek údržby naplánovaný při zahájení údržby virtuálního počítače v Azure |
| MaintenanceWindowEndTime              | Konec okna naplánované údržby, ve kterém Azure iniciuje údržbu na vašem VIRTUÁLNÍm počítači |
| LastOperationResultCode               | Výsledek posledního pokusu o spuštění údržby virtuálního počítače |



Stav údržby pro všechny virtuální počítače ve skupině prostředků můžete také získat pomocí [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) a nespecifikovat virtuální počítač.
 
```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Status
```

Následující příklad PowerShellu přijímá ID předplatného a vrátí seznam virtuálních počítačů, u kterých je naplánovaná údržba.

```powershell

function MaintenanceIterator
{
    Select-AzSubscription -SubscriptionId $args[0]

    $rgList= Get-AzResourceGroup 

    for ($rgIdx=0; $rgIdx -lt $rgList.Length ; $rgIdx++)
    {
        $rg = $rgList[$rgIdx]        
    $vmList = Get-AzVM -ResourceGroupName $rg.ResourceGroupName 
        for ($vmIdx=0; $vmIdx -lt $vmList.Length ; $vmIdx++)
        {
            $vm = $vmList[$vmIdx]
            $vmDetails = Get-AzVM -ResourceGroupName $rg.ResourceGroupName -Name $vm.Name -Status
              if ($vmDetails.MaintenanceRedeployStatus )
            {
                Write-Output "VM: $($vmDetails.Name)  IsCustomerInitiatedMaintenanceAllowed: $($vmDetails.MaintenanceRedeployStatus.IsCustomerInitiatedMaintenanceAllowed) $($vmDetails.MaintenanceRedeployStatus.LastOperationMessage)"               
            }
          }
    }
}

```

### <a name="start-maintenance-on-your-vm-using-powershell"></a>Spuštění údržby virtuálního počítače pomocí PowerShellu

Při použití informací z funkce v předchozí části spustí následující údržba virtuálního počítače, pokud je **IsCustomerInitiatedMaintenanceAllowed** nastavené na true.

```powershell
Restart-AzVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```

## <a name="classic-deployments"></a>Nasazení Classic

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

Pokud stále máte starší virtuální počítače nasazené pomocí modelu nasazení Classic, můžete použít PowerShell k dotazování na virtuální počítače a zahájit údržbu.

Stav údržby virtuálního počítače získáte tak, že zadáte:

```
Get-AzureVM -ServiceName <Service name> -Name <VM name>
```

Pokud chcete spustit údržbu klasického virtuálního počítače, zadejte:

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
```

## <a name="next-steps"></a>Další kroky

Plánovanou údržbu můžete také zvládnout pomocí [Azure CLI](maintenance-notifications-cli.md) nebo [portálu](maintenance-notifications-portal.md).
