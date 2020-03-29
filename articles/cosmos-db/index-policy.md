---
title: Zásady indexování Azure Cosmos DB
description: Zjistěte, jak nakonfigurovat a změnit výchozí zásady indexování pro automatické indexování a vyšší výkon v Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: tisande
ms.openlocfilehash: 930f156ebec76be860e7af02d41540ce67982f92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292071"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Zásady indexování ve službě Azure Cosmos DB

V Azure Cosmos DB každý kontejner má zásady indexování, která určuje, jak by měly být indexovány položky kontejneru. Výchozí zásady indexování pro nově vytvořené kontejnery indexuje každou vlastnost každé položky, vynucuje indexy rozsahu pro libovolný řetězec nebo číslo a prostorové indexy pro libovolný objekt GeoJSON typu Point. To vám umožní získat vysoký výkon dotazu bez nutnosti přemýšlet o indexování a správu indexu předem.

V některých situacích možná budete chtít toto automatické chování přepsat, aby lépe vyhovovalo vašim požadavkům. Zásady indexování kontejneru můžete přizpůsobit nastavením *režimu indexování*a zahrnout nebo vyloučit *cesty vlastností*.

> [!NOTE]
> Metoda aktualizace zásad indexování popsaných v tomto článku platí pouze pro rozhraní SQL (Core) rozhraní API Služby Azure Cosmos DB.

## <a name="indexing-mode"></a>Režim indexování

Azure Cosmos DB podporuje dva režimy indexování:

- **Konzistentní**: Index je aktualizován synchronně při vytváření, aktualizaci nebo odstraňování položek. To znamená, že konzistence vašich read dotazů bude [konzistence nakonfigurovaná pro účet](consistency-levels.md).
- **Žádné**: Indexování je v kontejneru zakázáno. To se běžně používá, když kontejner se používá jako úložiště čisté hodnoty klíče bez nutnosti sekundární indexy. Lze jej také použít ke zlepšení výkonu hromadných operací. Po dokončení hromadných operací lze režim indexu nastavit na konzistentní a poté sledovat pomocí [IndexTransformationProgress](how-to-manage-indexing-policy.md#use-the-net-sdk-v2) až do dokončení.

> [!NOTE]
> Azure Cosmos DB také podporuje režim opožděného indexování. Opožděné indexování provádí aktualizace indexu na mnohem nižší úrovni priority, když modul neprovádí žádnou jinou práci. Výsledkem mohou být **nekonzistentní nebo neúplné** výsledky dotazu. Pokud máte v plánu dotaz na kontejner Cosmos, neměli byste vybrat opožděné indexování.

Ve výchozím nastavení je zásada indexování nastavena na hodnotu `automatic`. Je dosaženo nastavením `automatic` vlastnostv zásadách indexování na `true`. Nastavení této `true` vlastnosti umožňuje Azure CosmosDB automaticky indexovat dokumenty, jak jsou zapsány.

## <a name="including-and-excluding-property-paths"></a><a id="include-exclude-paths"></a>Včetně a vyloučení cest vlastností

Vlastní zásady indexování můžete určit cesty vlastností, které jsou explicitně zahrnuty nebo vyloučeny z indexování. Optimalizací počtu cest, které jsou indexovány, můžete snížit velikost úložiště používaného kontejnerem a zlepšit latenci operací zápisu. Tyto cesty jsou definovány podle [metody popsané v části přehled indexování](index-overview.md#from-trees-to-property-paths) s následujícími dodatky:

- cesta vedoucí k skalární hodnotě (řetězec nebo číslo) končí`/?`
- prvky z pole jsou řešeny společně prostřednictvím zápisu `/[]` (namísto `/0`, `/1` atd.)
- zástupný `/*` znak lze použít k porovnění všech prvků pod uzlemi

Vezmeme-li stejný příklad znovu:

```
    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 }
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }
```

- `headquarters` `employees``/headquarters/employees/?`

- ' `locations` `country` cesta je`/locations/[]/country/?`

- cesta k čemukoli pod `headquarters` je`/headquarters/*`

Například bychom mohli `/headquarters/employees/?` zahrnout cestu. Tato cesta by zajistila indexování vlastnosti zaměstnanců, ale nebude indexovat další vnořené JSON v rámci této vlastnosti.

## <a name="includeexclude-strategy"></a>Zahrnout/vyloučit strategii

Všechny zásady indexování musí `/*` obsahovat kořenovou cestu jako zahrnutou nebo vyloučenou cestu.

- Zahrňte kořenovou cestu k selektivnímu vyloučení cest, které není nutné indexovat. Toto je doporučený přístup, protože umožňuje Azure Cosmos DB proaktivně indexovat všechny nové vlastnosti, které mohou být přidány do vašeho modelu.
- Vylučte kořenovou cestu a selektivně zahrňte cesty, které je třeba indexovat.

- U cest s pravidelnými znaky, které obsahují: alfanumerické znaky a _ (podtržítko), není třeba uniknout řetězci cesty kolem dvojitých uvozovek (například "/path/?"). Pro cesty s jinými speciálními znaky je třeba uniknout řetězec cesty\"kolem dvojité\"uvozovky (například "/ path-abc /?"). Pokud očekáváte speciální znaky v cestě, můžete uniknout každou cestu pro bezpečnost. Funkčně to neznamená žádný rozdíl, pokud uniknete každé cestě Vs jen ty, které mají speciální znaky.

- Vlastnost systému `_etag` je ve výchozím nastavení vyloučena z indexování, pokud není etag přidán do zahrnuté cesty pro indexování.

- Pokud je režim indexování **consistent**nastaven na `id` konzistentní `_ts` , vlastnosti systému a jsou automaticky indexovány.

Při zahrnutí a vyloučení cest se můžete setkat s následujícími atributy:

- `kind`může být `range` `hash`buď nebo . Funkce indexu rozsahu poskytuje všechny funkce indexu hash, proto doporučujeme použít index rozsahu.

- `precision`je číslo definované na úrovni indexu pro zahrnuté cesty. Hodnota `-1` označuje maximální přesnost. Doporučujeme vždy nastavit `-1`tuto hodnotu na .

- `dataType`může být `String` `Number`buď nebo . Označuje typy vlastností JSON, které budou indexovány.

Pokud není zadán, budou mít tyto vlastnosti následující výchozí hodnoty:

| **Název vlastnosti**     | **Výchozí hodnota** |
| ----------------------- | -------------------------------- |
| `kind`   | `range` |
| `precision`   | `-1`  |
| `dataType`    | `String` a `Number` |

V [této části](how-to-manage-indexing-policy.md#indexing-policy-examples) naleznete příklady zásad indexování pro zahrnutí a vyloučení cest.

## <a name="spatial-indexes"></a>Prostorové indexy

Když definujete prostorovou cestu v zásadách indexování, měli byste definovat, který index ```type``` by měl být použit pro tuto cestu. Možné typy prostorových indexů zahrnují:

* Bod

* Mnohoúhelník

* Multipolygon

* Linestring

Azure Cosmos DB ve výchozím nastavení nevytvoří žádné prostorové indexy. Pokud chcete použít prostorové sql vestavěné funkce, měli byste vytvořit prostorový index na požadované vlastnosti. V [této části](geospatial.md) naleznete příklady zásad indexování pro přidávání prostorových indexů.

## <a name="composite-indexes"></a>Složené indexy

Dotazy, které `ORDER BY` mají klauzuli se dvěma nebo více vlastnostmi, vyžadují složený index. Můžete také definovat složený index pro zlepšení výkonu mnoha dotazů rovnosti a rozsahu. Ve výchozím nastavení nejsou definovány žádné složené indexy, takže byste měli podle potřeby [přidat složené indexy.](how-to-manage-indexing-policy.md#composite-indexing-policy-examples)

Při definování složeného indexu určíte:

- Dvě nebo více cest vlastností. Pořadí, ve kterém jsou definovány cesty vlastností záležitosti.

- Pořadí (vzestupně nebo sestupně).

> [!NOTE]
> Přidáte-li složený index, dotaz bude využívat existující indexy rozsahu, dokud nebude dokončeno přidání nového složeného indexu. Proto při přidání složený index, nemusí okamžitě sledovat zlepšení výkonu. Je možné sledovat průběh transformace indexu [pomocí jednoho z sad SDK](how-to-manage-indexing-policy.md).

### <a name="order-by-queries-on-multiple-properties"></a>ORDER BY dotazy na více vlastností:

Následující aspekty se používají při použití složených `ORDER BY` indexů pro dotazy s klauzulí se dvěma nebo více vlastnostmi:

- Pokud cesty složenéindex neodpovídají pořadí vlastností v `ORDER BY` klauzuli, pak složený index nemůže podporovat dotaz.

- Pořadí složených cest indexu (vzestupně nebo sestupně) `order` by `ORDER BY` měl také odpovídat v klauzuli.

- Složený index také `ORDER BY` podporuje klauzuli s opačným pořadím na všech cestách.

Zvažte následující příklad, kde je složený index definován na vlastnosti název, stáří a _ts:

| **Složený index**     | **Ukázkový `ORDER BY` dotaz**      | **Podporováno složeným indexem?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.name ASC, c.age asc``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.age ASC, c.name asc```   | ```No```             |
| ```(name ASC, age ASC)```    | ```SELECT * FROM c ORDER BY c.name DESC, c.age DESC``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c ORDER BY c.name ASC, c.age DESC``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC, timestamp ASC``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC``` | ```No```            |

Zásady indexování byste měli přizpůsobit tak, abyste mohli obsluhovat všechny potřebné `ORDER BY` dotazy.

### <a name="queries-with-filters-on-multiple-properties"></a>Dotazy s filtry více vlastností

Pokud dotaz obsahuje filtry na dvě nebo více vlastností, může být užitečné vytvořit složený index pro tyto vlastnosti.

Zvažte například následující dotaz, který má filtr rovnosti na dvě vlastnosti:

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age = 18
```

Tento dotaz bude efektivnější, přičemž méně času a spotřebovává méně RU, pokud je schopen využít složený index na (název ASC, věk ASC).

Dotazy s filtry rozsahu lze také optimalizovat pomocí složeného indexu. Dotaz však může mít pouze jeden filtr rozsahu. Mezi filtry `<` `<=`rozsahu `>=`patří `!=` `>`, , , a . Filtr rozsahu by měl být definován jako poslední ve složeném indexu.

Zvažte následující dotaz s filtry rovnosti i rozsahu:

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age > 18
```

Tento dotaz bude efektivnější s složený index na (název ASC, věk ASC). Dotaz by však nevyužil složený index na (věk ASC, název ASC), protože filtry rovnosti musí být definovány nejprve ve složeném indexu.

Následující aspekty se používají při vytváření složených indexů pro dotazy s filtry na více vlastností

- Vlastnosti ve filtru dotazu by měly odpovídat vlastnostem ve složeném indexu. Pokud je vlastnost ve složeném indexu, ale není zahrnuta v dotazu jako filtr, dotaz nebude složený index využívat.
- Pokud dotaz obsahuje další vlastnosti ve filtru, které nebyly definovány ve složeném indexu, bude k vyhodnocení dotazu použita kombinace indexů a indexů rozsahu. To bude vyžadovat méně ŽP než výhradně pomocí indexů rozsahu.
- Pokud má vlastnost filtr`>`rozsahu `<` `<=`( `>=`, `!=`, , , nebo ), pak by tato vlastnost měla být definována jako poslední ve složeném indexu. Pokud dotaz obsahuje více než jeden filtr rozsahu, nebude využívat složený index.
- Při vytváření složeného indexu pro optimalizaci `ORDER` dotazů s více filtry nebude mít složený index žádný vliv na výsledky. Tato vlastnost je nepovinná.
- Pokud nedefinujete složený index pro dotaz s filtry na více vlastností, dotaz bude stále úspěšné. Náklady žP dotazu však mohou být sníženy pomocí složeného indexu.

Vezměte v úvahu následující příklady, kde je složený index definován na název vlastností, stáří a časové razítko:

| **Složený index**     | **Ukázkový dotaz**      | **Podporováno složeným indexem?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name DESC, age ASC)```    | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c WHERE c.name != "John" AND c.age > 18``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 123049923``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp = 123049923``` | ```No```            |

### <a name="queries-with-a-filter-as-well-as-an-order-by-clause"></a>Dotazy s filtrem i klauzulí ORDER BY

Pokud dotaz filtruje jednu nebo více vlastností a má různé vlastnosti klauzule ORDER BY, může být užitečné přidat vlastnosti ve filtru do `ORDER BY` klauzule.

Například přidáním vlastností ve filtru do klauzule ORDER BY může být následující dotaz přepsán, aby využil složený index:

Dotaz pomocí indexu rozsahu:

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp
```

Dotaz pomocí složeného indexu:

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.name, c.timestamp
```

Stejný vzor a optimalizace dotazů lze zobecnit pro dotazy s více filtry rovnosti:

Dotaz pomocí indexu rozsahu:

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.timestamp
```

Dotaz pomocí složeného indexu:

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.name, c.age, c.timestamp
```

Při vytváření složených indexů pro optimalizaci dotazu s `ORDER BY` filtrem a klauzulí se používají následující důležité informace:

* Pokud dotaz filtruje vlastnosti, tyto by `ORDER BY` měly být zahrnuty nejprve v klauzuli.
* Pokud nedefinujete složený index v dotazu s filtrem `ORDER BY` na jednu vlastnost a samostatnou klauzulí pomocí jiné vlastnosti, dotaz bude stále úspěšný. ŽP náklady na dotaz však může být snížena s složený `ORDER BY` index, zejména v případě, že vlastnost v klauzuli má vysokou mohutnost.
* Všechny důležité informace pro vytváření `ORDER BY` složených indexů pro dotazy s více vlastnostmi, stejně jako dotazy s filtry na více vlastností stále platí.


| **Složený index**                      | **Ukázkový `ORDER BY` dotaz**                                  | **Podporováno složeným indexem?** |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No`  |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC``` | ```No```   |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.age ASC, c.name ASC,c.timestamp ASC``` | `Yes` |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.timestamp ASC``` | `No` |

## <a name="modifying-the-indexing-policy"></a>Změna zásad indexování

Zásady indexování kontejneru lze kdykoli aktualizovat [pomocí portálu Azure nebo jedné z podporovaných sad SDK](how-to-manage-indexing-policy.md). Aktualizace zásad indexování spustí transformaci ze starého indexu na nový, který se provádí online a na místě (takže během operace není spotřebována žádná další paměťová mezera). Index staré zásady je efektivně transformována na nové zásady bez ovlivnění dostupnosti zápisu nebo propustnost zřízené v kontejneru. Transformace indexu je asynchronní operace a čas potřebný k dokončení závisí na zřízené propustnosti, počet položek a jejich velikost.

> [!NOTE]
> Při přidávání rozsahu nebo prostorový index, dotazy nemusí vrátit všechny odpovídající výsledky a provede tak bez vrácení chyb. To znamená, že výsledky dotazu nemusí být konzistentní, dokud není dokončena transformace indexu. Je možné sledovat průběh transformace indexu [pomocí jednoho z sad SDK](how-to-manage-indexing-policy.md).

Pokud je režim nové zásady indexování nastaven na konzistentní, nelze použít žádnou jinou změnu zásad indexování, zatímco probíhá transformace indexu. Spuštěná transformace indexu může být zrušena nastavením režimu zásad indexování na žádný (který okamžitě klesne index).

## <a name="indexing-policies-and-ttl"></a>Zásady indexování a TTL

Funkce [Time-to-Live (TTL)](time-to-live.md) vyžaduje, aby indexování bylo aktivní v kontejneru, který je zapnutý. To znamená, že:

- Není možné aktivovat TTL na kontejneru, kde je režim indexování nastaven na žádný,
- není možné nastavit režim indexování na Žádný v kontejneru, kde je aktivován TTL.

Pro scénáře, kde není třeba indexovat žádnou cestu vlastností, ale je vyžadovánt ttl, můžete použít zásadu indexování s:

- režim indexování nastavený na konzistentní a
- žádná zahrnutá cesta a
- `/*`jako jediná vyloučená cesta.

## <a name="next-steps"></a>Další kroky

Další informace o indexování naleznete v následujících článcích:

- [Přehled indexování](index-overview.md)
- [Jak spravovat zásady indexování](how-to-manage-indexing-policy.md)
