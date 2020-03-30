---
title: Události obrazců – Přehledy Azure Time Series | Dokumenty společnosti Microsoft
description: Přečtěte si o doporučených postupech a o tom, jak utvářet události pro dotazování v Azure Time Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/24/2020
ms.custom: seodec18
ms.openlocfilehash: 99a2f32c3f76d7fec475c9b299f7208b4db29cfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650919"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Události obrazců s azure time series insights preview

Tento článek definuje osvědčené postupy pro utváření datové části JSON pro ingestování v Azure Time Series Insights a maximalizovat efektivitu vašich dotazů náhledu.

## <a name="best-practices"></a>Osvědčené postupy

Nejlepší je pečlivě zvážit, jak odesíláte události do prostředí Time Series Insights Preview. 

Obecné osvědčené postupy zahrnují:

* Odesílejte data po síti co nejefektivněji.
* Ukládejte data způsobem, který vám pomůže je agregovat vhodněji pro váš scénář.

Chcete-li dosáhnout nejlepšího výkonu dotazů, dodržujte následující pravidla:

* Neposílejte nepotřebné vlastnosti. Časové řady Insights Náhled účty podle využití. Nejlepší je ukládat a zpracovávat pouze data, na která se budete dotazovat.
* Pro statická data použijte pole instancí. Tento postup pomáhá vyhnout se odesílání statických dat v síti. Pole instancí, součást modelu časové řady, fungují jako referenční data ve službě Time Series Insights, která je obecně dostupná. Další informace o polích instancí najdete v textu [Model časových řad](./time-series-insights-update-tsm.md).
* Sdílejte vlastnosti dimenze mezi dvěma nebo více událostmi. Tento postup pomáhá efektivněji odesílat data v síti.
* Nepoužívejte hluboké vnoření pole. Náhled přehledů časové řady podporuje až dvě úrovně vnořených polí, která obsahují objekty. Náhled přehledů časové řady sloučí pole ve zprávách do více událostí s dvojicemi hodnot vlastností.
* Pokud existuje pouze několik opatření pro všechny nebo většinu událostí, je lepší odeslat tyto míry jako samostatné vlastnosti v rámci stejného objektu. Jejich odeslání samostatně snižuje počet událostí a může zlepšit výkon dotazu, protože je třeba zpracovat méně událostí.

## <a name="column-flattening"></a>Sloučení sloupců

Během ingestování budou datové části, které obsahují vnořené objekty, sloučí te, takže název sloupce je jedna hodnota s vymezením.

* Například následující vnořené JSON:

   ```JSON
   "data": {
        "flow": 1.0172575712203979,
   },
   ```

   Stává `data_flow` se: při zploštění.

> [!IMPORTANT]
> * Azure Time Series Insights Preview`_`používá podtržítka ( ) pro vymezení sloupců.
> * Všimněte si rozdílu od obecné`.`dostupnosti, která místo toho používá období ( ).

Složitější scénáře jsou znázorněny níže.

#### <a name="example-1"></a>Příklad 1:

Následující scénář má dvě (nebo více) zařízení, která odesílají měření (signály): *průtok,* *tlak motorového oleje*, *teplota*a *vlhkost*.

Je tu jedna zpráva Azure IoT Hub odeslána, kde vnější pole obsahuje sdílenou část běžných hodnot dimenze (všimněte si dvě položky zařízení obsažené ve zprávě).

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

**Takeaways:**

* Příklad JSON má vnější pole, které používá data [instance časové řady](./time-series-insights-update-tsm.md#time-series-model-instances) ke zvýšení efektivity zprávy. I když se metadata zařízení Time Series Instance pravděpodobně nezmění, často poskytuje užitečné vlastnosti pro analýzu dat.

* JSON kombinuje dvě nebo více zpráv (jeden z každého zařízení) do jedné datové části šetří na šířku pásma v průběhu času.

* Jednotlivé datové body řady pro každé zařízení jsou sloučeny do jednoho atributu **řady,** který snižuje potřebu průběžně streamovat aktualizace pro každé zařízení.

> [!TIP]
> Chcete-li snížit počet zpráv potřebných k odesílání dat a zefektivnit telemetrii, zvažte dávkování společných hodnot dimenzí a metadat instance časové řady do jedné datové části JSON.

#### <a name="time-series-instance"></a>Instance časové řady 

Podívejme se blíže na to, jak používat [instanci časové řady](./time-series-insights-update-tsm.md#time-series-model-instances) k optimálnějšímu utváření svého JSON. 

> [!NOTE]
> ID [časové řady](./time-series-insights-update-how-to-id.md) níže jsou *deviceIds*.

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

Náhled přehledů časové řady se připojí k tabulce (po sloučení) během doby dotazu. Tabulka obsahuje další sloupce, například **Typ**.

| deviceId  | Typ | L1 | L2 | časové razítko | series_Flow Rychlost ft3/s | series_Engine tlak oleje psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | Simulátor | Bateriový systém | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34.7 |
| `FXXX` | Default_Type | Simulátor |   Bateriový systém |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | LINE_DATA SPOLEČNÉ | Simulátor |    Bateriový systém |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

> [!NOTE]
>  Předchozí tabulka představuje zobrazení dotazu v [Průzkumníku náhledu časové řady](./time-series-insights-update-explorer.md).

**Takeaways:**

* V předchozím příkladu jsou statické vlastnosti uloženy v náhledu Přehledy časových řad, aby se optimalizovala data odeslaná po síti.
* Data náhledu přehledů časové řady se připojují v době dotazu prostřednictvím ID časové řady, které je definováno v instanci.
* Používají se dvě vrstvy vnoření. Toto číslo je nejvíce, že Time Series Insights Náhled podporuje. Je důležité, aby se zabránilo hluboce vnořené pole.
* Vzhledem k tomu, že existuje několik opatření, jsou odesílány jako samostatné vlastnosti v rámci stejného objektu. V příkladu jsou jedinečné sloupce **series_Flow Hodnotit psi**, **series_Engine tlak oleje psi**a series_Flow **rychlost ft3/s.**

>[!IMPORTANT]
> Pole instancí nejsou uložena s telemetrií. Jsou uloženy s metadaty v modelu časové řady.

#### <a name="example-2"></a>Příklad 2:

Zvažte následující JSON:

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

Ve výše uvedeném příkladu `data["flow"]` by narovnáná vlastnost `data_flow` představovala kolizi názvů s vlastností.

V tomto případě *by nejnovější* hodnota vlastnosti přepsat předchozí. 

> [!TIP]
> Pro další pomoc kontaktujte tým Time Series Insights!

> [!WARNING] 
> * V případech, kdy duplicitní vlastnosti jsou k dispozici ve stejné (jednotné) datové části události z důvodu sloučení nebo jiný mechanismus, nejnovější hodnota vlastnosti > je uložena, přepsání všechny předchozí hodnoty.
> * Série kombinovaných událostí se vzájemně nepřepíší.

## <a name="next-steps"></a>Další kroky

* Chcete-li tyto pokyny uvést do praxe, přečtěte si [syntaxi dotazu Azure Time Series Insights Preview](./time-series-insights-query-data-csharp.md). Další informace o syntaxi dotazu pro [rozhraní](https://docs.microsoft.com/rest/api/time-series-insights/preview) REST API náhledu přehledů časové řady pro přístup k datům.

* Zkombinujte doporučené postupy JSON s [modelem řady Času](./time-series-insights-update-how-to-tsm.md).
