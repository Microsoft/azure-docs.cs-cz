---
title: Obrazce událostí pomocí Azure čas Series Insights ve verzi Preview | Dokumentace Microsoftu
description: Pochopit, jak tvarovat událostí pomocí Azure čas Series Insights ve verzi Preview.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: f0e1a79073596dcabfacb7163e12b33bb582b7c3
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66238914"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Tvar událostí pomocí Azure čas Series Insights ve verzi Preview

Tento článek pomáhá utvářet souboru JSON pro maximalizaci efektivity vašich dotazů Azure čas Series Insights ve verzi Preview.

## <a name="best-practices"></a>Osvědčené postupy

Představte si, jak odesílat události do čas Series Insights ve verzi Preview. Konkrétně měli byste vždy:

* co možná posílat data přes síť.
* Store vaše data způsobem, který umožňuje agregovat více vhodnou pro váš scénář.

Pro zajištění nejlepšího výkonu dotazů postupujte takto:

* Neposílat zbytečné vlastnosti. Čas Series Insights ve verzi Preview účtuje poplatky na použití. Doporučujeme ukládat a zpracovávat data, která budete dotazovat.
* Pomocí pole instancí pro statická data. Tento postup umožňuje vyhnout se odesílat statická data přes síť. Pole instancí, součástí modelu časové řady pracovat jako referenční data v Time Series Insights obecně dostupné služby. Další informace o poli instance najdete v tématu [čas řady modely](./time-series-insights-update-tsm.md).
* Sdílení vlastností dimenze mezi dva nebo více událostí. Tento postup pomáhá efektivněji posílat data přes síť.
* Nepoužívejte vnoření hloubkové pole. Čas Series Insights ve verzi Preview podporuje až dvě úrovně vnořená pole, které obsahují objekty. Čas Series Insights ve verzi Preview sloučí pole zpráv do více událostí pomocí dvojice vlastnost.
* Pokud jenom pár opatření existovat pro všechny nebo většina události, je lepší odesílat tyto míry jako samostatné vlastnosti v rámci stejného objektu. Odesláním samostatně snižuje počet událostí a může zlepšit výkon dotazů, protože je třeba zpracovat méně událostí.

## <a name="example"></a>Příklad:

Následující příklad je založen na scénář, kde dva nebo více zařízení odesílají měření nebo signálů. Měření nebo signály mohou být *tok míra*, *modul ropy tlak*, *teploty*, a *vlhkosti*.

V následujícím příkladu je do jedné zprávy služby Azure IoT Hub, kde vnější pole obsahuje sdílený oddíl běžných hodnot dimenzí. Vnější pole používá data Instance řady čas ke zvýšení efektivity zprávy. Instance řady času obsahuje metadata zařízení, která se nemění podle každé události, ale nabízí užitečné vlastnosti pro analýzu dat. Pokud chcete uložit v bajtech odeslaných přenosu a zefektivnit zprávy, vezměte v úvahu dávkování běžné hodnoty dimenze a použití Instance řady čas metadat.

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

### <a name="time-series-instance"></a>Instance řady čas 
> [!NOTE]
> Je čas řady ID *deviceId*.

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

Čas Series Insights ve verzi Preview spojuje tabulku (po sloučení) během doby dotazu. Tabulka obsahuje další sloupce, jako například **typ**. Následující příklad ukazuje, jak můžete [tvar](./time-series-insights-send-events.md#json) vaše telemetrická data.

| deviceId  | Typ | L1 | L2 | časové razítko | řada. Míra tok ft3/s | řada. Modul ropy tlak psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SIMULÁTOR | Systémové baterie | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34.7 |
| `FXXX` | Default_Type | SIMULÁTOR |   Systémové baterie |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | BĚŽNÉ LINE_DATA | SIMULÁTOR |    Systémové baterie |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

V předchozím příkladu mějte na paměti následující body:

* Statické vlastnosti se ukládají v čase Series Insights ve verzi Preview pro optimalizaci dat posílaných prostřednictvím sítě.
* Data časových řad Insights ve verzi Preview je spojena v době zpracování dotazu ID řady čas, který je definován v instanci.
* Používají se dvě vrstvy vnoření, což je maximum, který podporuje čas Series Insights ve verzi Preview. Je důležité, aby se zabránilo hluboce vnořených polí.
* Protože existuje pár opatření, odeslali jste jako samostatné vlastnosti v rámci stejného objektu. V tomto příkladu **řady. Tok míra psi**, **řady. Modul ropy tlak psi**, a **řady. Tok ft3 sazba za sekundu** jedinečných sloupců.

>[!IMPORTANT]
> Pole instancí nejsou uložené s telemetrií. Jsou uloženy s metadaty v **modelu časové řady**.
> V předchozí tabulce představuje zobrazení dotazu.

## <a name="next-steps"></a>Další postup

- Tyto pokyny převést do praxe, naleznete v tématu [syntaxe dotazů Azure čas Series Insights ve verzi Preview](./time-series-insights-query-data-csharp.md). Dozvíte se, že informace o syntaxi dotazů pro data, času Series Insights ve verzi Preview přístup k rozhraní REST API.
- Další informace o podporované tvary JSON najdete v tématu [tvary JSON nepodporuje](./time-series-insights-send-events.md#json).
