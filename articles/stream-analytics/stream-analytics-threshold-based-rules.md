---
title: Konfigurovatelná pravidla založená na prahových hodnotách ve službě Azure Stream Analytics
description: Tento článek popisuje, jak použít referenční data k dosažení řešení výstrah, které má konfigurovatelná pravidla založená na prahových hodnotách v Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 94fdddf11acb6763ed98a4b7e17304fbde0e25dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75369707"
---
# <a name="process-configurable-threshold-based-rules-in-azure-stream-analytics"></a>Zpracování konfigurovatelných pravidel založených na prahových hodnotách ve službě Azure Stream Analytics
Tento článek popisuje, jak použít referenční data k dosažení řešení výstrah, které používá konfigurovatelná pravidla založená na prahových hodnotách ve službě Azure Stream Analytics.

## <a name="scenario-alerting-based-on-adjustable-rule-thresholds"></a>Scénář: Upozornění na základě nastavitelných prahových hodnot pravidel
Možná budete muset vytvořit výstrahu jako výstup, když příchozí streamované události dosáhly určité hodnoty nebo pokud agregovaná hodnota založená na příchozích streamovaných událostech překročí určitou prahovou hodnotu. Je jednoduché nastavit dotaz Stream Analytics, který porovnával hodnotu se statickou prahovou hodnotou, která je pevná a předem určená. Pevná prahová hodnota může být pevně zakódována do syntaxe dotazu streamování pomocí jednoduchých číselných porovnání (větší než, menší než a rovnost).

V některých případech musí být hodnoty prahové hodnoty snadněji konfigurovatelné bez úprav syntaxe dotazu při každé změně prahové hodnoty. V ostatních případech můžete potřebovat mnoho zařízení nebo uživatelů zpracovaných stejným dotazem, přičemž každý z nich má na každém druhu zařízení jiné prahové hodnoty. 

Tento vzor lze dynamicky konfigurovat prahové hodnoty, selektivně zvolit, jaký druh zařízení prahová hodnota použije filtrováním vstupních dat a selektivně zvolit, která pole mají být zahrnuta do výstupu.

## <a name="recommended-design-pattern"></a>Doporučený návrhový vzor
Použijte referenční data vstup pro úlohu Stream Analytics jako vyhledávání prahových hodnot výstrah:
- Uložte prahové hodnoty do referenčních dat, jednu hodnotu na klíč.
- Připojte události vstupu datových proudů k referenčním datům ve sloupci klíče.
- Jako prahovou hodnotu použijte hodnotu klíče z referenčních dat.

## <a name="example-data-and-query"></a>Ukázková data a dotaz
V příkladu výstrahy jsou generovány, když agregace dat datových proudů ze zařízení v minutovém okně odpovídá stanovené hodnoty v pravidle zadané jako referenční data.

V dotazu, pro každý deviceId a každý metricName pod deviceId, můžete nakonfigurovat od 0 do 5 dimenzí group by. Jsou seskupeny pouze události s odpovídajícími hodnotami filtru. Po seskupení, okna agregáty Min, Max, Avg, jsou vypočteny přes 60-sekundom omílání okna. Filtry na agregované hodnoty jsou pak vypočteny podle nakonfigurované prahové hodnoty v odkazu, aby se vygenerovala výstupní událost výstrahy.

Jako příklad předpokládejme, že existuje úloha Stream Analytics, která má referenční datový vstup s názvem **pravidla**a streamování dat vstup s názvem **metriky**. 

## <a name="reference-data"></a>Referenční údaje
Tento příklad referenční chdatné údaje ukazují, jak by mohlo být reprezentováno pravidlo založené na prahové hodnotě. Soubor JSON obsahuje referenční data a je uložen do úložiště objektů blob Azure a tento kontejner úložiště objektů blob se používá jako referenční datový vstup s názvem **pravidla**. Tento soubor JSON můžete přepsat a nahradit konfiguraci pravidla podle času, bez zastavení nebo spuštění úlohy streamování.

