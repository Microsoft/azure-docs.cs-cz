---
title: Proces konfigurovatelné prahové hodnoty na základě pravidel v Azure Stream Analytics
description: Tento článek popisuje postup používání referenčních dat. k dosažení výstrahy řešení, který má konfigurovatelné prahové hodnoty na základě pravidel v Azure Stream Analytics.
services: stream-analytics
author: zhongc
ms.author: zhongc
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 1c131c2c9ca12556c1d2cd52e7976d2f4272a0c8
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
ms.locfileid: "32767937"
---
# <a name="process-configurable-threshold-based-rules-in-azure-stream-analytics"></a>Proces konfigurovatelná pravidla na základě prahové hodnoty v Azure Stream Analytics
Tento článek popisuje postup používání referenčních dat. k dosažení výstrahy řešení, které využívá konfigurovatelná pravidla na základě prahové hodnoty v Azure Stream Analytics.

## <a name="scenario-alerting-based-on-adjustable-rule-thresholds"></a>Scénář: Výstrahy na základě upravit pravidlo prahových hodnot
Musíte vytvořit výstrahu jako výstup, když příchozí přenášené datovými proudy událostí dosáhli určitou hodnotu, nebo když úhrnnou hodnotu na základě příchozí přenášené datovými proudy událostí překračuje určitou mez. To jednoduché nastavit Stream Analytics dotaz, který porovnání hodnota, která má statické prahová hodnota je pevná a při předdefinovány. Opravené prahová hodnota může být pevně zakódované do streamování pomocí jednoduchého numerické porovnání syntaxe dotazu (větší než je menší než a rovnosti).

V některých případech prahové hodnoty musí být snadno konfigurovat bez úprav syntaxe dotazu pokaždé, když se změní prahovou hodnotu. V jiných případech může být nutné množství zařízení nebo uživatelů, které jsou zpracovávány stejný dotaz s každým z nich má různé prahové hodnoty na jednotlivé typy zařízení. 

Tento vzor lze dynamicky nakonfigurovat prahové hodnoty, zvolte selektivně jaký druh zařízení prahová hodnota platí filtrováním vstupní data a selektivně pole, která chcete zahrnout do výstupu.

## <a name="recommended-design-pattern"></a>Doporučený návrh vzor
Pomocí referenčního datového vstupu do úlohy Stream Analytics k vyhledávání prahových hodnot výstrah:
- Referenční data, jednu hodnotu pro klíč uložte prahové hodnoty.
- Připojte streamování událostí vstupní data pro referenční data na klíčový sloupec.
- Použití s klíčem hodnoty z referenční data jako prahovou hodnotu.

## <a name="example-data-and-query"></a>Příklad dat a dotazu
V příkladu výstrahy jsou generovány, když agregace dat vysílání datového proudu ze zařízení, v okně minutu dlouho odpovídá stanovené hodnot v pravidle zadán jako referenční data.

V dotazu, pro každý ID zařízení a každou metricName pod deviceId můžete nakonfigurovat od 0 do 5 dimenze GROUP BY. Jsou seskupené jenom události s odpovídající hodnoty filtru. Jakmile seskupené, jsou agregací v časových oknech Min, Max, Avg, vypočítávaný u 60 sekund přeskakující okno. Filtry na agregované hodnoty jsou pak počítá podle nakonfigurovanou prahovou hodnotu v dokumentaci k vygenerování výstrahy výstupní událost.

Jako příklad předpokládejme je úloha Stream Analytics, který má referenčního datového vstupu s názvem **pravidla**a vysílání datového proudu vstup dat s názvem **metriky**. 

## <a name="reference-data"></a>Referenční data
Referenční data tento příklad ukazuje, jak může být reprezentován pravidla na základě prahové hodnoty. Soubor JSON obsahuje referenční data a je uložen do úložiště objektů blob v Azure a tento kontejner úložiště objektů blob se používá jako referenčního datového vstupu s názvem **pravidla**. Můžete přepsání tohoto souboru JSON a nahraďte konfiguraci pravidla odehrává, bez zastavení nebo spuštění úloha streamování.

- Příklad pravidlo se používá k reprezentování upravit výstrahu, pokud využití procesoru překročí (průměr je větší než nebo rovno) hodnotu `90` procent. `value` Pole se dá konfigurovat podle potřeby.
- Všimněte si, má pravidlo **operátor** pole, která dynamicky interpretována v syntaxi dotazu později na `AVGGREATEROREQUAL`. 
- Pravidlo filtruje data na určité dimenze klíč `2` s hodnotou `C1`. Ostatní pole jsou prázdný řetězec, označující není pro filtrování podle těchto událostí polí vstupního datového proudu. Může nastavit další pravidla procesoru chcete filtrovat další odpovídající pole, podle potřeby.
- Ne všechny sloupce mají být zahrnuty v události výstrah výstup. V takovém případě `includedDim` klíče číslo `2` zapnutý `TRUE` představují, že číslo pole 2 dat událostí v datovém proudu bude součástí určení výstupní události. V ostatních polích nejsou součástí výstrahy výstup, ale lze upravit seznam polí.


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

## <a name="example-streaming-query"></a>Příklad streamování dotazu
Tento dotaz služby Stream Analytics příklad připojí **pravidla** referenční data z výše uvedených příkladech do vstupního datového proudu dat s názvem **metriky**.

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

## <a name="example-streaming-input-event-data"></a>Příklad vstupní událost dat
Tento příklad JSON data představuje **metriky** vstupní data, která se používá ve výše uvedeném dotazu streamování. 

- Tři příklady událostí jsou uvedené v 1 minutu časový interval, hodnota `T14:50`. 
- Všechny tři mít stejnou `deviceId` hodnotu `978648`.
- Hodnoty metriky procesoru lišit v každé události `98`, `95`, `80` v uvedeném pořadí. Pouze první dva příklady událostí překročit pravidlo výstrahy procesoru navázat v pravidle.
- V poli includeDim pravidlo výstrahy se klíče číslem 2. Odpovídající pole v událostech příklad klíč 2 jmenuje `NodeName`. Tři příklady událostí mají hodnoty `N024`, `N024`, a `N014` v uvedeném pořadí. Ve výstupu se zobrazí pouze uzel `N024` to je jediná data, která odpovídá kritérií výstrah pro vysoké využití procesoru. `N014` nesplňuje prahovou hodnotu vysoké využití procesoru.
- Pravidlo výstrahy nastavena `filter` pouze na klíče číslo 2, které odpovídá `cluster` pole v ukázkové události. Všechny tři příklady událostí mít hodnotu `C1` a odpovídající kritériím filtru.

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
Tento příklad výstupu zobrazuje data JSON, které bylo vytvořeno jednu událost výstrahy založený na pravidlu prahová hodnota využití procesoru, definované v referenční data. Výstupní událost obsahuje název výstrahy, stejně jako agregované (průměrná, min, max) považovat za pole. Výstupní data událostí obsahuje pole klíče číslem 2 `NodeName` hodnotu `N024` kvůli konfiguraci tohoto pravidla. (Ve formátu JSON byl upraven, aby zobrazit konce řádků čitelnější.)

```JSON
{"time":"2018-05-01T02:03:00.0000000Z","deviceid":"978648","ruleid":1234,"metric":"CPU",
"alert":"hot node AVG CPU over 90","avg":96.5,"min":95.0,"max":98.0,
"dim0":null,"dim1":null,"dim2":"N024","dim3":null,"dim4":null}
```