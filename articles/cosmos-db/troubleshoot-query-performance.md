---
title: Řešení problémů s dotazem při používání Služby Azure Cosmos DB
description: Zjistěte, jak identifikovat, diagnostikovat a řešit problémy s dotazy Azure Cosmos DB SQL.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 04/20/2020
ms.author: tisande
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 4a8b61f3719a60af567d10f8839987e613babc9e
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870454"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>Řešení problémů s dotazem při používání Služby Azure Cosmos DB

Tento článek vás provede obecným doporučeným přístupem pro řešení potíží s dotazy v Azure Cosmos DB. I když byste neměli považovat kroky popsané v tomto článku úplnou obranu proti potenciálním problémům s dotazem, zahrnuli jsme zde nejběžnější tipy pro výkon. Tento článek byste měli použít jako výchozí místo pro řešení potíží s pomalými nebo nákladnými dotazy v rozhraní API jádra Azure Cosmos DB (SQL). Diagnostické [protokoly](cosmosdb-monitor-resource-logs.md) můžete také použít k identifikaci dotazů, které jsou pomalé nebo které spotřebovávají značné množství propustnosti.

Optimalizace dotazů v Azure Cosmos DB můžete široce kategorizovat:

- Optimalizace, které snižují poplatek za jednotku požadavku (ŽP) dotazu
- Optimalizace, které jen snižují latenci

Pokud snížíte poplatek ru dotazu, téměř jistě snížíte také latenci.

Tento článek obsahuje příklady, které můžete znovu vytvořit pomocí datové sady [výživy.](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)

## <a name="common-sdk-issues"></a>Běžné problémy sady SDK

- Nejlepší výkon najdete v tipech [pro výkon](performance-tips.md).
    > [!NOTE]
    > Pro zvýšení výkonu doporučujeme 64bitové zpracování hostitelů v systému Windows. Sql SDK obsahuje nativní ServiceInterop.dll analyzovat a optimalizovat dotazy místně. Služba ServiceInterop.dll je podporována pouze na platformě Windows x64. Pro Linux a další nepodporované platformy, kde ServiceInterop.dll není k dispozici, další síťové volání bude provedeno do brány získat optimalizovaný dotaz.
- Můžete nastavit `MaxItemCount` pro vaše dotazy, ale nemůžete zadat minimální počet položek.
    - Kód by měl zpracovat libovolnou `MaxItemCount`velikost stránky od nuly do .
    - Počet položek na stránce bude vždy menší `MaxItemCount`než zadaný . Nicméně, `MaxItemCount` je přísně maximální a tam by mohlo být méně výsledků, než tato částka.
- Někdy dotazy mohou mít prázdné stránky i v případě, že jsou výsledky na budoucí stránce. Důvody pro to může být:
    - Sada SDK může uskutečňovat více síťových volání.
    - Dotaz může trvat dlouhou dobu načíst dokumenty.
- Všechny dotazy mají token pokračování, který umožní pokračovat v dotazu. Ujistěte se, že vyprázdnění dotazu úplně. Podívejte se na ukázky sady `while` SDK a použijte smyčku k `FeedIterator.HasMoreResults` vyprázdnění celého dotazu.

## <a name="get-query-metrics"></a>Získání metrik dotazu

Když optimalizujete dotaz v Azure Cosmos DB, prvním krokem je vždy [získat metriky dotazu](profile-sql-api-query.md) pro váš dotaz. Tyto metriky jsou taky dostupné na webu Azure Portal:

[![Získávání metrik](./media/troubleshoot-query-performance/obtain-query-metrics.png) dotazů](./media/troubleshoot-query-performance/obtain-query-metrics.png#lightbox)

Po získání metrikdotazu porovnejte počet načtených dokumentů s počtem výstupních dokumentů pro dotaz. Toto porovnání slouží k identifikaci příslušných oddílů ke kontrole v tomto článku.

Počet načtených dokumentů je počet dokumentů, které dotaz potřebný k načtení. Počet výstupních dokumentů je počet dokumentů, které byly potřebné pro výsledky dotazu. Pokud načtený počet dokumentů je výrazně vyšší než počet výstupních dokumentů, byla alespoň jedna část dotazu, který nebyl schopen použít index a potřebné k prohledávání.

Naleznete v následujících částech pochopit příslušné optimalizace dotazů pro váš scénář.

### <a name="querys-ru-charge-is-too-high"></a>Poplatek ru dotazu je příliš vysoký

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>Počet načtených dokumentů je výrazně vyšší než počet výstupních dokumentů

- [Zahrnout nezbytné cesty v zásadách indexování.](#include-necessary-paths-in-the-indexing-policy)

- [Zjistěte, které systémové funkce používají index.](#understand-which-system-functions-use-the-index)

- [Pochopit, které agregační dotazy používají index.](#understand-which-aggregate-queries-use-the-index)

- [Upravte dotazy, které mají filtr a klauzuli ORDER BY.](#modify-queries-that-have-both-a-filter-and-an-order-by-clause)

- [Optimalizujte výrazy JOIN pomocí poddotazu.](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>Počet načtených dokumentů je přibližně roven počtu výstupních dokumentů.

- [Vyhněte se dotazy mezi oddíly.](#avoid-cross-partition-queries)

- [Optimalizujte dotazy, které mají filtry na více vlastností.](#optimize-queries-that-have-filters-on-multiple-properties)

- [Upravte dotazy, které mají filtr a klauzuli ORDER BY.](#modify-queries-that-have-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>Poplatek RU dotazu je přijatelný, ale latence je stále příliš vysoká

- [Zlepšete blízkost.](#improve-proximity)

- [Zvyšte zřízenou propustnost.](#increase-provisioned-throughput)

- [Zvyšte maximální měnu.](#increase-maxconcurrency)

- [Zvyšte počet maxbuffereditemcount.](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>Dotazy, u kterých počet načtených dokladů překračuje počet výstupních dokumentů

 Počet načtených dokumentů je počet dokumentů, které dotaz potřebný k načtení. Počet výstupních dokumentů je počet dokumentů, které byly potřebné pro výsledky dotazu. Pokud načtený počet dokumentů je výrazně vyšší než počet výstupních dokumentů, byla alespoň jedna část dotazu, který nebyl schopen použít index a potřebné k prohledávání.

Tady je příklad skenovacího dotazu, který nebyl indexem zcela obsluhován:

Dotaz:

 ```sql
SELECT VALUE c.description
FROM c
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
 ```

Metriky dotazu:

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

Počet načtených dokumentů (60 951) je výrazně vyšší než počet výstupních dokumentů (7), takže tento dotaz je potřeba provést prohledávání. V tomto případě funkce systému [UPPER()](sql-query-upper.md) nepoužívá index.

### <a name="include-necessary-paths-in-the-indexing-policy"></a>Zahrnout nezbytné cesty do zásad indexování

Zásady indexování by měly `WHERE` zahrnovat `ORDER BY` všechny vlastnosti zahrnuté v klauzulích, `JOIN`klauzulích a většině systémových funkcí. Cesta zadaná v zásadách indexu by se měla shodovat (rozlišují malá a velká písmena) vlastnost v dokumentech JSON.

Pokud spustíte jednoduchý dotaz na [datovou](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) sadu výživy, budete pozorovat `WHERE` mnohem nižší žP poplatek při indexování vlastnosti v klauzuli:

#### <a name="original"></a>Původní

Dotaz:

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

**Poplatek žP:** 409,51 RU

#### <a name="optimized"></a>Optimalizované

Aktualizované zásady indexování:

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

**Poplatek ŽP:** 2,98 RU

Vlastnosti můžete přidat do zásad indexování kdykoli, bez vlivu na dostupnost zápisu nebo výkon. Pokud přidáte novou vlastnost do indexu, dotazy, které používají vlastnost bude okamžitě používat nový dostupný index. Dotaz bude používat nový index, zatímco je právě stavěný. Takže výsledky dotazu může být nekonzistentní, zatímco index znovu sestavit probíhá. Pokud je indexována nová vlastnost, dotazy, které používají pouze existující indexy, nebudou během opětovného sestavení indexu ovlivněny. Můžete [sledovat průběh transformace indexu](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-net-sdk-v3).

### <a name="understand-which-system-functions-use-the-index"></a>Pochopit, které systémové funkce používají index

Pokud výraz lze přeložit do rozsahu řetězcové hodnoty, můžete použít index. Jinak nemůže.

Zde je seznam některých běžných funkcí řetězce, které lze použít index:

- STARTSWITH(str_expr, str_expr)
- LEFT(str_expr, num_expr) = str_expr
- SUBSTRING(str_expr, num_expr, num_expr) = str_expr, ale pouze v případě, že první num_expr je 0

Následují některé běžné systémové funkce, které nepoužívají index a musí načíst každý dokument:

| **Systémová funkce**                     | **Nápady pro optimalizaci**             |
| --------------------------------------- |------------------------------------------------------------ |
| CONTAINS                                | Pro fulltextové vyhledávání použijte Azure Search.                        |
| HORNÍ/DOLNÍ                             | Namísto použití funkce systému k normalizaci dat pro porovnání normalizujte kryt při vložení. Dotaz jako ```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'``` ```SELECT * FROM c WHERE c.name = 'BOB'```se stane . |
| Matematické funkce (neagregace) | Pokud potřebujete vypočítat hodnotu často v dotazu, zvažte ukládání hodnoty jako vlastnost v dokumentu JSON. |

------

Ostatní části dotazu může stále používat index, i když systémové funkce ne.

### <a name="understand-which-aggregate-queries-use-the-index"></a>Pochopit, které agregační dotazy používají index

Ve většině případů agregační systémové funkce v Azure Cosmos DB bude používat index. V závislosti na filtry nebo další klauzule v agregační dotaz, dotazovací stroj může být nutné načíst vysoký počet dokumentů. Dotazovací stroj obvykle použije nejprve filtry rovnosti a rozsahu. Po použití těchto filtrů může dotazovací stroj vyhodnotit další filtry a v případě potřeby se uchýlit k načtení zbývajících dokumentů, aby vypočítal agregaci.

Například vzhledem k těmto dvěma ukázkovým dotazům `CONTAINS` bude dotaz s filtrem rovnosti a `CONTAINS` systémových funkcí obecně efektivnější než dotaz pouze s filtrem systémových funkcí. Důvodem je, že filtr rovnosti je použit a používá index před `CONTAINS` dokumenty je třeba načíst pro dražší filtr.

Dotaz pouze `CONTAINS` s filtrem - vyšší žp.

```sql
SELECT COUNT(1) FROM c WHERE CONTAINS(c.description, "spinach")
```

Dotaz s filtrem `CONTAINS` rovnosti i filtrem - nižší poplatek žP:

```sql
SELECT AVG(c._ts) FROM c WHERE c.foodGroup = "Sausages and Luncheon Meats" AND CONTAINS(c.description, "spinach")
```

Zde jsou další příklady agregace dotazů, které nebudou plně používat index:

#### <a name="queries-with-system-functions-that-dont-use-the-index"></a>Dotazy se systémovými funkcemi, které nepoužívají index

Měli byste odkazovat na stránce příslušné [systémové funkce](sql-query-system-functions.md) a zjistit, zda používá index.

```sql
SELECT MAX(c._ts) FROM c WHERE CONTAINS(c.description, "spinach")
```

#### <a name="aggregate-queries-with-user-defined-functionsudfs"></a>Agregace dotazů s uživatelem definovanými funkcemi (UDF)

```sql
SELECT AVG(c._ts) FROM c WHERE udf.MyUDF("Sausages and Luncheon Meats")
```

#### <a name="queries-with-group-by"></a>Dotazy se skupinou PODLE

ŽP poplatek `GROUP BY` zvýší jako mohutnost vlastností `GROUP BY` v klauzuli zvyšuje. V tomto příkladu dotazovací stroj musí `c.foodGroup = "Sausages and Luncheon Meats"` načíst každý dokument, který odpovídá filtru, takže se očekává, že poplatek žP bude vysoký.

```sql
SELECT COUNT(1) FROM c WHERE c.foodGroup = "Sausages and Luncheon Meats" GROUP BY c.description
```

Pokud plánujete často spouštět stejné agregační dotazy, může být efektivnější vytvořit zhmotněné zobrazení v reálném čase s [informačním kanálem změn Azure Cosmos DB](change-feed.md) než spuštění jednotlivých dotazů.

### <a name="modify-queries-that-have-both-a-filter-and-an-order-by-clause"></a>Úprava dotazů, které mají filtr i klauzuli ORDER BY

Přestože dotazy, které mají `ORDER BY` filtr a klauzuli, budou obvykle používat index rozsahu, budou efektivnější, pokud mohou být obsluhovány z složeného indexu. Kromě úpravy zásad indexování byste měli do klauzule přidat `ORDER BY` všechny vlastnosti ve složeném indexu. Tato změna dotazu zajistí, že používá složený index.  Dopad můžete sledovat spuštěním dotazu na datovou sadu [výživy:](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)

#### <a name="original"></a>Původní

Dotaz:

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

**Poplatek žP:** 44,28 RU

#### <a name="optimized"></a>Optimalizované

Aktualizovaný dotaz (zahrnuje obě `ORDER BY` vlastnosti v klauzuli):

```sql
SELECT * FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c.foodGroup, c._ts ASC
```

Aktualizované zásady indexování:

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

**Poplatek ŽP:** 8,86 RU

### <a name="optimize-join-expressions-by-using-a-subquery"></a>Optimalizace výrazů JOIN pomocí poddotazu
Multi-value poddotazy můžete `JOIN` optimalizovat výrazy tím, že tlačí predikáty za každý výraz `WHERE` select-many, nikoli po všech křížových spojení v klauzuli.

Zvažte tento dotaz:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0
AND n.nutritionValue < 10) AND s.amount > 1
```

**Poplatek žP:** 167,62 RU

Pro tento dotaz index bude odpovídat libovolný dokument, který má značku s názvem "počáteční kojenecká výživa", nutritionValue větší než 0 a sloužící částka větší než 1. Výraz `JOIN` zde provede křížový součin všech položek značek, živin a porcí polí pro každý odpovídající dokument před použitím libovolného filtru. Klauzule `WHERE` pak použije predikát filtru `<c, t, n, s>` na každou řazenou kolekce členů.

Například pokud odpovídající dokument obsahuje 10 položek v každé ze tří polí, rozbalí se na 1 x 10 x 10 x 10 (to znamená 1 000) řazené kolekce členů. Použití poddotazů zde může pomoci odfiltrovat spojené položky pole před připojením s dalším výrazem.

Tento dotaz je ekvivalentní předchozímu dotazu, ale používá poddotazy:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**Poplatek žP:** 22,17 RU

Předpokládejme, že pouze jedna položka v poli značky odpovídá filtru a že existuje pět položek pro živiny a porce pole. Výrazy se `JOIN` rozbalí na 1 x 1 x 5 x 5 = 25 položek, na rozdíl od 1 000 položek v prvním dotazu.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>Dotazy, u kterých je počet načtených dokumentů roven počtu výstupních dokladů

Pokud načtený počet dokumentů je přibližně rovna počet výstupnídokument, dotaz nemusel skenovat mnoho nepotřebných dokumentů. U mnoha dotazů, jako jsou ty, které používají klíčové slovo TOP, může počet načtených dokumentů překročit počet výstupních dokumentů o 1. Nemusíš se tím bát.

### <a name="avoid-cross-partition-queries"></a>Vyhněte se dotazům na příčný oddíl

Azure Cosmos DB používá [dělení](partitioning-overview.md) k škálování jednotlivých kontejnerů jako jednotky požadavků a potřeby úložiště dat zvýšit. Každý fyzický oddíl má samostatný a nezávislý index. Pokud má dotaz filtr rovnosti, který odpovídá klíči oddílu vašeho kontejneru, budete muset zkontrolovat pouze index příslušného oddílu. Tato optimalizace snižuje celkový počet ru, které dotaz vyžaduje.

Pokud máte velký počet zřízených ru (více než 30 000) nebo velké množství dat uložených (více než přibližně 100 GB), pravděpodobně máte dostatečně velký kontejner pro zobrazení významné snížení poplatků ru dotazu.

Pokud například vytvoříte kontejner se skupinou foodGroup klíče oddílu, budou muset následující dotazy zkontrolovat pouze jeden fyzický oddíl:

```sql
SELECT * FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

Tyto dotazy by také být optimalizovány přidáním klíče oddílu v dotazu:

```sql
SELECT * FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

Dotazy, které mají filtry rozsahu na klíčoddílu nebo které nemají žádné filtry na klíč oddílu, bude muset zkontrolovat každý fyzický oddíl index pro výsledky:

```sql
SELECT * FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT * FROM c
WHERE c.foodGroup > "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

### <a name="optimize-queries-that-have-filters-on-multiple-properties"></a>Optimalizace dotazů s filtry pro více vlastností

Přestože dotazy, které mají filtry na více vlastností bude obvykle používat index rozsahu, budou efektivnější, pokud mohou být obsluhovány z složenéindexu. Pro malé množství dat tato optimalizace nebude mít významný dopad. To by však mohlo být užitečné pro velké množství dat. Maximálně můžete optimalizovat jeden filtr bez rovnosti na složený index. Pokud dotaz obsahuje více filtrů bez rovnosti, vyberte jeden z nich, který bude používat složený index. Zbytek bude i nadále používat indexy rozsahu. Filtr nerovnosti musí být definován jako poslední ve složeném indexu. [Další informace o složených indexech](index-policy.md#composite-indexes).

Zde jsou některé příklady dotazů, které by mohly být optimalizovány pomocí složeného indexu:

```sql
SELECT * FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts = 1575503264
```

```sql
SELECT * FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts > 1575503264
```

Zde je příslušný složený index:

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

## <a name="optimizations-that-reduce-query-latency"></a>Optimalizace, které snižují latenci dotazu

V mnoha případech žP poplatek může být přijatelné, pokud latence dotazu je stále příliš vysoká. V následujících částech je uveden přehled tipů pro snížení latence dotazu. Pokud spustíte stejný dotaz vícekrát na stejné datové sady, bude mít stejné ru poplatek pokaždé. Ale latence dotazu se může lišit mezi spuštění dotazu.

### <a name="improve-proximity"></a>Zlepšení blízkosti

Dotazy, které jsou spuštěny z jiné oblasti než účet Azure Cosmos DB bude mít vyšší latenci, než kdyby byly spuštěny uvnitř stejné oblasti. Například pokud používáte kód na stolním počítači, měli byste očekávat, že latence bude o desítky nebo stovky milisekund vyšší (nebo více), než kdyby dotaz přišel z virtuálního počítače ve stejné oblasti Azure jako Azure Cosmos DB. Je to jednoduché [globálně distribuovat data v Azure Cosmos DB,](distribute-data-globally.md) abyste zajistili, že můžete data přiblížit vaší aplikaci.

### <a name="increase-provisioned-throughput"></a>Zvýšení zřízené propustnosti

V Azure Cosmos DB se zřízené propustnost měří v jednotkách požadavků (RU). Představte si, že máte dotaz, který spotřebovává 5 RU propustnost. Například pokud zřídíte 1 000 ru, bude možné spustit tento dotaz 200 krát za sekundu. Pokud jste se pokusili spustit dotaz, když nebyla dostatečná propustnost k dispozici, Azure Cosmos DB vrátí chybu HTTP 429. Všechny aktuální sady SDK rozhraní API jádra (SQL) budou tento dotaz po krátkém čekání automaticky opakovat. Omezené požadavky trvat déle, takže zvýšení zřízené propustnosti můžete zlepšit latenci dotazu. Můžete sledovat [celkový počet omezené požadavky](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors) na **metriky** okno portálu Azure.

### <a name="increase-maxconcurrency"></a>Zvýšit maximální měnu

Paralelní dotazy fungují dotazováním více oddílů paralelně. Ale data z jednotlivých dělené kolekce je načten sériově s ohledem na dotaz. Takže pokud nastavíte MaxConcurrency na počet oddílů, máte nejlepší šanci na dosažení nejvýkonnější dotaz, za předpokladu, že všechny ostatní systémové podmínky zůstávají stejné. Pokud neznáte počet oddílů, můžete nastavit MaxConcurrency (nebo MaxDegreesOfParallelism ve starších verzích sady SDK) na vysoké číslo. Systém zvolí minimální (počet oddílů, vstup zadaný uživatelem) jako maximální stupeň paralelismu.

### <a name="increase-maxbuffereditemcount"></a>Zvýšit počet maxbuffereditemcount

Dotazy jsou navrženy tak, aby předem načíst výsledky při aktuální dávka výsledků je zpracovávána klientem. Předběžné načtení pomáhá zlepšit celkovou latenci dotazu. Nastavení MaxBufferedItemCount omezuje počet výsledků přednačtením. Pokud nastavíte tuto hodnotu na očekávaný počet vrácených výsledků (nebo vyšší číslo), dotaz může získat největší užitek z předběžného načítání. Pokud nastavíte tuto hodnotu na -1, systém automaticky určí počet položek do vyrovnávací paměti.

## <a name="next-steps"></a>Další kroky
Informace o měření ru na dotaz, získání statistiky spuštění k vyladění dotazů a další informace o tom, jak měřit jednotky Ru na dotaz, získat statistiky spuštění a vyladit dotazy:

* [Získání metrik spuštění dotazu SQL pomocí sady .NET SDK](profile-sql-api-query.md)
* [Ladění výkonu dotazů pomocí služby Azure Cosmos DB](sql-api-sql-query-metrics.md)
* [Tipy ke zvýšení výkonu pro .NET SDK](performance-tips.md)
