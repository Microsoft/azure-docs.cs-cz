---
title: Přesunutí prostředků přidružených ke konfiguraci údržby do jiné oblasti
description: Zjistěte, jak přesunout prostředky přidružené ke konfiguraci údržby virtuálních počítačů do jiné oblasti Azure
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 3e271e2467b495e79a93ce5eab5edee36e65e619
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304443"
---
# <a name="move-resources-in-a-maintenance-control-configuration-to-another-region"></a>Přesunutí prostředků v konfiguraci řízení údržby do jiné oblasti

Podle tohoto článku přesuňte prostředky přidružené ke konfiguraci řízení údržby do jiné oblasti Azure. Konfiguraci můžete přesunout z mnoha důvodů. Chcete-li například využít výhod nové oblasti, nasadit funkce nebo služby dostupné v určité oblasti, splnit požadavky na vnitřní zásady a zásady správného řízení nebo v reakci na plánování kapacity.

Řízení údržby s přizpůsobenými konfiguracemi údržby umožňuje řídit způsob použití aktualizací platformy pro virtuální počítače [s Windows](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) a [Linuxem](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Flinux%2Fbreadcrumb%2Ftoc.json&view=azure-java-stable) a na vyhrazené hostitele Azure. Existuje několik scénářů pro přesunutí řízení údržby napříč oblastmi:

- Chcete-li přesunout prostředky přidružené k konfiguraci údržby, ale ne samotnou konfiguraci, postupujte podle tohoto článku.
- Chcete-li přesunout konfiguraci řízení údržby, ale nikoli prostředky přidružené ke konfiguraci, postupujte [podle těchto pokynů](move-region-maintenance-configuration.md).
- Chcete-li přesunout konfiguraci údržby i prostředky s ní spojené, postupujte [nejprve](move-region-maintenance-configuration.md)podle těchto pokynů . Poté postupujte podle pokynů v tomto článku.

## <a name="prerequisites"></a>Požadavky

Než začnete přesouvat prostředky přidružené ke konfiguraci řízení údržby:

- Ujistěte se, že prostředky, které přesouváte, existují v nové oblasti, než začnete.
- Ověřte konfigurace řízení údržby přidružené k virtuálním počítačům Azure a vyhrazeným hostitelům Azure, které chcete přesunout. Zkontrolujte každý zdroj jednotlivě. V současné době neexistuje žádný způsob, jak načíst konfigurace pro více prostředků.
- Při načítání konfigurací pro prostředek:
    - Ujistěte se, že používáte ID předplatného pro účet, ne vyhrazené ID hostitele Azure.
    - CLI: Parametr --output table se používá pouze pro čitelnost a lze jej odstranit nebo změnit.
    - PowerShell: Parametr Název tabulky formátů se používá pouze pro čitelnost a lze jej odstranit nebo změnit.
    - Pokud používáte PowerShell, zobrazí se chyba, pokud se pokusíte vypsat konfigurace pro prostředek, který nemá žádné přidružené konfigurace. Chyba bude podobná: "Operace se nezdařila se stavem: Nebyl nalezen". Podrobnosti: 404 Chyba klienta: Nebyl nalezen pro url".

    
## <a name="prepare-to-move"></a>Připravte se na přesun

1. Než začnete, definujte tyto proměnné. Každému z nich jsme poskytli příklad.

    **Proměnná** | **Podrobnosti** | **Příklad**
    --- | ---
    $subId | ID předplatného obsahující konfigurace údržby | "naše-předplatné-ID"
    $rsrcGroupName | Název skupiny prostředků (virtuální počítač Azure) | "Skupina VMResourceGroup"
    $vmName | Název prostředku virtuálního_ |  "myVM"
    $adhRsrcGroupName |  Skupina prostředků (vyhrazení hostitelé) | "Skupina hostitelských zdrojů"
    $adh | Název vyhrazeného hostitele | "myHost"
    $adhParentName | Název nadřazeného prostředku | "Hostitelská skupina"
    
2. Chcete-li načíst konfigurace údržby pomocí příkazu PowerShell [Get-AZConfigurationAssignment:](https://docs.microsoft.com/powershell/module/az.maintenance/Get-AzConfigurationAssignment?view=azps-3.5.0)

    - Pro vyhrazené hostitele Azure spusťte:
        ```
        Get-AzConfigurationAssignment -ResourceGroupName $adhRsrcGroupName -ResourceName $adh -ResourceType hosts -ProviderName Microsoft.Compute -ResourceParentName $adhParentName -ResourceParentType hostGroups | Format-Table Name
        ```

    - Pro virtuální počítače Azure spusťte:

        ```
        Get-AzConfigurationAssignment -ResourceGroupName $rgName -ResourceName $vmName -ProviderName Microsoft.Compute -ResourceType virtualMachines | Format-Table Name
        ```
3. Chcete-li načíst konfigurace údržby pomocí příkazu [přiřazení údržby az](https://docs.microsoft.com/cli/azure/ext/maintenance/maintenance/assignment?view=azure-cli-latest) příkazu CLI:

    - Pro vyhrazené hostitele Azure:

        ```
        az maintenance assignment list --subscription $subId --resource-group $adhRsrcGroupName --resource-name $adh --resource-type hosts --provider-name Microsoft.Compute --resource-parent-name $adhParentName --resource-parent-type hostGroups --query "[].{HostResourceGroup:resourceGroup,ConfigName:name}" --output table
        ```

    - Pro virtuální počítače Azure:

        ```
        az maintenance assignment list --subscription $subId --provider-name Microsoft.Compute --resource-group $rsrcGroupName --resource-name $vmName --resource-type virtualMachines --query "[].{HostResourceGroup:resourceGroup, ConfigName:name}" --output table
        ```


## <a name="move"></a>Přesunout 

1. [Podle těchto pokynů](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) přesuňte virtuální počítače Azure do nové oblasti.
2. Po přesunutí prostředků znovu použít konfigurace údržby prostředků v nové oblasti podle potřeby, v závislosti na tom, zda jste přesunuli konfigurace údržby. Konfiguraci údržby můžete použít na prostředek pomocí [prostředí PowerShell](../virtual-machines/maintenance-control-powershell.md) nebo [CLI](../virtual-machines/maintenance-control-cli.md).


## <a name="verify-the-move"></a>Ověření přesunu

Ověřte prostředky v nové oblasti a ověřte přidružené konfigurace pro prostředky v nové oblasti. 

## <a name="clean-up-source-resources"></a>Vyčištění zdrojových prostředků

Po přesunutí zvažte odstranění přesunuté prostředky ve zdrojové oblasti.


## <a name="next-steps"></a>Další kroky

Pokud potřebujete přesunout konfigurace údržby, postupujte podle [těchto pokynů.](move-region-maintenance-configuration.md) 
