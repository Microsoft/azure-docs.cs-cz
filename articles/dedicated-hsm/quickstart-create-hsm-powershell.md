---
title: 'Rychlý Start: vytvoření vyhrazeného modulu HARDWAROVÉho zabezpečení Azure pomocí Azure PowerShell'
description: Vytvoření služby Azure Dedicated HSM s využitím Azure PowerShellu
services: dedicated-hsm
author: msmbaldwin
ms.author: mbaldwin
ms.topic: quickstart
ms.service: key-vault
ms.devlang: azurepowershell
ms.date: 11/13/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: eebfb257d0324cf2771bd3af979ddbebb8429fb7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94905619"
---
# <a name="quickstart-create-an-azure-dedicated-hsm-with-azure-powershell"></a>Rychlý Start: vytvoření vyhrazeného modulu HARDWAROVÉho zabezpečení Azure pomocí Azure PowerShell

Tento článek popisuje, jak můžete vytvořit vyhrazený modul HARDWAROVÉho zabezpečení Azure pomocí modulu [AZ. DedicatedHsm](/powershell/module/az.dedicatedhsm) prostředí PowerShell.

## <a name="requirements"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [azure-powershell-requirements-no-header.md](../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > I když je modul PowerShell **AZ. DedicatedHsm** ve verzi Preview, musíte ho nainstalovat samostatně pomocí `Install-Module` rutiny. Jakmile bude tento powershellový modul obecně dostupný, stane se součástí budoucích verzí modulu Az PowerShellu a bude ve výchozím nastavení dostupný v rámci Azure Cloud Shellu.

  ```azurepowershell-interactive
  Install-Module -Name Az.DedicatedHsm
  ```

* Pokud máte více předplatných Azure, vyberte příslušné předplatné, ve kterém se prostředky mají fakturovat. Vyberte konkrétní předplatné pomocí rutiny [set-AzContext](/powershell/module/az.accounts/set-azcontext) .

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte [skupinu prostředků Azure](../azure-resource-manager/management/overview.md) pomocí rutiny [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure jako skupina.

Následující příklad vytvoří skupinu prostředků se zadaným názvem a v zadaném umístění.

```azurepowershell-interactive
New-AzResourceGroup -Name myRG -Location westus
```

## <a name="create-a-dedicated-hsm"></a>Vytvoření vyhrazeného modulu HSM

K vytvoření vyhrazeného modulu HARDWAROVÉho zabezpečení použijte rutinu [New-AzDedicatedHsm](/powershell/module/az.dedicatedhsm/new-azdedicatedhsm) . Následující příklad vytvoří vyhrazený modul HARDWAROVÉho zabezpečení v zadaném předplatném.

```azurepowershell-interactive
$Params = @{
  Name  = 'MyHSM'
  ResourceGroupName = 'myRG'
  Location = 'westus'
  Sku = 'SafeNet Luna Network HSM A790'
  StampId = 'stamp1'
  SubnetId = '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myhsm-vnet/subnets/hsmsubnet'
  NetworkInterface = @{PrivateIPAddress = '10.2.1.120'}
}
New-AzDedicatedHsm @Params
```

```Output
Name       Provisioning State SKU                           Location
----       ------------------ ---                           --------
myhsm      Succeeded          SafeNet Luna Network HSM A790 westus
```

## <a name="get-a-dedicated-hsm"></a>Získání vyhrazeného modulu HSM

Pokud chcete načíst informace o existujícím vyhrazeném modulu HSM, použijte rutinu [Get-AzDedicatedHsm](/powershell/module/az.dedicatedhsm/get-azdedicatedhsm) . Následující příklad načte určený vyhrazený modul HSM.

```azurepowershell-interactive
Get-AzDedicatedHsm -Name MyHSM -ResourceGroupName myRG
```

```Output
Name       Provisioning State SKU                           Location
----       ------------------ ---                           --------
myhsm      Succeeded          SafeNet Luna Network HSM A790 westus
```

## <a name="update-a-dedicated-hsm"></a>Aktualizace vyhrazeného modulu HSM

K aktualizaci vyhrazeného modulu HARDWAROVÉho zabezpečení použijte rutinu [Update-AzDedicatedHsm](/powershell/module/az.dedicatedhsm/update-azdedicatedhsm) . Následující příklad aktualizuje vyhrazený modul HARDWAROVÉho zabezpečení v zadaném předplatném.

```azurepowershell-interactive
Update-AzDedicatedHsm -Name MyHSM -ResourceGroupName myRG -Tag @{'key1' = '1'; 'key2' = 2; 'key3' = 3}
```

```Output
PS C:\>Update-AzDedicatedHsm -Name  hsm-n7wfxi -ResourceGroupName dedicatedhsm-rg-n359cz -Tag @{'key1' = '1';
'key2' = 2; 'key3' = 3}

Name       Provisioning State SKU                           Location
----       ------------------ ---                           --------
myhsm      Succeeded          SafeNet Luna Network HSM A790 westus
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud prostředky vytvořené v tomto článku nepotřebujete, můžete je odstranit spuštěním následujících příkladů.

### <a name="remove-a-dedicated-hsm"></a>Odebrat vyhrazený modul HSM

K odebrání vyhrazeného modulu HARDWAROVÉho zabezpečení použijte rutinu [Remove-AzDedicatedHsm](/powershell/module/az.dedicatedhsm/remove-azdedicatedhsm) . Následující příklad odstraní zadaný vyhrazený modul HSM.

```azurepowershell-interactive
Remove-AzDedicatedHsm -Name hsm-7t2xaf -ResourceGroupName lucas-manual-test
```

### <a name="delete-the-resource-group"></a>Odstranění skupiny prostředků

> [!CAUTION]
> Následující příklad odstraní zadanou skupinu prostředků a všechny prostředky, které jsou v ní obsažené.
> Pokud v zadané skupině prostředků existují prostředky mimo rozsah tohoto článku, budou také odstraněny.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myRG
```

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [vyhrazeném modulu HSM Azure](overview.md).
