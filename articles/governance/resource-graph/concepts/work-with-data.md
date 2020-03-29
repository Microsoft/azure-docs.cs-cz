---
title: Práce s rozsáhlými datovými sadami
description: Zjistěte, jak získat, formátovat, stránkovat a přeskočit záznamy ve velkých datových sadách při práci s Azure Resource Graph.
ms.date: 03/20/2020
ms.topic: conceptual
ms.openlocfilehash: be15a6234935627ca748276e6330c50c3ee5a775
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064748"
---
# <a name="working-with-large-azure-resource-data-sets"></a>Práce s velkými datovými sadami prostředků Azure

Azure Resource Graph je navržený pro práci s a získávání informací o prostředcích ve vašem prostředí Azure. Resource Graph umožňuje rychlé získání těchto dat, a to i při dotazování tisíce záznamů. Resource Graph má několik možností pro práci s těmito velkými datovými sadami.

Pokyny pro práci s dotazy na vysoké frekvenci, naleznete v [tématu pokyny pro omezené požadavky](./guidance-for-throttled-requests.md).

## <a name="data-set-result-size"></a>Velikost výsledku sady dat

Ve výchozím nastavení aplikace Resource Graph omezuje jakýkoli dotaz na vrácení pouze **100** záznamů. Tento ovládací prvek chrání uživatele i službu před neúmyslnými dotazy, které by vedly k velkým datovým souborům. Tato událost se nejčastěji děje, když zákazník experimentuje s dotazy, aby našel a filtroval prostředky způsobem, který vyhovuje jejich konkrétním potřebám. Tento ovládací prvek se liší od použití [horní](/azure/kusto/query/topoperator) nebo [omezit](/azure/kusto/query/limitoperator) operátory jazyka Azure Data Explorer omezit výsledky.

> [!NOTE]
> Při použití **first**se doporučuje seřadit výsledky alespoň `asc` podle `desc`jednoho sloupce s písmenem a). Bez řazení jsou vrácené výsledky náhodné a nelze je opakovat.

Výchozí limit lze přepsat všemi metodami interakce s grafem prostředků. Následující příklady ukazují, jak změnit limit velikosti sady dat na _200_:

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --first 200 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -First 200
```

V [rozhraní REST API](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources)je ovládací prvek **$top** a je součástí **QueryRequestOptions**.

Ovládací prvek, který je _nejvíce omezující_ vyhraje. Pokud například dotaz používá **operátory top** nebo **limit** a výsledkem by bylo více záznamů než **První**, maximální počet vrácených záznamů by se rovnal **prvnímu**. Podobně pokud **je horní** nebo **limit** menší než **První**, vrácená sada záznamů by byla menší hodnota nakonfigurovaná **horním** nebo **limitem**.

**První** má v současné době maximální povolenou hodnotu _5000_.

## <a name="skipping-records"></a>Přeskočení záznamů

Další možností pro práci s velkými sadami dat je ovládací prvek **Přeskočit.** Tento ovládací prvek umožňuje dotazu přeskočit nebo přeskočit definovaný počet záznamů před vrácením výsledků. **Přeskočit** je užitečné pro dotazy, které řazení výsledky smysluplným způsobem, kde záměrem je získat na záznamy někde uprostřed sady výsledků. Pokud jsou potřebné výsledky na konci vrácené datové sady, je efektivnější použít jinou konfiguraci řazení a načíst výsledky z horní části sady dat.

> [!NOTE]
> Při použití **funkce Přeskočit**doporučujeme seřadit výsledky alespoň `asc` `desc`o jeden sloupec s písmenem nebo). Bez řazení jsou vrácené výsledky náhodné a nelze je opakovat.

Následující příklady ukazují, jak přeskočit prvních _10_ záznamů, které by dotaz měl za následek, místo toho, aby se sada vrácených výsledků spustila jedenáctým záznamem:

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -Skip 10
```

