---
title: Řešení potíží s ConstrainedAllocationFailed při nasazení cloudové služby do Azure | Microsoft Docs
description: Tento článek ukazuje, jak vyřešit výjimku ConstrainedAllocationFailed při nasazení cloudové služby do Azure.
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/04/2020
ms.openlocfilehash: de344bbcd89158676bacf2a8aa1743d282700b9d
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520961"
---
# <a name="troubleshoot-constrainedallocationfailed-when-deploying-a-cloud-service-to-azure"></a>Řešení potíží s ConstrainedAllocationFailed při nasazení cloudové služby do Azure

V tomto článku vyřešíte selhání přidělení, kde se Azure Cloud Services nemůže nasadit z důvodu omezení.

Microsoft Azure přiděluje:

- Upgrade instancí Cloud Services

- Přidání nových instancí webové role nebo role pracovního procesu

- Nasazení instancí do cloudové služby

Během těchto operací může občas docházet k chybám, i když jste dosáhli limitu předplatného Azure.

> [!TIP]
> Tyto informace mohou být užitečné také při plánování nasazení služeb.

## <a name="symptom"></a>Příznak

V Azure Portal přejděte na svou cloudovou službu a na bočním panelu vyberte *protokoly operací (klasické)* a zobrazte si protokoly.

Při kontrole protokolů vaší cloudové služby se zobrazí následující výjimka:

|Typ výjimky  |Chybová zpráva  |
|---------|---------|
|ConstrainedAllocationFailed     |Operace Azure se `{Operation ID}` nezdařila s kódem Compute. ConstrainedAllocationFailed. Podrobnosti: přidělení nebylo úspěšné; v požadavku nelze naplnit omezení. Požadované nasazení nové služby je spojené se skupinou vztahů nebo je jeho cílem virtuální síť, anebo je v této hostované službě nějaké nasazení. Kterákoli z těchto podmínek omezuje nové nasazení na konkrétní prostředky Azure. Zkuste to znovu později nebo zkuste zmenšit velikost virtuálního počítače nebo počet instancí rolí. Pokud je to možné, můžete případně odebrat výše uvedená omezení nebo vyzkoušet nasazení v jiné oblasti.|

## <a name="cause"></a>Příčina

Došlo k potížím s kapacitou v oblasti nebo clusteru, na který nasazujete. K tomu dojde, pokud SKU prostředků, které jste vybrali, není pro zadané umístění k dispozici.

> [!NOTE]
> Když je první uzel cloudové služby nasazený, *připnuté* do fondu zdrojů. Fond zdrojů může být samostatný cluster nebo skupina clusterů.
>
> V průběhu času se prostředky v tomto fondu zdrojů můžou plně využívat. Pokud cloudová služba vytvoří požadavek na přidělení dalších prostředků v případě, že nejsou k dispozici dostatečné prostředky v připnutém fondu zdrojů, bude mít tato žádost [chybnou alokaci](cloud-services-allocation-failures.md).

## <a name="solution"></a>Řešení

V tomto scénáři byste měli vybrat jinou oblast nebo SKU, do které chcete nasadit cloudovou službu. Než nasadíte nebo upgradujete cloudovou službu, můžete určit, které skladové jednotky jsou k dispozici v oblasti nebo zóně dostupnosti. Použijte níže uvedené procesy [Azure CLI](#list-skus-in-region-using-azure-cli), [PowerShell](#list-skus-in-region-using-powershell)nebo [REST API](#list-skus-in-region-using-rest-api) .

### <a name="list-skus-in-region-using-azure-cli"></a>Výpis SKU v oblasti pomocí Azure CLI

Můžete použít příkaz [AZ VM list-SKU](https://docs.microsoft.com/cli/azure/vm.html#az_vm_list_skus) .

- Pomocí `--location` parametru můžete filtrovat výstup do umístění, které používáte.
- Použijte `--size` parametr pro hledání podle názvu částečné velikosti.
- Další informace najdete v tématu věnovaném [chybě vyřešit chybu pro SKU, který není k dispozici](../azure-resource-manager/templates/error-sku-not-available.md#solution-2---azure-cli) .

    **Příklad:**

    ```azurecli
    az vm list-skus --location southcentralus --size Standard_F --output table
    ```

    **Příklady výsledků:** ![ Výstup Azure CLI s spuštěním příkazu AZ VM list-SKU--Location southcentralus--Size Standard_F--Output Table, který zobrazuje dostupné SKU.](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-constrained-allocation-failed-1.png)

#### <a name="list-skus-in-region-using-powershell"></a>Výpis SKU v oblasti pomocí PowerShellu

Můžete použít příkaz [Get-AzComputeResourceSku](https://docs.microsoft.com/powershell/module/az.compute/get-azcomputeresourcesku) .

- Filtrovat výsledky podle umístění.
- Pro tento příkaz musíte mít nejnovější verzi PowerShellu.
- Další informace najdete v tématu věnovaném [chybě vyřešit chybu pro SKU, který není k dispozici](../azure-resource-manager/templates/error-sku-not-available.md#solution-1---powershell) .

**Příklad:**

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

Můžete použít operaci [seznam SKU prostředku](https://docs.microsoft.com/rest/api/compute/resourceskus/list) . Vrátí dostupné SKU a oblasti v následujícím formátu:

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
    <Rest_of_your_file_is_located_here...>
  ]
}
    
```

## <a name="next-steps"></a>Další kroky

Další řešení pro selhání přidělení a lepší pochopení způsobu jejich vygenerování:

> [!div class="nextstepaction"]
> [Selhání přidělení (cloudové služby)](cloud-services-allocation-failures.md)

Pokud váš problém s Azure není v tomto článku vyřešen, navštivte fóra Azure na [webu MSDN a Stack Overflow](https://azure.microsoft.com/support/forums/). Svůj problém můžete vystavit na těchto fórech nebo odeslat na [ @AzureSupport Twitter](https://twitter.com/AzureSupport). Můžete také odeslat žádost o podporu Azure. Pokud chcete odeslat žádost o podporu, vyberte na stránce [podpory Azure](https://azure.microsoft.com/support/options/) možnost *získat podporu*.
