---
title: Řešení potíží s dotazy při používání služby Azure Cosmos DB
description: Naučte se identifikovat, diagnostikovat a řešit potíže s Azure Cosmos DB problémy s dotazy SQL.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 02/16/2021
ms.author: tisande
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 6701a580cbe7790dcce2cbbcc46889f9dff00107
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100559981"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>Řešení potíží s dotazy při používání služby Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Tento článek vás provede obecným doporučeným přístupem k řešení potíží s dotazy v Azure Cosmos DB. I když byste neměli zvážit kroky popsané v tomto článku s ucelenou ochranou proti potenciálním problémům s dotazy, zahrnuli jsme sem nejběžnější tipy k výkonu. Tento článek byste měli použít jako výchozí bod při řešení potíží s pomalými nebo nákladnými dotazy v rozhraní Core (SQL) API služby Azure Cosmos DB. K identifikaci dotazů, které jsou pomalé nebo které spotřebovávají významnou část propustnosti, můžete použít také [diagnostické protokoly](cosmosdb-monitor-resource-logs.md). Pokud používáte rozhraní API Azure Cosmos DB pro MongoDB, měli byste použít [rozhraní Azure Cosmos DB API pro Průvodce odstraňováním potíží dotazů MongoDB](mongodb-troubleshoot-query.md) .

Optimalizace dotazů v Azure Cosmos DB jsou v podstatě zařazené do kategorií následujícím způsobem:

- Optimalizace, které omezují poplatek za jednotku žádosti (RU) na dotaz
- Optimalizace, které pouze omezují latenci

Pokud omezíte poplatek za dotaz, obvykle se sníží latence.

Tento článek popisuje příklady, které můžete znovu vytvořit pomocí [nutriční datové sady](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json).

## <a name="common-sdk-issues"></a>Běžné problémy sady SDK

Před čtením tohoto průvodce je užitečné vzít v úvahu běžné problémy sady SDK, které nesouvisí s dotazovacím modulem.

- Postupujte podle těchto [tipů pro výkon sady SDK](performance-tips.md).
    - [Průvodce odstraňováním potíží sady .NET SDK](troubleshoot-dot-net-sdk.md)
    - [Průvodce odstraňováním potíží se sadou Java SDK](troubleshoot-java-sdk-v4-sql.md)
- Sada SDK umožňuje nastavení `MaxItemCount` pro vaše dotazy, ale nemůžete zadat minimální počet položek.
    - Kód by měl zpracovat libovolnou velikost stránky od nuly do `MaxItemCount` .
- Někdy dotazy mohou mít prázdné stránky i v případě, že na budoucí stránce dojde k výsledkům. Možné příčiny:
    - Sada SDK by mohla provedla více síťových volání.
    - Načtení dokumentů může trvat dlouhou dobu.
