---
title: Chyby SKU nejsou k dispozici
description: Popisuje, jak vyřešit chybu SKU, která není k dispozici při nasazování prostředků pomocí Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 04/14/2021
ms.openlocfilehash: 3baedf6a5c9f2dbfd3ddf666b458fac649fce2ac
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503894"
---
# <a name="resolve-errors-for-sku-not-available"></a>Řešení chyb kvůli nedostupné skladové položce

Tento článek popisuje, jak vyřešit chybu **SkuNotAvailable** . Pokud nemůžete najít vhodnou skladovou jednotku v této oblasti nebo zóně nebo v alternativní oblasti nebo zóně, která vyhovuje vašim obchodním potřebám, odešlete [požadavek na SKU](/troubleshoot/azure/general/region-access-request-process) do podpory Azure.

## <a name="symptom"></a>Příznak

Při nasazování prostředku (obvykle se jedná o virtuální počítač) se zobrazí následující kód chyby a chybová zpráva:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>'
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Příčina

Tato chyba se zobrazí, pokud pro vybrané umístění není k dispozici SKU prostředků (například velikost virtuálního počítače).

Pokud nasazujete instanci virtuálního počítače se službou Azure nebo virtuální počítač se škálováním na více instancí, nemusíte mít v tomto umístění žádnou kapacitu pro Azure. Další informace najdete v tématu [chybové zprávy na místě](../../virtual-machines/error-codes-spot.md).

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

Chcete-li filtrovat podle umístění a SKU, použijte:

```azurepowershell-interactive
$SubId = (Get-AzContext).Subscription.Id

$Region = "centralus" # change region here
$VMSku = "Standard_M" # change VM SKU here

$VMSKUs = Get-AzComputeResourceSku | where {$_.Locations.Contains($Region) -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains($VMSku)}

$OutTable = @()

foreach ($SkuName in $VMSKUs.Name)
        {
            $LocRestriction = if ((($VMSKUs | where Name -EQ $SkuName).Restrictions.Type | Out-String).Contains("Location")){"NotAvavalableInRegion"}else{"Available - No region restrictions applied" }
            $ZoneRestriction = if ((($VMSKUs | where Name -EQ $SkuName).Restrictions.Type | Out-String).Contains("Zone")){"NotAvavalableInZone: "+(((($VMSKUs | where Name -EQ $SkuName).Restrictions.RestrictionInfo.Zones)| Where-Object {$_}) -join ",")}else{"Available - No zone restrictions applied"}
            
            
            $OutTable += New-Object PSObject -Property @{
                                                         "Name" = $SkuName
                                                         "Location" = $Region
                                                         "Applies to SubscriptionID" = $SubId
                                                         "Subscription Restriction" = $LocRestriction
                                                         "Zone Restriction" = $ZoneRestriction
                                                         }
         }

$OutTable | select Name, Location, "Applies to SubscriptionID", "Region Restriction", "Zone Restriction" | Sort-Object -Property Name | FT
```

Příkaz vrátí výsledky jako:

```output
Name                   Location  Applies to SubscriptionID            Region Restriction                         Zone Restriction                        
----                   --------  -------------------------            ------------------------                   ----------------     
Standard_M128          centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Available - No region restrictions applied Available - No zone restrictions applied
Standard_M128-32ms     centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Available - No region restrictions applied Available - No zone restrictions applied
Standard_M128-64ms     centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Available - No region restrictions applied Available - No zone restrictions applied
Standard_M128dms_v2    centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx NotAvavalableInRegion                      NotAvavalableInZone: 1,2,3
```

## <a name="solution-2---azure-cli"></a>Řešení 2 – Azure CLI

K určení, které skladové jednotky jsou k dispozici v určité oblasti, použijte příkaz [AZ VM list-SKU](/cli/azure/vm#az_vm_list_skus) . Pomocí `--location` parametru můžete filtrovat výstup podle umístění. Použijte `--size` parametr pro hledání podle názvu částečné velikosti. Pomocí `--all` parametru můžete zobrazit všechny informace, včetně velikostí, které nejsou dostupné pro aktuální předplatné.

Musíte mít Azure CLI verze 2.15.0 nebo novější. K ověření verze použijte `az --version` . V případě potřeby [aktualizujte svou instalaci](/cli/azure/update-azure-cli).

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --all --output table
```

Příkaz vrátí výsledky jako:

```output
ResourceType     Locations       Name              Zones    Restrictions
---------------  --------------  ----------------  -------  --------------
virtualMachines  southcentralus  Standard_F1       1,2,3    None
virtualMachines  southcentralus  Standard_F2       1,2,3    None
virtualMachines  southcentralus  Standard_F4       1,2,3    None
...
virtualMachines  southcentralus  Standard_F72s_v2  1,2,3    NotAvailableForSubscription, type: Zone, locations: southcentralus, zones: 1,2,3
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