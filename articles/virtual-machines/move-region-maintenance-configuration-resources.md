---
title: Přesunutí prostředků spojených s konfigurací údržby do jiné oblasti
description: Přečtěte si, jak přesunout prostředky přidružené k konfiguraci údržby virtuálních počítačů do jiné oblasti Azure.
author: shants123
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 38532fba2be1fedd275ed2e7f9dfc1bf5752499d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86501649"
---
# <a name="move-resources-in-a-maintenance-control-configuration-to-another-region"></a>Přesunutí prostředků v konfiguraci řízení údržby do jiné oblasti

Podle tohoto článku můžete přesunout prostředky spojené s konfigurací řízení údržby do jiné oblasti Azure. Je možné, že budete chtít přesunout konfiguraci z několika důvodů. Například pro využití nové oblasti, k nasazení funkcí nebo služeb dostupných v konkrétní oblasti, k splnění požadavků na požadavky na interní zásady a zásady správného řízení nebo v reakci na plánování kapacity.

Řízení údržby s přizpůsobenými konfiguracemi údržby umožňuje řídit, jak se aktualizace platforem aplikují na virtuální počítače s [Windows](./maintenance-control-cli.md?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) a [Linux](./maintenance-control-cli.md?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Flinux%2Fbreadcrumb%2Ftoc.json&view=azure-java-stable) , a na vyhrazené hostitele Azure. Pro přesun řízení údržby mezi oblastmi je k dispozici několik scénářů:

- Postup přesunutí prostředků spojených s konfigurací údržby, ale ne samotné konfigurace, najdete v tomto článku.
- Pokud chcete přesunout konfiguraci řízení údržby, ale ne prostředky přidružené ke konfiguraci, postupujte podle [těchto pokynů](move-region-maintenance-configuration.md).
- Pokud chcete přesunout jak konfiguraci údržby, tak i prostředky, které jsou k němu přidružené, postupujte nejdříve podle [těchto pokynů](move-region-maintenance-configuration.md). Pak postupujte podle pokynů v tomto článku.

## <a name="prerequisites"></a>Požadavky

Než začnete přesouvat prostředky spojené s konfigurací řízení údržby:

- Než začnete, ujistěte se, že prostředky, které přesouváte, existují v nové oblasti.
- Ověřte konfigurace řízení údržby přidružené k virtuálním počítačům Azure a vyhrazeným hostitelům Azure, které chcete přesunout. Jednotlivě kontrolujte jednotlivé prostředky. V současné době neexistuje způsob, jak načíst konfigurace pro více prostředků.
- Při načítání konfigurací pro prostředek:
    - Ujistěte se, že používáte ID předplatného pro účet, nikoli ID vyhrazeného hostitele Azure.
    - CLI: parametr--Output Table se používá jen pro čtení a dá se odstranit nebo změnit.
    - PowerShell: parametr názvu Format-Table se používá jen pro čtení a dá se odstranit nebo změnit.
    - Pokud používáte PowerShell, zobrazí se chyba, pokud se pokusíte zobrazit seznam konfigurací pro prostředek, který nemá žádné přidružené konfigurace. Tato chyba bude podobná: "operace se nezdařila se stavem: ' Nenalezeno '. Podrobnosti: 404 chyba klienta: adresa URL nebyla nalezena.

    
## <a name="prepare-to-move"></a>Příprava na přesunutí

1. Než začnete, definujte tyto proměnné. Poskytujeme příklad pro každý.

    **Proměnná** | **Podrobnosti** | **Příklad**
    --- | ---
    $subId | ID pro předplatné obsahující konfigurace údržby | Naše-Subscription-ID
    $rsrcGroupName | Název skupiny prostředků (virtuální počítač Azure) | "VMResourceGroup"
    $vmName | Název prostředku virtuálního počítače |  MyVM
    $adhRsrcGroupName |  Skupina prostředků (vyhrazení hostitelé) | "HostResourceGroup"
    $adh | Vyhrazený název hostitele | "myHost"
    $adhParentName | Název nadřazeného prostředku | HostGroup
    
2. Načtení konfigurací údržby pomocí příkazu PowerShellu [Get-AZConfigurationAssignment](/powershell/module/az.maintenance/get-azconfigurationassignment?view=azps-3.5.0) :

    - U vyhrazených hostitelů Azure spusťte:
        ```
        Get-AzConfigurationAssignment -ResourceGroupName $adhRsrcGroupName -ResourceName $adh -ResourceType hosts -ProviderName Microsoft.Compute -ResourceParentName $adhParentName -ResourceParentType hostGroups | Format-Table Name
        ```

    - Pro virtuální počítače Azure spusťte:

        ```
        Get-AzConfigurationAssignment -ResourceGroupName $rgName -ResourceName $vmName -ProviderName Microsoft.Compute -ResourceType virtualMachines | Format-Table Name
        ```
3. Chcete-li načíst konfigurace údržby pomocí příkazu CLI [AZ Maintenance Assignment](/cli/azure/ext/maintenance/maintenance/assignment?view=azure-cli-latest) :

    - Pro vyhrazené hostitele Azure:

        ```
        az maintenance assignment list --subscription $subId --resource-group $adhRsrcGroupName --resource-name $adh --resource-type hosts --provider-name Microsoft.Compute --resource-parent-name $adhParentName --resource-parent-type hostGroups --query "[].{HostResourceGroup:resourceGroup,ConfigName:name}" --output table
        ```

    - Pro virtuální počítače Azure:

        ```
        az maintenance assignment list --subscription $subId --provider-name Microsoft.Compute --resource-group $rsrcGroupName --resource-name $vmName --resource-type virtualMachines --query "[].{HostResourceGroup:resourceGroup, ConfigName:name}" --output table
        ```


## <a name="move"></a>Přesunout 

1. Při přesunu virtuálních počítačů Azure do nové oblasti [postupujte podle těchto pokynů](../site-recovery/azure-to-azure-tutorial-migrate.md?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) .
2. Po přesunutí prostředků znovu použijte konfigurace údržby pro prostředky v nové oblasti podle toho, jestli jste přesunuli konfigurace údržby. Můžete použít konfiguraci údržby pro prostředek pomocí [PowerShellu](../virtual-machines/maintenance-control-powershell.md) nebo rozhraní příkazového [řádku](../virtual-machines/maintenance-control-cli.md).


## <a name="verify-the-move"></a>Ověřit přesunutí

Ověřte prostředky v nové oblasti a ověřte přidružené konfigurace pro prostředky v nové oblasti. 

## <a name="clean-up-source-resources"></a>Vyčištění zdrojových prostředků

Po přesunutí Zvažte odstranění přesunutých prostředků ve zdrojové oblasti.


## <a name="next-steps"></a>Další kroky

Pokud potřebujete přesunout konfigurace údržby, postupujte podle [těchto pokynů](move-region-maintenance-configuration.md) . 
