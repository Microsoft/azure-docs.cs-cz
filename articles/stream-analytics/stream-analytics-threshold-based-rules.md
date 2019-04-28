---
title: Pravidla ve službě Azure Stream Analytics na základě procesu konfigurovatelné prahové hodnoty
description: Tento článek popisuje, jak použít referenční data k dosažení výstrah řešení, které se dají konfigurovat na základě prahových hodnot pravidla ve službě Azure Stream Analytics.
services: stream-analytics
author: rockboyfor
ms.author: v-yeche
manager: digimobile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
origin.date: 04/30/2018
ms.date: 08/20/2018
ms.openlocfilehash: ce2cf6ebdfd74549114e94e4c7356e387576d3c8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60761722"
---
# <a name="process-configurable-threshold-based-rules-in-azure-stream-analytics"></a>Zpracovat konfigurovatelných pravidel založené na prahových hodnotách ve službě Azure Stream Analytics
Tento článek popisuje, jak použít referenční data k dosažení řešení, která používá konfigurovatelných pravidel založené na prahových hodnotách ve službě Azure Stream Analytics.

## <a name="scenario-alerting-based-on-adjustable-rule-thresholds"></a>Scénář: Výstrahy na základě přizpůsobitelná pravidla prahových hodnot
Budete muset vytvořit výstrahu jako výstup po určitou hodnotu příchozí streamovaná události nebo když agregovanou hodnotu na základě proudu příchozích událostí překročí určitou prahovou hodnotu. Je snadné nastavení dotazu Stream Analytics, který porovnávané hodnoty na statickou prahovou hodnotu, která je pevná a předdefinovány. Oprava prahová hodnota může být pevně zakódovaný do datových proudů syntaxi dotazu pomocí jednoduchého číselné porovnání (větší než je menší než a rovnost).

V některých případech prahové hodnoty musí být snadno konfigurovatelné bez úprav syntaxi dotazu pokaždé, když se změní prahovou hodnotu. V ostatních případech můžete potřebovat řadu zařízení nebo uživatelů zpracovány stejný dotaz s každou z nich má různé prahové hodnoty na jednotlivé typy zařízení. 

Tento model umožňuje dynamicky konfigurovat mezní hodnoty, si zvolit typ zařízení, že prahová hodnota platit pomocí filtrování vstupních dat a si zvolit pole, která chcete zahrnout do výstupu.

## <a name="recommended-design-pattern"></a>Doporučený návrhový vzor
Pomocí referenčního datového vstupu do úlohy Stream Analytics k vyhledávání prahových hodnot výstrah:
- Store prahové hodnoty v referenčních dat, jednu hodnotu pro klíč.
- Připojte se k streamovaných událostí vstupní data pro referenční data na klíčový sloupec.
- Použijte hodnotu s klíčem z referenčních dat jako prahovou hodnotu.

## <a name="example-data-and-query"></a>Příklad dat a dotazování
V tomto příkladu výstrahy jsou generovány, když agregaci dat přicházejících z zařízení v okně minutu získávaným odpovídá stanoveném hodnot v pravidle uvedené jako referenční data.

V dotazu, pro každé ID zařízení a každou metricName podle ID zařízení můžete nakonfigurovat od 0 do 5 dimenzí pro GROUP BY. Pouze události s odpovídající hodnoty filtru jsou seskupeny. Jakmile seskupeny, Min, Max, Avg, agregace v okně se počítají přes 60 sekund aktivační událost pro přeskakující okno. Filtry na agregované hodnoty jsou pak vypočítá podle nakonfigurovanou prahovou hodnotu v referenční dokumentaci k vygenerování výstrahy výstupní událost.

Jako příklad předpokládejme je úlohu Stream Analytics, která má referenčního datového vstupu s názvem **pravidla**a datových proudů vstup dat s názvem **metriky**. 

