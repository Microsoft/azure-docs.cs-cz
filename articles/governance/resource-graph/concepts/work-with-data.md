---
title: Práce s rozsáhlými datovými sadami
description: Seznamte se s tím, jak ve velkých datových sadách získat záznamy, které se mají při práci se službou Azure Resource Graph získávat, formátovat, stránkovat a přeskakovat.
ms.date: 01/27/2021
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 1eaabfdd78712966f3b21d869259a312db31b7bc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98917686"
---
# <a name="working-with-large-azure-resource-data-sets"></a>Práce s velkými sadami dat prostředků Azure

Azure Resource Graph je navržený pro práci s a získávání informací o prostředcích v prostředí Azure. Graf prostředků usnadňuje získávání těchto dat, i když se dotazuje na tisíce záznamů. Graf prostředků má několik možností pro práci s těmito velkými datovými sadami.

Pokyny k práci s dotazy s vysokou frekvencí najdete v tématu [doprovodné materiály k omezením požadavků](./guidance-for-throttled-requests.md).

## <a name="data-set-result-size"></a>Velikost výsledku sady dat

Ve výchozím nastavení graf prostředků omezuje všechny dotazy na vrácení pouze **100** záznamů. Tento ovládací prvek chrání uživatele i službu před neúmyslnými dotazy, které by mohly vést k rozsáhlým datovým sadám. K této události nejčastěji dochází, když zákazník experimentuje s dotazy, aby vyhledal a vyfiltroval prostředky způsobem, který vyhovuje jejich konkrétním potřebám. Tento ovládací prvek se liší od použití [horních](/azure/kusto/query/topoperator) nebo [Omezení](/azure/kusto/query/limitoperator) operátorů jazyka Azure Průzkumník dat k omezení výsledků.

> [!NOTE]
> Při použití **prvního** se doporučuje seřadit výsledky alespoň v jednom sloupci pomocí `asc` nebo `desc` . Bez řazení jsou vrácené výsledky náhodné a nelze je opakovat.

Výchozí omezení lze přepsat všemi metodami interakce s diagramem prostředků. Následující příklady ukazují, jak změnit omezení velikosti datové sady na _200_:

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --first 200 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -First 200
```

V [REST API](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources)je ovládací prvek **$Top** a je součástí **QueryRequestOptionsu**.

Ovládací prvek, který je _nejvíce omezující_ , se podaří. Pokud Váš dotaz například používá operátory **Top** nebo **limit** a výsledkem by bylo více záznamů než **první**, maximální počet vrácených záznamů bude stejný jako **první**. Podobně, pokud je **horní** nebo **limit** menší než **první**, vrácená sada záznamů bude menší hodnota nakonfigurovaná funkcí **Top** nebo **limit**.

V současné době **má aktuálně maximální** povolenou hodnotu _5000_, která dosahuje záznamy s [výsledky stránkování](#paging-results) _1000_ .

> [!IMPORTANT]
> Je-li **první** nakonfigurován tak, aby byl větší než _1000_ záznamů, musí dotaz pro práci stránkováním **projektovat** pole **ID** . Pokud v dotazu chybí, odpověď se nezíská [a výsledky](#paging-results) se budou omezovat na _1000_ záznamů.

## <a name="skipping-records"></a>Vynechávání záznamů

Další možností pro práci s velkými sadami dat je ovládací prvek **Skip** . Tento ovládací prvek umožňuje vašemu dotazu přeskočit nebo přeskočit definovaný počet záznamů před vrácením výsledků. Funkce **Skip** je užitečná pro dotazy, které seřadí výsledky smysluplně, kde je záměrem získat záznamy někam uprostřed sady výsledků dotazu. Pokud jsou požadované výsledky na konci vrácené datové sady, je efektivnější použít jinou konfiguraci řazení a načíst výsledky z horní části sady dat.

> [!NOTE]
> Při použití příkazu **Přeskočit** doporučujeme seřazení výsledků alespoň v jednom sloupci pomocí `asc` nebo `desc` . Bez řazení jsou vrácené výsledky náhodné a nelze je opakovat. Pokud `limit` `take` jsou v dotazu nebo použity, **přeskočení** je ignorováno.

Následující příklady ukazují, jak přeskočit prvních _10_ záznamů, které dotaz by měl mít za následek, že se místo toho spustí vrácená sada výsledků s jedenáctým záznamem:

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -Skip 10
```

V [REST API](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources)je ovládací prvek **$Skip** a je součástí **QueryRequestOptionsu**.

## <a name="paging-results"></a>Výsledky stránkování

