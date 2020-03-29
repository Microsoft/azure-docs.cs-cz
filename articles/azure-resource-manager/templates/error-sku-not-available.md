---
title: Skladová položka není k dispozici chyby
description: Popisuje, jak řešit chybu skladové položky není k dispozici při nasazování prostředků pomocí Správce prostředků Azure.
ms.topic: troubleshooting
ms.date: 02/18/2020
ms.openlocfilehash: 3dcc26f2d74799a6d282ee4bd733d36bec7b05e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942718"
---
# <a name="resolve-errors-for-sku-not-available"></a>Řešení chyb kvůli nedostupné skladové položce

Tento článek popisuje, jak vyřešit chybu **SkuNotAvailable.** Pokud se vám nedaří najít vhodnou skladovou položku v této oblasti nebo zóně nebo alternativní oblasti nebo zóně, která splňuje vaše obchodní potřeby, odešlete [žádost o skladovou položku](https://aka.ms/skurestriction) do podpory Azure.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Příznak

Při nasazování prostředku (obvykle virtuálního počítače) se zobrazí následující kód chyby a chybová zpráva:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>'
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Příčina

Tato chyba se zobrazí, když vybraná skladová položka prostředku (například velikost virtuálního počítače) není k dispozici pro vybrané umístění.

Pokud nasazujete instanci azure spot ového virtuálního počítače nebo škálovací sady spotů, neexistuje v tomto umístění žádná kapacita pro Azure Spot. Další informace naleznete v tématu [Spot chybové zprávy](../../virtual-machines/error-codes-spot.md).

## <a name="solution-1---powershell"></a>Řešení 1 - PowerShell

Chcete-li zjistit, které skladové položky jsou k dispozici v oblasti nebo zóně, použijte příkaz [Get-AzComputeResourceSku.](/powershell/module/az.compute/get-azcomputeresourcesku) Výsledky můžete filtrovat podle umístění. Pro tento příkaz musíte mít nejnovější verzi prostředí PowerShell.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

Výsledky zahrnují seznam skladových položek pro umístění a všechna omezení pro tuto skladovou položku. Všimněte si, že skladová položka může být uvedena jako `NotAvailableForSubscription`.

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

Připojení "fc" na konci vrátí další podrobnosti.

## <a name="solution-2---azure-cli"></a>Řešení 2 – Azure CLI

Chcete-li zjistit, které skum jsou `az vm list-skus` k dispozici v oblasti, použijte příkaz. Pomocí `--location` parametru můžete filtrovat výstup do umístění, které používáte. Pomocí `--size` parametru můžete hledat podle názvu částečné velikosti.

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --output table
```

Příkaz vrátí výsledky, jako jsou:

```output
ResourceType     Locations       Name              Zones    Capabilities    Restrictions
---------------  --------------  ----------------  -------  --------------  --------------
virtualMachines  southcentralus  Standard_F1                ...             None
virtualMachines  southcentralus  Standard_F2                ...             None
virtualMachines  southcentralus  Standard_F4                ...             None
...
```


## <a name="solution-3---azure-portal"></a>Řešení 3 – portál Azure

Chcete-li zjistit, které skum jsou k dispozici v oblasti, použijte [portál](https://portal.azure.com). Přihlaste se k portálu a přidejte prostředek prostřednictvím rozhraní. Při nastavujete hodnoty, zobrazí se dostupné skum pro tento prostředek. Není nutné dokončit nasazení.

Můžete například spustit proces vytváření virtuálního počítače. Chcete-li zobrazit další dostupnou velikost, vyberte **možnost Změnit velikost**.

![Vytvoření virtuálního počítače](./media/error-sku-not-available/create-vm.png)

Dostupné velikosti můžete filtrovat a procházet.

![Dostupné edice](./media/error-sku-not-available/available-sizes.png)

## <a name="solution-4---rest"></a>Řešení 4 - REST

Chcete-li zjistit, které skum jsou k dispozici v oblasti, použijte operace [Skus - seznam prostředků.](/rest/api/compute/resourceskus/list)

Vrátí dostupné sku a oblasti v následujícím formátu:

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

