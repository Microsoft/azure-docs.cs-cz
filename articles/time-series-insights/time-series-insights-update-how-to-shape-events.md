---
title: Události obrazce s Azure Time Series Insights Preview | Microsoft Docs
description: Pochopte, jak pomocí Azure Time Series Insights ve verzi Preview přetvarovat události.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: ed0594373c8702ab01b50facaf0ef5ece2d6c7e1
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72274273"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Události obrazce s Azure Time Series Insights Preview

Tento článek vám pomůže tvarovat soubor JSON, aby se maximalizovala efektivita vašich Azure Time Series Insights dotazů ve verzi Preview.

## <a name="best-practices"></a>Osvědčené postupy

Zamyslete se nad tím, jak odesíláte události do Time Series Insights ve verzi Preview. Konkrétně byste měli:

* Co nejefektivněji odesílejte data přes síť.
* Uložte si data způsobem, který vám pomůže ho agregovat lépe pro váš scénář.

Pro nejlepší možný výkon dotazů udělejte toto:

* Neodesílat nepotřebné vlastnosti. Ve verzi Preview se vám na používání vyTime Series Insights. Je nejvhodnější ukládat a zpracovávat data, která budete dotazovat.
* Použijte pole instance pro statická data. Tento postup vám pomůže vyhnout se posílání statických dat přes síť. Pole instancí, součást modelu časové řady, funguje jako referenční data v Time Series Insights všeobecně dostupná služba. Další informace o polích instance najdete v tématu [modely časových řad](./time-series-insights-update-tsm.md).
* Sdílení vlastností dimenze mezi dvěma nebo více událostmi. Tento postup vám pomůže rychleji odesílat data z sítě.
* Nepoužívejte vnořování hlubokých polí. Time Series Insights Preview podporuje až dvě úrovně vnořených polí, která obsahují objekty. Time Series Insights Preview sloučí pole ve zprávách do více událostí s páry hodnot vlastností.
* Pokud pro všechny nebo většinu událostí existuje jenom několik měr, je lepší posílat tyto míry jako samostatné vlastnosti v rámci stejného objektu. Jejich odeslání samostatně snižuje počet událostí a může zlepšit výkon dotazů, protože je potřeba zpracovat méně událostí.

## <a name="example"></a>Příklad

Následující příklad je založen na scénáři, kdy dvě nebo více zařízení odesílá měření nebo signály. Měření nebo signály mohou být *průtokové rychlosti*, *tlak v oleji*, *teplota*a *vlhkost*v motoru.

V následujícím příkladu je k dispozici jedna zpráva Azure IoT Hub, kde vnější pole obsahuje sdílený oddíl běžných hodnot dimenzí. Vnější pole používá data instance časové řady k zvýšení efektivity zprávy. Instance časové řady obsahuje metadata zařízení, která se nemění u každé události, ale poskytuje užitečné vlastnosti pro analýzu dat. Pokud chcete ušetřit bajty odeslané přes vodiče a zvýšit efektivitu zprávy, zvažte dávkování běžných hodnot dimenzí a využití metadat instance časových řad.

### <a name="example-json-payload"></a>Příklad datové části JSON

```JSON
[
    {
        "deviceId": "FXXX",
        "timestamp": "2018-01-17T01:17:00Z",
        "series": [
            {
                "Flow Rate ft3/s": 1.0172575712203979,
                "Engine Oil Pressure psi ": 34.7
            },
            {
                "Flow Rate ft3/s": 2.445906400680542,
                "Engine Oil Pressure psi ": 49.2
            }
        ]
    },
    {
        "deviceId": "FYYY",
        "timestamp": "2018-01-17T01:18:00Z",
        "series": [
            {
                "Flow Rate ft3/s": 0.58015072345733643,
                "Engine Oil Pressure psi ": 22.2
            }
        ]
    }
]
```

### <a name="time-series-instance"></a>Instance časové řady 
> [!NOTE]
> ID časové řady je *deviceId*.

```JSON
{
    "timeSeriesId": [
      "FXXX"
    ],
    "typeId": "17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds": [
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description": null,
    "instanceFields": {
      "L1": "REVOLT SIMULATOR",
      "L2": "Battery System",
    }
  },
  {
    "timeSeriesId": [
      "FYYY"
    ],
    "typeId": "17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds": [
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description": null,
    "instanceFields": {
      "L1": "COMMON SIMULATOR",
      "L2": "Battery System",
    }
  },
```

Time Series Insights Preview se v průběhu dotazu spojí s tabulkou (po sloučení). Tabulka obsahuje další sloupce, například **typ**. Následující příklad ukazuje, jak můžete [tvarovat](./time-series-insights-send-events.md#supported-json-shapes) data telemetrie.

| deviceId  | Typ | L1 | Paměť | časové razítko | řadu. Rychlost toku ft3/s | řadu. Psí tlak v oleji motoru |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SIMULÁTOR | Systém baterie | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34,7 |
| `FXXX` | Default_Type | SIMULÁTOR |   Systém baterie |    2018-01-17T01:17:00Z | 2.445906400680542 |  49,2 |
| `FYYY` | LINE_DATA SPOLEČNÉ | SIMULÁTOR |    Systém baterie |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22,2 |

V předchozím příkladu si všimněte následujících bodů:

* Statické vlastnosti jsou uložené v Time Series Insights Preview pro optimalizaci dat odesílaných přes síť.
* Data ve verzi Preview Time Series Insights jsou připojena v době dotazu pomocí ID časové řady, které je definováno v instanci.
* Používají se dvě vrstvy vnoření, což je maximum, které podporuje Time Series Insights Preview. Je důležité vyhnout se hluboko vnořeným polím.
* Vzhledem k tomu, že existuje několik opatření, jsou odesílána jako samostatné vlastnosti v rámci stejného objektu. V příkladu **Series. Psí Flow**, **řady. Psí zatížení motoru**a **řady. Ft3/s míry toku** jsou jedinečné sloupce.

>[!IMPORTANT]
> Pole instance nejsou uložena s telemetrie. Ukládají se s metadaty v **modelu časové řady**.
> Předchozí tabulka představuje zobrazení dotazu.

## <a name="next-steps"></a>Další kroky

- Chcete-li tyto pokyny do praxe umístit, přečtěte si článek [syntaxe dotazu Azure Time Series Insights Preview](./time-series-insights-query-data-csharp.md). Přečtěte si další informace o syntaxi dotazu pro Time Series Insights REST API pro přístup k datům ve verzi Preview.
- Další informace o podporovaných tvarech JSON najdete v tématu [podporované tvary JSON](./time-series-insights-send-events.md#supported-json-shapes).
