---
title: Azure Cosmos DB zásady indexování
description: Přečtěte si, jak nakonfigurovat a změnit výchozí zásady indexování pro automatické indexování a vyšší výkon v Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: tisande
ms.openlocfilehash: 9e62d6c475a4aeb366d034af1c80fc728f1a9211
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93335802"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Zásady indexování ve službě Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ve službě Azure Cosmos DB má každý kontejner zásady indexování, které určují, jakým způsobem by se měly indexovat položky kontejneru. Výchozí zásady indexování pro nově vytvořené kontejnery indexují všechny vlastnosti všech položek a u všech řetězců a čísel vynucují indexy rozsahu. To vám umožní získat dobrý výkon dotazů, aniž byste se museli zabývat předem s indexováním a správou indexu.

V některých situacích možná budete chtít toto automatické chování přepsat, aby lépe vyhovovalo vašim požadavkům. Zásady indexování kontejneru můžete přizpůsobit nastavením jeho *režimu indexování* a zahrnutí nebo vyloučení *cest k vlastnostem*.

> [!NOTE]
> Metoda aktualizace zásad indexování popsaná v tomto článku se týká jenom rozhraní API pro Azure Cosmos DB SQL (Core). Další informace o indexování v [rozhraní Azure Cosmos DB API pro MongoDB](mongodb-indexing.md)

## <a name="indexing-mode"></a>Režim indexování

Azure Cosmos DB podporuje dva režimy indexování:

- **Konzistentní** : index se aktualizuje synchronně při vytváření, aktualizaci nebo odstraňování položek. To znamená, že konzistence vašich dotazů pro čtení bude [konzistence nakonfigurovaná pro tento účet](consistency-levels.md).
- **Žádné** : indexování je v kontejneru zakázané. To se běžně používá, když se kontejner používá jako úložiště čistě klíč-hodnota bez nutnosti sekundárních indexů. Dá se použít také ke zlepšení výkonu hromadných operací. Po dokončení hromadných operací může být režim indexu nastaven na konzistentní a následně sledován pomocí [IndexTransformationProgress](how-to-manage-indexing-policy.md#dotnet-sdk) , dokud nebude dokončen.

> [!NOTE]
> Azure Cosmos DB také podporuje režim opožděného indexování. Opožděné indexování provádí aktualizace indexu na mnohem nižší úrovni priority v době, kdy modul neprovádí žádnou jinou práci. To může vést k **nekonzistentním nebo neúplným** výsledkům dotazů. Pokud plánujete dotazy na kontejner Cosmos, neměli byste vybírat opožděné indexování. Nové kontejnery nemůžou vybírat opožděné indexování. Můžete požádat o výjimku tím, že se obrátíte na [podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) (s výjimkou případů, kdy používáte účet Azure Cosmos v režimu bez [serveru](serverless.md) , který nepodporuje opožděné indexování).

Ve výchozím nastavení je zásada indexování nastavena na `automatic` . Dosáhnete tím, že nastavíte `automatic` vlastnost v zásadě indexování na `true` . Nastavením této vlastnosti `true` umožníte, aby Azure CosmosDB automaticky indexoval dokumenty při jejich zápisu.

## <a name="including-and-excluding-property-paths"></a><a id="include-exclude-paths"></a>Zahrnutí a vyloučení cest k vlastnostem

Vlastní zásada indexování může určovat cesty vlastností, které jsou explicitně zahrnuté nebo vyloučené z indexování. Optimalizací počtu indexovaných cest můžete významně snížit latenci a RU za operace zápisu. Tyto cesty jsou definovány podle [metody popsané v části Přehled indexování](index-overview.md#from-trees-to-property-paths) s následujícími přídavky:

- Cesta vedoucí k skalární hodnotě (řetězec nebo číslo) končí na `/?`
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

- Cesta k libovolnému prvku `headquarters` je `/headquarters/*`

Příkladem může být `/headquarters/employees/?` cesta. Tato cesta zajistí, že bude indexována vlastnost Employees, ale v rámci této vlastnosti se neindexuje další vnořený kód JSON.

## <a name="includeexclude-strategy"></a>Zahrnout/vyloučit strategii

Všechny zásady indexování musí zahrnovat kořenovou cestu `/*` buď jako zahrnutou, nebo jako vyloučenou cestu.

- Zahrňte kořenovou cestu pro selektivní vyloučení cest, které nemusejí být indexovány. To je doporučený postup, protože umožňuje Azure Cosmos DB proaktivní indexování všech nových vlastností, které mohou být přidány do modelu.
- Vylučte kořenovou cestu pro selektivní zahrnutí cest, které je třeba indexovat.

- Pro cesty s běžnými znaky, které zahrnují: alfanumerické znaky a _ (podtržítko), nemusíte řídicí řetězec cesty kolem dvojitých uvozovek (například "/path/?"). V případě cest s dalšími speciálními znaky je nutné před dvojitými uvozovkami vyčínat řetězec cesty (například "/ \" path-ABC \" /?"). Pokud očekáváte, že v cestě jsou speciální znaky, můžete každý z nich vymezit řídicím znakem. Funkčně nezpůsobuje žádný rozdíl, pokud řídíte každou cestu a pouze ty, které obsahují speciální znaky.

- Vlastnost System `_etag` je vyloučena z indexování ve výchozím nastavení, pokud není značka ETag přidána do zahrnuté cesty pro indexování.

- Pokud je režim indexování nastavený na **konzistentní** , vlastnosti systému `id` a `_ts` jsou automaticky indexovány.

Při zahrnutí a vyloučení cest se můžete setkat s následujícími atributy:

- `kind` může být buď `range` nebo `hash` . Podpora indexu hash je omezená na filtry rovnosti. Funkce indexu rozsahu poskytuje veškerou funkčnost indexů hash a také efektivní řazení, filtry rozsahu a systémové funkce. Vždycky doporučujeme použít index rozsahu.

- `precision` je číslo definované na úrovni indexu pro zahrnuté cesty. Hodnota `-1` označuje maximální přesnost. Doporučujeme vždycky nastavit tuto hodnotu na `-1` .

- `dataType` může být buď `String` nebo `Number` . Určuje typy vlastností JSON, které budou indexovány.

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

**Zahrnutá cesta** : `/food/ingredients/nutrition/*`

**Vyloučená cesta** : `/food/ingredients/*`

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

Azure Cosmos DB ve výchozím nastavení nebudou vytvořeny žádné prostorové indexy. Pokud chcete použít integrované funkce prostorového SQL serveru, měli byste vytvořit prostorový index podle požadovaných vlastností. V [této části](sql-query-geospatial-index.md) najdete příklady zásad indexování pro přidání prostorových indexů.

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
- Dotazy s agregačními typy (například COUNT nebo SUM) a filtry také využívají složené indexy.

Vezměte v úvahu následující příklady, kde je pro název, stáří a časové razítko definováno složený index:

| **Složený index**     | **Vzorový dotaz**      | **Podporuje složený index?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name ASC, age ASC)```   | ```SELECT COUNT(1) FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
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
* Pokud dotaz filtruje více vlastností, musí být filtry rovnosti prvními vlastnostmi v `ORDER BY` klauzuli.
* Pokud nedefinujete složený index pro dotaz s filtrem na jednu vlastnost a samostatnou `ORDER BY` klauzulí s použitím jiné vlastnosti, dotaz bude stále úspěšný. Náklady na dotaz na dotaz se ale dají snížit pomocí složeného indexu, zejména pokud vlastnost v `ORDER BY` klauzuli má vysokou mohutnost.
* Všechny požadavky na vytváření složených indexů pro `ORDER BY` dotazy s více vlastnostmi a dotazy s filtry na více vlastností jsou stále používány.


| **Složený index**                      | **Vzorový `ORDER BY` dotaz**                                  | **Podporuje složený index?** |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" AND c.timestamp > 1589840355 ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(timestamp ASC, name ASC)```          | ```SELECT * FROM c WHERE c.timestamp > 1589840355 AND c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No`  |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC``` | ```No```   |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.age ASC, c.name ASC,c.timestamp ASC``` | `Yes` |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.timestamp ASC``` | `No` |

## <a name="modifying-the-indexing-policy"></a>Změna zásad indexování

Zásadu indexování kontejneru lze kdykoli aktualizovat [pomocí Azure Portal nebo jedné z podporovaných sad SDK](how-to-manage-indexing-policy.md). Aktualizace zásad indexování spustí transformaci ze starého indexu do nového, který je proveden online a místně (takže během operace se nespotřebovává žádný další prostor úložiště). Stará zásada indexování je efektivně transformovaná na nové zásady, aniž by to ovlivnilo dostupnost zápisu, dostupnost čtení nebo propustnost zřízenou v kontejneru. Transformace indexu je asynchronní operace a čas potřebný k dokončení závisí na zřízené propustnosti, počtu položek a jejich velikosti.

> [!IMPORTANT]
> Transformace indexu je operace, která využívá [jednotky žádosti](request-units.md). Pokud používáte kontejnery bez [serveru](serverless.md) , nejsou aktuálně účtovány jednotky žádosti spotřebované transformací indexu. Tyto jednotky žádosti se budou účtovat, jakmile budou všeobecně dostupné servery bez serveru.

> [!NOTE]
> Je možné sledovat průběh transformace indexu [pomocí jedné ze sad SDK](how-to-manage-indexing-policy.md).

V žádném z transformací indexu není žádný vliv na dostupnost zápisu. Transformace indexu používá zřízené ru, ale s nižší prioritou než operace CRUD nebo dotazy.

Při přidávání nového indexu nemá žádný vliv na dostupnost čtení. Po dokončení transformace indexu budou dotazy využívat pouze nové indexy. Při transformaci indexu bude modul dotazu dál používat stávající indexy, takže budete mít podobný výkon při čtení během transformace indexování na to, co jste předtím provedli při zahájení změny indexování. Při přidávání nových indexů nehrozí riziko neúplných nebo nekonzistentních výsledků dotazu.

Při odebírání indexů a okamžitém spuštění dotazů, které filtrují na vyřazených indexech, není zaručeno konzistentní nebo úplné výsledky dotazu. Pokud odeberete více indexů a v rámci jedné změny zásad indexování zadáte jednu, nástroj pro vytváření dotazů poskytne konzistentní a kompletní výsledky v celé transformaci indexu. Pokud ale odeberete indexy pomocí několika změn zásad indexování, dotazovací modul nebude poskytovat konzistentní nebo úplné výsledky, dokud se nedokončí všechny transformace indexu. Většina vývojářů nevynechává indexy a potom se okamžitě pokusí spustit dotazy, které tyto indexy využívají, takže v praxi je tato situace nepravděpodobná.

> [!NOTE]
> Pokud je to možné, měli byste vždycky zkusit seskupit více změn indexování do jedné změny zásad indexování.

## <a name="indexing-policies-and-ttl"></a>Indexování zásad a TTL

Použití [funkce TTL (Time-to-Live)](time-to-live.md) vyžaduje indexování. To znamená, že:

- hodnotu TTL není možné aktivovat u kontejneru, ve kterém je nastaven režim indexování `none` ,
- není možné nastavit režim indexování na žádný v kontejneru, kde je aktivována hodnota TTL.

U scénářů, ve kterých není nutné indexovat žádnou cestu k vlastnostem, ale je vyžadována hodnota TTL, můžete použít zásady indexování s režimem indexování nastaveným na `consistent` , žádné zahrnuté cesty a `/*` jako jediná Vyloučená cesta.

## <a name="next-steps"></a>Další kroky

Další informace o indexování najdete v následujících článcích:

- [Přehled indexování](index-overview.md)
- [Jak spravovat zásady indexování](how-to-manage-indexing-policy.md)