- Ukázkové pravidlo se používá k reprezentaci nastavitelné výstrahy při překročení procesoru (průměr je větší nebo roven) procentuální hodnoty. `90` Pole `value` je konfigurovatelné podle potřeby.
- Všimněte si, že pravidlo má pole **operátoru,** které `AVGGREATEROREQUAL`je dosud dynamicky interpretováno v syntaxi dotazu později . 
- Pravidlo filtruje data na klíč `2` určité `C1`dimenze s hodnotou . Ostatní pole jsou prázdný řetězec, označující, že není filtrovat vstupní datový proud podle těchto polí událostí. Můžete nastavit další pravidla procesoru pro filtrování dalších odpovídajících polí podle potřeby.
- Ne všechny sloupce mají být zahrnuty do události výstrahy výstupu. V takovém `includedDim` případě `2` je číslo `TRUE` klíče zapnuto, aby představovalo toto pole číslo 2 dat událostí v datovém proudu, které bude zahrnuto do kvalifikačních výstupních událostí. Ostatní pole nejsou zahrnuta ve výstupu výstrahy, ale seznam polí lze upravit.


```json
{
    "ruleId": 1234, 
    "deviceId" : "978648", 
    "metricName": "CPU", 
    "alertName": "hot node AVG CPU over 90",
    "operator" : "AVGGREATEROREQUAL",
    "value": 90, 
    "includeDim": {
        "0": "FALSE", 
        "1": "FALSE", 
        "2": "TRUE", 
        "3": "FALSE", 
        "4": "FALSE"
    },
    "filter": {
        "0": "", 
        "1": "",
        "2": "C1", 
        "3": "", 
        "4": ""
    }    
}
```

## <a name="example-streaming-query"></a>Ukázkový dotaz pro streamování
Tento příklad dotazu Stream Analytics spojuje referenční data **pravidel** z výše uvedeného příkladu se vstupním datovým proudem dat s názvem **metriky**.

```sql
WITH transformedInput AS
(
    SELECT
        dim0 = CASE rules.includeDim.[0] WHEN 'TRUE' THEN metrics.custom.dimensions.[0].value ELSE NULL END,
        dim1 = CASE rules.includeDim.[1] WHEN 'TRUE' THEN metrics.custom.dimensions.[1].value ELSE NULL END,
        dim2 = CASE rules.includeDim.[2] WHEN 'TRUE' THEN metrics.custom.dimensions.[2].value ELSE NULL END,
        dim3 = CASE rules.includeDim.[3] WHEN 'TRUE' THEN metrics.custom.dimensions.[3].value ELSE NULL END,
        dim4 = CASE rules.includeDim.[4] WHEN 'TRUE' THEN metrics.custom.dimensions.[4].value ELSE NULL END,
        metric = metrics.metric.value,
        metricName = metrics.metric.name,
        deviceId = rules.deviceId, 
        ruleId = rules.ruleId, 
        alertName = rules.alertName,
        ruleOperator = rules.operator, 
        ruleValue = rules.value
    FROM 
        metrics
        timestamp by eventTime
    JOIN 
        rules
        ON metrics.deviceId = rules.deviceId AND metrics.metric.name = rules.metricName
    WHERE
        (rules.filter.[0] = '' OR metrics.custom.filters.[0].value = rules.filter.[0]) AND 
        (rules.filter.[1] = '' OR metrics.custom.filters.[1].value = rules.filter.[1]) AND
        (rules.filter.[2] = '' OR metrics.custom.filters.[2].value = rules.filter.[2]) AND
        (rules.filter.[3] = '' OR metrics.custom.filters.[3].value = rules.filter.[3]) AND
        (rules.filter.[4] = '' OR metrics.custom.filters.[4].value = rules.filter.[4])
)

SELECT
    System.Timestamp as time, 
    transformedInput.deviceId as deviceId,
    transformedInput.ruleId as ruleId,
    transformedInput.metricName as metric,
    transformedInput.alertName as alert,
    AVG(metric) as avg,
    MIN(metric) as min, 
    MAX(metric) as max, 
    dim0, dim1, dim2, dim3, dim4
FROM
    transformedInput
GROUP BY
    transformedInput.deviceId,
    transformedInput.ruleId,
    transformedInput.metricName,
    transformedInput.alertName,
    dim0, dim1, dim2, dim3, dim4,
    ruleOperator, 
    ruleValue, 
    TumblingWindow(second, 60)
HAVING
    (
        (ruleOperator = 'AVGGREATEROREQUAL' AND avg(metric) >= ruleValue) OR
        (ruleOperator = 'AVGEQUALORLESS' AND avg(metric) <= ruleValue) 
    )
```

