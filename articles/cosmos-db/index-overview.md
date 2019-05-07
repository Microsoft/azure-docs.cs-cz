---
title: Indexování ve službě Azure Cosmos DB
description: Zjistěte, jak funguje indexování ve službě Azure Cosmos DB.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: thweiss
ms.openlocfilehash: 44706e5ebe2442dcb45dfc45e2c322938cf7dca9
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65068661"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Indexování ve službě Azure Cosmos DB – přehled

Azure Cosmos DB je nezávislý na schématu databáze, která umožňuje iterovat aplikace bez nutnosti schéma nebo správu indexů. Ve výchozím nastavení, služby Azure Cosmos DB automaticky indexuje každou vlastnost pro všechny položky ve vaší [kontejneru](databases-containers-items.md#azure-cosmos-containers) aniž byste museli definovat žádné schéma nebo nakonfigurovat sekundární indexy.

Cílem tohoto článku je vysvětlují, jak službu Azure Cosmos DB indexuje data a způsob používání mechanismu indexy pro zlepšení výkonu dotazů. Doporučujeme projít tento oddíl prozkoumáte přizpůsobení [zásadám indexování](index-policy.md).

## <a name="from-items-to-trees"></a>Z položky na stromy

Pokaždé, když je položka uložená v kontejneru, jeho obsah je promítat jako dokument JSON a potom převést na strom reprezentace. To znamená, že každé vlastnosti této položky získá reprezentována jako uzel ve stromu. Kořenový uzel pseudo se vytvoří jako nadřazená k vlastnostem na první úrovni položky. Uzly typu list obsahovat skutečná skalárních hodnot provést u položky.

Jako příklad vezměte v úvahu tuto položku:

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

Bude reprezentovat stromu následující:

![Předchozí položka reprezentována jako strom](./media/index-overview/item-as-tree.png)

Všimněte si, jak jsou pole kódovány ve stromové struktuře: Každá položka v poli získá zprostředkující uzel s názvem s indexem tuto položku v poli (0, 1 atd.).

## <a name="from-trees-to-property-paths"></a>Ze stromů cesty vlastností

Důvod, proč Azure Cosmos DB transformuje položky do stromové struktury totiž umožňuje vlastnosti se nesmí odkazovat pomocí jeho cest v rámci stromů. K získání cesty pro vlastnost, můžeme procházení stromu od kořenového uzlu s danou vlastností a zřetězit popisky každého procházený uzlu.

Tady jsou cesty pro každou vlastnost z příklad položky popsané výše:

    /locations/0/country: "Germany"
    /locations/0/city: "Berlin"
    /locations/1/country: "France"
    /locations/1/city: "Paris"
    /headquarters/country: "Belgium"
    /headquarters/employees: 250
    /exports/0/city: "Moscow"
    /exports/1/city: "Athens"

Při zápisu položky služby Azure Cosmos DB efektivně indexuje každou vlastnost cesty a jeho odpovídající hodnota.

## <a name="index-kinds"></a>Index typy

Azure Cosmos DB v současné době podporuje dva druhy indexů:

**Rozsah** typ indexu se používá pro:

- dotazy na rovnost: 

   ```sql SELECT * FROM container c WHERE c.property = 'value'```

- Dotazy na rozsah: 

   ```sql SELECT * FROM container c WHERE c.property > 'value'``` (funguje pro `>`, `<`, `>=`, `<=`, `!=`)

- `ORDER BY` dotazy:

   ```sql SELECT * FROM container c ORDER BY c.property```

- `JOIN` dotazy: 

   ```sql SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'```

Rozsah indexů lze na skalární hodnoty (řetězec nebo číslo).

**Prostorových** typ indexu se používá pro:

- geoprostorové dotazy vzdálenost: 

   ```sql SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40```

- geoprostorové v rámci dotazů: 

   ```sql SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] } })```

Prostorové indexy jde použít na správný formát [GeoJSON](geospatial.md) objekty. LineStrings bodů a mnohoúhelníků jsou aktuálně podporovány.

**Složené** typ indexu se používá pro:

- `ORDER BY` dotazy na více vlastností: 

   ```sql SELECT * FROM container c ORDER BY c.firstName, c.lastName```

## <a name="querying-with-indexes"></a>Dotazování s indexy

Cesty extrahovat názvy při indexování dat usnadňují vyhledání index při zpracování dotazu. To provede spárováním odpovídajících `WHERE` klauzule dotazu se seznamem indexované cesty, je možné k identifikaci položky, které odpovídají predikátu dotazu velmi rychle.

Zvažte například následující dotaz: `SELECT location FROM location IN company.locations WHERE location.country = 'France'`. Predikátu dotazu (filtrování položek, kde libovolného umístění má "France" jako jeho země) by odpovídala cestě zvýrazněný červenou barvou níže:

![Odpovídající konkrétní cestě v rámci stromu](./media/index-overview/matching-path.png)

> [!NOTE]
> `ORDER BY` Klauzuli, která řadí podle jedné vlastnosti *vždy* potřebuje rozsah indexu a selže, pokud cesta odkazuje na nemá. Podobně s více `ORDER BY` dotazu *vždy* potřebuje složeném indexu.

## <a name="next-steps"></a>Další postup

Další informace o indexování v následujících článcích:

- [Zásady indexování](index-policy.md)
- [Jak spravovat zásady indexování](how-to-manage-indexing-policy.md)
