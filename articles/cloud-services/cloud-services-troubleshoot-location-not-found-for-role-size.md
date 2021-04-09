---
title: Řešení potíží s LocationNotFoundForRoleSize při nasazení cloudové služby (Classic) do Azure | Microsoft Docs
description: Tento článek popisuje, jak vyřešit výjimku LocationNotFoundForRoleSize při nasazení cloudové služby (Classic) do Azure.
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 3d1a1135db0421d89d4c6c9f278c86e02cb5cb32
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558954"
---
# <a name="troubleshoot-locationnotfoundforrolesize-when-deploying-a-cloud-service-classic-to-azure"></a>Řešení potíží s LocationNotFoundForRoleSize při nasazení cloudové služby (Classic) do Azure

V tomto článku vyřešíte selhání přidělení, kde při nasazení cloudové služby Azure (Classic) není dostupná velikost virtuálního počítače.

Když nasadíte instance do cloudové služby (Classic) nebo přidáte nové instance webových nebo pracovních rolí, Microsoft Azure přidělí výpočetní prostředky.

Během těchto operací může občas docházet k chybám, i když jste dosáhli limitu předplatného Azure.

> [!TIP]
> Tyto informace mohou být užitečné také při plánování nasazení služeb.

## <a name="symptom"></a>Příznak

V Azure Portal přejděte do vaší cloudové služby (Classic) a na bočním panelu vyberte *protokol operací (Classic)* a zobrazte si protokoly.

![Image zobrazuje okno protokol operací (Classic).](./media/cloud-services-troubleshoot-location-not-found-for-role-size/cloud-services-troubleshoot-allocation-logs.png)

Při kontrole protokolů vaší cloudové služby (Classic) se zobrazí následující výjimka:

|Typ výjimky  |Chybová zpráva  |
|---------|---------|
|LocationNotFoundForRoleSize     |Operace ' `{Operation ID}` ' se nezdařila: ' požadovaná vrstva virtuálního počítače není v oblasti ( `{Region ID}` ) pro toto předplatné aktuálně k dispozici. Zkuste prosím jinou vrstvu nebo ji nasaďte jinam.|

## <a name="cause"></a>Příčina

Došlo k potížím s kapacitou v oblasti nebo clusteru, na který nasazujete. K výjimce *LocationNotFoundForRoleSize* dojde, pokud pro určenou oblast není k dispozici vybraná SKU prostředků (velikost virtuálního počítače).

## <a name="solution"></a>Řešení

V tomto scénáři byste měli vybrat jinou oblast nebo SKU, do které chcete nasadit cloudovou službu (Classic). Než nasadíte nebo upgradujete cloudovou službu (Classic), můžete určit, které skladové jednotky jsou k dispozici v oblasti nebo zóně dostupnosti. Použijte níže uvedené procesy [Azure CLI](#list-skus-in-region-using-azure-cli), [PowerShell](#list-skus-in-region-using-powershell)nebo [REST API](#list-skus-in-region-using-rest-api) .

### <a name="list-skus-in-region-using-azure-cli"></a>Výpis SKU v oblasti pomocí Azure CLI

Můžete použít příkaz [AZ VM list-SKU](/cli/azure/vm.html#az_vm_list_skus) .

- Pomocí `--location` parametru můžete filtrovat výstup do umístění, které používáte.
- Použijte `--size` parametr pro hledání podle názvu částečné velikosti.
- Další informace najdete v tématu věnovaném [chybě vyřešit chybu pro SKU, který není k dispozici](../azure-resource-manager/templates/error-sku-not-available.md#solution-2---azure-cli) .

    **Například:**

    ```azurecli
    az vm list-skus --location southcentralus --size Standard_F --output table
    ```

    **Příklady výsledků:** ![ Výstup Azure CLI s spuštěním příkazu AZ VM list-SKU--Location southcentralus--Size Standard_F--Output Table, který zobrazuje dostupné SKU.](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-constrained-allocation-failed-1.png)

#### <a name="list-skus-in-region-using-powershell"></a>Výpis SKU v oblasti pomocí PowerShellu

Můžete použít příkaz [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) .

- Filtrovat výsledky podle umístění.
- Pro tento příkaz musíte mít nejnovější verzi PowerShellu.
- Další informace najdete v tématu věnovaném [chybě vyřešit chybu pro SKU, který není k dispozici](../azure-resource-manager/templates/error-sku-not-available.md#solution-1---powershell) .

**Například:**

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

**Některé další užitečné příkazy:**

Vyfiltrujte umístění, která obsahují velikost (Standard_DS14_v2):

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("Standard_DS14_v2")}
```

Vyfiltrujte všechna umístění, která obsahují velikost (V3):

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("v3")} | fc
```

#### <a name="list-skus-in-region-using-rest-api"></a>Výpis SKU v oblasti pomocí REST API

Můžete použít operaci [seznam SKU prostředku](/rest/api/compute/resourceskus/list) . Vrátí dostupné SKU a oblasti v následujícím formátu:

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
      <<The rest of your file is located here>>
  ]
}
    
```

## <a name="next-steps"></a>Další kroky

Další řešení pro selhání přidělení a lepší pochopení způsobu jejich vygenerování:

> [!div class="nextstepaction"]
> [Selhání přidělení – cloudová služba (klasická)](cloud-services-allocation-failures.md)

Pokud váš problém s Azure není v tomto článku vyřešen, navštivte fóra Azure na [webu MSDN a Stack Overflow](https://azure.microsoft.com/support/forums/). Svůj problém můžete vystavit na těchto fórech nebo odeslat na [ @AzureSupport Twitter](https://twitter.com/AzureSupport). Můžete také odeslat žádost o podporu Azure. Pokud chcete odeslat žádost o podporu, vyberte na stránce [podpory Azure](https://azure.microsoft.com/support/options/) možnost *získat podporu*.