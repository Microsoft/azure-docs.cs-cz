---
title: Azure Cosmos DB zásady indexování
description: Přečtěte si, jak nakonfigurovat a změnit výchozí zásady indexování pro automatické indexování a vyšší výkon v Azure Cosmos DB.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: thweiss
ms.openlocfilehash: 01e3e1f1c9bffee0604de1260e8e466f5b1d229d
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467877"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Indexování zásad v Azure Cosmos DB

V Azure Cosmos DB má každý kontejner zásady indexování, které určují, jak by měly být indexovány položky kontejneru. Výchozí zásada indexování pro nově vytvořené kontejnery indexuje každou vlastnost každé položky, vynucuje indexy rozsahu pro libovolný řetězec nebo číslo a prostorové indexy pro libovolný objekt typu Point. JSON. To vám umožní získat vysoký výkon dotazů, aniž byste se museli zabývat o indexování a správě indexu předem.

V některých situacích může být vhodné toto automatické chování přepsat, aby lépe vyhovovalo vašim požadavkům. Zásady indexování kontejneru můžete přizpůsobit nastavením jeho *režimu indexování*a zahrnutí nebo vyloučení *cest*k vlastnostem.

> [!NOTE]
> Metoda aktualizace zásad indexování popsaná v tomto článku se týká jenom rozhraní API pro Azure Cosmos DB SQL (Core).

## <a name="indexing-mode"></a>Režim indexování

Azure Cosmos DB podporuje dva režimy indexování:

- **Konzistentní**: Pokud je zásada indexování kontejneru nastavená na konzistentní, index se při vytváření, aktualizaci nebo odstraňování položek aktualizuje synchronně. To znamená, že konzistence vašich dotazů pro čtení bude [konzistence nakonfigurovaná pro tento účet](consistency-levels.md).

- **Žádný**: Pokud je zásada indexování kontejneru nastavená na hodnotu žádné, indexování je v tomto kontejneru efektivně zakázané. To se běžně používá, když se kontejner používá jako úložiště čistě klíč-hodnota bez nutnosti sekundárních indexů. Může také urychlit operace hromadného vložení.

## <a name="including-and-excluding-property-paths"></a>Zahrnutí a vyloučení cest k vlastnostem

