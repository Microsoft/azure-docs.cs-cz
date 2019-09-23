---
title: Osvědčené postupy pro tvarování JSON v Azure Time Series Insights dotazy | Microsoft Docs
description: Přečtěte si, jak vylepšit efektivitu dotazů Azure Time Series Insights.
services: time-series-insights
author: ashannon7
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 08/09/2019
ms.author: dpalled
ms.custom: seodec18
ms.openlocfilehash: 48e09a64812f7552bd79c529138db693df283790
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947151"
---
# <a name="shape-json-to-maximize-query-performance"></a>Formát JSON obrazce pro maximalizaci výkonu dotazů 

V tomto článku najdete pokyny k tomu, jak můžete tvarovat JSON a maximalizovat efektivitu vašich Azure Time Series Insights dotazů.

## <a name="video"></a>Video

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>Naučte se osvědčené postupy pro tvarování formátu JSON, abyste splnili požadavky na úložiště.</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>Osvědčené postupy
Zamyslete se nad tím, jak odesíláte události do Time Series Insights. Konkrétně je to vždy:

1. co možná posílat data přes síť.
1. Ujistěte se, že vaše data jsou ukládána způsobem, aby bylo možné provádět agregace vhodné pro váš scénář.
1. Ujistěte se, že nedosáhnete Time Series Insights maximálních limitů vlastností pro:
   - 600 vlastnosti (sloupce) pro prostředí S1.
   - 800 vlastnosti (sloupce) pro prostředí S2

Následující doprovodné materiály pomáhají zajistit nejlepší možný výkon dotazů:

1. Nepoužívejte dynamické vlastnosti, jako je například ID značky, jako název vlastnosti. Toto použití přispívá k dosažení maximálního limitu vlastností.
1. Neposílat zbytečné vlastnosti. Pokud se vlastnost dotazu nepožaduje, je vhodné ji Neodesílat. Tímto způsobem se vyhnete omezením úložiště.
1. Použijte [referenční data](time-series-insights-add-reference-data-set.md) , abyste se vyhnuli posílání statických dat přes síť.
1. Sdílejte vlastnosti dimenze mezi více událostmi, aby bylo možné data v síti rychleji posílat efektivněji.
1. Nepoužívejte vnoření hloubkové pole. Time Series Insights podporuje až dvě úrovně vnořených polí, která obsahují objekty. Time Series Insights sloučí pole ve zprávách do více událostí s páry hodnot vlastností.
1. Pokud jenom pár opatření existovat pro všechny nebo většina události, je lepší odesílat tyto míry jako samostatné vlastnosti v rámci stejného objektu. Jejich odeslání samostatně snižuje počet událostí a může zlepšit výkon dotazů, protože je potřeba zpracovat méně událostí. Když existuje několik měr, jejich odeslání jako hodnot do jedné vlastnosti minimalizuje možnost dosažení maximálního limitu vlastností.

## <a name="example-overview"></a>Příklad přehledu

Následující dva příklady ukazují, jak odeslat události pro zvýraznění předchozích doporučení. Podle každého příkladu uvidíte, jak se doporučení používala.

Příklady jsou založeny na scénáři, ve kterém více zařízení odesílá měření nebo signály. Měření nebo signály mohou být průtokové rychlosti, tlak v oleji, teplota a vlhkost v motoru. V prvním příkladu je několik měření napříč všemi zařízeními. Druhý příklad obsahuje mnoho zařízení a každé zařízení odesílá mnoho jedinečných měření.

## <a name="scenario-one-only-a-few-measurements-exist"></a>Scénář 1: Existuje pouze několik měření.

> [!TIP]
> Doporučujeme odeslat každé měření nebo signál jako samostatnou vlastnost nebo sloupec.

V následujícím příkladu je k dispozici jedna zpráva Azure IoT Hub, kde vnější pole obsahuje sdílený oddíl běžných hodnot dimenzí. Vnější pole používá referenční data ke zvýšení efektivity zprávy. Referenční data obsahují metadata zařízení, která se při každé události nemění, ale poskytují užitečné vlastnosti pro analýzu dat. Dávkování běžných hodnot dimenzí a využívání referenčních dat se ukládá v bajtech odesílaných po síti, což usnadňuje zpracování zprávy.

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

* Tabulka referenčních dat, která má klíčovou vlastnost **deviceId**:

   | deviceId | messageId | deviceLocation |
   | --- | --- | --- |
   | FXXX | LINE\_DATA | EU |
   | FYYY | LINE\_DATA | US |

* Time Series Insights tabulka událostí po sloučení:

   | deviceId | messageId | deviceLocation | timestamp | řada. Míra tok ft3/s | řada. Modul ropy tlak psi |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | DATA\_ŘÁDKU | EVROPA | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
   | FXXX | DATA\_ŘÁDKU | EVROPA | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
   | FYYY | DATA\_ŘÁDKU | USA | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

Poznámky k těmto dvěma tabulkám:

- Sloupec **deviceId** slouží jako záhlaví sloupce pro různá zařízení v rámci loďstva. Když hodnota deviceId nastaví svůj název vlastní vlastnosti, omezí se celkový počet zařízení na 595 (pro prostředí S1) nebo 795 (pro prostředí S2) s dalšími pěti sloupci.
- Nepotřebné vlastnosti jsou zabráněno, například informace o značkaech a modelu. Vzhledem k tomu, že se vlastnosti v budoucnu nedotazují, jejich vyloučení umožní lepší efektivitu sítě a úložiště.
- Referenční data se používají ke snížení počtu bajtů přenesených přes síť. Dva atributy **MessageID** a **deviceLocation** jsou spojeny pomocí **deviceId**vlastnosti klíče. Tato data jsou propojena s daty telemetrie v době vstupu a jsou pak uložena v Time Series Insights pro dotazování.
- Používají se dvě vrstvy vnoření, což je maximální množství vnořování podporovaného Time Series Insights. Je důležité, aby se zabránilo hluboce vnořených polí.
- Míry jsou odesílány jako samostatné vlastnosti v rámci stejného objektu, protože existuje několik měr. Tady, **řady. Rozhraní psi a řady toků toku** **. Ft3/s tlakového oleje v motoru** jsou jedinečné sloupce.

## <a name="scenario-two-several-measures-exist"></a>Scénář 2: Existuje několik opatření.

> [!TIP]
> Doporučujeme, abyste odeslali měření jako "typ", "jednotka" a "value" (řazené kolekce členů).

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

* Tabulka referenčních dat, která má vlastnosti klíče **deviceId** a **Series. tagId**:

   | deviceId | series.tagId | messageId | deviceLocation | type | jednotka |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | DATA\_ŘÁDKU | EVROPA | Rychlost toku | ft3/s |
   | FXXX | oilPressure | DATA\_ŘÁDKU | EVROPA | Tlak v oleji motoru | psi |
   | FYYY | pumpRate | DATA\_ŘÁDKU | USA | Rychlost toku | ft3/s |
   | FYYY | oilPressure | DATA\_ŘÁDKU | USA | Tlak v oleji motoru | psi |

* Time Series Insights tabulka událostí po sloučení:

   | deviceId | series.tagId | messageId | deviceLocation | type | jednotka | timestamp | Series. Value |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | DATA\_ŘÁDKU | EVROPA | Rychlost toku | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 | 
   | FXXX | oilPressure | DATA\_ŘÁDKU | EVROPA | Tlak v oleji motoru | psi | 2018-01-17T01:17:00Z | 34.7 |
   | FXXX | pumpRate | DATA\_ŘÁDKU | EVROPA | Rychlost toku | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 | 
   | FXXX | oilPressure | DATA\_ŘÁDKU | EVROPA | Tlak v oleji motoru | psi | 2018-01-17T01:17:00Z | 49.2 |
   | FYYY | pumpRate | DATA\_ŘÁDKU | USA | Rychlost toku | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
   | FYYY | oilPressure | DATA\_ŘÁDKU | USA | Tlak v oleji motoru | psi | 2018-01-17T01:18:00Z | 22.2 |

Poznámky k těmto dvěma tabulkám:

- Sloupce **deviceId** a **Series. tagId** slouží jako záhlaví sloupců pro různá zařízení a značky v rámci loďstva. Použití každého vlastního atributu omezí dotaz na 594 (pro prostředí S1) nebo 794 (pro prostředí S2) celkem zařízení s ostatními šesti sloupci.
- Z důvodu citovaného v prvním příkladu se zabránilo zbytečným vlastnostem.
- Referenční data se používají ke snížení počtu bajtů přenesených přes síť, a totak, že zavedeme do ID zařízení, které se používá pro jedinečnou dvojici **MessageID** a **deviceLocation**. Složená **série klíčů. tagId** se používá pro jedinečné páry **typu** a **jednotky**. Složený klíč umožňuje použít dvojici **deviceId** a **Series. tagId** k odkazování na čtyři hodnoty: **MessageID, deviceLocation, Type** a **Unit**. Tato data jsou spojená s daty telemetrie v čase vstupu. Pak je uložený v Time Series Insights pro dotazování.
- Používají se dvě vrstvy vnoření z důvodu citovaného v prvním příkladu.

### <a name="for-both-scenarios"></a>Pro oba scénáře

Pro vlastnost s velkým počtem možných hodnot je nejlepší poslat jako jedinečné hodnoty v jednom sloupci místo vytvoření nového sloupce pro každou hodnotu. V předchozích dvou příkladech:

  - V prvním příkladu má několik vlastností několik hodnot, takže je vhodné vytvořit samostatnou vlastnost.
  - V druhém příkladu nejsou míry zadány jako jednotlivé vlastnosti. Místo toho se jedná o pole hodnot nebo měr v rámci společné vlastnosti řady. Pošle se nový Key **tagId** , který vytvoří nový sloupec **Series. tagId** ve sloučené tabulce. Nové **typy** vlastností a **jednotka** se vytvoří pomocí referenčních dat, takže se nedosáhne limitu vlastností.

## <a name="next-steps"></a>Další postup

- Další informace o syntaxi dotazu pro Time Series Insights REST API přístupu k datům najdete v [Azure Time Series Insights syntaxi dotazů](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-syntax) .
- Naučte [se, jak obrazce událostí](./time-series-insights-send-events.md).