Pokud je nutné rozdělit sadu výsledků na menší sady záznamů ke zpracování nebo protože sada výsledků by překročila maximální povolenou hodnotu _1000_ vrácených záznamů, použijte stránkování. [REST API](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources) 
 **QueryResponse** poskytuje hodnoty pro indikaci sady výsledků byla rozdělena: **resultTruncated** a **$skipToken**. **resultTruncated** je logická hodnota, která informuje příjemce, pokud v odpovědi není vráceno více záznamů. Tato podmínka se dá identifikovat také v případě, že vlastnost **Count** je menší než vlastnost **totalRecords** . **totalRecords** definuje počet záznamů, které odpovídají dotazu.

 **resultTruncated** má **hodnotu true** , pokud je buď zakázáno stránkování, nebo není možné, protože není `id` k dispozici žádný sloupec, nebo pokud je k dispozici méně prostředků, než dotaz požaduje. Pokud  má resultTruncated **hodnotu true**, vlastnost **$skipToken** není nastavena.

Následující příklady ukazují, jak **Přeskočit** prvních 3000 záznamů a vracet **prvních** 1000 záznamů po přeskočení těchto záznamů pomocí Azure CLI a Azure PowerShell:

```azurecli-interactive
az graph query -q "Resources | project id, name | order by id asc" --first 1000 --skip 3000
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project id, name | order by id asc" -First 1000 -Skip 3000
```

> [!IMPORTANT]
> Aby bylo možné stránkování fungovat, dotaz musí **projektovat** pole **ID** . Pokud v dotazu chybí, nebude odpověď zahrnovat **$skipToken**.

Příklad naleznete v tématu [dotaz na další stránku](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources#next-page-query) v dokumentaci REST API.

## <a name="formatting-results"></a>Formátování výsledků

Výsledky dotazu grafu prostředků jsou k dispozici ve dvou formátech, _tabulce_ a _ObjectArray_. Formát je nakonfigurován s parametrem **resultFormat** jako součást možností žádosti. Formát _tabulky_ je výchozí hodnota pro **resultFormat**.

Výsledky z Azure CLI se ve výchozím nastavení poskytují ve formátu JSON. Výsledkem je, že ve výchozím nastavení jsou Azure PowerShell **PSCustomObject** , ale dají se rychle převést na JSON pomocí `ConvertTo-Json` rutiny. Pro jiné sady SDK se můžou výsledky dotazu nakonfigurovat na výstup formátu _ObjectArray_ .

### <a name="format---table"></a>Formát – tabulka

Výchozí formát, _tabulka_, vrátí výsledek ve formátu JSON navrženém pro zdůraznění návrhu sloupce a hodnot řádků vlastností vrácených dotazem. Tento formát se velmi podobá datům definovaným ve strukturované tabulce nebo tabulce se sloupci určenými jako první a pak každý řádek, který představuje data zarovnaná na tyto sloupce.

Tady je ukázka výsledku dotazu s formátováním _tabulky_ :

```json
{
    "totalRecords": 47,
    "count": 1,
    "data": {
        "columns": [{
                "name": "name",
                "type": "string"
            },
            {
                "name": "type",
                "type": "string"
            },
            {
                "name": "location",
                "type": "string"
            },
            {
                "name": "subscriptionId",
                "type": "string"
            }
        ],
        "rows": [
            [
                "veryscaryvm2-nsg",
                "microsoft.network/networksecuritygroups",
                "eastus",
                "11111111-1111-1111-1111-111111111111"
            ]
        ]
    },
    "facets": [],
    "resultTruncated": "true"
}
```

### <a name="format---objectarray"></a>Format – ObjectArray

Formát _ObjectArray_ také vrátí výsledky ve formátu JSON. Tento návrh ale zarovnává vztah mezi dvojici klíč/hodnota společný ve formátu JSON, kde se sloupce a data řádku shodují v rámci skupin polí.

Tady je ukázka výsledku dotazu s formátováním _ObjectArray_ :

```json
{
    "totalRecords": 47,
    "count": 1,
    "data": [{
        "name": "veryscaryvm2-nsg",
        "type": "microsoft.network/networksecuritygroups",
        "location": "eastus",
        "subscriptionId": "11111111-1111-1111-1111-111111111111"
    }],
    "facets": [],
    "resultTruncated": "true"
}
```

Tady je několik příkladů nastavení **resultFormat** pro použití formátu _ObjectArray_ :

```csharp
var requestOptions = new QueryRequestOptions( resultFormat: ResultFormat.ObjectArray);
var request = new QueryRequest(subscriptions, "Resources | limit 1", options: requestOptions);
```

```python
request_options = QueryRequestOptions(
    result_format=ResultFormat.object_array
)
request = QueryRequest(query="Resources | limit 1", subscriptions=subs_list, options=request_options)
response = client.resources(request)
```

## <a name="next-steps"></a>Další kroky

- Podívejte se na jazyk používaný v [počátečních dotazech](../samples/starter.md).
- Viz rozšířená použití v [rozšířených dotazech](../samples/advanced.md).
- Přečtěte si další informace o tom, jak [prozkoumat prostředky](explore-resources.md).