## <a name="example-streaming-input-event-data"></a>Příklad dat vstupní události streamování
Tento příklad JSON data představuje vstupní data **metriky,** která se používá ve výše uvedeném dotazu streamování. 

- Tři ukázkové události jsou uvedeny v `T14:50`rámci 1 minutového časovým rozpětí, hodnota . 
- Všechny tři mají `deviceId` `978648`stejnou hodnotu .
- Hodnoty metriky procesoru se `98` `95`liší `80` v každé události , , v uvedeném pořadí. Pouze první dvě ukázkové události překročí pravidlo výstrahy procesoru stanovené v pravidle.
- Pole includeDim v pravidle výstrahy bylo klíčové číslo 2. Odpovídající pole klíče 2 v ukázkových událostech je pojmenováno `NodeName`. Tři ukázkové události `N024` `N024`mají `N014` hodnoty , a v uvedeném pořadí. Ve výstupu se zobrazí pouze `N024` uzel, protože se jedná o jediná data, která odpovídají kritériím výstrahy pro vysoký procesor. `N014`nesplňuje vysokou prahovou hodnotu procesoru.
- Pravidlo výstrahy je `filter` nakonfigurováno pouze na klíč číslo `cluster` 2, což odpovídá poli v ukázkových událostech. Všechny tři ukázkové `C1` události mají hodnotu a odpovídají kritériím filtru.

```json
{
    "eventTime": "2018-04-30T14:50:23.1324132Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N1"
            },
            "1": {
                "name": "Cluster",
                "value": "C1"
            },
            "2": {
                "name": "NodeName",
                "value": "N024"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N1"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 98,
        "count": 1.0,
        "min": 98,
        "max": 98,
        "stdDev": 0.0
    }
}
{
    "eventTime": "2018-04-30T14:50:24.1324138Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N2"
            },
            "1": {
                "name": "Cluster",
                "value": "C1"
            },
            "2": {
                "name": "NodeName",
                "value": "N024"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N2"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 95,
        "count": 1,
        "min": 95,
        "max": 95,
        "stdDev": 0
    }
}
{
    "eventTime": "2018-04-30T14:50:37.1324130Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N3"
            },
            "1": {
                "name": "Cluster",
                "value": "C1 "
            },
            "2": {
                "name": "NodeName",
                "value": "N014"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N3"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 80,
        "count": 1,
        "min": 80,
        "max": 80,
        "stdDev": 0
    }
}
```

## <a name="example-output"></a>Příklad výstupu
Tento příklad výstupních dat JSON ukazuje, že byla vytvořena jedna událost výstrahy na základě pravidla prahové hodnoty procesoru definovaného v referenčních datech. Výstupní událost obsahuje název výstrahy a agregované (průměr, min, max) zvažovaných polí. Výstupní data události zahrnují `NodeName` hodnotu `N024` klíče pole číslo 2 z důvodu konfigurace pravidla. (JSON byl změněn tak, aby zobrazoval zalomení řádků pro čitelnost.)

```JSON
{"time":"2018-05-01T02:03:00.0000000Z","deviceid":"978648","ruleid":1234,"metric":"CPU",
"alert":"hot node AVG CPU over 90","avg":96.5,"min":95.0,"max":98.0,
"dim0":null,"dim1":null,"dim2":"N024","dim3":null,"dim4":null}
```
