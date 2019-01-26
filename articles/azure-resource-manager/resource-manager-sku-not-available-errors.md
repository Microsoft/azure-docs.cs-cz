---
title: Azure SKU není k dispozici chyby | Dokumentace Microsoftu
description: Popisuje postup řešení potíží s SKU není k dispozici při nasazení.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/19/2018
ms.author: tomfitz
ms.openlocfilehash: 4688acbb2742579e0f9f3fbb2604ffd8ef12bfd5
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2019
ms.locfileid: "55081037"
---
# <a name="resolve-errors-for-sku-not-available"></a>Řešení chyb pro SKU není k dispozici

Tento článek popisuje, jak vyřešit **zprávy typu SkuNotAvailable** chyby. Pokud nemůžete najít vhodné SKU v dané oblasti nebo alternativní oblast, která splňuje vaše obchodní potřeby, odešlete [SKU požadavek](https://aka.ms/skurestriction) podpory Azure.


## <a name="symptom"></a>Příznak

Při nasazování prostředků (obvykle virtuálního počítače), se zobrazí následující kód chyby a chybová zpráva:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>' 
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Příčina

Tato chyba se zobrazí, když není k dispozici pro umístění, které jste vybrali prostředek SKU, které jste vybrali (jako je například velikost virtuálního počítače).

## <a name="solution-1---powershell"></a>Řešení 1 – PowerShell

Chcete-li zjistit, které skladové položky jsou k dispozici v oblasti, použijte [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) příkazu. Filtrovat výsledky podle umístění. Musíte mít nejnovější verzi prostředí PowerShell pro tento příkaz.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

Výsledky obsahovat seznam skladových položek pro umístění a všechna omezení pro dané skladové jednotce. Všimněte si, že se skladová položka může být uvedena jako `NotAvailableForSubscription`.

```powershell
ResourceType          Name        Locations   Restriction                      Capability           Value
------------          ----        ---------   -----------                      ----------           -----
virtualMachines       Standard_A0 centralus   NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1 centralus   NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2 centralus   NotAvailableForSubscription      MaxResourceVolumeMB  138240
```

## <a name="solution-2---azure-cli"></a>Řešení 2 – rozhraní příkazového řádku Azure

Chcete-li zjistit, které skladové položky jsou k dispozici v oblasti, použijte `az vm list-skus` příkazu. Použití `--location` parametr filtrovat výstup do umístění, které používáte. Použití `--size` parametru hledání podle názvu částečné velikost.

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --output table
```

Příkaz vrátí výsledky, jako jsou:

```azurecli
ResourceType     Locations       Name              Zones    Capabilities    Restrictions
---------------  --------------  ----------------  -------  --------------  --------------
virtualMachines  southcentralus  Standard_F1                ...             None
virtualMachines  southcentralus  Standard_F2                ...             None
virtualMachines  southcentralus  Standard_F4                ...             None
...
```


## <a name="solution-3---azure-portal"></a>Řešení 3 – Azure portal

Chcete-li zjistit, které skladové položky jsou k dispozici v oblasti, použijte [portál](https://portal.azure.com). Přihlaste se k portálu a přidat prostředek prostřednictvím rozhraní. Při nastavování hodnoty, zobrazí se dostupné skladové položky pro daný prostředek. Není nutné k dokončení nasazení.

Například spusťte proces vytváření virtuálního počítače. Pokud chcete zobrazit další dostupné velikosti, vyberte **změnit velikost**.

![Vytvoření virtuálního počítače](./media/resource-manager-sku-not-available-errors/create-vm.png)

Můžete filtrovat a posuňte se až si dostupné velikosti.

![Dostupné skladové položky](./media/resource-manager-sku-not-available-errors/available-sizes.png)

## <a name="solution-4---rest"></a>Řešení 4 – REST

Chcete-li zjistit, které skladové položky jsou k dispozici v oblasti, použijte [skladové položky prostředků – seznam](/rest/api/compute/resourceskus/list) operace.

Vrátí dostupné skladové položky a oblastmi v následujícím formátu:

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    ...
  ]
}
```

