---
title: Práce s velkými datovými sadami
description: Pochopit, jak vrátit velkých datových sad z grafu prostředků Azure.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/31/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: ab24e2045dabf045f1879dd76e599f5ab344f07b
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55513439"
---
# <a name="working-with-large-azure-resource-data-sets"></a>Práce s datovými sadami velkých prostředků Azure

Azure Graph prostředků je určená pro práci s a získávání informací o prostředcích v prostředí Azure. Prostředek grafu díky zrychlujeme tato data, i v případě, že dotazování tisíc záznamů. Zdroj grafu obsahuje celou řadu možností pro práci s těmito velkými datovými sadami.

## <a name="data-set-result-size"></a>Velikost datové sady výsledků

Ve výchozím omezení prostředků grafu jakýkoli dotaz vrací pouze **100** záznamy. Tento ovládací prvek chrání před neúmyslným dotazy, které by mělo za následek velkých datových sad uživatele a služby. Nejčastěji dochází k tomu jako zákazník je experimentování s dotazy k hledání a filtrování zdrojů způsobem, který vyhovuje jejich potřebám. Tento ovládací prvek se liší od používání [horní](/azure/kusto/query/topoperator) nebo [limit](/azure/kusto/query/limitoperator) operátory jazyka Průzkumníka služby Azure Data omezit rozsah výsledků.

Přes všechny metody interakci s grafem prostředků lze přepsat výchozí omezení. Následující příklady ukazují, jak chcete-li změnit omezení velikosti datové sady do _200_:

```azurecli-interactive
az graph query -q "project name | order by name asc" --first 200 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "project name | order by name asc" -First 200
```

V [rozhraní REST API](/rest/api/azureresourcegraph/resources/resources), je ovládací prvek **$top** a je součástí **QueryRequestOptions**.

Ovládací prvek, který je _nejvíce omezující_ vyhraje. Například, pokud váš dotaz využívá **horní** nebo **limit** operátory a bude mít za následek více záznamů, než **první**, maximální počet záznamů, vrátila by byla stejná **První**. Podobně pokud **horní** nebo **limit** je menší než **první**, sady vrácené záznamů bude menší hodnotu nakonfiguroval **horní** nebo **limit**.

**První** maximální povolenou hodnotu, která má v současné době _5000_.

## <a name="skipping-records"></a>Přeskakuje záznamy

Další možnost pro práci s velkými datovými sadami **přeskočit** ovládacího prvku. Tento ovládací prvek umožňuje tak přeskočím nebo přeskočit definovaný počet záznamů před vrácením výsledky dotazu. **Přeskočit** je užitečné pro dotazy, které řazení výsledků srozumitelným způsobem kde je cílem získat na záznamy někde uprostřed sadu výsledků dotazu. Pokud jsou na konci sady dat vrácené výsledky potřebné, je efektivnější použít konfiguraci jinou řazení a místo toho načtěte výsledky z horní části datové sady.

Následující příklady ukazují, jak chcete-li přeskočit první _10_ záznamy dotazu by výsledkem, místo toho počínaje vrácený výsledek nastavit s 11 záznam:

```azurecli-interactive
az graph query -q "project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "project name | order by name asc" -Skip 10
```

V [rozhraní REST API](/rest/api/azureresourcegraph/resources/resources), je ovládací prvek **$skip** a je součástí **QueryRequestOptions**.

## <a name="paging-results"></a>Výsledky stránkování

V případě potřeby rozdělit do menších sad záznamů pro zpracování sady výsledků, nebo protože je sada výsledků dotazu by překročil maximální povolenou hodnotu, která _5000_ vrácené záznamy, použít stránkování. [Rozhraní REST API](/rest/api/azureresourcegraph/resources/resources) **QueryResponse** poskytuje hodnoty k označení výsledků bylo přerušeno set up: **resultTruncated** a **$skipToken** .
**resultTruncated** je logická hodnota, která informuje příjemce, pokud existují další záznamy se ne vrátila v odpovědi. K tomuto stavu může být také identifikovat, kdy **počet** vlastnost je menší než **totalRecords** vlastnost. **totalRecords** definuje, kolik záznamů, které odpovídají dotazu.

Když **resultTruncated** je **true**, **$skipToken** je nastavena v odpovědi. Tato hodnota se používá se stejnými hodnotami dotazu a předplatného můžete získat další sady záznamů, které odpovídají dotazu.

> [!IMPORTANT]
> Dotaz musí **projektu** **id** pole v pořadí pro stránkování pro práci. Pokud je v dotazu nebyl nalezen, nebude obsahovat odpověď rozhraní REST API **$skipToken**.

Příklad najdete v tématu [další stránky dotaz](/rest/api/azureresourcegraph/resources/resources#next_page_query) v dokumentaci rozhraní REST API.

## <a name="next-steps"></a>Další postup

- Zobrazit jazyk v aplikaci [Starter dotazy](../samples/starter.md)
- Viz advanced používá v [upřesňujících dotazů](../samples/advanced.md)
- Naučte se [prozkoumat zdroje](explore-resources.md)