- Všechny dotazy mají token pro pokračování, který umožní, aby dotaz pokračoval. Nezapomeňte dotaz úplně vyprázdnit. Další informace o [zpracování více stránek výsledků](sql-query-pagination.md#handling-multiple-pages-of-results)

## <a name="get-query-metrics"></a>Získat metriky dotazů

Když v Azure Cosmos DB optimalizujete dotaz, prvním krokem je vždycky [získat metriky dotazu](profile-sql-api-query.md) pro dotaz. Tyto metriky jsou také k dispozici prostřednictvím Azure Portal. Po spuštění dotazu v Průzkumník dat jsou metriky dotazů zobrazené vedle karty **výsledky** :

:::image type="content" source="./media/troubleshoot-query-performance/obtain-query-metrics.png" alt-text="Získávání metrik dotazů" lightbox="./media/troubleshoot-query-performance/obtain-query-metrics.png":::

Po získání metriky dotazu Porovnejte **počet načtených dokumentů** s **počtem výstupních dokumentů** pro váš dotaz. Pomocí tohoto porovnání Identifikujte relevantní oddíly, které je potřeba si projít v tomto článku.

**Počet načtených dokumentů** je počet dokumentů, které dotazovací stroj potřebuje k načtení. **Počet výstupních dokumentů** je počet dokumentů, které byly potřeba pro výsledky dotazu. Pokud je **počet načtených dokumentů** výrazně vyšší než **Počet výstupních dokumentů**, existovala aspoň jedna část dotazu, která nedokázala použít index a aby se provedla kontrola.

V následujících částech najdete informace o relevantních optimalizaci dotazů pro váš scénář.

### <a name="querys-ru-charge-is-too-high"></a>Poplatek za RU v dotazu je příliš vysoký.

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>Počet načtených dokumentů je výrazně vyšší než počet výstupních dokumentů.

- [Do zásad indexování zahrňte potřebné cesty.](#include-necessary-paths-in-the-indexing-policy)

- [Zjistěte, které systémové funkce používají index.](#understand-which-system-functions-use-the-index)

- [Vylepšete provádění řetězcové funkce systému.](#improve-string-system-function-execution)

- [Pochopení, které agregační dotazy používají index.](#understand-which-aggregate-queries-use-the-index)

- [Optimalizujte dotazy, které mají klauzuli Filter i ORDER BY.](#optimize-queries-that-have-both-a-filter-and-an-order-by-clause)

- [Optimalizujte výrazy JOIN pomocí poddotazu.](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>Počet načtených dokumentů je přibližně roven počtu výstupních dokumentů.

- [Minimalizujte dotazy mezi oddíly.](#minimize-cross-partition-queries)

- [Optimalizujte dotazy, které mají filtry na více vlastností.](#optimize-queries-that-have-filters-on-multiple-properties)

- [Optimalizujte dotazy, které mají klauzuli Filter i ORDER BY.](#optimize-queries-that-have-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>Náklady na dotaz na RU jsou přijatelné, ale latence je stále příliš vysoká.

- [Zlepšení blízkosti.](#improve-proximity)

- [Zvyšte zřízenou propustnost.](#increase-provisioned-throughput)

- [Zvyšte MaxConcurrency.](#increase-maxconcurrency)

- [Zvyšte MaxBufferedItemCount.](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>Dotazy, kde počet načtených dokumentů překračuje počet výstupních dokumentů

 **Počet načtených dokumentů** je počet dokumentů, které dotazovací stroj potřebuje k načtení. **Počet výstupních dokumentů** je počet dokumentů vrácených dotazem. Pokud je **počet načtených dokumentů** výrazně vyšší než **Počet výstupních dokumentů**, existovala aspoň jedna část dotazu, která nedokázala použít index a aby se provedla kontrola.

Tady je příklad skenovacího dotazu, který nebyl zcela obsluhován indexem:

Dotaz:

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

**Načtený počet dokumentů** (60 951) je podstatně vyšší než **Počet výstupních dokumentů** (7), což znamená, že tento dotaz vedlo k prověřování dokumentu. V tomto případě systémová funkce [Upper ()](sql-query-upper.md) nepoužívá index.

### <a name="include-necessary-paths-in-the-indexing-policy"></a>Zahrnutí potřebných cest do zásad indexování

Vaše zásada indexování by měla zahrnovat jakékoli vlastnosti zahrnuté v `WHERE` klauzulích, `ORDER BY` klauzulích `JOIN` a většině systémových funkcí. Požadované cesty zadané v zásadách indexu by měly odpovídat vlastnostem v dokumentech JSON.

> [!NOTE]
> Vlastnosti v zásadách indexování Azure Cosmos DB rozlišují velká a malá písmena.

Pokud spustíte následující jednoduchý dotaz na [nutriční](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) datové sadě, budete při indexování klauzule v klauzuli pozorovat mnohem nižší poplatky za ru `WHERE` :

#### <a name="original"></a>Původní

Dotaz:

```sql
SELECT *
FROM c
WHERE c.description = "Malabar spinach, cooked"
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

#### <a name="optimized"></a>Optimalizované

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

Můžete kdykoli přidat vlastnosti k zásadě indexování bez vlivu na zápis nebo čtení. [Průběh transformace indexu můžete sledovat](./how-to-manage-indexing-policy.md#dotnet-sdk).

### <a name="understand-which-system-functions-use-the-index"></a>Pochopení, které systémové funkce používají index

Většina systémových funkcí používá indexy. Tady je seznam některých běžných řetězcových funkcí, které používají indexy:

- StartsWith
- Contains
- RegexMatch
- Left
- Dílčí řetězec – ale pouze v případě, že první num_expr je 0

Níže jsou uvedeny některé běžné systémové funkce, které nepoužívají index a musí při použití v klauzuli načíst každý dokument `WHERE` :

| **Systémová funkce**                     | **Nápady pro optimalizaci**             |
| --------------------------------------- |------------------------------------------------------------ |
| Horní/dolní                         | Namísto použití systémové funkce k normalizování dat pro porovnání, Normalizujte při vložení velká a malá písmena. Dotaz, jako ```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'``` se má ```SELECT * FROM c WHERE c.name = 'BOB'``` . |
| GetCurrentDateTime/GetCurrentTimestamp/GetCurrentTicks | Vypočítá aktuální čas před provedením dotazu a použije tuto řetězcovou hodnotu v `WHERE` klauzuli. |
| Matematické funkce (neagregace) | Pokud v dotazu potřebujete často vypočítat hodnotu, zvažte uložení hodnoty jako vlastnosti v dokumentu JSON. |

Tyto systémové funkce mohou používat indexy s výjimkou případů, kdy se používá v dotazech s agregacemi:

| **Systémová funkce**                     | **Nápady pro optimalizaci**             |
| --------------------------------------- |------------------------------------------------------------ |
| Funkce prostorového systému                        | Uložení výsledků dotazu do materializované zobrazení v reálném čase |

Při použití v `SELECT` klauzuli neefektivní systémové funkce nebudou mít vliv na to, jak dotazy mohou používat indexy.

### <a name="improve-string-system-function-execution"></a>Zlepšení provádění řetězcové funkce systému

U některých systémových funkcí, které používají indexy, můžete vylepšit spouštění dotazů přidáním `ORDER BY` klauzule do dotazu. 

Přesněji řečeno, jakákoli systémová funkce, jejíž poplatek za RU se zvyšuje, protože mohutnost vlastnosti se zvyšuje, může být výhodné `ORDER BY` v dotazu. Tyto dotazy provedou prohledávání indexů, takže výsledkem řazení výsledků dotazu může být dotaz efektivnější.

Tato optimalizace může zlepšit spuštění následujících systémových funkcí:

- StartsWith (kde nerozlišuje velká a malá písmena = true)
- StringEquals (kde nerozlišuje velká a malá písmena = true)
- Contains
- RegexMatch
- EndsWith

Například zvažte následující dotaz s `CONTAINS` . `CONTAINS` bude používat indexy, ale v některých případech i po přidání relevantního indexu, můžete při spuštění níže uvedeného dotazu stále sledovat velmi vysoký poplatek.

Původní dotaz:

```sql
SELECT *
FROM c
WHERE CONTAINS(c.town, "Sea")
```

Můžete zlepšit spouštění dotazů přidáním `ORDER BY` :

```sql
SELECT *
FROM c
WHERE CONTAINS(c.town, "Sea")
ORDER BY c.town
```

Stejná optimalizace vám může pomáhat v dotazech s dalšími filtry. V takovém případě je nejlepší také přidat vlastnosti s filtry rovnosti do `ORDER BY` klauzule.

Původní dotaz:

```sql
SELECT *
FROM c
WHERE c.name = "Samer" AND CONTAINS(c.town, "Sea")
```

Můžete zlepšit spouštění dotazů přidáním `ORDER BY` a [složeného indexu](index-policy.md#composite-indexes) pro (c.Name, c. město):

```sql
SELECT *
FROM c
WHERE c.name = "Samer" AND CONTAINS(c.town, "Sea")
ORDER BY c.name, c.town
```

### <a name="understand-which-aggregate-queries-use-the-index"></a>Vysvětlení, které agregační dotazy používají index

Ve většině případů agregační funkce systému v Azure Cosmos DB použijí index. V závislosti na filtrech nebo dalších klauzulích v agregačních dotazech však může být dotazovací modul nutný k načtení vysokého počtu dokumentů. V dotazovacím modulu se obvykle nejprve použijí filtry rovnosti a rozsah. Po použití těchto filtrů může dotazovací modul vyhodnotit další filtry a použít k načtení zbývajících dokumentů pro výpočet agregace v případě potřeby.

Například vzhledem k těmto dvěma ukázkovým dotazům bude dotaz s filtrem rovnosti a `CONTAINS` funkcí filtru systému obecně efektivnější než dotaz pouze s `CONTAINS` filtrem systémové funkce. Důvodem je, že je použit filtr rovnosti, který používá index před tím, než bude nutné načíst dokumenty pro dražší `CONTAINS` Filtr.

Dotaz s pouze `CONTAINS` filtrem s vyššími náklady na ru:

```sql
SELECT COUNT(1)
FROM c
WHERE CONTAINS(c.description, "spinach")
```

Dotaz pomocí filtru rovnosti a `CONTAINS` filtru – poplatek za nižší úrovně ru:

```sql
SELECT AVG(c._ts)
FROM c
WHERE c.foodGroup = "Sausages and Luncheon Meats" AND CONTAINS(c.description, "spinach")
```

Tady jsou další příklady agregačních dotazů, které neúplný index nepoužívají:

#### <a name="queries-with-system-functions-that-dont-use-the-index"></a>Dotazy se systémovými funkcemi, které nepoužívají index

Měli byste se podívat na [stránku příslušné systémové funkce](sql-query-system-functions.md) a zjistit, jestli používá index.

```sql
SELECT MAX(c._ts)
FROM c
WHERE CONTAINS(c.description, "spinach")
```

#### <a name="aggregate-queries-with-user-defined-functionsudfs"></a>Agregační dotazy s uživatelsky definovanými funkcemi (UDF)

```sql
SELECT AVG(c._ts)
FROM c
WHERE udf.MyUDF("Sausages and Luncheon Meats")
```

#### <a name="queries-with-group-by"></a>Dotazy pomocí GROUP BY

Náklady na RU v dotazech, které se budou zvyšovat, se zvýší `GROUP BY` mohutnost vlastností v `GROUP BY` klauzuli. V níže uvedeném dotazu se například poplatek za RU v dotazu zvýší, protože se zvýší počet jedinečných popisů.

Poplatek za částku v agregační funkci s `GROUP BY` klauzulí bude vyšší než poplatek za agregační funkci samotnou. V tomto příkladu musí dotazovací modul načítat každý dokument, který odpovídá `c.foodGroup = "Sausages and Luncheon Meats"` filtru, aby poplatek za ru byl vysoký.

```sql
SELECT COUNT(1)
FROM c
WHERE c.foodGroup = "Sausages and Luncheon Meats"
GROUP BY c.description
```

Pokud máte v plánu často spouštět stejné agregované dotazy, může být efektivnější vytvořit materializované zobrazení v reálném čase s [Azure Cosmos DBmi změnami](change-feed.md) , než se spouštějí jednotlivé dotazy.

### <a name="optimize-queries-that-have-both-a-filter-and-an-order-by-clause"></a>Optimalizujte dotazy, které mají klauzuli Filter i ORDER BY.

I když dotazy, které obsahují klauzuli Filter a `ORDER BY` klauzule, budou normálně používat index rozsahu, budou efektivnější, pokud je lze zpracovat ze složeného indexu. Kromě změny zásad indexování byste měli do klauzule přidat všechny vlastnosti složeného indexu `ORDER BY` . Tato změna dotazu zajistí, že se použije složený index.  Můžete sledovat dopad spuštěním dotazu na [nutriční](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) datovou sadu:

#### <a name="original"></a>Původní

Dotaz:

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c._ts ASC
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

#### <a name="optimized"></a>Optimalizované

Aktualizovaný dotaz (zahrnuje obě vlastnosti v `ORDER BY` klauzuli):

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
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

### <a name="optimize-join-expressions-by-using-a-subquery"></a>Optimalizujte výrazy JOIN pomocí poddotazu.

Podhodnoty poddotazů mohou optimalizovat `JOIN` výrazy vložením predikátů za každý výraz SELECT-many místo po všech křížových spojeních v `WHERE` klauzuli.

Vezměte v úvahu tento dotaz:

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

Pro tento dotaz bude index odpovídat jakémukoli dokumentu, který má značku s názvem `infant formula` , `nutritionValue` větší než 0 a `amount` větší než 1. Tento `JOIN` výraz provede více produktů všech položek značek, živin a zachová pole pro každý shodný dokument před použitím jakéhokoli filtru. `WHERE`Klauzule pak použije predikát filtru u každé `<c, t, n, s>` řazené kolekce členů.

Pokud má například odpovídající dokument 10 položek v každém ze tří polí, rozšíří se na 1 x 10 x 10 x 10 (tj. 1 000) řazené kolekce členů. Použití poddotazů tady může přispět k filtrování propojených položek pole před připojením k dalšímu výrazu.

Tento dotaz je ekvivalentní předchozímu, ale používá poddotazy:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**Poplatek za ru:** 22,17 ru

Předpokládejte, že pouze jedna položka v poli značek odpovídá filtru a že existuje pět položek pro živiny a pole. `JOIN`Výrazy se rozšíří na 1 x 1 x 5 × 5 = 25 položek, a to na rozdíl od 1 000 položek v prvním dotazu.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>Dotazy, kde se načtený počet dokumentů rovná počtu výstupních dokumentů

Pokud je **počet načtených dokumentů** přibližně roven **výstupnímu počtu dokumentů**, stroj dotazů nemusel kontrolovat mnoho zbytečných dokumentů. U mnoha dotazů, například u těch, které používají `TOP` klíčové slovo, může **počet načtených dokumentů** překročit **Počet výstupních dokumentů** o 1. Nemusíte se k tomu zabývat.

### <a name="minimize-cross-partition-queries"></a>Minimalizace dotazů mezi oddíly

Azure Cosmos DB používá [dělení](partitioning-overview.md) ke škálování jednotlivých kontejnerů v případě, že se vyžadují jednotky žádosti a úložiště dat. Každý fyzický oddíl má samostatný a nezávislý index. Pokud má váš dotaz filtr rovnosti, který odpovídá klíči oddílu kontejneru, bude nutné zaškrtnout pouze index relevantního oddílu. Tato optimalizace snižuje celkový počet ru, které dotaz vyžaduje.

Pokud máte velký počet zřízených ru (více než 30 000) nebo velký objem uložených dat (více než přibližně 100 GB), je pravděpodobné, že máte dostatečně velký kontejner, abyste viděli výrazné snížení nákladů na dotaz RU.

Pokud například vytvoříte kontejner s klíčovým adaptérem klíče oddílu, bude nutné, aby následující dotazy kontrolovaly pouze jeden fyzický oddíl:

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

Dotazy, které mají `IN` Filtr s klíčem oddílu, kontrolují pouze relevantní fyzické oddíly a nebudou "ventilátor-out":

```sql
SELECT *
FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

Dotazy, které mají filtry rozsahu na klíč oddílu, nebo které nemají žádné filtry pro klíč oddílu, budou muset "ventilátor-out" a kontrolovat výsledky každého fyzického oddílu na základě indexu:

```sql
SELECT *
FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT *
FROM c
WHERE c.foodGroup > "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

### <a name="optimize-queries-that-have-filters-on-multiple-properties"></a>Optimalizace dotazů, které mají filtry na více vlastností

I když dotazy, které mají filtry na více vlastností, budou normálně používat index rozsahu, budou efektivnější, pokud je lze zpracovat ze složeného indexu. U malých objemů dat tato optimalizace nemá významný dopad. U velkých objemů dat to však může být užitečné. V rámci složeného indexu můžete optimalizovat jenom jeden filtr nerovnosti. Pokud má váš dotaz více filtrů bez rovnosti, vyberte jeden z nich, který bude používat složený index. Zbytek bude dál používat indexy rozsahu. Filtr bez rovnosti musí být v složeném indexu definován jako poslední. [Přečtěte si další informace o složených indexech](index-policy.md#composite-indexes).

Tady jsou některé příklady dotazů, které by mohly být optimalizované pomocí složeného indexu:

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts = 1575503264
```

```sql
SELECT *
FROM c
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

## <a name="optimizations-that-reduce-query-latency"></a>Optimalizace, které snižují latenci dotazů

V mnoha případech může být poplatek za RU přijatelný, pokud je latence dotazů pořád příliš vysoká. Následující části obsahují přehled tipů pro snížení latence dotazů. Pokud stejný dotaz spouštíte několikrát pro stejnou datovou sadu, bude to mít obvykle za každou dobu stejnou částku RU. Latence dotazů se ale může lišit mezi provedeními dotazu.

### <a name="improve-proximity"></a>Zlepšení blízkosti

Dotazy, které jsou spouštěny z jiné oblasti než Azure Cosmos DB účtu, budou mít vyšší latenci, než kdyby byly spuštěny ve stejné oblasti. Pokud například spouštíte kód na stolním počítači, měli byste očekávat, že latence bude desítkově nebo stovky milisekund vyšší (nebo více), než kdyby dotaz nacházel z virtuálního počítače ve stejné oblasti Azure jako Azure Cosmos DB. [Globální distribuce dat v Azure Cosmos DB](distribute-data-globally.md) je jednoduchá, aby bylo zajištěno, že vaše data budou blíž do vaší aplikace.

### <a name="increase-provisioned-throughput"></a>Zvýšení zřízené propustnosti

V Azure Cosmos DB se zřízená propustnost měří v jednotkách žádosti (ru). Představte si, že máte dotaz, který spotřebovává 5 ru propustnosti. Pokud například zřídíte 1 000 ru, budete moct spustit tento dotaz 200 krát za sekundu. Pokud jste se pokusili spustit dotaz, když není k dispozici dostatek propustnosti, Azure Cosmos DB by vrátil chybu HTTP 429. Jakákoli ze současných sad SDK rozhraní API jádra (SQL) Tento dotaz automaticky zopakuje po krátké době. Omezené žádosti trvá déle, takže zvýšení zajištěné propustnosti může zlepšit latenci dotazů. [Celkový počet omezených požadavků](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors) můžete sledovat v okně **metriky** Azure Portal.

### <a name="increase-maxconcurrency"></a>Zvýšit MaxConcurrency

Paralelní dotazy fungují paralelně dotazování na více oddílů. Data z jednotlivých dělených kolekcí se ale v souvislosti s dotazem načítají sériově. Takže pokud nastavíte MaxConcurrency na počet oddílů, budete mít největší šanci na to, aby se dosáhlo nejvíce výkonného dotazu. za předpokladu, že všechny ostatní systémové podmínky zůstanou stejné. Pokud neznáte počet oddílů, můžete nastavit MaxConcurrency (nebo MaxDegreesOfParallelism ve starších verzích sady SDK) na vysoké číslo. Systém vybere minimální (počet oddílů, zadání uživatelem zadaných) jako maximální stupeň paralelismu.

### <a name="increase-maxbuffereditemcount"></a>Zvýšit MaxBufferedItemCount

Dotazy jsou navržené tak, aby výsledky byly předem načteny, zatímco aktuální dávka výsledků je zpracovávána klientem. Předběžné načítání pomáhá zlepšit celkovou latenci dotazu. Nastavení MaxBufferedItemCount omezuje počet předběžně načtených výsledků. Pokud nastavíte tuto hodnotu na očekávaný počet vrácených výsledků (nebo vyšší číslo), dotaz může získat největší užitek z předběžného načítání. Pokud tuto hodnotu nastavíte na hodnotu-1, systém automaticky určí počet položek, které se mají ukládat do vyrovnávací paměti.

## <a name="next-steps"></a>Další kroky
V následujících článcích najdete informace o tom, jak měřit ru na dotaz, získat statistiku spuštění pro optimalizaci vašich dotazů a další:

* [Získat metriky spouštění dotazů SQL pomocí sady .NET SDK](profile-sql-api-query.md)
* [Ladění výkonu dotazů pomocí služby Azure Cosmos DB](./sql-api-query-metrics.md)
* [Tipy ke zvýšení výkonu pro .NET SDK](performance-tips.md)
* [Tipy ke zvýšení výkonu pro Java v4 SDK](performance-tips-java-sdk-v4-sql.md)