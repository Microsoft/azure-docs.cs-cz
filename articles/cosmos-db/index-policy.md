---
title: Azure Cosmos DB zásady indexování
description: Přečtěte si, jak nakonfigurovat a změnit výchozí zásady indexování pro automatické indexování a vyšší výkon v Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/09/2020
ms.author: tisande
ms.openlocfilehash: a335da61fac914368b4044a97582ef0060f5de4a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84636321"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Zásady indexování ve službě Azure Cosmos DB

V Azure Cosmos DB má každý kontejner zásady indexování, které určují, jak by měly být indexovány položky kontejneru. Výchozí zásada indexování pro nově vytvořené kontejnery indexuje každou vlastnost každé položky, vynucuje indexy rozsahu pro libovolný řetězec nebo číslo a prostorové indexy pro libovolný objekt typu Point. JSON. To vám umožní získat vysoký výkon dotazů, aniž byste se museli zabývat o indexování a správě indexu předem.

V některých situacích možná budete chtít toto automatické chování přepsat, aby lépe vyhovovalo vašim požadavkům. Zásady indexování kontejneru můžete přizpůsobit nastavením jeho *režimu indexování*a zahrnutí nebo vyloučení *cest k vlastnostem*.

> [!NOTE]
> Metoda aktualizace zásad indexování popsaná v tomto článku se týká jenom rozhraní API pro Azure Cosmos DB SQL (Core).

## <a name="indexing-mode"></a>Režim indexování

Azure Cosmos DB podporuje dva režimy indexování:

- **Konzistentní**: index se aktualizuje synchronně při vytváření, aktualizaci nebo odstraňování položek. To znamená, že konzistence vašich dotazů pro čtení bude [konzistence nakonfigurovaná pro tento účet](consistency-levels.md).
- **Žádné**: indexování je v kontejneru zakázané. To se běžně používá, když se kontejner používá jako úložiště čistě klíč-hodnota bez nutnosti sekundárních indexů. Dá se použít také ke zlepšení výkonu hromadných operací. Po dokončení hromadných operací může být režim indexu nastaven na konzistentní a následně sledován pomocí [IndexTransformationProgress](how-to-manage-indexing-policy.md#dotnet-sdk) , dokud nebude dokončen.

> [!NOTE]
> Azure Cosmos DB také podporuje režim opožděného indexování. Opožděné indexování provádí aktualizace indexu na mnohem nižší úrovni priority, pokud modul neprovede žádnou jinou práci. Výsledkem může být **nekonzistentní nebo neúplné** výsledky dotazu. Pokud plánujete dotaz na kontejner Cosmos, neměli byste vybírat opožděné indexování. V červnu 2020 jsme zavedli změnu, která již neumožňuje nastavit nové kontejnery na režim opožděného indexování. Pokud váš Azure Cosmos DB účet už obsahuje aspoň jeden kontejner s opožděným indexováním, tento účet se od změny automaticky nezbavuje. Můžete také požádat o výjimku tím, že se obrátíte na [podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

Ve výchozím nastavení je zásada indexování nastavena na `automatic` . Dosáhnete tím, že nastavíte `automatic` vlastnost v zásadě indexování na `true` . Nastavením této vlastnosti `true` umožníte, aby Azure CosmosDB automaticky indexoval dokumenty při jejich zápisu.

## <a name="including-and-excluding-property-paths"></a><a id="include-exclude-paths"></a>Zahrnutí a vyloučení cest k vlastnostem

Vlastní zásada indexování může určovat cesty vlastností, které jsou explicitně zahrnuté nebo vyloučené z indexování. Optimalizací počtu indexovaných cest můžete snížit velikost úložiště využitého vaším kontejnerem a zlepšit latenci operací zápisu. Tyto cesty jsou definovány podle [metody popsané v části Přehled indexování](index-overview.md#from-trees-to-property-paths) s následujícími přídavky:

- Cesta vedoucí k skalární hodnotě (řetězec nebo číslo) končí na`/?`
- prvky z pole jsou řešeny společně pomocí `/[]` zápisu (místo `/0` `/1` atd.)
- `/*`zástupný znak se dá použít k vyhledání všech prvků pod uzlem.

Opětovné provedení stejného příkladu:

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

- `headquarters` `employees` cesta je`/headquarters/employees/?`

- `locations` `country` cesta je`/locations/[]/country/?`

- Cesta k libovolnému prvku `headquarters` je`/headquarters/*`

Příkladem může být `/headquarters/employees/?` cesta. Tato cesta zajistí, že bude indexována vlastnost Employees, ale v rámci této vlastnosti se neindexuje další vnořený kód JSON.

## <a name="includeexclude-strategy"></a>Zahrnout/vyloučit strategii

Všechny zásady indexování musí zahrnovat kořenovou cestu `/*` buď jako zahrnutou, nebo jako vyloučenou cestu.

- Zahrňte kořenovou cestu pro selektivní vyloučení cest, které nemusejí být indexovány. To je doporučený postup, protože umožňuje Azure Cosmos DB proaktivní indexování všech nových vlastností, které mohou být přidány do modelu.
- Vylučte kořenovou cestu pro selektivní zahrnutí cest, které je třeba indexovat.

- Pro cesty s běžnými znaky, které zahrnují: alfanumerické znaky a _ (podtržítko), nemusíte řídicí řetězec cesty kolem dvojitých uvozovek (například "/path/?"). V případě cest s dalšími speciálními znaky je nutné před dvojitými uvozovkami vyčínat řetězec cesty (například "/ \" path-ABC \" /?"). Pokud očekáváte, že v cestě jsou speciální znaky, můžete každý z nich vymezit řídicím znakem. Funkce bez jakýchkoli rozdílů neprovádí žádnou odchylku, pokud zadáte všechny cesty a pouze ty, které mají speciální znaky.

- Vlastnost System `_etag` je vyloučena z indexování ve výchozím nastavení, pokud není značka ETag přidána do zahrnuté cesty pro indexování.

- Pokud je režim indexování nastavený na **konzistentní**, vlastnosti systému `id` a `_ts` jsou automaticky indexovány.

Při zahrnutí a vyloučení cest se můžete setkat s následujícími atributy:

- `kind`může být buď `range` nebo `hash` . Funkce indexu rozsahu poskytuje všechny funkce indexu hash, proto doporučujeme použít index rozsahu.

- `precision`je číslo definované na úrovni indexu pro zahrnuté cesty. Hodnota `-1` označuje maximální přesnost. Doporučujeme vždycky nastavit tuto hodnotu na `-1` .

- `dataType`může být buď `String` nebo `Number` . Určuje typy vlastností JSON, které budou indexovány.

Pokud tento parametr nezadáte, budou mít tyto vlastnosti následující výchozí hodnoty:

| **Název vlastnosti**     | **Výchozí hodnota** |
| ----------------------- | -------------------------------- |
| `kind`   | `range` |
| `precision`   | `-1`  |
| `dataType`    | `String` a `Number` |

V [této části](how-to-manage-indexing-policy.md#indexing-policy-examples) najdete příklady zásad indexování pro zahrnutí a vyloučení cest.

## <a name="includeexclude-precedence"></a>Zahrnout/vyloučit prioritu

Pokud jsou zahrnuté cesty a vyloučené cesty v konfliktu, má přednost přesnější cesta.

Tady je příklad:

**Zahrnutá cesta**:`/food/ingredients/nutrition/*`

**Vyloučená cesta**:`/food/ingredients/*`

V takovém případě má zahrnutá cesta přednost před vyloučenou cestou, protože je přesnější. Na základě těchto cest `food/ingredients` by se z indexu vyloučila jakákoli data v cestě nebo vnořená v rámci. Výjimkou jsou data v rámci zahrnuté cesty: `/food/ingredients/nutrition/*` , která by byla indexována.

Tady jsou některá pravidla pro zahrnuté a vyloučené priority cest v Azure Cosmos DB:

- Hlubší cesty jsou přesnější než zúžení cest. Příklad: je přesnější `/a/b/?` než `/a/?` .

- Je přesnější `/?` než `/*` . Například `/a/?` je přesnější, než `/a/*` `/a/?` má přednost.

- Cesta `/*` musí být buď zahrnutá cesta, nebo Vyloučená cesta.

## <a name="spatial-indexes"></a>Prostorové indexy

Když v zásadách indexování definujete prostorovou cestu, měli byste definovat, který index se má ```type``` pro tuto cestu použít. Mezi možné typy prostorových indexů patří:

* Vyberte

* Mnohoúhelník

* MultiPolygon

* LineString

Azure Cosmos DB ve výchozím nastavení nebudou vytvořeny žádné prostorové indexy. Pokud chcete použít integrované funkce prostorového SQL serveru, měli byste vytvořit prostorový index podle požadovaných vlastností. V [této části](geospatial.md) najdete příklady zásad indexování pro přidání prostorových indexů.

## <a name="composite-indexes"></a>Složené indexy

Dotazy, které mají `ORDER BY` klauzuli se dvěma nebo více vlastnostmi, vyžadují složený index. Můžete také definovat složený index pro zlepšení výkonu mnoha dotazů na rovnost a rozsah. Ve výchozím nastavení nejsou definovány žádné složené indexy, takže v případě potřeby byste měli [Přidat složené indexy](how-to-manage-indexing-policy.md#composite-indexing-policy-examples) .

Na rozdíl od zahrnutých nebo vyloučených cest nemůžete vytvořit cestu se `/*` zástupným znakem. Každá složená cesta má implicitní `/?` na konci cesty, kterou nemusíte zadávat. Složené cesty vedou k skalární hodnotě a toto je jediná hodnota, která je obsažena ve složeném indexu.

Při definování složeného indexu zadáte:

- Dvě nebo více cest k vlastnostem. Pořadí, ve kterém jsou definovány cesty k vlastnostem.

- Pořadí (vzestupně nebo sestupně).

> [!NOTE]
> Když přidáte složený index, dotaz bude využívat existující indexy rozsahu, dokud nebude dokončeno nové složené rejstříky. Proto když přidáte složený index, nesmíte hned sledovat zvýšení výkonu. Je možné sledovat průběh transformace indexu [pomocí jedné ze sad SDK](how-to-manage-indexing-policy.md).

### <a name="order-by-queries-on-multiple-properties"></a>Seřadit podle dotazů na více vlastností:

Při použití složených indexů pro dotazy s `ORDER BY` klauzulí se dvěma nebo více vlastnostmi se používají následující požadavky:

- Pokud se složené cesty indexu neshodují s pořadím vlastností v `ORDER BY` klauzuli, nemůže složený index podporovat dotaz.

- Pořadí složených indexových cest (vzestupně nebo sestupně) by mělo být také shodné s `order` `ORDER BY` klauzulí v klauzuli.

- Složený index také podporuje `ORDER BY` klauzuli s opačným pořadím na všech cestách.

Vezměte v úvahu následující příklad, ve kterém je definován složený index ve vlastnostech název, věk a _ts:

| **Složený index**     | **Vzorový `ORDER BY` dotaz**      | **Podporuje složený index?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.name ASC, c.age asc``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.age ASC, c.name asc```   | ```No```             |
| ```(name ASC, age ASC)```    | ```SELECT * FROM c ORDER BY c.name DESC, c.age DESC``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c ORDER BY c.name ASC, c.age DESC``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC, timestamp ASC``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC``` | ```No```            |

Zásady indexování byste měli přizpůsobit, abyste mohli obsluhovat všechny potřebné `ORDER BY` dotazy.

### <a name="queries-with-filters-on-multiple-properties"></a>Dotazy s filtry více vlastností

Pokud dotaz obsahuje filtry na dvě nebo více vlastností, může být užitečné vytvořit složený index pro tyto vlastnosti.

Zvažte například následující dotaz, který má filtr rovnosti na dvou vlastnostech:

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age = 18
```

Tento dotaz bude efektivnější a bude trvat méně času a bude využívat méně RU, pokud dokáže využít složený index (název ASC, věk ASC).

Dotazy s filtry rozsahu lze také optimalizovat pomocí složeného indexu. Dotaz však může mít pouze jeden filtr rozsahu. Mezi filtry rozsahu patří `>` ,,, a `<` `<=` `>=` `!=` . Filtr rozsahu by měl být definován jako poslední ve složeném indexu.

Vezměte v úvahu následující dotaz s filtry rovnosti a rozsahu:

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age > 18
```

Tento dotaz bude efektivnější u složeného indexu (název ASC, věk ASC). Dotaz však nevyužívá složený index na (věk ASC, název ASC), protože filtry rovnosti musí být definovány jako první ve složeném indexu.

Při vytváření složených indexů pro dotazy s filtry na více vlastností se používají následující požadavky.

- Vlastnosti ve filtru dotazu by měly odpovídat hodnotám ve složeném indexu. Pokud je vlastnost ve složeném indexu, ale není obsažena v dotazu jako filtr, dotaz nebude používat složený index.
- Pokud má dotaz další vlastnosti, které nebyly definovány ve složeném indexu, pak se pro vyhodnocení dotazu použije kombinace složených a rozsahových indexů. To bude vyžadovat méně RU než výhradně pomocí indexů rozsahu.
- Pokud má vlastnost filtr rozsahu ( `>` ,,, `<` `<=` `>=` nebo `!=` ), měla by být tato vlastnost definována jako poslední ve složeném indexu. Pokud má dotaz více než jeden filtr rozsahu, nebude složený index využívat.
- Při vytváření složeného indexu pro optimalizaci dotazů s více filtry `ORDER` nebude mít složený index žádný vliv na výsledky. Tato vlastnost je nepovinná.
- Pokud nedefinujete složený index pro dotaz s filtry na více vlastností, dotaz bude stále úspěšný. Náklady na dotaz na RU se ale můžou snížit pomocí složeného indexu.

Vezměte v úvahu následující příklady, kde je pro název, stáří a časové razítko definováno složený index:

| **Složený index**     | **Vzorový dotaz**      | **Podporuje složený index?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name DESC, age ASC)```    | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c WHERE c.name != "John" AND c.age > 18``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 123049923``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp = 123049923``` | ```No```            |

### <a name="queries-with-a-filter-as-well-as-an-order-by-clause"></a>Dotazy s filtrem i klauzulí ORDER BY

Pokud dotaz filtruje jednu nebo více vlastností a má jiné vlastnosti v klauzuli ORDER BY, může být užitečné přidat vlastnosti ve filtru k `ORDER BY` klauzuli.

Například přidáním vlastností ve filtru do klauzule ORDER by může být přepsán následující dotaz pro využití složeného indexu:

Dotaz pomocí indexu rozsahu:

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp
```

Dotaz používající složený index:

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.name, c.timestamp
```

Stejné optimalizace vzorů a dotazů je možné zobecnit pro dotazy s více filtry rovnosti:

Dotaz pomocí indexu rozsahu:

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.timestamp
```

Dotaz používající složený index:

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.name, c.age, c.timestamp
```

Při vytváření složených indexů k optimalizaci dotazu pomocí filtru a klauzule se používají následující požadavky `ORDER BY` :

* Pokud dotaz filtruje vlastnosti, měly by být zahrnuty do `ORDER BY` klauzule First.
* Pokud nedefinujete složený index pro dotaz s filtrem na jednu vlastnost a samostatnou `ORDER BY` klauzulí s použitím jiné vlastnosti, dotaz bude stále úspěšný. Náklady na dotaz na dotaz se ale dají snížit pomocí složeného indexu, zejména pokud vlastnost v `ORDER BY` klauzuli má vysokou mohutnost.
* Všechny požadavky na vytváření složených indexů pro `ORDER BY` dotazy s více vlastnostmi a dotazy s filtry na více vlastností jsou stále používány.


| **Složený index**                      | **Vzorový `ORDER BY` dotaz**                                  | **Podporuje složený index?** |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No`  |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC``` | ```No```   |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.age ASC, c.name ASC,c.timestamp ASC``` | `Yes` |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.timestamp ASC``` | `No` |

## <a name="modifying-the-indexing-policy"></a>Změna zásad indexování

Zásadu indexování kontejneru lze kdykoli aktualizovat [pomocí Azure Portal nebo jedné z podporovaných sad SDK](how-to-manage-indexing-policy.md). Aktualizace zásad indexování spustí transformaci z původního indexu na novou, která je provedena online a na místě (takže se během operace nespotřebovává žádný další prostor úložiště). Index staré zásady se efektivně transformuje na nové zásady, aniž by to ovlivnilo dostupnost zápisu nebo propustnost zřízenou v kontejneru. Transformace indexu je asynchronní operace a čas potřebný k dokončení závisí na zřízené propustnosti, počtu položek a jejich velikosti.

> [!NOTE]
> Při přidávání rozsahu nebo prostorového indexu nemusí dotazy vracet všechny výsledky, které odpovídají, a to tak, aby se nevrátily žádné chyby. To znamená, že výsledky dotazu nemusí být konzistentní, dokud se nedokončí transformace indexu. Je možné sledovat průběh transformace indexu [pomocí jedné ze sad SDK](how-to-manage-indexing-policy.md).

Pokud je nový režim zásad indexování nastavený na konzistentní, při transformaci indexu se nedají použít žádné jiné změny zásad indexování. Spuštěnou transformaci indexu můžete zrušit tak, že nastavíte režim zásad indexování na žádný (který bude ihned index vyřadit).

## <a name="indexing-policies-and-ttl"></a>Indexování zásad a TTL

[Funkce TTL (Time-to-Live)](time-to-live.md) vyžaduje, aby bylo indexování aktivní na kontejneru, ve kterém je zapnuté. To znamená, že:

- hodnotu TTL není možné aktivovat u kontejneru, ve kterém je režim indexování nastavený na None.
- není možné nastavit režim indexování na žádný v kontejneru, kde je aktivována hodnota TTL.

U scénářů, ve kterých není nutné indexovat žádnou cestu k vlastnostem, ale je vyžadována hodnota TTL, můžete použít zásadu indexování pomocí:

- režim indexování nastavený na konzistentní a
- žádná zahrnutá cesta a
- `/*`jako jediná Vyloučená cesta.

## <a name="next-steps"></a>Další kroky

Další informace o indexování najdete v následujících článcích:

- [Přehled indexování](index-overview.md)
- [Jak spravovat zásady indexování](how-to-manage-indexing-policy.md)
