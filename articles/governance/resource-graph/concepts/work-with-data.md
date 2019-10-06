---
title: Práce s rozsáhlými datovými sadami
description: Naučte se, jak získat a řídit velké datové sady při práci s Azure Resource graphem.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/01/2019
ms.topic: conceptual
ms.service: resource-graph
ms.openlocfilehash: 4da890a5ef7acb44d0e8628dc4ec3904f6a065e4
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2019
ms.locfileid: "71980332"
---
# <a name="working-with-large-azure-resource-data-sets"></a>Práce s velkými sadami dat prostředků Azure

Azure Resource Graph je navržený pro práci s a získávání informací o prostředcích v prostředí Azure. Graf prostředků usnadňuje získávání těchto dat, i když se dotazuje na tisíce záznamů. Graf prostředků má několik možností pro práci s těmito velkými datovými sadami.

Pokyny k práci s dotazy s vysokou frekvencí najdete v tématu [doprovodné materiály k omezením požadavků](./guidance-for-throttled-requests.md).

## <a name="data-set-result-size"></a>Velikost výsledku sady dat

Ve výchozím nastavení graf prostředků omezuje všechny dotazy na vrácení pouze **100** záznamů. Tento ovládací prvek chrání uživatele i službu před neúmyslnými dotazy, které by mohly vést k rozsáhlým datovým sadám. K této události nejčastěji dochází, když zákazník experimentuje s dotazy, aby vyhledal a vyfiltroval prostředky způsobem, který vyhovuje jejich konkrétním potřebám. Tento ovládací prvek se liší od použití [horních](/azure/kusto/query/topoperator) nebo [Omezení](/azure/kusto/query/limitoperator) operátorů jazyka Azure Průzkumník dat k omezení výsledků.

> [!NOTE]
> Při použití **prvního**se doporučuje seřadit výsledky alespoň v jednom sloupci pomocí `asc` nebo `desc`. Bez řazení jsou vrácené výsledky náhodné a nelze je opakovat.

Výchozí omezení lze přepsat všemi metodami interakce s diagramem prostředků. Následující příklady ukazují, jak změnit omezení velikosti datové sady na _200_:

```azurecli-interactive
az graph query -q "project name | order by name asc" --first 200 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "project name | order by name asc" -First 200
```

V [REST API](/rest/api/azureresourcegraph/resources/resources)je ovládací prvek **$Top** a je součástí **QueryRequestOptionsu**.

Ovládací prvek, který je _nejvíce omezující_ , se podaří. Pokud Váš dotaz například používá operátory **Top** nebo **limit** a výsledkem by bylo více záznamů než **první**, maximální počet vrácených záznamů bude stejný jako **první**. Podobně, pokud je **horní** nebo **limit** menší než **první**, vrácená sada záznamů bude menší hodnota nakonfigurovaná funkcí **Top** nebo **limit**.

**Aktuálně má** maximální povolenou hodnotu _5000_.

## <a name="skipping-records"></a>Vynechávání záznamů

Další možností pro práci s velkými sadami dat je ovládací prvek **Skip** . Tento ovládací prvek umožňuje vašemu dotazu přeskočit nebo přeskočit definovaný počet záznamů před vrácením výsledků. Funkce **Skip** je užitečná pro dotazy, které seřadí výsledky smysluplně, kde je záměrem získat záznamy někam uprostřed sady výsledků dotazu. Pokud jsou požadované výsledky na konci vrácené datové sady, je efektivnější použít jinou konfiguraci řazení a načíst výsledky z horní části sady dat.

> [!NOTE]
> Při použití příkazu **Přeskočit**doporučujeme seřazení výsledků alespoň v jednom sloupci pomocí `asc` nebo `desc`. Bez řazení jsou vrácené výsledky náhodné a nelze je opakovat.

Následující příklady ukazují, jak přeskočit prvních _10_ záznamů, které dotaz by měl mít za následek, že se místo toho spustí vrácená sada výsledků s jedenáctým záznamem:

```azurecli-interactive
az graph query -q "project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "project name | order by name asc" -Skip 10
```

V [REST API](/rest/api/azureresourcegraph/resources/resources)je ovládací prvek **$Skip** a je součástí **QueryRequestOptionsu**.

## <a name="paging-results"></a>Výsledky stránkování

Pokud je nutné rozdělit sadu výsledků na menší sady záznamů ke zpracování nebo protože sada výsledků by překročila maximální povolenou hodnotu _1000_ vrácených záznamů, použijte stránkování. [REST API](/rest/api/azureresourcegraph/resources/resources) **QueryResponse** poskytuje hodnoty pro indikaci sady výsledků byla rozdělena: **resultTruncated** a **$skipToken**.
**resultTruncated** je logická hodnota, která informuje příjemce, pokud v odpovědi nejsou vráceny další záznamy. Tato podmínka se dá identifikovat také v případě, že vlastnost **Count** je menší než vlastnost **totalRecords** . **totalRecords** definuje počet záznamů, které odpovídají dotazu.

Pokud má resultTruncated **hodnotu true**, v odpovědi se nastaví vlastnost **$skipToken** . Tato hodnota se používá se stejnými hodnotami dotazů a předplatného k získání další sady záznamů, které odpovídají dotazu.

Následující příklady ukazují, jak **Přeskočit** prvních 3000 záznamů a vracet **prvních** 1000 záznamů po vynechání pomocí Azure CLI a Azure PowerShell:

```azurecli-interactive
az graph query -q "project id, name | order by id asc" --first 1000 --skip 3000
```

```azurepowershell-interactive
Search-AzGraph -Query "project id, name | order by id asc" -First 1000 -Skip 3000
```

> [!IMPORTANT]
> Aby bylo možné stránkování fungovat, dotaz musí **projektovat** pole **ID** . Pokud v dotazu chybí, nebude odpověď zahrnovat **$skipToken**.

Příklad naleznete v tématu [dotaz na další stránku](/rest/api/azureresourcegraph/resources/resources#next-page-query) v dokumentaci REST API.

## <a name="next-steps"></a>Další kroky

- Podívejte se na jazyk používaný v [počátečních dotazech](../samples/starter.md).
- Viz rozšířená použití v [rozšířených dotazech](../samples/advanced.md).
- Naučte se [prozkoumat prostředky](explore-resources.md).