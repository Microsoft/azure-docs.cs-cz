---
title: Azure Cosmos DB zásadám indexování
description: Zjistěte, jak konfigurovat a měnit výchozí zásady pro automatické indexování a vyšší výkon ve službě Azure Cosmos DB indexování.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: thweiss
ms.openlocfilehash: 67bc3076be91ade140b39b7dd8037299902546a9
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "60005090"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Zásady indexování ve službě Azure Cosmos DB

Každý kontejner ve službě Azure Cosmos DB, má zásady indexování, který určuje, jak položky kontejneru se má indexovat. Výchozí zásady pro indexování nově vytvořeny kontejnery indexy každou vlastnost každé položce, vynucování rozsah indexů pro libovolný řetězec nebo číslo, a zadejte prostorové indexy pro libovolný objekt GeoJSON bodu. To umožňuje získat vysokého výkonu dotazu bez nutnosti uvažovat o indexování a správou indexů předem.

V některých případech můžete chtít přepsat toto automatické chování, aby lépe vyhovovala vašim požadavkům. Zásady indexování kontejneru můžete přizpůsobit tak, že nastavíte její *indexování režimu*a zahrnout nebo vyloučit *cesty vlastností*.

## <a name="indexing-mode"></a>Indexování režimu

Azure Cosmos DB podporuje dva režimy indexování:

- **Konzistentní**: Pokud zásady indexování kontejneru je nastavena na konzistentní, index se aktualizuje synchronně, jak vytvářet, aktualizovat nebo odstranit položky. To znamená, že by se konzistence čtení dotazy [konzistence pro účet nakonfigurovaný](consistency-levels.md).

- **Žádný**: Pokud zásady indexování kontejneru je nastavena na hodnotu None, indexování efektivně zakázaná v tomto kontejneru. To se běžně používá, když kontejner se používá jako čistě úložiště dvojic klíč hodnota bez nutnosti sekundární indexy. Může také pomoct urychlení hromadnou operací vložení.

## <a name="including-and-excluding-property-paths"></a>Zahrnutí a vyloučení cesty vlastností

Vlastní zásady indexování můžete zadat vlastnost cesty, které jsou výslovně zahrnuty nebo vyloučeny ze indexování. Díky optimalizaci počet cest, která jsou indexována, můžete snížit velikost úložiště využitá službou kontejner a zlepšení latence operace zápisu. Tyto cesty jsou definovány následující [metody popsané v části Přehled indexování](index-overview.md#from-trees-to-property-paths) s těmito přídavky:

- cesta, která vede na skalární hodnota (řetězec nebo číslo) končí `/?`
- elementy v matici, jsou vyřešeny společně `/[]` zápis (místo `/0`, `/1` atd.)
- `/*` zástupný znak můžete použít tak, aby odpovídaly elementy pod uzlem

Znovu trvá stejný příklad:

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

- `headquarters`společnosti `employees` cesta `/headquarters/employees/?`
- `locations`" `country` cesta `/locations/[]/country/?`
- Cesta k ničemu v rámci `headquarters` je `/headquarters/*`

Cesta je explicitně obsažen v zásady indexování, je také nutné definovat index typy, které bude použito na tuto cestu a pro každý typ indexu, tento index se vztahuje na datový typ:

| Typ indexu | Povolené cílové datové typy |
| --- | --- |
| Rozsah | Řetězec nebo číslo |
| Spatial | Point, LineString nebo mnohoúhelníku |

Například jsme zahrnuli `/headquarters/employees/?` cesty a určit, že `Range` indexu bude použito na tuto cestu pro obě `String` a `Number` hodnoty.

### <a name="includeexclude-strategy"></a>Zahrnutí a vyloučení strategie

Žádné zásady indexování musí obsahovat kořenová cesta `/*` jako vloženou nebo Vyloučená cesta.

- Zahrnují kořenovou cestou za účelem selektivně vyloučit cesty, které není třeba indexovat. To je doporučená, protože umožňuje proaktivně indexovat žádné nové vlastnosti, který může být přidán do modelu služby Azure Cosmos DB.
- Vylučte kořenovou cestou za účelem selektivně zahrnout cesty, které je třeba indexovat.

Zobrazit [v této části](how-to-manage-indexing-policy.md#indexing-policy-examples) indexování příklady zásad.

## <a name="modifying-the-indexing-policy"></a>Úprava zásady indexování

Zásady indexování kontejneru je aktualizovat v každém okamžiku [pomocí webu Azure portal nebo jeden z podporovaných sad SDK](how-to-manage-indexing-policy.md). Aktualizace zásady indexování aktivuje transformace od starého indexu do nového, která se provádí online i lokálně (takže žádné další úložný prostor se spotřebovává během operace). Index starou zásadu se efektivně transformuje na novou zásadu bez ovlivnění dostupnosti zápisu nebo ke kontejneru zřízené propustnosti. Index transformace je asynchronní operace a čas potřebný k dokončení závisí na zřízenou propustnost, počet položek a jejich velikost. 

> [!NOTE]
> Probíhá vytvoření nového indexu, dotazy nesmí vracet odpovídající výsledky a udělá to bez vrátí všechny chyby. To znamená, že výsledky dotazu nemusí být konzistentní, dokud se nedokončí transformace indexu. Je možné sledovat průběh index transformace [pomocí jedné ze sad SDK](how-to-manage-indexing-policy.md).

Pokud nové zásady indexování režim je nastaven na konzistentní, jiné indexování změny zásad lze použít během transformace indexu. Spuštění transformace indexu se dá zrušit tak, že nastavíte zásady indexování režim na hodnotu None (což bude okamžitě drop index).

## <a name="indexing-policies-and-ttl"></a>Zásady indexování a hodnota TTL

[Time-to-Live (TTL) funkce](time-to-live.md) vyžaduje, aby byly aktivní v kontejneru je zapnuté indexování. To znamená, že:

- není možné aktivovat TTL na kontejner, ve kterém indexování režim je nastaven na hodnotu None,
- není možné nastavit indexování režim na hodnotu None na kontejner, kde se hodnota TTL aktivovat.

Pro scénáře, kde žádná cesta k vlastnosti musí indexovat, ale hodnota TTL je nutné můžete použít zásady indexování pomocí:

- indexování režim nastavený na konzistentní, a
- žádná cesta součástí a
- `/*` jako pouze Vyloučená cesta.

## <a name="obsolete-attributes"></a>Zastaralé atributy

Při práci se zásadami indexování, může dojít k následující atributy, které jsou nyní zastaralé:

- `automatic` Logická hodnota definované v kořenovém adresáři zásady indexování. Nyní je ignorována a může být nastaven na `true`, když používáte nástroj vyžaduje.
- `precision` je číslo definované na úrovni index pro zahrnuté cesty. Nyní je ignorována a může být nastaven na `-1`, když používáte nástroj vyžaduje.
- `hash` je index typ, který je nyní nahrazena druh rozsahu.

## <a name="next-steps"></a>Další postup

Další informace o indexování v následujících článcích:

- [Indexování – přehled](index-overview.md)
- [Jak spravovat zásady indexování](how-to-manage-indexing-policy.md)
