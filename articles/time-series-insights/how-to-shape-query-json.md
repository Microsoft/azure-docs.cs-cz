---
title: Doporučené postupy pro tvarování dotazů JSON – Azure Time Series Insights | Dokumenty společnosti Microsoft
description: Zjistěte, jak zlepšit efektivitu dotazů Azure Time Series Insights tvarováním JSON.
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 04/17/2020
ms.custom: seodec18
ms.openlocfilehash: 63a708f80ad18309269e37c354b047c304a260d3
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641299"
---
# <a name="shape-json-to-maximize-query-performance"></a>Obrazec JSON pro maximalizaci výkonu dotazu

Tento článek obsahuje pokyny, jak utvářet JSON tak, aby maximalizoval efektivitu vašich dotazů Azure Time Series Insights.

## <a name="video"></a>Video

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>Seznamte se s osvědčenými postupy pro tvarování jsonu tak, aby vyhovoval vašim potřebám úložiště.</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>Osvědčené postupy

Zamyslete se nad tím, jak odesíláte události do time series insights. Jmenovitě, vždy:

1. Odesílejte data po síti co nejefektivněji.
1. Ujistěte se, že vaše data jsou uložena způsobem, takže můžete provádět agregace vhodné pro váš scénář.
1. Ujistěte se, že nedosáhnete maximálních limitů vlastností Time Series Insights:
   - 600 vlastností (sloupců) pro prostředí S1.
   - 800 vlastností (sloupců) pro prostředí S2.

> [!TIP]
> Zkontrolujte [limity a plánování](time-series-insights-update-plan.md) ve verzi Azure Time Series Insights Preview.

Následující pokyny pomáhají zajistit nejlepší možný výkon dotazu:

1. Nepoužívejte dynamické vlastnosti, například ID značky, jako název vlastnosti. Toto použití přispívá k dosažení maximálního limitu vlastností.
1. Neposílejte nepotřebné vlastnosti. Pokud není vyžadována vlastnost dotazu, je vhodné ji neodesílat. Tímto způsobem se vyhnete omezení úložiště.
1. Pomocí [referenčních dat](time-series-insights-add-reference-data-set.md) se vyhněte odesílání statických dat po síti.
1. Sdílejte vlastnosti dimenze mezi více událostmi a odesílejte data po síti efektivněji.
1. Nepoužívejte hluboké vnoření pole. Time Series Insights podporuje až dvě úrovně vnořených polí, které obsahují objekty. Time Series Insights sloučí pole ve zprávách do více událostí s dvojicemi hodnot vlastností.
1. Pokud existuje pouze několik opatření pro všechny nebo většinu událostí, je lepší odeslat tyto míry jako samostatné vlastnosti v rámci stejného objektu. Jejich odeslání samostatně snižuje počet událostí a může zlepšit výkon dotazu, protože je třeba zpracovat méně událostí. Pokud existuje několik opatření, jejich odesláním jako hodnoty v jedné vlastnosti minimalizuje možnost dosažení maximálního limitu vlastnosti.

## <a name="example-overview"></a>Ukázkový přehled

Následující dva příklady ukazují, jak odesílat události pro zvýraznění předchozích doporučení. V jednotlivých příkladech můžete zkontrolovat, jak byla doporučení použita.

Příklady jsou založeny na scénáři, kdy více zařízení odesílá měření nebo signály. Měření nebo signály mohou být průtok, tlak motorového oleje, teplota a vlhkost. V prvním příkladu je několik měření navšech zařízeních. Druhý příklad má mnoho zařízení a každé zařízení odesílá mnoho jedinečných měření.

## <a name="scenario-one-only-a-few-measurements-exist"></a>Scénář první: Existuje pouze několik měření

> [!TIP]
> Doporučujeme odeslat každé měření nebo signál jako samostatnou vlastnost nebo sloupec.

V následujícím příkladu je jedna zpráva Služby Azure IoT Hub, kde vnější pole obsahuje sdílenou část běžných hodnot dimenzí. Vnější pole používá referenční data ke zvýšení efektivity zprávy. Referenční data obsahují metadata zařízení, která se nemění s každou událostí, ale poskytuje užitečné vlastnosti pro analýzu dat. Dávkování běžných hodnot dimenzí a využití referenčních dat šetří bajty odeslané po drátě, což zefektivňuje zprávu.

Zvažte následující datovou část JSON odeslanou do prostředí GA Time Series Insights pomocí [objektu zprávy zařízení IoT,](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.message?view=azure-dotnet) který je serializován do JSON při odeslání do cloudu Azure:


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

* Tabulka referenčních dat, která má vlastnost key **deviceId**:

   | deviceId | Messageid | deviceLocation |
   | --- | --- | --- |
   | FXXX | ÚDAJE\_O ŘÁDCÍCH | EU |
   | Fyyy | ÚDAJE\_O ŘÁDCÍCH | USA |

* Tabulka událostí Time Series Insights po sloučení selby:

   | deviceId | Messageid | deviceLocation | časové razítko | Série. Průtok ft3/s | Série. Tlak motorového oleje psi |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | ÚDAJE\_O ŘÁDCÍCH | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
   | FXXX | ÚDAJE\_O ŘÁDCÍCH | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
   | Fyyy | ÚDAJE\_O ŘÁDCÍCH | USA | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

