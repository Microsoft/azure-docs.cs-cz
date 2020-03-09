---
title: Řešení potíží s dotazy při použití Azure Cosmos DB
description: Naučte se identifikovat, diagnostikovat a řešit potíže s Azure Cosmos DB problémy s dotazy SQL.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: tisande
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 0dd3cb12c52e23a0a8acd57bf401ba68acfb9925
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623691"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>Řešení potíží s dotazy při použití Azure Cosmos DB

Tento článek vás provede obecným doporučeným přístupem k řešení potíží s dotazy v Azure Cosmos DB. I když se postup uvedený v tomto dokumentu nesmí považovat za "zachytit vše" pro potenciální problémy s dotazy, zahrnuli jsme do něj nejběžnější tipy k výkonu. Tento dokument byste měli použít jako počáteční místo pro řešení potíží s pomalými nebo nákladnými dotazy v rozhraní API jádra Azure Cosmos DB (SQL). [Diagnostické protokoly](cosmosdb-monitor-resource-logs.md) můžete použít také k identifikaci dotazů, které jsou pomalé nebo využívají významné množství propustnosti.

Optimalizace dotazů můžete široce kategorizovat v Azure Cosmos DB: optimalizace, které omezují náklady na žádost (RU) na dotaz a optimalizace, které pouze omezují latenci. Omezením poplatku za dotaz by se snížila i latence.

Tento dokument bude používat příklady, které se dají znovu vytvořit pomocí [nutriční](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) datové sady.

## <a name="important"></a>Důležité

- Nejlepší výkon získáte pomocí [tipů ke zvýšení výkonu](performance-tips.md).
    > [!NOTE] 
    > Pro zvýšení výkonu se doporučuje zpracování bitového hostitelského systému Windows 64. Sada SQL SDK obsahuje nativní ServiceInterop. dll k analýze a optimalizaci dotazů v místním prostředí a je podporována pouze na platformě Windows x64. Pro Linux a jiné nepodporované platformy, kde ServiceInterop. dll není k dispozici, provede další síťové volání brány k získání optimalizovaného dotazu. 
- Cosmos DB dotaz nepodporuje minimální počet položek.
    - Kód by měl zpracovat libovolnou velikost stránky od 0 do maximálního počtu položek.
    - Počet položek na stránce může a bude změněn bez předchozího upozornění.
- Pro dotazy jsou očekávány prázdné stránky a lze je kdykoli zobrazit. 
    - Důvody, proč jsou v sadách SDK zpřístupněny prázdné stránky, umožňují více příležitostí zrušit dotaz. Také umožňuje vymazat, že sada SDK provádí více síťových volání.
    - Prázdné stránky se mohou zobrazit ve stávajících úlohách, protože fyzický oddíl je rozdělen Cosmos DB. První oddíl má nyní 0 výsledků, což způsobí, že bude prázdná stránka.
    - Prázdné stránky jsou způsobeny back-endu pro dotazování, protože dotaz vychází z back-endu s větším množstvím času pro načtení dokumentů. Pokud Cosmos DB zruší dotaz, vrátí token pro pokračování, který umožní, aby dotaz pokračoval. 
- Nezapomeňte dotaz úplně vyprázdnit. Podívejte se na ukázky sady SDK a pomocí smyčky while na `FeedIterator.HasMoreResults` vyprázdněte celý dotaz.

### <a name="obtaining-query-metrics"></a>Získávání metrik dotazů:

Při optimalizaci dotazu v Azure Cosmos DB je prvním krokem vždy [získání metriky dotazu](profile-sql-api-query.md) pro dotaz. K dispozici jsou také prostřednictvím Azure Portal, jak je znázorněno níže:

