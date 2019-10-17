---
title: Chyby SKU Azure nejsou k dispozici | Microsoft Docs
description: Popisuje, jak vyřešit chybu SKU, která není k dispozici při nasazování prostředků pomocí Azure Resource Manager.
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
ms.openlocfilehash: fca028412052a9a1520e1178f5d182a9987a9a85
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390221"
---
# <a name="resolve-errors-for-sku-not-available"></a>Řešení chyb pro SKU není k dispozici

Tento článek popisuje, jak vyřešit chybu **SkuNotAvailable** . Pokud nemůžete najít vhodnou skladovou jednotku v této oblasti nebo v jiné oblasti, která vyhovuje vašim obchodním potřebám, odešlete [požadavek na SKU](https://aka.ms/skurestriction) do podpory Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>Příznak

Při nasazování prostředku (obvykle se jedná o virtuální počítač) se zobrazí následující kód chyby a chybová zpráva:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>' 
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Příčina

Tato chyba se zobrazí, pokud pro vybrané umístění není k dispozici SKU prostředků (například velikost virtuálního počítače).

## <a name="solution-1---powershell"></a>Řešení 1 – PowerShell

K určení, které SKU jsou k dispozici v určité oblasti, použijte příkaz [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) . Filtrovat výsledky podle umístění. Pro tento příkaz musíte mít nejnovější verzi PowerShellu.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

Výsledky obsahují seznam SKU pro umístění a veškerá omezení pro danou skladovou jednotku. Všimněte si, že SKU může být uvedeno jako `NotAvailableForSubscription`.

```powershell
ResourceType          Name        Locations   Restriction                      Capability           Value
------------          ----        ---------   -----------                      ----------           -----
virtualMachines       Standard_A0 centralus   NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1 centralus   NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2 centralus   NotAvailableForSubscription      MaxResourceVolumeMB  138240
```

## <a name="solution-2---azure-cli"></a>Řešení 2 – Azure CLI

K určení, které SKU jsou k dispozici v oblasti, použijte příkaz `az vm list-skus`. K filtrování výstupu do umístění, které používáte, použijte parametr `--location`. Pomocí parametru `--size` můžete hledat podle názvu částečné velikosti.

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --output table
```

Příkaz vrátí výsledky jako:

```azurecli
ResourceType     Locations       Name              Zones    Capabilities    Restrictions
---------------  --------------  ----------------  -------  --------------  --------------
virtualMachines  southcentralus  Standard_F1                ...             None
virtualMachines  southcentralus  Standard_F2                ...             None
virtualMachines  southcentralus  Standard_F4                ...             None
...
```


## <a name="solution-3---azure-portal"></a>Řešení 3 – Azure Portal

K určení, které SKU jsou k dispozici v určité oblasti, použijte [portál](https://portal.azure.com). Přihlaste se k portálu a přidejte prostředek přes rozhraní. Při nastavování hodnot se zobrazí dostupné SKU pro daný prostředek. Nemusíte dokončit nasazení.

Zahajte například proces vytváření virtuálního počítače. Pokud chcete zobrazit další dostupnou velikost, vyberte **změnit velikost**.

![Vytvoření virtuálního počítače](./media/resource-manager-sku-not-available-errors/create-vm.png)

Dostupné velikosti můžete filtrovat a procházet.

![Dostupné SKU](./media/resource-manager-sku-not-available-errors/available-sizes.png)

## <a name="solution-4---rest"></a>Řešení 4 – REST

Chcete-li zjistit, které skladové jednotky jsou k dispozici v určité oblasti, použijte operaci [seznam SKU prostředku](/rest/api/compute/resourceskus/list) .

Vrátí dostupné SKU a oblasti v následujícím formátu:

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

