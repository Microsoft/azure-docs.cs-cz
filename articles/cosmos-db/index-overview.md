---
title: Indexování ve službě Azure Cosmos DB
description: Zjistěte, jak funguje indexování v Azure Cosmos DB, různé druhy indexů, jako je rozsah, prostorové, složené indexy podporované.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: thweiss
ms.openlocfilehash: 684799ee12715c789910accf80aa5b4afec763d4
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273235"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Indexování ve službě Azure Cosmos DB – Přehled

Azure Cosmos DB je databáze bez ohledu na schéma, která umožňuje iterate na vaší aplikaci bez nutnosti řešit správu schématu nebo indexu. Ve výchozím nastavení Azure Cosmos DB automaticky indexuje všechny vlastnosti pro všechny položky v [kontejneru](databases-containers-items.md#azure-cosmos-containers) bez nutnosti definovat jakékoli schéma nebo konfigurovat sekundární indexy.

Cílem tohoto článku je vysvětlit, jak služba Azure Cosmos DB indexuje data a jak pomocí indexů zlepšuje výkon dotazů. Před prozkoumáním přizpůsobení [zásad indexování](index-policy.md)doporučujeme projít tuto část.

## <a name="from-items-to-trees"></a>Od položek ke stromům

Pokaždé, když je položka uložena v kontejneru, její obsah je promítán jako dokument JSON a poté převeden na reprezentaci stromu. Co to znamená, že každá vlastnost této položky získá reprezentován jako uzel ve stromu. Pseudo kořenový uzel je vytvořen jako nadřazený ke všem vlastnostem první úrovně položky. Uzly listu obsahují skutečné skalární hodnoty nesené položkou.

Jako příklad zvažte tuto položku:

```json
    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 },
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }
```

To by být reprezentován následující strom:

![Předchozí položka reprezentovaná jako strom](./media/index-overview/item-as-tree.png)

Všimněte si, jak jsou pole kódována ve stromu: každá položka v poli získá mezilehlý uzel označený indexem této položky v rámci pole (0, 1 atd.).

## <a name="from-trees-to-property-paths"></a>Ze stromů na cesty vlastností

Důvod, proč Azure Cosmos DB transformuje položky do stromů je, protože umožňuje vlastnosti, které mají být odkazovány jejich cesty v rámci těchto stromů. Chcete-li získat cestu pro vlastnost, můžeme procházet strom z kořenového uzlu do této vlastnosti a zřetězit popisky každého projetého uzlu.

Zde jsou cesty pro každou vlastnost z výše popsané ukázkové položky:

    /locations/0/country: "Germany"
    /locations/0/city: "Berlin"
    /locations/1/country: "France"
    /locations/1/city: "Paris"
    /headquarters/country: "Belgium"
    /headquarters/employees: 250
    /exports/0/city: "Moscow"
    /exports/1/city: "Athens"

Při zápisu položky Azure Cosmos DB efektivně indexuje cestu každé vlastnosti a její odpovídající hodnotu.

## <a name="index-kinds"></a>Indexovat druhy

Azure Cosmos DB aktuálně podporuje tři druhy indexů.

### <a name="range-index"></a>Index rozsahu

**Index rozsahu** je založen na uspořádané stromové struktuře. Druh indexu rozsahu se používá pro:

- Dotazy týkající se rovnosti:

    ```sql
   SELECT * FROM container c WHERE c.property = 'value'
   ```

   ```sql
   SELECT * FROM c WHERE c.property IN ("value1", "value2", "value3")
   ```

   Shoda rovnosti na prvku pole
   ```sql
    SELECT * FROM c WHERE ARRAY_CONTAINS(c.tags, "tag1")
    ```

- Dotazy na rozsah:

   ```sql
   SELECT * FROM container c WHERE c.property > 'value'
   ```
  (pracuje `>`pro `<` `>=`, `<=` `!=`, , , )

- Kontrola přítomnosti vlastnosti:

   ```sql
   SELECT * FROM c WHERE IS_DEFINED(c.property)
   ```

- Shody předpony řetězce (klíčové slovo CONTAINS nevyužije index rozsahu):

   ```sql
   SELECT * FROM c WHERE STARTSWITH(c.property, "value")
   ```

- `ORDER BY`Dotazy:

   ```sql
   SELECT * FROM container c ORDER BY c.property
   ```

- `JOIN`Dotazy:

   ```sql
   SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'
   ```

Indexy rozsahu lze použít pro skalární hodnoty (řetězec nebo číslo).

### <a name="spatial-index"></a>Prostorový index

**Prostorové** indexy umožňují efektivní dotazy na geoprostorové objekty, jako jsou - body, čáry, mnoholyny a multipolygon. Tyto dotazy používají klíčová slova ST_DISTANCE, ST_WITHIN ST_INTERSECTS. Následují některé příklady, které používají druh prostorového indexu:

- Geoprostorové vzdálenosti:

   ```sql
   SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40
   ```

- Geoprostorové v rámci dotazů:

   ```sql
   SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] } })
   ```

- Geoprostorové protínající se dotazy:

   ```sql
   SELECT * FROM c WHERE ST_INTERSECTS(c.property, { 'type':'Polygon', 'coordinates': [[ [31.8, -5], [32, -5], [31.8, -5] ]]  })  
   ```

Prostorové indexy lze použít na správně formátované objekty [GeoJSON.](geospatial.md) Body, LineStrings, Mnohonoly a MultiPolygons jsou aktuálně podporovány.

### <a name="composite-indexes"></a>Složené indexy

**Složené** indexy zvyšují efektivitu při provádění operací na více polích. Druh složeného indexu se používá pro:

- `ORDER BY`dotazy na více vlastností:

```sql
 SELECT * FROM container c ORDER BY c.property1, c.property2
```

- Dotazy s filtrem `ORDER BY`a . Tyto dotazy mohou využít složený index, pokud je `ORDER BY` vlastnost filtru přidána do klauzule.

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' ORDER BY c.property1, c.property2
```

- Dotazy s filtrem na dvě nebo více vlastností, kde alespoň jedna vlastnost je filtr rovnosti

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' AND c.property2 > 'value'
```

Tak dlouho, dokud jeden predikát filtru používá jeden z druhu indexu, dotazovací stroj vyhodnotí, že první před skenování mzda zbytek. Pokud máte například dotaz SQL, například`SELECT * FROM c WHERE c.firstName = "Andrew" and CONTAINS(c.lastName, "Liu")`

* Výše uvedený dotaz nejprve filtruje položky, kde firstName = "Andrew" pomocí indexu. Potom předat všechny firstName = "Andrew" položky prostřednictvím následného kanálu k vyhodnocení obsahuje predikát filtru.

* Můžete urychlit dotazy a vyhnout se úplné skenování kontejneru při použití funkcí, které nepoužívají index (např. obsahuje) přidáním dalších predikátů filtru, které používají index. Pořadí klauzulí filtru není důležité. Dotazovací stroj je zjistí, které predikáty jsou selektivnější a spustit dotaz odpovídajícím způsobem.


## <a name="querying-with-indexes"></a>Dotazování pomocí indexů

Cesty extrahované při indexování dat usnadňují vyhledávání indexu při zpracování dotazu. Porovnáním `WHERE` klauzule dotazu se seznamem indexovaných cest je možné velmi rychle identifikovat položky, které odpovídají predikátu dotazu.

Zvažte například následující `SELECT location FROM location IN company.locations WHERE location.country = 'France'`dotaz: . Predikát dotazu (filtrování položek, kde jakékoli umístění má jako svou zemi "Francie") by odpovídalo cestě zvýrazněné červeně níže:

![Porovnání určité cesty ve stromu](./media/index-overview/matching-path.png)

> [!NOTE]
> Klauzule, `ORDER BY` která objednávky podle jedné vlastnosti *vždy* potřebuje index rozsahu a nezdaří, pokud cesta odkazuje nemá jeden. Podobně `ORDER BY` dotaz, který objednávky podle více vlastností *vždy* potřebuje složený index.

## <a name="next-steps"></a>Další kroky

Další informace o indexování naleznete v následujících článcích:

- [Zásady indexování](index-policy.md)
- [Jak spravovat zásady indexování](how-to-manage-indexing-policy.md)