V [rozhraní REST API](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources)je ovládací prvek **$skip** a je součástí **QueryRequestOptions**.

## <a name="paging-results"></a>Výsledky stránkování

Pokud je nutné rozdělit sadu výsledků na menší sady záznamů pro zpracování nebo protože sada výsledků by překročila maximální povolenou hodnotu _1000_ vrácených záznamů, použijte stránkování. [Rest API](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources) **QueryResponse** poskytuje hodnoty označující sadu výsledků byla rozdělena: **resultTruncated** a **$skipToken**.
**resultTruncated** je logická hodnota, která informuje spotřebitele, pokud nejsou v odpovědi vráceny další záznamy. Tuto podmínku lze také identifikovat, pokud je vlastnost **count** menší než vlastnost **totalRecords.** **totalRecords** definuje, kolik záznamů odpovídá dotazu.

 **resultTruncated** je **true,** pokud je stránkování zakázáno nebo není možné z důvodu žádného `id` sloupce nebo pokud je k dispozici méně prostředků, než je dotaz požadováno. Pokud **resultTruncated** je **true**, **vlastnost $skipToken** není nastavena.

Následující příklady ukazují, jak **přeskočit** prvních 3000 záznamů a vrátit **prvních** 1000 záznamů po těchto záznamů přeskočené pomocí Azure CLI a Azure PowerShell:

```azurecli-interactive
az graph query -q "Resources | project id, name | order by id asc" --first 1000 --skip 3000
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project id, name | order by id asc" -First 1000 -Skip 3000
```

> [!IMPORTANT]
> Dotaz musí **promítnout** **id** pole, aby stránkování fungovalo. Pokud v dotazu chybí, odpověď nebude obsahovat **$skipToken**.

Příklad najdete v tématu [Další stránkový dotaz](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources#next-page-query) v dokumentech rozhraní REST API.

## <a name="formatting-results"></a>Formátování výsledků

Výsledky dotazu na graf prostředků jsou k dispozici ve dvou formátech _Table_ a _ObjectArray_. Formát je konfigurován s parametrem **resultFormat** jako součást možností požadavku. Formát _Tabulka_ je výchozí hodnota pro **resultFormat**.

Výsledky z Azure CLI jsou k dispozici v JSON ve výchozím nastavení. Výsledky v Azure PowerShell jsou **PSCustomObject** ve výchozím nastavení, ale lze `ConvertTo-Json` je rychle převést na JSON pomocí rutiny. U ostatních sad SDK lze výsledky dotazu nakonfigurovat tak, aby výstup _formátu ObjectArray._

### <a name="format---table"></a>Formát – tabulka

Výchozí formát _Tabulka_vrátí výsledky ve formátu JSON určeném ke zvýraznění návrhu sloupce a hodnot řádků vlastností vrácených dotazem. Tento formát se velmi podobá datům definovaným ve strukturované tabulce nebo tabulce se sloupci identifikovanými jako první a potom s každým řádkem představujícím data zarovnaná k těmto sloupcům.

Zde je ukázka výsledku dotazu s formátováním _tabulky:_

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

### <a name="format---objectarray"></a>Formát - Objekt ObjectArray

Formát _ObjectArray_ také vrací výsledky ve formátu JSON. Tento návrh je však zarovnán s vztahem páru klíč/hodnota, který je běžný v jsonu, kde jsou data sloupce a řádku spárována ve skupinách polí.

Zde je ukázka výsledku dotazu s formátováním _ObjectArray:_

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

Zde jsou některé příklady nastavení **resultFormat** pro použití formátu _ObjectArray:_

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

- Podívejte se na jazyk, který se používá v [dotazech Starter](../samples/starter.md).
- Zobrazení pokročilých použití v [rozšířených dotazech](../samples/advanced.md).
- Přečtěte si další informace o tom, jak [prozkoumat zdroje](explore-resources.md).