---
title: Indexování v Azure Cosmos DB
description: Pochopte, jak funguje indexování v Azure Cosmos DB.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: thweiss
ms.openlocfilehash: c8e21ea89f3e23709d636ab8af4716bff76d7217
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479292"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Indexování v Azure Cosmos DB – přehled

Azure Cosmos DB je databáze nezávislá schématu, která umožňuje iterovat na aplikaci bez nutnosti zabývat se správou schématu nebo indexu. Ve výchozím nastavení Azure Cosmos DB automaticky indexuje všechny vlastnosti pro všechny položky ve vašem [kontejneru](databases-containers-items.md#azure-cosmos-containers) bez nutnosti definovat nějaké schéma nebo nakonfigurovat sekundární indexy.

Cílem tohoto článku je vysvětlit, jak Azure Cosmos DB indexovat data a jak používá indexy ke zlepšení výkonu dotazů. Před zkoumáním způsobu přizpůsobení [zásad indexování](index-policy.md)doporučujeme projít si tuto část.

## <a name="from-items-to-trees"></a>Z položek do stromů

Pokaždé, když je položka uložená v kontejneru, její obsah se prochází jako dokument JSON a pak se převede do reprezentace stromu. To znamená, že všechny vlastnosti této položky se reprezentují jako uzel ve stromové struktuře. Pseudo kořenový uzel je vytvořen jako nadřazený pro všechny vlastnosti první úrovně položky. Uzly list obsahují skutečné skalární hodnoty přenesené položkou.

Zvažte například tuto položku:

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

Bude reprezentovaná následujícím stromem:

![Předchozí položka reprezentovaná jako strom](./media/index-overview/item-as-tree.png)

Všimněte si, jak jsou pole kódována ve stromové struktuře: Každá položka v poli získá zprostředkující uzel označený indexem této položky v poli (0, 1 atd.).

## <a name="from-trees-to-property-paths"></a>Ze stromů na cesty vlastností

Důvod, proč Azure Cosmos DB transformuje položky do stromů, je, že umožňuje, aby v rámci těchto stromů byly odkazy na vlastnosti odkazovány pomocí jejich cest. Chcete-li získat cestu k vlastnosti, můžeme procházet stromovou strukturu z kořenového uzlu do této vlastnosti a zřetězit popisky jednotlivých procházených uzlů.

Tady jsou cesty pro jednotlivé vlastnosti z ukázkové položky popsané výše:

    /locations/0/country: "Germany"
    /locations/0/city: "Berlin"
    /locations/1/country: "France"
    /locations/1/city: "Paris"
    /headquarters/country: "Belgium"
    /headquarters/employees: 250
    /exports/0/city: "Moscow"
    /exports/1/city: "Athens"

Při zápisu položky Azure Cosmos DB efektivně indexuje cestu každé vlastnosti a její odpovídající hodnotu.

## <a name="index-kinds"></a>Typy indexů

Azure Cosmos DB aktuálně podporuje tři druhy indexů:

Typ indexu **rozsahu** se používá pro:

- Dotazy na rovnost:

    ```sql
   SELECT * FROM container c WHERE c.property = 'value'
    ```

- Dotazy na rozsah:

   ```sql
   SELECT * FROM container c WHERE c.property > 'value'
   ``` 
  (funguje pro `>`, `<`, `>=` ,`<=`, )`!=`

- `ORDER BY`odešle

   ```sql 
   SELECT * FROM container c ORDER BY c.property
   ```

- `JOIN`odešle

   ```sql
   SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'
   ```

Indexy rozsahu lze použít na skalárních hodnotách (String nebo Number).

Druh **prostorového** indexu se používá pro:

- Dotazy na geoprostorové vzdálenosti: 

   ```sql
   SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40
   ```

- Geoprostorové v rámci dotazů: 

   ```sql
   SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] } })
   ```

Prostorové indexy lze použít na správně formátovaných objektech typu [injson](geospatial.md) . Body, LineStrings a mnohoúhelníky se momentálně podporují.

Typ **složeného** indexu se používá pro:

- `ORDER BY`dotazy na více vlastností: 

   ```sql
   SELECT * FROM container c ORDER BY c.firstName, c.lastName
   ```

## <a name="querying-with-indexes"></a>Dotazování s indexy

Cesty extrahované při indexování dat usnadňují vyhledání indexu při zpracování dotazu. Porovnáním `WHERE` klauzule dotazu se seznamem indexovaných cest je možné identifikovat položky, které odpovídají predikátu dotazu velmi rychle.

Zvažte například následující dotaz: `SELECT location FROM location IN company.locations WHERE location.country = 'France'`. Predikát dotazu (filtrování položek, kde jakékoli umístění má "France" jako země), by odpovídala cestě zvýrazněné červeně:

![Odpovídá konkrétní cestě v rámci stromu.](./media/index-overview/matching-path.png)

> [!NOTE]
> Klauzule, která má ORDER by jedna vlastnost, vždy potřebuje index rozsahu a nezdaří se, pokud cesta, na kterou odkazuje, nemá jednu.  `ORDER BY` Podobně dotaz s více `ORDER BY` dotazy *vždy* potřebuje složený index.

## <a name="next-steps"></a>Další postup

Další informace o indexování najdete v následujících článcích:

- [Zásady indexování](index-policy.md)
- [Jak spravovat zásady indexování](how-to-manage-indexing-policy.md)
