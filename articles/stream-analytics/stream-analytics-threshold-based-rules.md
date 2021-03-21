---
title: Konfigurovatelná pravidla na základě prahové hodnoty v Azure Stream Analytics
description: Tento článek popisuje, jak pomocí referenčních dat dosáhnout řešení upozornění, které má konfigurovatelné pravidla na základě prahové hodnoty v Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 04/30/2018
ms.openlocfilehash: 2f9d132084f0254486be533daea6b54239f4e450
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019971"
---
# <a name="process-configurable-threshold-based-rules-in-azure-stream-analytics"></a>Zpracování konfigurovatelných pravidel na základě prahových hodnot v Azure Stream Analytics
Tento článek popisuje, jak pomocí referenčních dat dosáhnout řešení upozornění, které používá konfigurovatelné pravidla na základě prahové hodnoty v Azure Stream Analytics.

## <a name="scenario-alerting-based-on-adjustable-rule-thresholds"></a>Scénář: upozorňování na základě prahových hodnot upravitelných pravidel
Je možné, že budete muset vytvořit výstrahu jako výstup, když příchozí datové proudy dosáhly určité hodnoty, nebo když agregovaná hodnota na základě událostí příchozího streamu překročí určitou prahovou hodnotu. Je jednoduché nastavit Stream Analytics dotaz, který porovnává hodnotu se statickou prahovou hodnotou, která je pevná a předem stanovená. Pevná prahová hodnota může být pevně zakódována do syntaxe dotazu streamování pomocí jednoduchých čísel porovnání (větší než, menší než a rovnost).

V některých případech je třeba prahové hodnoty snadněji konfigurovat bez nutnosti upravovat syntaxi dotazu pokaždé, když se změní prahová hodnota. V jiných případech možná budete potřebovat spoustu zařízení nebo uživatelů, kteří zpracovávají stejný dotaz, přičemž každý z nich má v každém typu zařízení různé mezní hodnoty. 

Tento model se dá použít k dynamické konfiguraci prahových hodnot, selektivně zvolit, který typ zařízení se prahová hodnota aplikuje filtrováním vstupních dat, a selektivně zvolit, která pole se mají zahrnout do výstupu.

## <a name="recommended-design-pattern"></a>Doporučený vzor návrhu
Jako vyhledávání prahových hodnot výstrah použít vstup referenčních dat do Stream Analytics úlohy:
- Uloží prahové hodnoty do referenčních dat, jednu hodnotu na klíč.
- Zapojte události vstupu streamování dat do referenčních dat ve sloupci Key.
- Použijte hodnotu s klíčem z referenčních dat jako prahovou hodnotu.

## <a name="example-data-and-query"></a>Ukázková data a dotazy
V tomto příkladu jsou výstrahy vygenerovány, když agregace datových proudů ze zařízení v minutovém okně odpovídá stanoveným hodnotám v pravidle, které jste zadali jako referenční data.

V dotazu, pro každý deviceId a každou hodnotu metric za deviceId, můžete nakonfigurovat od 0 do 5 dimenzí, podle kterých se SESKUPí. Seskupeny jsou pouze události, které mají odpovídající hodnoty filtru. Po seskupení jsou agregované agregované hodnoty min, Max, AVG, počítány v rámci 60 sekundového okna. Filtry agregovaných hodnot se pak vypočítávají podle nakonfigurované prahové hodnoty v odkazu, aby se vygenerovala událost výstupu výstrahy.

Předpokládejme například, že je k dispozici Stream Analytics úloha, která má referenční vstupní data s názvem **rules** a datový proud streamování s názvem **metriky**. 

## <a name="reference-data"></a>Referenční data
Tento příklad referenčních dat ukazuje, jak je možné znázornit pravidlo na základě prahové hodnoty. Soubor JSON obsahuje referenční data a ukládá se do úložiště objektů BLOB v Azure a tento kontejner úložiště objektů BLOB se používá jako referenční datový vstup s názvem **rules**. Tento soubor JSON můžete přepsat a nahradit konfiguraci pravidla na čas, aniž byste museli zastavit nebo spustit úlohu streamování.

- Ukázkové pravidlo se používá k reprezentaci nastavitelného upozornění při překročení procesoru (průměr je větší než nebo rovno) hodnota `90` Percent. `value`Pole lze podle potřeby konfigurovat.
- Všimněte si, že pravidlo má pole **operátoru** , které je dynamicky interpretováno v syntaxi dotazu později `AVGGREATEROREQUAL` . 
- Pravidlo filtruje data na určitém klíči dimenze `2` s hodnotou `C1` . Další pole jsou prázdné řetězce, což značí, že se vstupním datovým proudem nefiltrují tato pole události. Můžete nastavit další pravidla procesoru pro filtrování dalších vyhovujících polí podle potřeby.
- Do výstupní události výstrahy nejsou zahrnuty všechny sloupce. V takovém případě `includedDim` je klíčové číslo `2` zapnuté, `TRUE` aby představovalo, že pole číslo 2 dat události v datovém proudu bude zahrnuto do vykvalifikačních výstupních událostí. Ostatní pole nejsou součástí výstupu výstrahy, ale seznam polí lze upravit.


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

## <a name="example-streaming-query"></a>Ukázkový dotaz streamování
V tomto příkladu se Stream Analytics dotazu spojí **pravidla** referenčních dat z výše uvedeného příkladu ke vstupnímu proudu dat s pojmenovanými **metrikami**.

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

## <a name="example-streaming-input-event-data"></a>Příklad streamování vstupních dat událostí
Tato ukázková data JSON představují vstupní data **metrik** , která se používají ve výše uvedeném dotazu streamování. 

- Tři ukázkové události jsou uvedeny v rozmezí 1 – minuty, hodnota `T14:50` . 
- Všechny tři mají stejnou `deviceId` hodnotu `978648` .
- Hodnoty metrik CPU se v každé události, v `98` `95` `80` uvedeném pořadí liší. Pouze první dvě ukázkové události překračují pravidlo upozornění procesoru, které je v pravidle navázáno.
- Pole includeDim v pravidle výstrahy bylo číslo klíče 2. Odpovídající pole klíče 2 v vzorových událostech je pojmenováno `NodeName` . Tři ukázkové události mají hodnoty `N024` , `N024` a `N014` . Ve výstupu se zobrazí jenom uzel `N024` , který je jediná data, která odpovídají kritériím výstrahy pro vysoký procesor. `N014` nesplňuje maximální prahovou hodnotu procesoru.
- Pravidlo výstrahy je konfigurováno pouze s `filter` klíčem číslo 2, který odpovídá `cluster` poli v vzorových událostech. Tři ukázkové události mají hodnotu `C1` a odpovídají kritériím filtru.

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
V tomto příkladu jsou výstupní data JSON, která vygenerovala jednu událost výstrahy na základě pravidla prahové hodnoty CPU definované v referenčních datech. Výstupní událost obsahuje název výstrahy a také agregované (průměr, minimum, maximum) uvažovaného pole. Výstupní data události obsahují hodnotu klíče pole číslo 2 `NodeName` `N024` z důvodu konfigurace pravidla. (Kód JSON byl změněn tak, aby zobrazoval konce řádků pro čitelnost.)

```JSON
{"time":"2018-05-01T02:03:00.0000000Z","deviceid":"978648","ruleid":1234,"metric":"CPU",
"alert":"hot node AVG CPU over 90","avg":96.5,"min":95.0,"max":98.0,
"dim0":null,"dim1":null,"dim2":"N024","dim3":null,"dim4":null}
```