[![získávání metriky dotazu](./media/troubleshoot-query-performance/obtain-query-metrics.png)](./media/troubleshoot-query-performance/obtain-query-metrics.png#lightbox)

Po získání metriky dotazu Porovnejte počet načtených dokumentů s počtem výstupních dokumentů pro váš dotaz. Pomocí tohoto porovnání můžete určit relevantní oddíly pro odkaz níže.

Počet načtených dokumentů je počet dokumentů, které vyžaduje dotaz k načtení. Počet výstupních dokumentů je počet dokumentů, které byly potřeba pro výsledky dotazu. Pokud je počet načtených dokumentů výrazně vyšší než počet výstupních dokumentů, měla by být alespoň jedna část dotazu, která nedokázala použít index a která je nutná k prohledání.

Na níže uvedený oddíl můžete získat informace, abyste pochopili relevantní optimalizace dotazů pro váš scénář:

### <a name="querys-ru-charge-is-too-high"></a>Poplatek za RU v dotazu je příliš vysoký.

#### <a name="retrieved-document-count-is-significantly-greater-than-output-document-count"></a>Počet načtených dokumentů je podstatně větší než počet výstupních dokumentů.

- [Zahrnutí potřebných cest do zásad indexování](#include-necessary-paths-in-the-indexing-policy)

- [Informace o tom, které systémové funkce využívají index](#understand-which-system-functions-utilize-the-index)

- [Dotazy s klauzulí Filter a ORDER BY](#queries-with-both-a-filter-and-an-order-by-clause)

- [Optimalizujte výrazy JOIN pomocí poddotazu.](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>Počet načtených dokumentů je přibližně roven počtu výstupních dokumentů.

- [Vyhnout se dotazům mezi oddíly](#avoid-cross-partition-queries)

- [Filtry u více vlastností](#filters-on-multiple-properties)

- [Dotazy s klauzulí Filter a ORDER BY](#queries-with-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>Náklady na dotaz na RU jsou přijatelné, ale latence je stále příliš vysoká.

- [Zlepšení blízkosti](#improve-proximity)

- [Zvýšení zřízené propustnosti](#increase-provisioned-throughput)

- [Zvýšit MaxConcurrency](#increase-maxconcurrency)

- [Zvýšit MaxBufferedItemCount](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>Dotazy, kde počet načtených dokumentů překračuje počet výstupních dokumentů

 Počet načtených dokumentů je počet dokumentů, které vyžaduje dotaz k načtení. Počet výstupních dokumentů je počet dokumentů, které byly potřeba pro výsledky dotazu. Pokud je počet načtených dokumentů výrazně vyšší než počet výstupních dokumentů, měla by být alespoň jedna část dotazu, která nedokázala použít index a která je nutná k prohledání.

 Níže je uveden příklad skenovacího dotazu, který nebyl zcela obsluhován indexem.

Dotaz

 ```sql
SELECT VALUE c.description
FROM c
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
 ```

Metriky dotazů:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

Počet načtených dokumentů (60 951) je podstatně větší než počet výstupních dokumentů (7), aby tento dotaz vyžadoval kontrolu. V tomto případě systémová funkce [Upper ()](sql-query-upper.md) nevyužívá index.

## <a name="include-necessary-paths-in-the-indexing-policy"></a>Zahrnutí potřebných cest do zásad indexování

Vaše zásada indexování by měla zahrnovat jakékoli vlastnosti zahrnuté v klauzulích `WHERE`, `ORDER BY` klauzulích, `JOIN`a většině systémových funkcí. Cesta zadaná v zásadách indexu by se měla shodovat s vlastností v dokumentech JSON (rozlišuje velká a malá písmena).

Pokud spustíme jednoduchý dotaz na [nutriční](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) datovou sadu, při každém indexování je v případě, že je vlastnost v klauzuli `WHERE` indexována, zabereme mnohem nižší poplatek za ru.

### <a name="original"></a>původně

Dotaz

```sql
SELECT * FROM c WHERE c.description = "Malabar spinach, cooked"
```

Zásady indexování:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/description/*"
        }
    ]
}
```

**Poplatek za ru:** 409,51 ru

### <a name="optimized"></a>Optimalizované

Zásady indexování se aktualizovaly:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

**Poplatek za ru:** 2,98 ru

K zásadám indexování můžete kdykoli přidat další vlastnosti bez dopadu na dostupnost nebo výkon zápisu. Pokud do indexu přidáte novou vlastnost, budou dotazy, které tuto vlastnost používají, okamžitě využívat nový dostupný index. Dotaz bude při sestavení používat nový index. Výsledkem je, že výsledky dotazu můžou být nekonzistentní, protože probíhá opětovné sestavení indexu. Pokud je nová vlastnost indexována, dotazy, které využívají pouze existující indexy, nebudou při opětovném sestavení indexu ovlivněny. [Průběh transformace indexu můžete sledovat](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-net-sdk-v3).

## <a name="understand-which-system-functions-utilize-the-index"></a>Informace o tom, které systémové funkce využívají index

Pokud je výraz možné přeložit na rozsah řetězcových hodnot, znamená to, že může využívat index, jinak ne.

Tady je seznam řetězcových hodnot, které můžou využívat index:

- STARTSWITH(str_expr, str_expr)
- LEFT(str_expr, num_expr) = str_expr
- SUBSTRING(str_expr, num_expr, num_expr) = str_expr, ale pouze pokud má první parametr num_expr hodnotu 0

Některé běžné systémové funkce, které nepoužívají index a musí načíst každý dokument, jsou následující:

| **Systémová funkce**                     | **Nápady pro optimalizaci**             |
| --------------------------------------- |------------------------------------------------------------ |
| CONTAINS                                | Použití Azure Search pro fulltextové vyhledávání                        |
| HORNÍ/DOLNÍ                             | Namísto použití systémové funkce k normalizaci dat pokaždé pro porovnávání místo toho při vložení normalizují velká a malá písmena. Dotaz, jako je například ```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'```, se jednoduše naplní ```SELECT * FROM c WHERE c.name = 'BOB'``` |
| Matematické funkce (neagregace) | Pokud v dotazu potřebujete často vypočítat hodnotu, zvažte možnost Uložit tuto hodnotu jako vlastnost v dokumentu JSON. |

------

Ostatní části dotazu mohou i nadále využívat index navzdory tím, že systémové funkce nepoužívají index.

## <a name="queries-with-both-a-filter-and-an-order-by-clause"></a>Dotazy s klauzulí Filter a ORDER BY

I když dotazy s filtrem a klauzulí `ORDER BY` obvykle využívají index rozsahu, budou efektivnější, pokud je lze zpracovat ze složeného indexu. Kromě změny zásad indexování byste měli do klauzule `ORDER BY` přidat všechny vlastnosti složeného indexu. Tato úprava dotazu zajistí, že bude používat složený index.  Můžete sledovat dopad spuštěním dotazu na [nutriční](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) datovou sadu.

### <a name="original"></a>původně

Dotaz

```sql
SELECT * FROM c WHERE c.foodGroup = "Soups, Sauces, and Gravies" ORDER BY c._ts ASC
```

Zásady indexování:

```json
{

        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[]
}
```

**Poplatek za ru:** 44,28 ru

### <a name="optimized"></a>Optimalizované

Aktualizovaný dotaz (zahrnuje obě vlastnosti v klauzuli `ORDER BY`):

```sql
SELECT * FROM c
WHERE c.foodGroup = “Soups, Sauces, and Gravies”
ORDER BY c.foodGroup, c._ts ASC
```

Zásady indexování se aktualizovaly:

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
        },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
    }

```

**Poplatek za ru:** 8,86 ru

## <a name="optimize-join-expressions-by-using-a-subquery"></a>Optimalizujte výrazy JOIN pomocí poddotazu.
Podhodnoty poddotazů mohou optimalizovat `JOIN` výrazy vložením predikátů za každý výraz SELECT-many místo po všech křížových spojeních v klauzuli `WHERE`.

Vezměte v úvahu následující dotaz:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0
AND n.nutritionValue < 10) AND s.amount > 1
```

**Poplatek za ru:** 167,62 ru

Pro tento dotaz bude index odpovídat jakémukoli dokumentu, který má značku s názvem "počáteční vzorec", nutritionValue větší než 0 a obsluhující množství větší než 1. Výraz `JOIN` v tomto případě provede všechny položky značek, živin a zařadí pole pro každý shodný dokument před použitím jakéhokoli filtru. Klauzule `WHERE` pak použije predikát filtru u každé `<c, t, n, s>` řazené kolekce členů.

Pokud má například odpovídající dokument 10 položek v každém ze tří polí, rozšíří se na 1 x 10 x 10 x 10 (tj. 1 000) řazené kolekce členů. Použití poddotazů tady může pomoci při filtrování propojených položek pole před připojením k dalšímu výrazu.

Tento dotaz je ekvivalentní předchozímu, ale používá poddotazy:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**Poplatek za ru:** 22,17 ru

Předpokládejme, že filtr odpovídá pouze jedné položce v poli značek a existuje pět položek pro živiny i pole. Výrazy `JOIN` se pak rozbalí na 1 x 1 x 5 × 5 = 25 položek, a to na rozdíl od 1 000 položek v prvním dotazu.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>Dotazy, kde se načtený počet dokumentů rovná počtu výstupních dokumentů

Pokud je počet načtených dokumentů přibližně roven výstupnímu počtu dokumentů, znamená to, že dotaz nemusel kontrolovat mnoho zbytečných dokumentů. Pro mnoho dotazů, jako jsou například ty, které používají klíčové slovo TOP, může počet načtených dokumentů překročit počet výstupních dokumentů o 1. To by nemělo způsobovat obavy.

## <a name="avoid-cross-partition-queries"></a>Vyhnout se dotazům mezi oddíly

Azure Cosmos DB používá [dělení](partitioning-overview.md) ke škálování jednotlivých kontejnerů v případě, že se vyžadují jednotky žádosti a úložiště dat. Každý fyzický oddíl má samostatný a nezávislý index. Pokud má váš dotaz filtr rovnosti, který odpovídá klíči oddílu vašeho kontejneru, budete muset pouze ověřit index relevantního oddílu. Tato optimalizace snižuje celkový počet RU dotazů, které vyžaduje dotaz.

Pokud máte velké množství zřízené RU (více než 30 000) nebo velký objem uložených dat (více než 100 GB), budete pravděpodobně mít velký kontejner, ve kterém se můžete podívat na výrazné snížení nákladů na dotaz RU.

Pokud například vytvoříme kontejner s klíčovým adaptérem oddílu, budou následující dotazy potřebovat pouze kontrolovat jeden fyzický oddíl:

```sql
SELECT * FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

Tyto dotazy by taky byly optimalizované zahrnutím klíče oddílu do dotazu:

```sql
SELECT * FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

Dotazy, které mají na klíč oddílu filtry rozsahů, nebo nemají žádné filtry pro klíč oddílu, budou muset "ventilátor-out" a kontrolovat výsledky pro každý z indexu fyzického oddílu.

```sql
SELECT * FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT * FROM c
WHERE c.foodGroup > "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

## <a name="filters-on-multiple-properties"></a>Filtry u více vlastností

I když dotazy s filtry u více vlastností budou normálně využívat index rozsahu, budou efektivnější, pokud je lze zpracovat ze složeného indexu. U malých objemů dat tato optimalizace nebude mít významný dopad. U velkých objemů dat se ale může ukázat jako užitečné. V rámci složeného indexu můžete optimalizovat jenom jeden filtr nerovnosti. Pokud má váš dotaz více filtrů bez rovnosti, měli byste vybrat jeden z nich, který bude používat složený index. Zbytek bude dál používat indexy rozsahu. Filtr bez rovnosti musí být v složeném indexu definován jako poslední. [Další informace o složených indexech](index-policy.md#composite-indexes)

Tady jsou některé příklady dotazů, které by mohly být optimalizované pomocí složeného indexu:

```sql
SELECT * FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts = 1575503264
```

```sql
SELECT * FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts > 1575503264
```

Tady je příslušný složený index:

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
                },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
}
```

## <a name="optimizations-that-reduce-query-latency"></a>Optimalizace, které snižují latenci dotazů:

V mnoha případech může být poplatek přijatelný, ale latence dotazů je stále příliš vysoká. Níže uvedené části poskytují přehled tipů pro snížení latence dotazů. Pokud stejný dotaz spouštíte několikrát pro stejnou datovou sadu, bude mít každý čas stejný poplatek za RU. Latence dotazů se ale může lišit mezi provedeními dotazu.

## <a name="improve-proximity"></a>Zlepšení blízkosti

Dotazy, které jsou spouštěny z jiné oblasti, než je účet Azure Cosmos DB, budou mít vyšší latenci, než kdyby byly spuštěny ve stejné oblasti. Pokud jste například spustili kód na stolním počítači, měli byste očekávat, že latence bude Desítková nebo stovka (nebo více) milisekund větší než v případě, že dotaz pochází z virtuálního počítače ve stejné oblasti Azure jako Azure Cosmos DB. [Globální distribuce dat v Azure Cosmos DB](distribute-data-globally.md) je jednoduchá, aby bylo zajištěno, že vaše data budou blíž do vaší aplikace.

## <a name="increase-provisioned-throughput"></a>Zvýšení zřízené propustnosti

V Azure Cosmos DB se zřízená propustnost měří v jednotkách žádosti (RU). Představte si, že máte dotaz, který spotřebovává 5 RU propustnosti. Pokud například zřídíte 1 000 RU, budete moct spustit tento dotaz 200 krát za sekundu. Pokud jste se pokusili spustit dotaz, když není k dispozici dostatek propustnosti, Azure Cosmos DB by vrátil chybu HTTP 429. Po krátké době se tento dotaz automaticky zopakuje v jakékoli aktuální sadě SDK rozhraní API jádra (SQL). Omezené požadavky mohou trvat delší dobu, takže zvýšení zajištěné propustnosti může zlepšit latenci dotazů. [Celkový počet omezených požadavků](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors) můžete sledovat v okně metriky Azure Portal.

## <a name="increase-maxconcurrency"></a>Zvýšit MaxConcurrency

Paralelní dotazy fungují paralelně dotazování na více oddílů. Data z jednotlivých dělených kolekcí se ale v souvislosti s dotazem načítají sériově. To znamená, že úprava MaxConcurrency na počet oddílů má maximální šanci dosáhnout nejvíce výkonného dotazu, za předpokladu, že všechny ostatní systémové podmínky zůstanou stejné. Pokud neznáte počet oddílů, můžete nastavit MaxConcurrency (nebo MaxDegreesOfParallelism ve starších verzích sady SDK) na vysoké číslo a systém zvolí minimální úroveň paralelismu (počet oddílů, zadání uživatelem zadaných uživatelem).

## <a name="increase-maxbuffereditemcount"></a>Zvýšit MaxBufferedItemCount

Dotazy jsou navržené tak, aby výsledky byly předem načteny, zatímco aktuální dávka výsledků je zpracovávána klientem. Předběžné načítání pomáhá při celkové latenci v rámci dotazu. Nastavení MaxBufferedItemCount omezuje počet předběžně načtených výsledků. Když nastavíte tuto hodnotu na očekávaný počet vrácených výsledků (nebo vyšší číslo), dotaz může získat maximální přínos před načtením. Nastavení této hodnoty na hodnotu-1 umožní systému automaticky rozhodovat o počtu položek do vyrovnávací paměti.

## <a name="next-steps"></a>Další kroky
Další informace o tom, jak měřit ru na dotaz, získat statistiku spouštění a vyladit dotazy a další informace:

* [Získání metrik spouštění dotazů SQL pomocí sady .NET SDK](profile-sql-api-query.md)
* [Ladění výkonu dotazů pomocí služby Azure Cosmos DB](sql-api-sql-query-metrics.md)
* [Tipy ke zvýšení výkonu pro sadu .NET SDK](performance-tips.md)