## <a name="reference-data"></a>Referenční data
Referenční data tento příklad ukazuje, jak můžou být reprezentované pravidlo založené na prahových hodnotách. Soubor JSON obsahuje referenční data a uložit do úložiště objektů blob v Azure, a tento kontejner objektů blob úložiště se používá jako referenčního datového vstupu s názvem **pravidla**. Může přepsat tento soubor JSON a nahradit konfigurace pravidla podle času nenastane, bez zastavení a spuštění úlohy streamování.

- Příklad pravidla se používá k reprezentování měnitelné výstrahu, pokud využití procesoru překročí (průměr je větší než nebo rovno) hodnotu `90` procent. `value` Pole se dají konfigurovat podle potřeby.
- Všimněte si, že má pravidlo **operátor** pole, které dynamicky interpretována v syntaxi dotazů později `AVGGREATEROREQUAL`. 
- Pravidlo filtruje data na určitou klávesu dimenze `2` s hodnotou `C1`. Ostatní pole jsou prázdný řetězec označující není pro filtrování na základě těchto událostí polí vstupního datového proudu. Může nastavit další pravidla procesoru k filtrování další odpovídající pole, podle potřeby.
- Ne všechny sloupce se mají být zahrnuty do výstupní událost upozornění. V takovém případě `includedDim` klíče číslo `2` zapnutý `TRUE` znázornit, že se data událostí v datovém proudu číslo 2 zahrnou kvalifikující události výstup. V ostatních polích nejsou součástí výstupu výstrahy, ale v seznamu polí je možné upravit.

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
Tento příklad dotazu Stream Analytics se připojí **pravidla** odkazují na data v příkladu výše, do vstupního datového proudu dat s názvem **metriky**.

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

## <a name="example-streaming-input-event-data"></a>Příklad streamovaná data vstupní události
Představuje JSON data v tomto příkladu **metriky** vstupní data, která se používá ve výše uvedeném dotazu datových proudů. 

- V rámci časový interval 1 minuta, hodnoty jsou uvedeny tři příklady událostí `T14:50`. 
- Všechny tři mají stejné `deviceId` hodnota `978648`.
- Hodnoty metrik využití procesoru se liší v každé události `98`, `95`, `80` v uvedeném pořadí. Pouze první dva příklady událostí překročit pravidlo upozornění procesoru v pravidle.
- Pole includeDim v pravidle výstrahy byla číslo klíče 2. Odpovídající pole klíče 2 v příklady událostí má název `NodeName`. Všechny tři příklad události mají hodnoty `N024`, `N024`, a `N014` v uvedeném pořadí. Ve výstupu se zobrazí pouze uzel `N024` jako, který je pouze data, která odpovídá kritéria výstrahy vysoké využití procesoru. `N014` nesplňuje prahovou hodnotu vysoké využití procesoru.
- Pravidlo upozornění se nakonfigurují `filter` jenom na klíče číslo 2, které odpovídá `cluster` v ukázkové události. Všechny tři příklady událostí mají hodnotu `C1` a kritéria filtru.

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
Tento příklad výstupu JSON daty se dozvíte, které bylo vytvořeno jednu událost výstrahy založené na pravidlo mezní hodnoty využití procesoru podle referenční data. Výstupní událost obsahuje název výstrahy a také agregované (průměrná, min, max) považovat za pole. Výstupní data události obsahují číslo pole klíče 2 `NodeName` hodnotu `N024` kvůli konfiguraci tohoto pravidla. (Ve formátu JSON byl změněn na Zobrazit konce řádků pro lepší čitelnost).

```JSON
{"time":"2018-05-01T02:03:00.0000000Z","deviceid":"978648","ruleid":1234,"metric":"CPU",
"alert":"hot node AVG CPU over 90","avg":96.5,"min":95.0,"max":98.0,
"dim0":null,"dim1":null,"dim2":"N024","dim3":null,"dim4":null}
```
<!--Update_Description: updat meta properties, wording update-->