---
title: Azure Time Series Insights JSON – osvědčené postupy pro strukturování JSON v dotazech Azure Time Series Insights | Dokumentace Microsoftu
description: Zjistěte, jak ke zlepšení efektivity dotazů Azure Time Series Insights.
services: time-series-insights
author: ashannon7
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 05/24/2018
ms.author: anshan
ms.custom: seodec18
ms.openlocfilehash: 284bbf435c7940658753e7bbf1daff00a79d57a1
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53273843"
---
# <a name="how-to-shape-json-to-maximize-query-performance"></a>Jak tvarovat JSON pro zajištění maximálního výkonu dotazů 

Tento článek obsahuje pokyny pro úpravu JSON pro maximalizaci efektivity vašich dotazů Azure Time Series Insights (TSI).

## <a name="video"></a>Video: 

### <a name="in-this-video-we-cover-best-practices-around-shaping-json-to-meet-your-storage-needsbr"></a>V tomto videu si probereme osvědčené postupy tvarování JSON podle svých potřeb úložiště.</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>Osvědčené postupy

Je důležité zvážit způsob odesílání událostí do služby Time Series Insights. Konkrétně měli byste vždy:

1. co možná posílat data přes síť.
2. Zajistěte, aby že vaše data jsou uložená způsobem, který umožňuje provádět agregace, které jsou vhodné pro váš scénář.
3. zajištění není dosáhnete maximální vlastnost omezení TSI.
   - 600 vlastnosti (sloupce) pro prostředí S1.
   - 800 vlastnosti (sloupce) pro S2 prostředí.

Následující pokyny k pomáhá zajistit možné dotazy vracely co nejlepší:

1. Nepoužívejte dynamické vlastnosti, jako je například ID značky jako název vlastnosti, protože přispívá k dosažení limitu maximální vlastnosti.
2. Neposílat zbytečné vlastnosti. Pokud vlastnost dotazu není povinné, doporučujeme odeslat, a vyhněte se omezení úložiště.
3. Použití [odkazují na data](time-series-insights-add-reference-data-set.md), se odesílání statických dat přes síť.
4. Sdílení vlastností dimenze mezi více událostí k odesílání dat přes síť efektivněji.
5. Nepoužívejte vnoření hloubkové pole. TSI podporuje až dvě úrovně vnořená pole, které obsahují objekty. TSI pole ve zprávách, sloučí do více událostí pomocí dvojice vlastnost.
6. Pokud jenom pár opatření existovat pro všechny nebo většina události, je lepší odesílat tyto míry jako samostatné vlastnosti v rámci stejného objektu. Odesláním samostatně snižuje počet událostí a může být výkonnější jako menším počtem událostí musí být zpracovány dotazy. Po několika měr se odesílá jako hodnoty v jedné vlastnosti minimalizuje možnost dosažení maximální vlastnost limit.

## <a name="examples"></a>Příklady

Následující dva příklady znázorňují odesílání událostí, abyste měli na očích předchozí doporučení. Po každý příklad vidíte, jak byly použity doporučení.

V příkladech jsou založené na scénáři, kde více zařízení odesílají měření nebo signálů. Tok rychlost, modul ropy přetížení, teploty a vlhkosti, může být měření nebo signálů. V prvním příkladu existuje několik měření napříč všemi zařízeními. V druhém příkladu existuje mnoho zařízení a každou odesílá mnoho jedinečný měření.

### <a name="scenario-only-a-few-measurementssignals-exist"></a>Scénář: existuje pouze několik měření/signály

**Doporučení:** odeslat míry jako samostatný vlastnost nebo sloupec.

