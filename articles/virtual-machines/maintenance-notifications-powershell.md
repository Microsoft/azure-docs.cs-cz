---
title: Získejte oznámení o údržbě pro virtuální počítače Azure pomocí PowerShellu
description: Zobrazení oznámení o údržbě pro virtuální počítače spuštěné v Azure a spusťte samoobslužnou údržbu pomocí PowerShellu.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: b23c210d7c8a9f1d42e6e1b46e0f7f81bda857b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77916078"
---
# <a name="handling-planned-maintenance-using-powershell"></a>Zpracování plánované údržby pomocí prostředí PowerShell

**Tento článek se vztahuje na virtuální počítače se systémem Linux i Windows.**

Azure PowerShell můžete použít k zobrazení, kdy jsou virtuální počítače naplánované na [údržbu](maintenance-notifications.md). Informace o plánované údržbě jsou k dispozici v rutině [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) při použití parametru. `-status`
  
Informace o údržbě jsou vráceny pouze v případě, že je plánována údržba. Pokud není naplánována žádná údržba, která má vliv na virtuální ms, rutina nevrátí žádné informace o údržbě. 


```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Name myVM -Status
```

Následující vlastnosti jsou vráceny v rámci MaintenanceRedeployStatus: 

| Hodnota | Popis   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Označuje, zda můžete spustit údržbu na virtuálním počítači v tomto okamžiku |
| Doba předúdržbouoknaStartTime         | Začátek samoobslužného okna údržby, když můžete zahájit údržbu na vašem virtuálním počítači |
| Doba předúdržbou oknaEndTime           | Konec samoobslužného okna údržby, když můžete zahájit údržbu na vašem virtuálním počítači |
| ÚdržbaWindowStartTime            | Začátek naplánované údržby, ve kterém Azure iniciuje údržbu na vašem virtuálním počítači |
| ÚdržbaOknoEndTime              | Konec naplánovaného okna údržby, ve kterém Azure iniciuje údržbu na vašem virtuálním počítači |
| LastOperationResultCode               | Výsledek posledního pokusu o zahájení údržby na virtuálním počítači |



Můžete také získat stav údržby pro všechny virtuální společnosti ve skupině prostředků pomocí [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) a neurčení virtuálního účtu.
 
```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Status
```

Následující příklad Prostředí PowerShell převezme ID předplatného a vrátí seznam virtuálních počítačů, které jsou naplánovány na údržbu.

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

Pomocí informací z funkce v předchozí části následující spustí údržbu na virtuálním počítači, pokud **IsCustomerInitiatedMaintenanceAllowed** je nastavena na hodnotu true.

```powershell
Restart-AzVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```

## <a name="classic-deployments"></a>Nasazení Classic

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

Pokud máte stále starší virtuální počítače, které byly nasazeny pomocí klasického modelu nasazení, můžete použít Prostředí PowerShell k dotazování na virtuální počítače a zahájení údržby.

Pokud chcete získat stav údržby virtuálního soudu, zadejte:

```
Get-AzureVM -ServiceName <Service name> -Name <VM name>
```

Chcete-li zahájit údržbu klasického virtuálního počítače, zadejte:

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
```

## <a name="next-steps"></a>Další kroky

Plánovanou údržbu můžete také zpracovat pomocí [příkazového příkazu k řešení Azure](maintenance-notifications-cli.md) nebo [portálu](maintenance-notifications-portal.md).
