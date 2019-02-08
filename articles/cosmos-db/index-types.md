---
title: Typy indexu ve službě Azure Cosmos DB
description: Přehled typů indexu ve službě Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/5/2018
ms.author: rimman
ms.openlocfilehash: 02055ec07de2b08abdc949e17c668912431e00ce
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55871247"
---
# <a name="index-types-in-azure-cosmos-db"></a>Typy indexu ve službě Azure Cosmos DB

Existuje několik možností kde konfigurujete zásady indexování pro cestu. Můžete zadat jednu nebo víc definic indexování pro každou cestu:

- **Datový typ:** Řetězec, číslo, bod, mnohoúhelník nebo LineString (může obsahovat jenom jeden záznam za datový typ na cestu).

- **Typ indexu:** Hodnota hash (dotazy na rovnost), rozsah (rovnosti, oblast nebo ORDER BY dotazy) nebo Spatial (prostorový dotazy).

- **Přesnost:** Pro index Hash to se liší od 1 do 8 pro čísla i řetězce a výchozí hodnota je 3. Hodnota Maximální přesnost pro index na rozsah, je -1. To se může lišit od 1 do 100 (Maximální přesnost) pro řetězec nebo číselné hodnoty.

## <a name="index-kind"></a>Typ indexu

Azure Cosmos DB podporuje hodnoty Hash index a rozsah index pro každou cestu, která je možné nakonfigurovat pro datové typy řetězec nebo číslo, nebo obojí.

- **Hash index** podporuje efektivní rovnosti a dotazy spojení. Pro většinu případů použití nepotřebujete indexy Hash vyšší přesností než na výchozí hodnotu 3 bajtů. Datový typ může být řetězec nebo číslo.

  > [!NOTE]
  > Kontejnery služby Azure Cosmos podporuje nové rozložení index, který se již nepoužívá index typu Hash. Pokud chcete zadat index typu Hash na zásady indexování, CRUD požadavků na kontejner, bude tiše ignorovat typ indexu a odpověď z kontejneru obsahuje pouze index typu rozsah. Všechny nové kontejnery Cosmos použít nové rozložení index ve výchozím nastavení. 
  
- **Index rozsahu** podporuje dotazy na rovnost efektivní, dotazy na rozsah (pomocí >, <>, =, < =,! =) a dotazy klauzule ORDER BY. Dotazy klauzule ORDER By ve výchozím nastavení také vyžadovat maximální index přesnosti (-1). Datový typ může být řetězec nebo číslo.

- **Prostorový index** podporuje efektivní spatial (v rámci a vzdálenost) dotazy. Datový typ může být bodu mnohoúhelníku či LineString. Azure Cosmos DB podporuje také typ prostorového indexu pro každou cestu, která se dá nastavit pro datové typy, které bod mnohoúhelníku či LineString. Hodnota v zadané cestě musí být platný fragment GeoJSON jako {"type": "Point", "coordinates": [0.0, 10.0]}. Azure Cosmos DB podporuje automatické indexování bodu mnohoúhelníku a LineString datových typů.

Tady jsou příklady dotazů, které hodnoty Hash, rozsah, a prostorové indexy lze použít pro obsluhu:

| **Typ indexu** | **Popis/případ** |
| ---------- | ---------------- |
| Hodnota hash  | Hodnota hash přes/prop /? (nebo /) umožňuje efektivně slouží následující dotazy:<br><br>Vyberte z kolekce c WHERE c.prop = "hodnota"<br><br>Hodnota hash přes/vlastnosti / [] /? (nebo / / vlastnosti/nebo) umožňuje efektivně slouží následující dotazy:<br><br>Vyberte značku z kolekce c spojení značky v c.props značky WHERE = 5  |
| Rozsah  | V rozsahu přes/prop /? (nebo /) umožňuje efektivně slouží následující dotazy:<br><br>Vyberte z kolekce c WHERE c.prop = "hodnota"<br><br>Vyberte z kolekce c WHERE c.prop > 5<br><br>Vyberte z kolekce c ORDER BY c.prop   |
| Spatial     | V rozsahu přes/prop /? (nebo /) umožňuje efektivně slouží následující dotazy:<br><br>Vyberte z kolekce c<br><br>KDE ST_DISTANCE (c.prop, {"type": "Point", "coordinates": [0.0, 10.0]}) < 40<br><br>Vyberte z kolekce c kde ST_WITHIN(c.prop, {"type": "Místo",...}) --s indexování povolený tento počet bodů<br><br>Vyberte z kolekce c kde ST_WITHIN({"type": "Mnohoúhelníku",...}, c.prop) – s indexování na mnohoúhelníky povolena.     |