Vlastní zásada indexování může určovat cesty vlastností, které jsou explicitně zahrnuté nebo vyloučené z indexování. Optimalizací počtu indexovaných cest můžete snížit velikost úložiště využitého vaším kontejnerem a zlepšit latenci operací zápisu. Tyto cesty jsou definovány podle [metody popsané v části Přehled indexování](index-overview.md#from-trees-to-property-paths) s následujícími přídavky:

- Cesta vedoucí k skalární hodnotě (řetězec nebo číslo) končí na`/?`
- prvky z pole jsou řešeny společně pomocí `/[]` zápisu ( `/0`místo `/1` atd.)
- `/*` zástupný znak se dá použít k vyhledání všech prvků pod uzlem.

Opětovné provedení stejného příkladu:

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

- `headquarters`cesta je`employees``/headquarters/employees/?`
- `locations`cesta je`country``/locations/[]/country/?`
- Cesta k libovolnému `headquarters` prvku je`/headquarters/*`

Je-li cesta explicitně obsažena v zásadách indexování, musí také definovat, které typy indexů by měly být použity na tuto cestu a pro každý typ indexu, datový typ, pro který tento index platí:

| Typ indexu | Povolené cílové datové typy |
| --- | --- |
| Rozsah | Řetězec nebo číslo |
| Spatial | Point, LineString nebo mnohoúhelník |

Například můžeme zahrnout `/headquarters/employees/?` cestu a určit `Range` , že se má index použít `String` na tuto cestu pro hodnoty a `Number` .

### <a name="includeexclude-strategy"></a>Zahrnout/vyloučit strategii

Všechny zásady indexování musí zahrnovat kořenovou cestu `/*` buď jako zahrnutou, nebo jako vyloučenou cestu.

- Zahrňte kořenovou cestu pro selektivní vyloučení cest, které nemusejí být indexovány. To je doporučený postup, protože umožňuje Azure Cosmos DB proaktivní indexování všech nových vlastností, které mohou být přidány do modelu.
- Vylučte kořenovou cestu pro selektivní zahrnutí cest, které je třeba indexovat.

- Pro cesty s běžnými znaky, které zahrnují: alfanumerické znaky a _ (podtržítko), nemusíte řídicí řetězec cesty kolem dvojitých uvozovek (například "/path/?"). V případě cest s dalšími speciálními znaky je nutné před dvojitými uvozovkami vyčínat řetězec cesty (například "\"/Path-\"ABC/?"). Pokud očekáváte, že v cestě jsou speciální znaky, můžete každý z nich vymezit řídicím znakem. Funkce bez jakýchkoli rozdílů neprovádí žádnou odchylku, pokud zadáte všechny cesty a pouze ty, které mají speciální znaky.

- Vlastnost System "ETag" je vyloučena z indexování ve výchozím nastavení, pokud není značka ETag přidána do zahrnuté cesty pro indexování.

Příklady zásad indexování najdete v [této části](how-to-manage-indexing-policy.md#indexing-policy-examples) .

## <a name="composite-indexes"></a>Složené indexy

Dotaz, `ORDER BY` že dvě nebo více vlastností vyžadují složený index. V současné době se složené indexy využívají jenom ve více `ORDER BY` dotazech. Ve výchozím nastavení nejsou definovány žádné složené indexy, takže v případě potřeby byste měli [Přidat složené indexy](how-to-manage-indexing-policy.md#composite-indexing-policy-examples) .

Při definování složeného indexu zadáte:

- Dvě nebo více cest k vlastnostem. Pořadí, ve kterém jsou definovány cesty k vlastnostem.
- Pořadí (vzestupně nebo sestupně).

Při použití složených indexů se používají následující požadavky:

- Pokud se složené cesty indexu neshodují s pořadím vlastností v klauzuli ORDER BY, nemůže složený index podporovat dotaz.

- Pořadí složených indexových cest (vzestupně nebo sestupně) by mělo také odpovídat pořadí v klauzuli ORDER BY.

- Složený index také podporuje klauzuli ORDER BY s opačným pořadím na všech cestách.

Vezměte v úvahu následující příklad, kde je složený index definován pro vlastnosti a, b a c:

| **Složený index**     | **Vzorový `ORDER BY` dotaz**      | **Podporováno indexem?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(a asc, b asc)```         | ```ORDER BY  a asc, b asc```        | ```Yes```            |
| ```(a asc, b asc)```          | ```ORDER BY  b asc, a asc```        | ```No```             |
| ```(a asc, b asc)```          | ```ORDER BY  a desc, b desc```      | ```Yes```            |
| ```(a asc, b asc)```          | ```ORDER BY  a asc, b desc```       | ```No```             |
| ```(a asc, b asc, c asc)``` | ```ORDER BY  a asc, b asc, c asc``` | ```Yes```            |
| ```(a asc, b asc, c asc)``` | ```ORDER BY  a asc, b asc```        | ```No```            |

Zásady indexování byste měli přizpůsobit, abyste mohli obsluhovat všechny potřebné `ORDER BY` dotazy.

## <a name="modifying-the-indexing-policy"></a>Změna zásad indexování

Zásadu indexování kontejneru lze kdykoli aktualizovat [pomocí Azure Portal nebo jedné z podporovaných sad SDK](how-to-manage-indexing-policy.md). Aktualizace zásad indexování spustí transformaci z původního indexu na novou, která je provedena online a na místě (takže se během operace nespotřebovává žádný další prostor úložiště). Index staré zásady se efektivně transformuje na nové zásady, aniž by to ovlivnilo dostupnost zápisu nebo propustnost zřízenou v kontejneru. Transformace indexu je asynchronní operace a čas potřebný k dokončení závisí na zřízené propustnosti, počtu položek a jejich velikosti. 

> [!NOTE]
> I když probíhá opětovné indexování, dotazy nemusí vracet všechny výsledky, které odpovídají, a to tak, aby se nevrátily žádné chyby. To znamená, že výsledky dotazu nemusí být konzistentní, dokud se nedokončí transformace indexu. Je možné sledovat průběh transformace indexu [pomocí jedné ze sad SDK](how-to-manage-indexing-policy.md).

Pokud je nový režim zásad indexování nastavený na konzistentní, při transformaci indexu se nedají použít žádné jiné změny zásad indexování. Spuštěnou transformaci indexu můžete zrušit tak, že nastavíte režim zásad indexování na žádný (který bude ihned index vyřadit).

## <a name="indexing-policies-and-ttl"></a>Indexování zásad a TTL

[Funkce TTL (Time-to-Live)](time-to-live.md) vyžaduje, aby bylo indexování aktivní na kontejneru, ve kterém je zapnuté. To znamená, že:

- hodnotu TTL není možné aktivovat u kontejneru, ve kterém je režim indexování nastavený na None.
- není možné nastavit režim indexování na žádný v kontejneru, kde je aktivována hodnota TTL.

U scénářů, ve kterých není nutné indexovat žádnou cestu k vlastnostem, ale je vyžadována hodnota TTL, můžete použít zásadu indexování pomocí:

- režim indexování nastavený na konzistentní a
- žádná zahrnutá cesta a
- `/*`jako jediná Vyloučená cesta.

## <a name="obsolete-attributes"></a>Zastaralé atributy

Při práci se zásadami indexování může dojít k následujícím atributům, které jsou nyní zastaralé:

- `automatic`je logická hodnota definovaná v kořenu zásady indexování. Teď se ignoruje a dá se nastavit na `true`, když ho nástroj vyžaduje.
- `precision`je číslo definované na úrovni indexu pro zahrnuté cesty. Teď se ignoruje a dá se nastavit na `-1`, když ho nástroj vyžaduje.
- `hash`je typ indexu, který je nyní nahrazen druhem rozsahu.

## <a name="next-steps"></a>Další postup

Další informace o indexování najdete v následujících článcích:

- [Přehled indexování](index-overview.md)
- [Jak spravovat zásady indexování](how-to-manage-indexing-policy.md)
