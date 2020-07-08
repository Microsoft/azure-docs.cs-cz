---
title: Události obrazce – Azure Time Series Insights | Microsoft Docs
description: Přečtěte si o osvědčených postupech a o tom, jak obrazce událostí pro dotazování v Azure Time Insights ve verzi Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/24/2020
ms.custom: seodec18
ms.openlocfilehash: fd2c58b07f3be5d5fa6d99d0c8c64906b81e7de4
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2020
ms.locfileid: "86036980"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Události obrazce s Azure Time Series Insights Preview

Tento článek definuje osvědčené postupy pro objednání datových částí JSON pro ingestování v Azure Time Series Insights a pro maximalizaci efektivity dotazů ve verzi Preview.

## <a name="best-practices"></a>Osvědčené postupy

Je nejlepší pečlivě zvážit, jak odesílat události do prostředí Time Series Insights ve verzi Preview. 

Obecné osvědčené postupy zahrnují:

* Co nejefektivněji odesílejte data přes síť.
* Uložte si data způsobem, který vám pomůže ho agregovat lépe pro váš scénář.

Pro dosažení nejlepšího výkonu dotazů je nutné dodržovat následující pravidla pro palec:

* Neodesílat nepotřebné vlastnosti. Time Series Insights verze Preview podle využití. Je nejvhodnější ukládat a zpracovávat pouze data, která budete dotazovat.
* Použijte pole instance pro statická data. Tento postup pomáhá vyhnout se posílání statických dat přes síť. Pole instancí – komponenta modelu časové řady, která funguje jako referenční data ve službě Time Series Insights všeobecně dostupná. Další informace o polích instance získáte v [modelu časové řady](./concepts-model-overview.md)pro čtení.
* Sdílení vlastností dimenze mezi dvěma nebo více událostmi. Tento postup vám pomůže rychleji odesílat data z sítě.
* Nepoužívejte vnořování hlubokých polí. Time Series Insights Preview podporuje až dvě úrovně vnořených polí, která obsahují objekty. Time Series Insights Preview sloučí pole ve zprávách do více událostí s páry hodnot vlastností.
* Pokud pro všechny nebo většinu událostí existuje jenom několik měr, je lepší posílat tyto míry jako samostatné vlastnosti v rámci stejného objektu. Jejich odeslání samostatně snižuje počet událostí a může zlepšit výkon dotazů, protože je potřeba zpracovat méně událostí.

## <a name="column-flattening"></a>Sloučení sloupců

Během ingestování budou datové části obsahující vnořené objekty shrnuty tak, že název sloupce je jediná hodnota s oddělovačem.

* Například následující vnořený kód JSON:

   ```JSON
   "data": {
        "flow": 1.0172575712203979,
   },
   ```

   Bude: `data_flow` při sloučení.

> [!IMPORTANT]
> * Azure Time Series Insights Preview používá `_` pro delineing sloupce podtržítka ().
> * Všimněte si rozdílů od všeobecné dostupnosti, která místo toho používá tečky ( `.` ).

Složitější scénáře jsou znázorněny níže.

#### <a name="example-1"></a>Příklad 1:

Následující scénář obsahuje dvě (nebo více) zařízení, která odesílají měření (signály): *rychlost toku*, *tlak v oleji*, *teplota*a *vlhkost*.

Byla odeslána jedna zpráva Azure IoT Hub, kde vnější pole obsahuje sdílený oddíl běžných hodnot dimenzí (Všimněte si dvou položek zařízení obsažených ve zprávě).

```JSON
[
  {
    "deviceId":"FXXX",
    "timestamp":"2018-01-17T01:17:00Z",
    "series":[
      {
        "Flow Rate ft3/s":1.0172575712203979,
        "Engine Oil Pressure psi ":34.7
      },
      {
        "Flow Rate ft3/s":2.445906400680542,
        "Engine Oil Pressure psi ":49.2
      }
    ]
  },
  {
    "deviceId":"FYYY",
    "timestamp":"2018-01-17T01:18:00Z",
    "series":[
      {
        "Flow Rate ft3/s":0.58015072345733643,
        "Engine Oil Pressure psi ":22.2
      }
    ]
  }
]
```

**Poznatky**

