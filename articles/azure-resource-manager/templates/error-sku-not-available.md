---
title: Chyby SKU nejsou k dispozici
description: Popisuje, jak vyřešit chybu SKU, která není k dispozici při nasazování prostředků pomocí Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 02/18/2020
ms.openlocfilehash: 37ad70093cce4cea6689fecbc3b6ccb91db5017b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333218"
---
# <a name="resolve-errors-for-sku-not-available"></a>Řešení chyb kvůli nedostupné skladové položce

Tento článek popisuje, jak vyřešit chybu **SkuNotAvailable** . Pokud nemůžete najít vhodnou skladovou jednotku v této oblasti nebo zóně nebo v alternativní oblasti nebo zóně, která vyhovuje vašim obchodním potřebám, odešlete [požadavek na SKU](https://aka.ms/skurestriction) do podpory Azure.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Příznak

Při nasazování prostředku (obvykle se jedná o virtuální počítač) se zobrazí následující kód chyby a chybová zpráva:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>'
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Příčina

Tato chyba se zobrazí, pokud pro vybrané umístění není k dispozici SKU prostředků (například velikost virtuálního počítače).

Pokud nasazujete instanci virtuálního počítače se službou Azure nebo virtuální počítač se škálováním na více instancí, nemusíte mít v tomto umístění žádnou kapacitu pro Azure na místě. Další informace najdete v tématu [chybové zprávy na místě](../../virtual-machines/error-codes-spot.md).

## <a name="solution-1---powershell"></a>Řešení 1 – PowerShell

K určení, které skladové jednotky jsou k dispozici v oblasti nebo zóně, použijte příkaz [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) . Filtrovat výsledky podle umístění. Pro tento příkaz musíte mít nejnovější verzi PowerShellu.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

Výsledky obsahují seznam SKU pro umístění a veškerá omezení pro danou skladovou jednotku. Všimněte si, že položka SKU může být uvedena jako `NotAvailableForSubscription` .

```output
ResourceType          Name           Locations   Zone      Restriction                      Capability           Value
------------          ----           ---------   ----      -----------                      ----------           -----
virtualMachines       Standard_A0    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2    centralus             NotAvailableForSubscription      MaxResourceVolumeMB  138240
virtualMachines       Standard_D1_v2 centralus   {2, 1, 3}                                  MaxResourceVolumeMB
```

Některé další vzorky:

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("Standard_DS14_v2")}
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("v3")} | fc
```

Připojení "FC" na konci vrátí další podrobnosti.

## <a name="solution-2---azure-cli"></a>Řešení 2 – Azure CLI

K určení, které SKU jsou k dispozici v určité oblasti, použijte `az vm list-skus` příkaz. Použijte `--location` parametr k filtrování výstupu do umístění, které používáte. Použijte `--size` parametr pro hledání podle názvu částečné velikosti.

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --output table
```

Příkaz vrátí výsledky jako:

```output
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

![Vytvoření virtuálního počítače](./media/error-sku-not-available/create-vm.png)

Dostupné velikosti můžete filtrovat a procházet.

![Dostupné edice](./media/error-sku-not-available/available-sizes.png)

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