## <a name="default-behavior-of-index-kinds"></a>Výchozí chování druhů indexu

- Pokud není žádný index rozsahu žádné přesnosti, který signalizuje, že kontrola může být potřeba poskytovat dotazu, v takovém případě ve výchozím nastavení, pro dotazy v rozsahu operátory, jako je vrácena chyba > =.

- Dotazy na rozsah můžete provést bez index na rozsah s použitím "x-ms-documentdb-enable kontroly" záhlaví v rozhraní REST API nebo žádost o možnost "EnableScanInQuery" s použitím sady .NET SDK. Pokud nejsou žádné filtry v dotazu, že služby Azure Cosmos DB pomocí indexu můžete filtrovat proti, je vrácena žádná chyba.

- Ve výchozím nastavení je vrácena chyba pro prostorových dotazů není k dispozici prostorový index nebo jinými filtry, které může obsloužit z indexu. Tyto dotazy můžete pomocí x-ms-documentdb-enable kontroly nebo EnableScanInQuery možné provést, protože kontroly.

## <a name="index-precision"></a>Index přesnost

- Aby kompromis mezi nároky na úložiště indexů a výkon dotazů, můžete použít index přesnost. Pro čísla doporučujeme použít výchozí konfiguraci přesnost-1 (maximální). Protože jsou čísla 8 bajtů ve formátu JSON, jde o ekvivalent konfigurace 8 bajtů. Výběrem hodnoty nižší přesnost, jako je například 1 až 7, prostředky, které hodnoty v rámci některé oblasti mapují na stejný index položky. Proto můžete snížit index úložného prostoru, ale provádění dotazů může být nutné zpracovat více položek. V důsledku toho využívá další propustnost/RU.

- Index přesnosti má více praktické využití s oblastmi řetězec. Protože řetězce může být jakékoli libovolné délky, volba přesnosti indexu může ovlivnit výkon dotazů na rozsah řetězec. Velikost indexu úložného prostoru, který je potřeba také může ovlivnit. Řetězec rozsah indexů lze nastavit s přesností na index mezi 1 a 100 nebo -1 (maximální). Pokud chcete provést klauzule ORDER BY dotazy na vlastnosti řetězce, je nutné zadat s přesností na -1 pro odpovídající cesty.

- Prostorové indexy vždy používat výchozí přesnost index pro všechny typy (Point, LineString a mnohoúhelníku). Nelze přepsat výchozí přesnost index pro prostorové indexy.

Azure Cosmos DB vrátí chybu při dotazu pomocí klauzule ORDER BY ale není zaškrtnuta možnost index na rozsah proti dotazované cestu s nejvyšší přesností.

> [!NOTE]
> Kontejnery služby Azure Cosmos podporuje nové rozložení index, který se už nevyžaduje vlastního indexového přesností než value(-1) maximální přesnost. Pomocí této metody vždy jsou cesty indexované Maximální přesnost. Pokud zadáte hodnotu přesnost na zásady indexování, žádosti CRUD kontejnerům, bude tiše ignorovat hodnota přesnosti a odpověď z kontejneru obsahuje pouze value(-1) maximální přesnost.  Všechny nové kontejnery Cosmos použít nové rozložení index ve výchozím nastavení.

## <a name="next-steps"></a>Další postup

Další informace o indexování ve službě Azure Cosmos DB, najdete v následujících článcích:

- [Přehled indexování](index-overview.md)
- [Zásady indexování](indexing-policies.md)
- [Index cesty](index-paths.md)