* Vzorový kód JSON má vnější pole, které používá data [instance časové řady](./concepts-model-overview.md#time-series-model-instances) k zvýšení efektivity zprávy. I když instance časových řad nemění metadata zařízení, často poskytuje užitečné vlastnosti pro analýzu dat.

* JSON kombinuje dvě nebo více zpráv (jedno ze zařízení) do jedné datové části, která v průběhu času ukládá šířku pásma.

* Jednotlivé datové body řady pro každé zařízení jsou zkombinovány do jediného atributu **řady** , což snižuje nutnost nepřetržitého streamování aktualizací pro každé zařízení.

> [!TIP]
> Chcete-li snížit počet zpráv potřebných k odeslání dat a zvýšit efektivitu telemetrie, zvažte dávkování běžných hodnot dimenzí a metadat instance časových řad do jedné datové části JSON.

#### <a name="time-series-instance"></a>Instance časové řady 

Pojďme se podíváme na to, jak používat [instanci časové řady](./concepts-model-overview.md#time-series-model-instances) k lepšímu tvarování JSON. 

> [!NOTE]
> Níže uvedená [ID časových řad](./time-series-insights-update-how-to-id.md) jsou *DeviceID*.

```JSON
[
  {
    "timeSeriesId":[
      "FXXX"
    ],
    "typeId":"17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds":[
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description":null,
    "instanceFields":{
      "L1":"REVOLT SIMULATOR",
      "L2":"Battery System"
    }
  },
  {
    "timeSeriesId":[
      "FYYY"
    ],
    "typeId":"17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds":[
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description":null,
    "instanceFields":{
      "L1":"COMMON SIMULATOR",
      "L2":"Battery System"
    }
  }
]
```

Time Series Insights Preview se v průběhu dotazu spojí s tabulkou (po sloučení). Tabulka obsahuje další sloupce, například **typ**.

| deviceId  | Typ | L1 | Paměť | časové razítko | series_Flow ft3 míry/s | vyseries_Engine psí tlak v oleji |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SIMULÁTOR | Systém baterie | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34,7 |
| `FXXX` | Default_Type | SIMULÁTOR |   Systém baterie |    2018-01-17T01:17:00Z | 2.445906400680542 |  49,2 |
| `FYYY` | LINE_DATA COMMON | SIMULÁTOR |    Systém baterie |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22,2 |

> [!NOTE]
>  Předchozí tabulka představuje zobrazení dotazu v Průzkumníkovi ve službě [Time Series Preview](./time-series-insights-update-explorer.md).

**Poznatky**

* V předchozím příkladu jsou statické vlastnosti uložené v Time Series Insights Preview, aby se optimalizoval data odesílaná přes síť.
* Data ve verzi Preview Time Series Insights jsou připojena v době dotazu prostřednictvím ID časové řady, které je definováno v instanci.
* Používají se dvě vrstvy vnoření. Toto číslo je maximum, které podporuje Time Series Insights Preview. Je důležité vyhnout se hluboko vnořeným polím.
* Vzhledem k tomu, že existuje několik opatření, jsou odesílána jako samostatné vlastnosti v rámci stejného objektu. V příkladu **series_Flow psi**, **series_Engine psí tlak v olejích**a **series_Flow sazba ft3/s** jsou jedinečné sloupce.

>[!IMPORTANT]
> Pole instance nejsou uložena s telemetrie. Ukládají se s metadaty v modelu časové řady.

#### <a name="example-2"></a>Příklad 2:

Vezměte v úvahu následující kód JSON:

```JSON
{
  "deviceId": "FXXX",
  "timestamp": "2019-01-18T01:17:00Z",
  "data": {
        "flow": 1.0172575712203979,
    },
  "data_flow" : 1.76435072345733643
}
```

V předchozím příkladu by shrnutá `data["flow"]` vlastnost představovala kolize názvů s `data_flow` vlastností.

V tomto případě by *Poslední* hodnota vlastnosti přepsala předchozí. 

> [!TIP]
> Pokud potřebujete další pomoc, obraťte se na tým Time Series Insights.

> [!WARNING] 
> * V případech, kdy jsou duplicitní vlastnosti přítomny ve stejné (jednotné) datové části události v důsledku sloučení nebo jiného mechanismu, je uložena nejnovější hodnota vlastnosti >, při které se přepíší všechny předchozí hodnoty.
> * Řada kombinovaných událostí nebude navzájem popsána.

## <a name="next-steps"></a>Další kroky

* Pokud chcete tyto pokyny do praxe přidat, přečtěte si [syntaxi dotazu Azure Time Series Insights Preview](./time-series-insights-query-data-csharp.md). Přečtěte si další informace o syntaxi dotazů pro Time Series Insights [Preview REST API](https://docs.microsoft.com/rest/api/time-series-insights/preview) pro přístup k datům.

* Kombinovat osvědčené postupy JSON s [postupy pro model časových řad](./time-series-insights-update-how-to-tsm.md).
