---
title: Osvědčené postupy pro strukturování JSON v dotazech Azure Time Series Insights | Dokumentace Microsoftu
description: Zjistěte, jak ke zlepšení efektivity dotazů Azure Time Series Insights.
services: time-series-insights
author: ashannon7
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 05/09/2019
ms.author: dpalled
ms.custom: seodec18
ms.openlocfilehash: 089285637bb740fea47f1fd07de0906dfe46662b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66244462"
---
# <a name="shape-json-to-maximize-query-performance"></a>Tvar JSON pro zajištění maximálního výkonu dotazů 

Tento článek obsahuje pokyny o tom, jak tvaru JSON pro maximalizaci efektivity vašich dotazů Azure Time Series Insights.

## <a name="video"></a>Video

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>Přečtěte si osvědčené postupy pro strukturování JSON podle svých potřeb úložiště.</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>Osvědčené postupy
Představte si, jak odesílat události do služby Time Series Insights. Konkrétně můžete vždy:

1. co možná posílat data přes síť.
1. Zajistěte, aby že vaše data uložená způsobem tak, aby bylo možné provádět agregace, které jsou vhodné pro váš scénář.
1. Ujistěte se, že není narazíte na omezení maximální vlastnost Time Series Insights:
   - 600 vlastnosti (sloupce) pro prostředí S1.
   - 800 vlastnosti (sloupce) pro S2 prostředí.

Následující pokyny k pomáhá zajistit, je to možné dotazy vracely co nejlepší:

1. Nepoužívejte dynamické vlastnosti, jako je například Identifikátor značky, jako název vlastnosti. Toto použití přispívá k dosažení limitu maximální vlastnosti.
1. Neposílat zbytečné vlastnosti. Pokud vlastnost dotazu není povinný, je nejlepší odesláním. Tímto způsobem se vyhnete omezení úložiště.
1. Použití [odkazují na data](time-series-insights-add-reference-data-set.md) se odesílání statických dat přes síť.
1. Sdílení vlastností dimenze mezi více událostí k odesílání dat přes síť efektivněji.
1. Nepoužívejte vnoření hloubkové pole. Time Series Insights podporuje až dvě úrovně vnořená pole, které obsahují objekty. Time Series Insights sloučí do více událostí pomocí vlastností dvojice pole ve zprávách.
1. Pokud jenom pár opatření existovat pro všechny nebo většina události, je lepší odesílat tyto míry jako samostatné vlastnosti v rámci stejného objektu. Odesláním samostatně snižuje počet událostí a může zlepšit výkon dotazů, protože je třeba zpracovat méně událostí. Po několika měr se odesílá jako hodnoty v jedné vlastnosti minimalizuje možnost dosažení limitu maximální vlastnost.

## <a name="example-overview"></a>Základní informace o ukázkovém

Následující dva příklady ukazují, jak odesílat události, abyste měli na očích předchozí doporučení. Po každý příklad vidíte, jak byly použity doporučení.

V příkladech jsou založené na scénáři, kde více zařízení odesílají měření nebo signálů. Tok rychlost, modul ropy přetížení, teploty a vlhkosti, může být měření nebo signálů. V prvním příkladu existuje několik měření napříč všemi zařízeními. Druhý příklad má mnoho zařízení a jednotlivá zařízení odesílá mnoho jedinečný měření.

## <a name="scenario-one-only-a-few-measurements-exist"></a>Scénář č. 1: Existuje pouze několik měření

> [!TIP]
> Doporučujeme odeslat každý měření nebo signál jako samostatné property nebo column.

V následujícím příkladu je do jedné zprávy služby Azure IoT Hub, kde vnější pole obsahuje sdílený oddíl běžných hodnot dimenzí. Vnější pole používá referenčních dat ke zvýšení efektivity zprávy. Referenční data obsahuje metadata zařízení, která se nemění podle každé události, ale pro analýzu dat poskytuje vhodné vlastnosti. Dávkování běžné hodnoty dimenze a použití odkaz, který data uloží v bajtech odeslaných přenosu, který představuje zprávu efektivnější.

Příklad datové části JSON:

```json
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

* Referenční tabulky dat, který má klíčovou vlastnost **deviceId**:

   | deviceId | messageId | deviceLocation |
   | --- | --- | --- |
   | FXXX | ŘÁDEK\_DAT | EVROPA |
   | FRRR | ŘÁDEK\_DAT | USA |

* Series Insights události tabulka doby, po sloučení:

   | deviceId | messageId | deviceLocation | timestamp | řada. Míra tok ft3/s | řada. Modul ropy tlak psi |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | ŘÁDEK\_DAT | EVROPA | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
   | FXXX | ŘÁDEK\_DAT | EVROPA | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
   | FRRR | ŘÁDEK\_DAT | USA | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

Poznámky k těmito dvěma tabulkami:

- **DeviceId** sloupec slouží jako záhlaví sloupce pro různá zařízení v park. Aby se ID zařízení hodnota názvu vlastnosti omezuje celkový počet zařízení, která chcete 595 (pro S1 prostředí) nebo 795 (pro S2 prostředí) s 5 sloupci.
- Příklad, značku a model informace jsou vyhnout zbytečné vlastnosti. Protože vlastnosti nesmí být dotazována v budoucnu, jejich odstranění umožňuje lepší sítě a efektivitu úložiště.
- Referenční data slouží k omezení počtu bajtů přenesených v síti. Dva atributy **messageId** a **deviceLocation** se propojují s použitím klíčová vlastnost **deviceId**. Tato data se propojuje se telemetrická data v době příchozího přenosu dat a pak uloženy v Time Series Insights pro dotazování.
- Používají se dvě vrstvy vnoření, což je maximální množství vnoření podporuje Time Series Insights. Je důležité, aby se zabránilo hluboce vnořených polí.
- Míry jsou odeslat jako samostatné vlastnosti v rámci stejného objektu, protože několika měr. Tady **řady. Tok míra psi** a **řady. Modul ropy tlak ft3 za sekundu** jedinečných sloupců.

## <a name="scenario-two-several-measures-exist"></a>Scénář č. 2: Existuje několik míry

> [!TIP]
> Doporučujeme, abyste odeslání měření psaní"," "jednotka" a "hodnota" řazených kolekcí členů.

Příklad datové části JSON:

```json
[
    {
        "deviceId": "FXXX",
        "timestamp": "2018-01-17T01:17:00Z",
        "series": [
            {
                "tagId": "pumpRate",
                "value": 1.0172575712203979
            },
            {
                "tagId": "oilPressure",
                "value": 49.2
            },
            {
                "tagId": "pumpRate",
                "value": 2.445906400680542
            },
            {
                "tagId": "oilPressure",
                "value": 34.7
            }
        ]
    },
    {
        "deviceId": "FYYY",
        "timestamp": "2018-01-17T01:18:00Z",
        "series": [
            {
                "tagId": "pumpRate",
                "value": 0.58015072345733643
            },
            {
                "tagId": "oilPressure",
                "value": 22.2
            }
        ]
    }
]
```

* Referenční tabulky dat, který obsahuje vlastnosti klíče **deviceId** a **series.tagId**:

   | deviceId | series.tagId | messageId | deviceLocation | type | Jednotka |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | ŘÁDEK\_DAT | EVROPA | Míra toku | ft3/s |
   | FXXX | oilPressure | ŘÁDEK\_DAT | EVROPA | Modul ropy tlaku | psi |
   | FRRR | pumpRate | ŘÁDEK\_DAT | USA | Míra toku | ft3/s |
   | FRRR | oilPressure | ŘÁDEK\_DAT | USA | Modul ropy tlaku | psi |

* Series Insights události tabulka doby, po sloučení:

   | deviceId | series.tagId | messageId | deviceLocation | type | Jednotka | timestamp | Series.Value |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | ŘÁDEK\_DAT | EVROPA | Míra toku | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 | 
   | FXXX | oilPressure | ŘÁDEK\_DAT | EVROPA | Modul ropy tlaku | psi | 2018-01-17T01:17:00Z | 34.7 |
   | FXXX | pumpRate | ŘÁDEK\_DAT | EVROPA | Míra toku | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 | 
   | FXXX | oilPressure | ŘÁDEK\_DAT | EVROPA | Modul ropy tlaku | psi | 2018-01-17T01:17:00Z | 49.2 |
   | FRRR | pumpRate | ŘÁDEK\_DAT | USA | Míra toku | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
   | FRRR | oilPressure | ŘÁDEK\_DAT | USA | Modul ropy tlaku | psi | 2018-01-17T01:18:00Z | 22.2 |

Poznámky k těmito dvěma tabulkami:

- Sloupce **deviceId** a **series.tagId** sloužit jako záhlaví sloupců pro různá zařízení a klíčových slov do park. Pomocí všech jako vlastní atribut omezuje dotaz 594 (pro S1 prostředí) nebo 794 (pro S2 prostředí) Celkový počet zařízení s šest sloupců.
- z důvodů uvedených v prvním příkladu se vyhnout zbytečné vlastnosti.
- Referenční data slouží k omezení počtu bajtů přenesených v síti Představujeme **deviceId**, který se používá pro jedinečnou dvojici **messageId** a **deviceLocation**. Složený klíč **series.tagId** se používá pro jedinečnou dvojici **typ** a **jednotky**. Složený klíč umožňuje aplikaci **deviceId** a **series.tagId** pár, který se má použít k odkazování na čtyři hodnoty: **zadejte ID zprávy, deviceLocation,** a **jednotky**. Tato data se propojuje se telemetrická data v době příchozího přenosu. Pak uloží se v Time Series Insights pro dotazování.
- z důvodů uvedených v prvním příkladu se používají dvě vrstvy vnoření.

### <a name="for-both-scenarios"></a>Pro oba scénáře

Pro vlastnost s velkým množstvím možných hodnot doporučujeme odeslat jako různé hodnoty v rámci jednoho sloupce místo vytvoření nového sloupce pro každou hodnotu. V předchozích dvou příkladech:

  - V prvním příkladu mají několik vlastností několik hodnot, proto je vhodný pro každé nastavení samostatné vlastnosti.
  - V druhém příkladu opatření nejsou zadány jako jednotlivé vlastnosti. Místo toho jsou pole hodnoty nebo míry v části Obecné vlastnosti řady. Nový klíč **vrátí parametr tagId** se odešle, která vytvoří nový sloupec **series.tagId** ve sloučené tabulce. Nové vlastnosti **typ** a **jednotky** jsou vytvořeny pomocí referenčních dat tak, aby se dosáhne maximálního vlastnost.

## <a name="next-steps"></a>Další postup

- Čtení [syntaxe dotazu Azure Time Series Insights](/rest/api/time-series-insights/ga-query-syntax) získat další informace o syntaxi dotazu pro rozhraní REST API pro přístup k datům služby Time Series Insights.
- Přečtěte si [jak události obrazce](./time-series-insights-send-events.md).