> [!NOTE]
> - Sloupec **deviceId** slouží jako záhlaví sloupce pro různá zařízení ve vozovém parku. Vytvoření **deviceId hodnotu** vlastní název vlastnosti omezuje celkový počet zařízení na 595 (pro prostředí S1) nebo 795 (pro prostředí S2) s dalšími pěti sloupci.
> - Nepotřebné vlastnosti jsou zabráněno (například informace o make-up a model). Vzhledem k tomu, že vlastnosti nebudou v budoucnu dotazovány, jejich odstranění umožňuje lepší efektivitu sítě a úložiště.
> - Referenční data se používají ke snížení počtu bajtů přenesených po síti. Dva atributy **messageId** a **deviceLocation** jsou spojeny pomocí vlastnosti key **deviceId**. Tato data jsou spojena s telemetrická data v době příchozího přenosu dat a pak je uložen a čas series Insights pro dotazování.
> - Používají se dvě vrstvy vnoření, což je maximální množství vnoření podporované time series insights. Je důležité, aby se zabránilo hluboce vnořené pole.
> - Rozměry jsou odesílány jako samostatné vlastnosti v rámci stejného objektu, protože existuje několik opatření. Tady, **seriál. Průtok psi** a **série. Tlak motorového oleje ft3/s** jsou jedinečné kolony.

## <a name="scenario-two-several-measures-exist"></a>Scénář druhý: Existuje několik opatření

> [!TIP]
> Doporučujeme odeslat měření jako "typ", "jednotka" a "hodnota" řazené kolekce členů.

Příklad datové části JSON:

```JSON
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

* Tabulka referenčních dat, která má vlastnosti klíče **deviceId** a **series.tagId**:

   | deviceId | series.tagId | Messageid | deviceLocation | type | unit |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | ÚDAJE\_O ŘÁDCÍCH | EU | Průtok | ft3/s |
   | FXXX | olejTlak | ÚDAJE\_O ŘÁDCÍCH | EU | Tlak motorového oleje | psi |
   | Fyyy | pumpRate | ÚDAJE\_O ŘÁDCÍCH | USA | Průtok | ft3/s |
   | Fyyy | olejTlak | ÚDAJE\_O ŘÁDCÍCH | USA | Tlak motorového oleje | psi |

* Tabulka událostí Time Series Insights po sloučení selby:

   | deviceId | series.tagId | Messageid | deviceLocation | type | unit | časové razítko | series.value |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | ÚDAJE\_O ŘÁDCÍCH | EU | Průtok | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 | 
   | FXXX | olejTlak | ÚDAJE\_O ŘÁDCÍCH | EU | Tlak motorového oleje | psi | 2018-01-17T01:17:00Z | 34.7 |
   | FXXX | pumpRate | ÚDAJE\_O ŘÁDCÍCH | EU | Průtok | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 | 
   | FXXX | olejTlak | ÚDAJE\_O ŘÁDCÍCH | EU | Tlak motorového oleje | psi | 2018-01-17T01:17:00Z | 49.2 |
   | Fyyy | pumpRate | ÚDAJE\_O ŘÁDCÍCH | USA | Průtok | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
   | Fyyy | olejTlak | ÚDAJE\_O ŘÁDCÍCH | USA | Tlak motorového oleje | psi | 2018-01-17T01:18:00Z | 22.2 |

> [!NOTE]
> - Sloupce **deviceId** a **series.tagId** slouží jako záhlaví sloupců pro různá zařízení a značky ve vozovém parku. Použití každého jako vlastní atribut omezuje dotaz na 594 (pro prostředí S1) nebo 794 (pro prostředí S2) celkový počet zařízení s ostatními šesti sloupců.
> - Z důvodu uvedeného v prvním příkladu se vyvarovali nepotřebných vlastností.
> - Referenční data se používají ke snížení počtu bajtů přenesených po síti zavedením **deviceId**, který se používá pro jedinečný pár **messageId** a **deviceLocation**. Složený klíč **series.tagId** se používá pro jedinečný pár **typu** a **jednotky**. Složený klíč umožňuje **deviceId** a **series.tagId** dvojice, která má být použita k odkazování na čtyři hodnoty: **messageId, deviceLocation, typ** a **jednotka**. Tato data jsou spojena s telemetrickámi daty v době příchozího přenosu dat. Je pak uložen v Time Series Insights pro dotazování.
> - Z důvodu uvedeného v prvním příkladu se používají dvě vrstvy hnízdění.

### <a name="for-both-scenarios"></a>Pro oba scénáře

Pro vlastnost s velkým počtem možných hodnot je nejlepší odeslat jako odlišné hodnoty v rámci jednoho sloupce namísto vytvoření nového sloupce pro každou hodnotu. Z předchozích dvou příkladů:

  - V prvním příkladu několik vlastností mají několik hodnot, takže je vhodné, aby každý samostatnou vlastnost.
  - V druhém příkladu nejsou míry zadány jako jednotlivé vlastnosti. Místo toho jsou pole hodnot nebo opatření v rámci společné řady vlastnosti. Nový **klíč tagId** je odeslána, která vytvoří nový sloupec **series.tagId** v tabulce sloučení. Nový **typ** vlastností a **jednotka** jsou vytvořeny pomocí referenčních dat tak, aby nebylo dosaženo limitu vlastností.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o odesílání [zpráv zařízení služby IoT Hub do cloudu](../iot-hub/iot-hub-devguide-messages-construct.md).

- Přečtěte si [syntaxi dotazu Azure Time Series Insights](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-syntax) a získejte další informace o syntaxi dotazu pro rozhraní REST API pro přístup k datům Time Series Insights.

- Přečtěte [si, jak utvářet události](./time-series-insights-send-events.md).
