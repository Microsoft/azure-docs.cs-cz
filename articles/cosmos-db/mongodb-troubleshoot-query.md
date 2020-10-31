---
title: Řešení potíží s dotazy při použití rozhraní Azure Cosmos DB API pro MongoDB
description: Naučte se identifikovat, diagnostikovat a řešit potíže s rozhraním API Azure Cosmos DB pro problémy s dotazy MongoDB.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 10/12/2020
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: ece814ea316fe62bc2b8b64036b08983f65ccbef
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096355"
---
# <a name="troubleshoot-query-issues-when-using-the-azure-cosmos-db-api-for-mongodb"></a>Řešení potíží s dotazy při použití rozhraní Azure Cosmos DB API pro MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Tento článek vás provede obecným doporučeným přístupem k řešení potíží s dotazy v Azure Cosmos DB. I když byste neměli zvážit kroky popsané v tomto článku s ucelenou ochranou proti potenciálním problémům s dotazy, zahrnuli jsme sem nejběžnější tipy k výkonu. Tento článek byste měli použít jako počáteční místo pro řešení potíží s pomalými nebo nákladnými dotazy v rozhraní API Azure Cosmos DB pro MongoDB. Pokud používáte rozhraní API pro Azure Cosmos DB Core (SQL), přečtěte si článek [Průvodce řešením potíží s dotazem rozhraní SQL API](troubleshoot-query-performance.md) .

Optimalizace dotazů v Azure Cosmos DB jsou v podstatě zařazené do kategorií následujícím způsobem:

- Optimalizace, které omezují poplatek za jednotku žádosti (RU) na dotaz
- Optimalizace, které pouze omezují latenci

Pokud omezíte poplatek za dotaz, obvykle se sníží latence.

Tento článek popisuje příklady, které můžete znovu vytvořit pomocí [nutriční datové sady](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json).

