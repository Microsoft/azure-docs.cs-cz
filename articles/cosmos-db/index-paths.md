---
title: Práce s cestami indexu ve službě Azure Cosmos DB
description: Přehled cesty indexu ve službě Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/5/2018
ms.author: rimman
ms.openlocfilehash: 0515397fb9ab0f05b4c763a2b05e9d986960bd91
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51628959"
---
# <a name="index-paths-in-azure-cosmos-db"></a>Index cesty ve službě Azure Cosmos DB

S použitím cesty indexu ve službě Azure Cosmos DB, můžete zahrnout nebo vyloučit určité cesty indexování. Výběr určitých cestách nabízí vylepšené zápisu výkon a dolní index úložiště pro scénáře, ve kterých víte, vzory dotazů. Index začátku cesty s kořenovým (`/`) operátor zástupný znak a obvykle ukončen `?` zástupných znaků operátoru. Tento model označuje, že existuje několik možných hodnot pro předponu. Například k poskytování dotaz `SELECT * FROM Families F WHERE F.familyName = "Andersen"`, musí obsahovat cestu k indexu pro `/familyName/?` v kontejneru zásad indexu.

Cesty k indexu můžete použít také `*` operátor zástupný znak můžete určit chování pro rekurzivně cest v rámci předponu. Například `/payload/*` lze použít k vyloučení všechno v datové části vlastnost indexování.

## <a name="common-patterns-for-index-paths"></a>Obecné vzory pro index cesty

Tady jsou běžné vzory pro zadání cesty k indexu:

| **Cesta** | **Popis/případ** |
| ---------- | ------- |
| /   | Výchozí cesta pro kolekci. Rekurzivní a platí pro celý dokument stromu.|
| / prop /?  | Cesta index zapotřebí pro zpracování dotazů jako následující (s typy hodnot Hash nebo rozsahu, v uvedeném pořadí):<br><br>Vyberte z kolekce c WHERE c.prop = "hodnota"<br><br>Vyberte z kolekce c WHERE c.prop > 5<br><br>Vyberte z kolekce c ORDER BY c.prop  |
| / prop / *  | Cesta index u všech cest v rámci zadaného popisku. Funguje s následující dotazy<br><br>Vyberte z kolekce c WHERE c.prop = "hodnota"<br><br>Vyberte z kolekce c WHERE c.prop.subprop > 5<br><br>Vyberte z kolekce c WHERE c.prop.subprop.nextprop = "hodnota"<br><br>Vyberte z kolekce c ORDER BY c.prop |
| / Vlastnosti / [] /?  | Požadované index cestu iterace a připojte se k dotazy na pole skaláry, jako je ["a", "b", "c"]:<br><br>Vyberte značku ze značky v collection.props značky WHERE = "hodnota"<br><br>Vyberte značku z kolekce c spojení značky v c.props kde označit > 5  |
| [] /subprop/ /props/? | Index cesta zapotřebí pro zpracování iterace a spojení dotazy na pole objektů, jako je [{subprop: "a"}, {subprop: "b"}]:<br><br>Vyberte značku ze značky v collection.props WHERE tag.subprop = "hodnota"<br><br>Vyberte značku z kolekce c spojení značky v c.props WHERE tag.subprop = "hodnota" |
| / prop/subprop /? | Cesta indexu, které jsou zapotřebí pro zpracování dotazů (s typy hodnot Hash nebo rozsahu, v uvedeném pořadí):<br><br>Vyberte z kolekce c WHERE c.prop.subprop = "hodnota"<br><br>Vyberte z kolekce c WHERE c.prop.subprop > 5  |

Když nastavíte vlastní index cesty, je nutné zadat výchozí pravidlo indexování pro celou položku udávají zvláštní cesta `/*`.

## <a name="next-steps"></a>Další postup

Další informace o indexování ve službě Azure Cosmos DB v následujících článcích:

- [Přehled indexování](index-overview.md)
- [Zásady indexování ve službě Azure Cosmos DB](indexing-policies.md)
- [Typy indexu ve službě Azure Cosmos DB](index-types.md)