V následujícím příkladu je do jedné zprávy služby IoT Hub, kde vnější pole obsahuje sdílený oddíl běžných hodnot dimenzí. Vnější pole používá referenčních dat ke zvýšení efektivity zprávy. Referenční data obsahuje metadata zařízení, který nemění každou událost, ale poskytuje užitečné vlastnosti pro analýzu dat. Dávkování běžné hodnoty dimenze i využití referenčních dat, uloží v bajtech odeslaných přenosu, díky čemuž zprávy efektivnější.

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
                "Flow Rate psi": 0.58015072345733643,
                "Engine Oil Pressure psi ": 22.2
            }
        ]
    }
]
```

Referenční data tabulku (ID zařízení je klíčovou vlastnost):

| deviceId | ID zprávy | deviceLocation |
| --- | --- | --- |
| FXXX | ŘÁDEK\_DAT | EU |
| FRRR | ŘÁDEK\_DAT | USA |

Tabulka událostí doby Series Insights (po sloučení):

| deviceId | ID zprávy | deviceLocation | časové razítko | řada. Míra tok ft3/s | řada. Modul ropy tlak psi |
| --- | --- | --- | --- | --- | --- |
| FXXX | ŘÁDEK\_DAT | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
| FXXX | ŘÁDEK\_DAT | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
| FRRR | ŘÁDEK\_DAT | USA | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

Mějte na paměti následující v předchozím příkladu:

- **DeviceId** sloupec slouží jako záhlaví sloupce pro různá zařízení v park. Pokusu o vytvoření hodnoty deviceId svůj vlastní název vlastnosti by mají omezenou 595 (S1 prostředích) nebo 795 (prostředích S2), celkový počet zařízení s pět sloupců.

- Zbytečné vlastnosti se vyhnout, například informace o značku a model, atd. Protože nesmí být dotazována v budoucnu, jejich odstranění umožňuje lepší sítě a efektivitu úložiště.

- Referenční data slouží k omezení počtu bajtů přenesených v síti. Dva atributy **messageId** a **deviceLocation** , jsou spojeny pomocí klíčová vlastnost **deviceId**. Tato data je spojen s telemetrická data v době příchozího přenosu dat a následně uložená ve službě TSI pro dotazování.

- Používají se dvě vrstvy vnoření, což je maximální množství vnoření podporuje TSI. Je důležité, aby se zabránilo hluboce vnořených polí.

- Míry se odesílají jako samostatné vlastnosti v rámci stejného objektu, protože několika měr. Tady **řady. Tok míra psi** a **řady. Modul ropy tlak ft3 za sekundu** jedinečných sloupců.

### <a name="scenario-several-measures-exist"></a>Scénář: existuje několik opatření

**Doporučení:** posílat měření jako "type", "jednotka", "value" řazených kolekcí členů.

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

Referenční Data (klíče vlastnosti se ID zařízení a series.tagId):

| deviceId | series.tagId | ID zprávy | deviceLocation | type | jednotka |
| --- | --- | --- | --- | --- | --- |
| FXXX | pumpRate | ŘÁDEK\_DAT | EU | Míra toku | ft3/s |
| FXXX | oilPressure | ŘÁDEK\_DAT | EU | Modul ropy tlaku | psi |
| FRRR | pumpRate | ŘÁDEK\_DAT | USA | Míra toku | ft3/s |
| FRRR | oilPressure | ŘÁDEK\_DAT | USA | Modul ropy tlaku | psi |

Tabulka událostí doby Series Insights (po sloučení):

| deviceId | series.tagId | ID zprávy | deviceLocation | type | jednotka | časové razítko | Series.Value |
| --- | --- | --- | --- | --- | --- | --- | --- |
| FXXX | pumpRate | ŘÁDEK\_DAT | EU | Míra toku | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 |
| FXXX | oilPressure | ŘÁDEK\_DAT | EU | Modul ropy tlaku | psi | 2018-01-17T01:17:00Z | 34.7 |
| FXXX | pumpRate | ŘÁDEK\_DAT | EU | Míra toku | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 |
| FXXX | oilPressure | ŘÁDEK\_DAT | EU | Modul ropy tlaku | psi | 2018-01-17T01:17:00Z | 49.2 |
| FRRR | pumpRate | ŘÁDEK\_DAT | USA | Míra toku | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
| FRRR | oilPressure | ŘÁDEK\_DAT | USA | Modul ropy tlaku | psi | 2018-01-17T01:18:00Z | 22.2 |

Mějte na paměti následující v předchozím příkladu a podobně jako v prvním příkladu:

- sloupce **deviceId** a **series.tagId** sloužit jako záhlaví sloupců pro různá zařízení a klíčových slov do park. Pomocí všech jako vlastní atribut by mají omezenou dotaz 594 (S1 prostředích) nebo 794 (prostředích S2) Celkový počet zařízení s šest sloupců.

- z důvodů uvedených v prvním příkladu se vyhnout zbytečné vlastnosti.

- referenční data slouží k omezení počtu bajtů přenesených v síti Představujeme **deviceId**, jedinečné dvojice **messageId** a **deviceLocation**. Je použit složený klíč, **series.tagId**, jedinečné dvojice **typ** a **jednotku**. Složený klíč umožňuje aplikaci **deviceId** a **series.tagId** pár se použije k odkazování na čtyři hodnoty: **zadejte ID zprávy, deviceLocation,** a **jednotky** . Tato data je spojen s telemetrická data v době příchozího přenosu dat a následně uložená ve službě TSI pro dotazování.

- z důvodů uvedených v prvním příkladu se používají dvě vrstvy vnoření.

### <a name="for-both-scenarios"></a>Pro oba scénáře

Pokud máte vlastnosti s velkým množstvím možných hodnot, doporučujeme odeslat jako různé hodnoty v rámci jednoho sloupce a nevytvářejte nový sloupec pro každou hodnotu. V předchozích dvou příkladech:
  - V prvním příkladu se několik vlastností, které mají několik hodnot, takže je vhodné provádět každý samostatný vlastnost. 
  - V druhém příkladu, ale uvidíte, že opatření nejsou zadány jako jednotlivé vlastnosti, ale spíše, pole hodnoty nebo míry v části Obecné vlastnosti řady. Posílá se nový klíč, **vrátí parametr tagId** , která vytvoří nový sloupec, **series.tagId** ve sloučené tabulce. Vytvoření nové vlastnosti **typ** a **jednotky**pomocí referenčních dat, proto vlastnost limit brání se přístupů.

## <a name="next-steps"></a>Další postup

Tyto pokyny převést do praxe, naleznete v tématu [syntaxe dotazu Azure Time Series Insights](/rest/api/time-series-insights/time-series-insights-reference-query-syntax) získat další informace o syntaxi dotazu pro rozhraní REST API pro přístup k datům služby TSI.