> [!NOTE] 
> V tomto článku se předpokládá, že používáte rozhraní API verze 3,6 Azure Cosmos DB s pro MongoDB. Některé dotazy, které nesplní ve verzi 3,2, mají významná vylepšení verze 3,6. Proveďte upgrade na verzi 3,6 tím, že [požádáte žádost o podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="use-explain-command-to-get-metrics"></a>Použití příkazu $explain k získání metrik

Když v Azure Cosmos DB optimalizujete dotaz, první krok vždy [získá poplatek za ru](find-request-unit-charge-mongodb.md) za váš dotaz. V rámci hrubých pokynů byste měli prozkoumat způsoby, jak snížit poplatek za dotazy s náklady většími než 50 ru. 

Kromě získání poplatků za RU byste měli pomocí `$explain` příkazu získat metriky využití dotazů a indexů. Tady je příklad, který spouští dotaz a pomocí `$explain` příkazu zobrazuje metriky využití dotazů a indexů:

**$explain příkaz:**

```
db.coll.find({foodGroup: "Baby Foods"}).explain({"executionStatistics": true })
```

**Výkonem**

```json
{
    "stages" : [ 
        {
            "stage" : "$query",
            "timeInclusiveMS" : 905.2888,
            "timeExclusiveMS" : 905.2888,
            "in" : 362,
            "out" : 362,
            "details" : {
                "database" : "db-test",
                "collection" : "collection-test",
                "query" : {
                    "foodGroup" : {
                        "$eq" : "Baby Foods"
                    }
                },
                "pathsIndexed" : [],
                "pathsNotIndexed" : [ 
                    "foodGroup"
                ],
                "shardInformation" : [ 
                    {
                        "activityId" : "e68e6bdd-5e89-4ec5-b053-3dbbc2428140",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 788.5867,
                        "preemptions" : 1,
                        "outputDocumentCount" : 362,
                        "retrievedDocumentCount" : 8618
                    }
                ],
                "queryMetrics" : {
                    "retrievedDocumentCount" : 8618,
                    "retrievedDocumentSizeBytes" : 104963042,
                    "outputDocumentCount" : 362,
                    "outputDocumentSizeBytes" : 2553535,
                    "indexHitRatio" : 0.0016802042237178,
                    "totalQueryExecutionTimeMS" : 777.72,
                    "queryPreparationTimes" : {
                        "queryCompilationTimeMS" : 0.19,
                        "logicalPlanBuildTimeMS" : 0.14,
                        "physicalPlanBuildTimeMS" : 0.09,
                        "queryOptimizationTimeMS" : 0.03
                    },
                    "indexLookupTimeMS" : 0,
                    "documentLoadTimeMS" : 687.22,
                    "vmExecutionTimeMS" : 774.09,
                    "runtimeExecutionTimes" : {
                        "queryEngineExecutionTimeMS" : 37.45,
                        "systemFunctionExecutionTimeMS" : 10.82,
                        "userDefinedFunctionExecutionTimeMS" : 0
                    },
                    "documentWriteTimeMS" : 49.42
                }
            }
        }
    ],
    "estimatedDelayFromRateLimitingInMilliseconds" : 0.0,
    "continuation" : {
        "hasMore" : false
    },
    "ok" : 1.0
}
```

`$explain`Výstup příkazu má délku a obsahuje podrobné informace o spuštění dotazu. Obecně platí, že existuje několik oddílů, kde byste se měli zaměřit na optimalizaci výkonu dotazů:

| Metrika | Popis | 
| ------ | ----------- |
| `timeInclusiveMS` | Latence dotazů back-endu |
| `pathsIndexed` | Zobrazí indexy používané dotazem. | 
| `pathsNotIndexed` | Zobrazí indexy, které mohl dotaz použít, pokud je k dispozici. | 
| `shardInformation` | Souhrn výkonu dotazů pro určitý [fyzický oddíl](./partitioning-overview.md#physical-partitions) | 
| `retrievedDocumentCount` | Počet dokumentů načtených dotazovacím modulem | 
| `outputDocumentCount` | Počet dokumentů vrácených ve výsledcích dotazu | 
| `estimatedDelayFromRateLimitingInMilliseconds` | Odhad další latence dotazů z důvodu omezení četnosti | 

Po získání metriky dotazu Porovnejte `retrievedDocumentCount` s `outputDocumentCount` pro dotaz. Pomocí tohoto porovnání Identifikujte relevantní oddíly, které je potřeba si projít v tomto článku. `retrievedDocumentCount`Je počet dokumentů, které musí modul pro dotazy načíst. `outputDocumentCount`Je počet dokumentů, které byly potřeba pro výsledky dotazu. Pokud `retrievedDocumentCount`  je výrazně vyšší než `outputDocumentCount` , existovala aspoň jedna část dotazu, která nedokázala použít index a aby se provedla kontrola.

V následujících částech najdete informace o relevantních optimalizaci dotazů pro váš scénář.

### <a name="querys-ru-charge-is-too-high"></a>Poplatek za RU v dotazu je příliš vysoký.

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>Počet načtených dokumentů je výrazně vyšší než počet výstupních dokumentů.

- [Zahrňte potřebné indexy.](#include-necessary-indexes)

- [Zjistěte, které agregační operace používají index.](#understand-which-aggregation-operations-use-the-index)

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>Počet načtených dokumentů je přibližně roven počtu výstupních dokumentů.

- [Minimalizujte dotazy mezi oddíly.](#minimize-cross-partition-queries)

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>Náklady na dotaz na RU jsou přijatelné, ale latence je stále příliš vysoká.

- [Zlepšení blízkosti.](#improve-proximity)

- [Zvyšte zřízenou propustnost.](#increase-provisioned-throughput)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>Dotazy, kde počet načtených dokumentů překračuje počet výstupních dokumentů

 `retrievedDocumentCount`Je počet dokumentů, které dotazovací modul potřebuje k načtení. `outputDocumentCount`Je počet dokumentů vrácených dotazem. Pokud `retrievedDocumentCount` je výrazně vyšší než `outputDocumentCount` , existovala aspoň jedna část dotazu, která nedokázala použít index a aby se provedla kontrola.

Tady je příklad skenovacího dotazu, který nebyl zcela obsluhován indexem:

**$explain příkaz:**

```
db.coll.find(
  {
    $and : [
            { "foodGroup" : "Cereal Grains and Pasta"}, 
            { "description" : "Oat bran, cooked"}
        ]
  }
).explain({"executionStatistics": true })
```

**Výkonem**

```json
{
    "stages" : [ 
        {
            "stage" : "$query",
            "timeInclusiveMS" : 436.5716,
            "timeExclusiveMS" : 436.5716,
            "in" : 1,
            "out" : 1,
            "details" : {
                "database" : "db-test",
                "collection" : "indexing-test",
                "query" : {
                    "$and" : [ 
                        {
                            "foodGroup" : {
                                "$eq" : "Cereal Grains and Pasta"
                            }
                        }, 
                        {
                            "description" : {
                                "$eq" : "Oat bran, cooked"
                            }
                        }
                    ]
                },
                "pathsIndexed" : [],
                "pathsNotIndexed" : [ 
                    "foodGroup", 
                    "description"
                ],
                "shardInformation" : [ 
                    {
                        "activityId" : "13a5977e-a10a-4329-b68e-87e4f0081cac",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 435.4867,
                        "preemptions" : 1,
                        "outputDocumentCount" : 1,
                        "retrievedDocumentCount" : 8618
                    }
                ],
                "queryMetrics" : {
                    "retrievedDocumentCount" : 8618,
                    "retrievedDocumentSizeBytes" : 104963042,
                    "outputDocumentCount" : 1,
                    "outputDocumentSizeBytes" : 6064,
                    "indexHitRatio" : 0.0,
                    "totalQueryExecutionTimeMS" : 433.64,
                    "queryPreparationTimes" : {
                        "queryCompilationTimeMS" : 0.12,
                        "logicalPlanBuildTimeMS" : 0.09,
                        "physicalPlanBuildTimeMS" : 0.1,
                        "queryOptimizationTimeMS" : 0.02
                    },
                    "indexLookupTimeMS" : 0,
                    "documentLoadTimeMS" : 387.44,
                    "vmExecutionTimeMS" : 432.93,
                    "runtimeExecutionTimes" : {
                        "queryEngineExecutionTimeMS" : 45.36,
                        "systemFunctionExecutionTimeMS" : 16.86,
                        "userDefinedFunctionExecutionTimeMS" : 0
                    },
                    "documentWriteTimeMS" : 0.13
                }
            }
        }
    ],
    "estimatedDelayFromRateLimitingInMilliseconds" : 0.0,
    "continuation" : {
        "hasMore" : false
    },
    "ok" : 1.0
}
```

`retrievedDocumentCount`(8618) je podstatně vyšší než `outputDocumentCount` (1), což znamená, že tento dotaz vyžaduje skenování dokumentu. 

### <a name="include-necessary-indexes"></a>Zahrnutí potřebných indexů

Měli byste zaškrtnout `pathsNotIndexed` pole a přidat tyto indexy. V tomto příkladu jsou cesty `foodGroup` a `description` měly by být indexovány.

```json
"pathsNotIndexed" : [ 
                    "foodGroup", 
                    "description"
                ]
```

Indexování osvědčených postupů v rozhraní Azure Cosmos DB API pro MongoDB se liší od MongoDB. V rozhraní API Azure Cosmos DB pro MongoDB jsou složené indexy používány pouze v dotazech, které potřebují efektivně řadit podle více vlastností. Pokud máte dotazy s filtry pro více vlastností, měli byste pro každou z těchto vlastností vytvořit indexy jednoho pole. Predikáty dotazů můžou používat víc indexů jednoho pole.

[Indexy zástupných znaků](mongodb-indexing.md#wildcard-indexes) můžou zjednodušit indexování. Na rozdíl od MongoDB můžou indexy zástupných znaků podporovat více polí v predikátech dotazů. Pokud použijete jeden index zástupného znaku namísto vytvoření samostatného indexu pro každou vlastnost, nebudete mít rozdíl ve výkonu dotazů. Přidání indexu se zástupnými znaky pro všechny vlastnosti představuje nejjednodušší způsob, jak optimalizovat všechny vaše dotazy.

Nové indexy můžete kdykoli přidat, aniž by to mělo vliv na zápis nebo čtení. [Průběh transformace indexu můžete sledovat](./how-to-manage-indexing-policy.md#dotnet-sdk).

### <a name="understand-which-aggregation-operations-use-the-index"></a>Informace o tom, které agregační operace používají index

Ve většině případů agregační operace v rozhraní Azure Cosmos DB API pro MongoDB budou částečně používat indexy. V dotazovacím modulu se obvykle použijí filtry rovnosti a rozsahu a použijí se indexy. Po použití těchto filtrů může dotazovací modul vyhodnotit další filtry a použít k načtení zbývajících dokumentů pro výpočet agregace v případě potřeby. 

Tady je příklad:

```
db.coll.aggregate( [
   { $match: { foodGroup: 'Fruits and Fruit Juices' } },
   {
     $group: {
        _id: "$foodGroup",
        total: { $max: "$version" }
     }
   }
] )
```

V tomto případě mohou indexy optimalizovat `$match` fázi. Přidání indexu pro `foodGroup` bude významně zlepšit výkon dotazů. Podobně jako v MongoDB byste měli mít `$match` k dispozici co nejdříve v rámci agregovaného kanálu agregace, aby bylo možné maximalizovat využití indexů.

V rozhraní Azure Cosmos DB API pro MongoDB se indexy nepoužívají pro skutečnou agregaci, která v tomto případě je `$max` . Přidáním indexu `version` se nezvýší výkon dotazů.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>Dotazy, kde se načtený počet dokumentů rovná počtu výstupních dokumentů

Pokud `retrievedDocumentCount` je přibližně rovno `outputDocumentCount` , dotazovací stroj nemusel kontrolovat mnoho zbytečných dokumentů.

### <a name="minimize-cross-partition-queries"></a>Minimalizace dotazů mezi oddíly

Azure Cosmos DB používá [dělení](partitioning-overview.md) ke škálování jednotlivých kontejnerů v případě, že se vyžadují jednotky žádosti a úložiště dat. Každý fyzický oddíl má samostatný a nezávislý index. Pokud má váš dotaz filtr rovnosti, který odpovídá klíči oddílu kontejneru, bude nutné zaškrtnout pouze index relevantního oddílu. Tato optimalizace snižuje celkový počet ru, které dotaz vyžaduje. [Přečtěte si další informace o rozdílech mezi dotazy v rámci oddílu a dotazy mezi oddíly](how-to-query-container.md).

Pokud máte velký počet zřízených ru (více než 30 000) nebo velký objem uložených dat (více než přibližně 100 GB), je pravděpodobné, že máte dostatečně velký kontejner, abyste viděli výrazné snížení nákladů na dotaz RU. 

Můžete zaškrtnout pole `shardInformation` , abyste pochopili metriky dotazů pro jednotlivé fyzické oddíly. Počet jedinečných `shardKeyRangeId` hodnot je počet fyzických oddílů, ve kterých je nutné dotaz provést. V tomto příkladu byl dotaz proveden na čtyřech fyzických oddílech. Je důležité pochopit, že provádění je zcela nezávislé na využití indexu. Jinými slovy, dotazy mezi oddíly můžou pořád používat indexy.

```json
  "shardInformation" : [ 
                    {
                        "activityId" : "42f670a8-a201-4c58-8023-363ac18d9e18",
                        "shardKeyRangeId" : "5",
                        "durationMS" : 24.3859,
                        "preemptions" : 1,
                        "outputDocumentCount" : 463,
                        "retrievedDocumentCount" : 463
                    }, 
                    {
                        "activityId" : "a8bf762a-37b9-4c07-8ed4-ae49961373c0",
                        "shardKeyRangeId" : "2",
                        "durationMS" : 35.8328,
                        "preemptions" : 1,
                        "outputDocumentCount" : 905,
                        "retrievedDocumentCount" : 905
                    }, 
                    {
                        "activityId" : "3754e36b-4258-49a6-8d4d-010555628395",
                        "shardKeyRangeId" : "1",
                        "durationMS" : 67.3969,
                        "preemptions" : 1,
                        "outputDocumentCount" : 1479,
                        "retrievedDocumentCount" : 1479
                    }, 
                    {
                        "activityId" : "a69a44ee-db97-4fe9-b489-3791f3d52878",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 185.1523,
                        "preemptions" : 1,
                        "outputDocumentCount" : 867,
                        "retrievedDocumentCount" : 867
                    }
                ]
```

## <a name="optimizations-that-reduce-query-latency"></a>Optimalizace, které snižují latenci dotazů

V mnoha případech může být poplatek za RU přijatelný, pokud je latence dotazů pořád příliš vysoká. Následující části obsahují přehled tipů pro snížení latence dotazů. Pokud stejný dotaz spouštíte několikrát pro stejnou datovou sadu, bude to mít obvykle za každou dobu stejnou částku RU. Latence dotazů se ale může lišit mezi provedeními dotazu.

### <a name="improve-proximity"></a>Zlepšení blízkosti

Dotazy, které jsou spouštěny z jiné oblasti než Azure Cosmos DB účtu, budou mít vyšší latenci, než kdyby byly spuštěny ve stejné oblasti. Pokud například spouštíte kód na stolním počítači, měli byste očekávat, že latence bude desítkově nebo stovky milisekund vyšší (nebo více), než kdyby dotaz nacházel z virtuálního počítače ve stejné oblasti Azure jako Azure Cosmos DB. [Globální distribuce dat v Azure Cosmos DB](tutorial-global-distribution-mongodb.md) je jednoduchá, aby bylo zajištěno, že vaše data budou blíž do vaší aplikace.

### <a name="increase-provisioned-throughput"></a>Zvýšení zřízené propustnosti

V Azure Cosmos DB se zřízená propustnost měří v jednotkách žádosti (ru). Představte si, že máte dotaz, který spotřebovává 5 ru propustnosti. Pokud například zřídíte 1 000 ru, budete moct spustit tento dotaz 200 krát za sekundu. Pokud jste se pokusili spustit dotaz, když není k dispozici dostatek propustnosti, Azure Cosmos DB omezí počet požadavků. Azure Cosmos DB rozhraní API pro MongoDB se tento dotaz automaticky zopakuje po krátké době. Omezené žádosti trvá déle, takže zvýšení zajištěné propustnosti může zlepšit latenci dotazů.

Hodnota `estimatedDelayFromRateLimitingInMilliseconds` poskytuje smysl výhod potenciální latence při zvýšení propustnosti.

## <a name="next-steps"></a>Další kroky

* [Řešení potíží s výkonem dotazů (SQL API)](troubleshoot-query-performance.md)
* [Správa indexování v rozhraní Azure Cosmos DB API pro MongoDB](mongodb-indexing.md)