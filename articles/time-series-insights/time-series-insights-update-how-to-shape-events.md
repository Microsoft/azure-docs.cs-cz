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
ms.date: 12/16/2019
ms.custom: seodec18
ms.openlocfilehash: 567770c00c645aeb79e1efb0e9119b9ac829f3fe
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2020
ms.locfileid: "75861655"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Tvar událostí pomocí Azure čas Series Insights ve verzi Preview

Tento článek vám pomůže tvarovat soubor JSON pro ingestování a maximalizovat efektivitu vašich Azure Time Series Insights dotazů ve verzi Preview.

## <a name="best-practices"></a>Osvědčené postupy

Zamyslete se nad tím, jak odesíláte události do Time Series Insights ve verzi Preview. Konkrétně měli byste vždy:

* co možná posílat data přes síť.
* Store vaše data způsobem, který umožňuje agregovat více vhodnou pro váš scénář.

Pro nejlepší výkon dotazů udělejte toto:

* Neposílat zbytečné vlastnosti. Čas Series Insights ve verzi Preview účtuje poplatky na použití. Doporučujeme ukládat a zpracovávat data, která budete dotazovat.
* Pomocí pole instancí pro statická data. Tento postup umožňuje vyhnout se odesílat statická data přes síť. Pole instancí – komponenta modelu časové řady, která funguje jako referenční data ve službě Time Series Insights všeobecně dostupná. Další informace o polích instance získáte v [modelu časové řady](./time-series-insights-update-tsm.md)pro čtení.
* Sdílení vlastností dimenze mezi dva nebo více událostí. Tento postup pomáhá efektivněji posílat data přes síť.
* Nepoužívejte vnoření hloubkové pole. Time Series Insights Preview podporuje až dvě úrovně vnořených polí, která obsahují objekty. Čas Series Insights ve verzi Preview sloučí pole zpráv do více událostí pomocí dvojice vlastnost.
* Pokud jenom pár opatření existovat pro všechny nebo většina události, je lepší odesílat tyto míry jako samostatné vlastnosti v rámci stejného objektu. Jejich odeslání samostatně snižuje počet událostí a může zlepšit výkon dotazů, protože je potřeba zpracovat méně událostí.

V průběhu příjmu budou datové části obsahující vnořování shrnuty tak, že název sloupce je jediná hodnota s použitím oddělovače. Time Series Insights Preview používá pro deline podtržítka. Všimněte si, že se jedná o změnu z verze GA produktu, který používal tečky. Během období Preview dochází k upozorněním na sloučení, které je znázorněno v druhém příkladu níže.

## <a name="examples"></a>Příklady

Následující příklad je založen na scénář, kde dva nebo více zařízení odesílají měření nebo signálů. Měření nebo signály mohou být *průtokové rychlosti*, *tlak v oleji*, *teplota*a *vlhkost*v motoru.

V tomto příkladu je k dispozici jedna zpráva Azure IoT Hub, kde vnější pole obsahuje sdílený oddíl běžných hodnot dimenzí. Vnější pole používá data Instance řady čas ke zvýšení efektivity zprávy. 

Instance časové řady obsahuje metadata zařízení. Tato metadata se nemění u každé události, ale poskytuje užitečné vlastnosti pro analýzu dat. Pokud chcete ušetřit bajty odeslané přes vodič a zvýšit efektivitu zprávy, zvažte dávkování běžných hodnot dimenzí a používání metadat instance časových řad.

### <a name="example-1"></a>Příklad 1:

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

### <a name="time-series-instance"></a>Instance řady čas 

> [!NOTE]
> Je čas řady ID *deviceId*.

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

Čas Series Insights ve verzi Preview spojuje tabulku (po sloučení) během doby dotazu. Tabulka obsahuje další sloupce, jako například **typ**. Následující příklad ukazuje, jak můžete [tvarovat](./time-series-insights-send-events.md#supported-json-shapes) data telemetrie.

| deviceId  | Typ | L1 | L2 | časové razítko | series_Flow ft3 míry/s | vyseries_Engine psí tlak v oleji |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SIMULÁTOR | Systémové baterie | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34.7 |
| `FXXX` | Default_Type | SIMULÁTOR |   Systémové baterie |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | BĚŽNÉ LINE_DATA | SIMULÁTOR |    Systémové baterie |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

V předchozím příkladu mějte na paměti následující body:

* Statické vlastnosti se ukládají v čase Series Insights ve verzi Preview pro optimalizaci dat posílaných prostřednictvím sítě.
* Data ve verzi Preview Time Series Insights jsou připojena v době dotazu prostřednictvím ID časové řady, které je definováno v instanci.
* Používají se dvě vrstvy vnoření. Toto číslo je maximum, které podporuje Time Series Insights Preview. Je důležité, aby se zabránilo hluboce vnořených polí.
* Protože existuje pár opatření, odeslali jste jako samostatné vlastnosti v rámci stejného objektu. V příkladu **series_Flow psi**, **series_Engine psí tlak v olejích**a **series_Flow sazba ft3/s** jsou jedinečné sloupce.

>[!IMPORTANT]
> Pole instance nejsou uložena s telemetrie. Ukládají se s metadaty v modelu časové řady.
> V předchozí tabulce představuje zobrazení dotazu.

### <a name="example-2"></a>Příklad 2:

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
V předchozím příkladu by vlastnost plochých `data_flow` představovala kolize názvů s vlastností `data_flow`. V tomto případě by *Poslední* hodnota vlastnosti přepsala předchozí. Pokud toto chování představuje výzvu pro vaše obchodní scénáře, obraťte se prosím na tým TSI.

> [!WARNING] 
> V případech, kdy jsou duplicitní vlastnosti přítomny ve stejné datové části události z důvodu sloučení nebo jiného mechanismu, je uložena poslední hodnota vlastnosti, overwritting se všechny předchozí hodnoty.


## <a name="next-steps"></a>Další kroky

- Pokud chcete tyto pokyny do praxe přidat, přečtěte si [syntaxi dotazu Azure Time Series Insights Preview](./time-series-insights-query-data-csharp.md). Přečtěte si další informace o syntaxi dotazů pro Time Series Insights Preview REST API pro přístup k datům.
- Další informace o podporovaných tvarech JSON najdete v článku [podporované tvary JSON](./time-series-insights-send-events.md#supported-json-shapes).
