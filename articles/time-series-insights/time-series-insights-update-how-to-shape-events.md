---
title: Jak události obrazec s Azure Time Series Insights (preview) | Dokumentace Microsoftu
description: Pochopení způsobu, jak tvarovat události s Azure Time Series Insights (preview)
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/30/2018
ms.openlocfilehash: edc1dac05a8ab4281eee3ee0eb4c5e6b7571b404
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52855785"
---
# <a name="shaping-events-with-azure-time-series-insights-preview"></a>Strukturování události s Azure Time Series Insights (preview)

Tento článek obsahuje pokyny pro úpravu JSON, pro maximalizaci efektivity jste už dotazů Azure Time Series Insights (preview).

## <a name="best-practices"></a>Osvědčené postupy

> [!NOTE]
> Omezení vlastnost 600 800 S1/s2 pro Azure TSI (preview) se nevztahují.

Je důležité zvážit způsob odesílání událostí do Azure TSI. Konkrétně měli byste vždy:

1. co možná posílat data přes síť.
1. Zajistěte, aby že vaše data jsou uložená způsobem, který umožňuje provádět agregace, které jsou vhodné pro váš scénář.

Následující pokyny k pomáhá zajistit možné dotazy vracely co nejlepší:

1. Neposílat zbytečné vlastnosti. TSI (preview) vám bude účtovat na vaše využití a je osvědčeným postupem je ukládat a zpracovávat data, která se bude dotazovat.
1. Pomocí pole instancí pro statická data se odesílání statických dat přes síť. Pole instancí, součástí modelu časové řady fungují jako referenční data v obecně dostupné služby TSI. Další informace o pole instance, [čas řady modely](./time-series-insights-update-tsm.md).
1. Sdílení vlastností dimenze mezi více událostí k odesílání dat přes síť efektivněji.
1. Nepoužívejte vnoření hloubkové pole. TSI podporuje až dvě úrovně vnořená pole, které obsahují objekty. TSI pole ve zprávách, sloučí do více událostí pomocí dvojice vlastnost.
1. Pokud jenom pár opatření existovat pro všechny nebo většina události, je lepší odesílat tyto míry jako samostatné vlastnosti v rámci stejného objektu. Odesláním samostatně snižuje počet událostí a může být výkonnější jako menším počtem událostí musí být zpracovány dotazy.

## <a name="example"></a>Příklad:

V příkladu je založené na scénářích kde více zařízení odesílají měření nebo signálů. Měření nebo signálů může být **tok míra**, **modul ropy tlak**, **teploty**, a **vlhkosti**.

V následujícím příkladu je do jedné zprávy služby IoT Hub, kde vnější pole obsahuje sdílený oddíl běžných hodnot dimenzí. Vnější pole používá data Instance řady čas ke zvýšení efektivity zprávy. Instance řady času obsahuje metadata zařízení, který nemění každou událost, ale poskytuje užitečné vlastnosti pro analýzu dat. Dávkování běžné hodnoty dimenze i využití Instance řady čas metadata, uloží na přenosu, díky čemuž efektivnější zprávy odeslané bajty.

Příklad datové části JSON:

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
                "Flow Rate psi": 0.58015072345733643,
                "Engine Oil Pressure psi ": 22.2
            }
        ]
    }
]
```

Time Series Instance (Poznámka: **ID řady času** je *deviceId*):

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

TSI připojené tabulky (po sloučení) v době dotazu. V tabulce bude obsahovat další sloupce, jako je například typ. Tento příklad ukazuje, jak můžete tvarovat telemetrická data:

| deviceId  | Typ | L1 | L2 | časové razítko | řada. Míra tok ft3/s | řada. Modul ropy tlak psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SIMULÁTOR REVOLT | Systémové baterie | 2018-01-17T01:17:00Z |    1.0172575712203979 |    34.7 |
| `FXXX` | LINE_DATA REVOLT | SIMULÁTOR |    Systémové baterie |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | BĚŽNÉ LINE_DATA | SIMULÁTOR |    Systémové baterie |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

Mějte na paměti následující v předchozím příkladu:

* Statické vlastnosti se ukládají jako TSI k optimalizaci dat posílaných prostřednictvím sítě.
* TSI Data je spojena v době zpracování dotazu *timeSeriesId* definovány v instanci.
* Používají se dvě vrstvy vnoření, což je maximální množství vnoření podporuje TSI. Je důležité, aby se zabránilo hluboce vnořených polí.
* Míry se odesílají jako samostatné vlastnosti v rámci stejného objektu, protože několika měr. Tady **řady. Tok míra psi**, řady **modul ropy tlak**, a **ft3 za sekundu** jedinečných sloupců.

>[!IMPORTANT]
> Pole instancí nejsou uložené pomocí telemetrie, ukládají se metadata v **modelu časové řady**.
> Výše uvedené tabulce představuje zobrazení dotazu.

## <a name="next-steps"></a>Další postup

Tyto pokyny převést do praxe, naleznete v tématu [syntaxe dotazů Azure TSI](./time-series-insights-query-data-csharp.md) získat další informace o syntaxi dotazu pro rozhraní REST API pro přístup k datům služby TSI.
