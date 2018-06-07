---
title: Osvědčené postupy pro úpravu JSON v dotazech Statistika Azure časové řady.
description: Zjistěte, jak ke zlepšení efektivity časové řady Statistika dotazu.
services: time-series-insights
author: ashannon7
manager: timlt
ms.service: time-series-insights
ms.topic: article
ms.date: 05/24/2018
ms.author: bryanla
ms.openlocfilehash: 29919a3ce8c18982c88f7f0e6bbd774c612e9c44
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660770"
---
# <a name="how-to-shape-json-to-maximize-query-performance"></a>Postup tvar JSON maximalizovat výkon dotazů 

Tento článek obsahuje pokyny pro úpravu formát JSON, maximalizovat efektivitu své dotazy Azure časové řady přehledy (TSI).

## <a name="best-practices"></a>Osvědčené postupy

Je důležité zvážit, jak odesílat události ke statistice časové řady. Konkrétně měli byste vždy:

1. co možná nejefektivnější odesílání dat přes síť.
2. Zkontrolujte, zda že je vaše data uložená způsobem, který umožňuje provádět agregace, které jsou vhodné pro váš scénář.
3. Ujistěte se, nemusíte dosáhl maximální vlastnost omezení na TSI
   - 600 vlastnosti (sloupce) pro S1 prostředí.
   - 800 vlastnosti (sloupce) pro S2 prostředí.

Následující pokyny pomáhá zajistit nejlepší výkon dotazů možné:

1. Nepoužívejte dynamické vlastnosti, jako je například ID značky jako název vlastnosti, jako přispívá ke nedosáhli limitu maximální vlastnosti.
2. Neodesílat nepotřebné vlastnosti. Pokud není požadovaná vlastnost dotaz, je nejlepší není odeslat a vyhnout se omezeních úložiště.
3. Použití [referenční data](time-series-insights-add-reference-data-set.md), aby se zabránilo odesílání statických dat v síti.
4. Sdílet vlastností dimenze mezi více událostí k odesílání dat přes síť efektivněji.
5. Nepoužívejte vnoření hloubkové pole. TSI podporuje až dvě úrovně vnořených polí, které obsahují objekty. TSI sloučí pole ve zprávách, do více událostí s páry hodnota.
6. Pokud jenom pro všechny nebo většinu událostí existuje pár opatření, je lepší odesílat tyto míry jako samostatné vlastnosti v rámci stejného objektu. Odesláním samostatně snižuje počet událostí a může být dotazy další původce, jako třeba zpracovat méně událostmi. Po několika míry je pošlete jako hodnoty do vlastnosti jediné minimalizuje možnost nedosáhli limitu maximální vlastnost.

## <a name="examples"></a>Příklady

Následující dva příklady ukazují odesílání událostí, abyste měli na očích předchozí doporučení. Následující každý příkladu uvidíte, jak byly použity doporučení.

Příklady jsou založeny na scénář, kde více zařízení odesílat měření nebo signály. Míra toku, modul těžba ropy přetížení, teploty a vlhkosti, může být měření nebo signály. V prvním příkladu existuje několik měření ve všech zařízeních. V druhém příkladu existuje mnoho zařízení a každou odešle mnoho jedinečný měření.

### <a name="scenario-only-a-few-measurementssignals-exist"></a>Scénář: existuje jenom pár měření/signály

**Doporučení:** odesílat každé měření jako samostatný vlastnost nebo sloupec.

V následujícím příkladu je do jedné zprávy služby IoT Hub, kde vnější pole obsahuje sdílený oddíl běžných hodnot dimenze. Vnější pole používá ke zvýšení efektivity zprávy referenční data. Referenční data obsahuje metadata, zařízení, která se nemění s každou událostí, ale poskytuje užitečné vlastnosti pro analýzu dat. Dávkování běžné hodnoty dimenze i zaměstnávající referenční data, uloží na bajtů odeslány prostřednictvím sítě, díky čemuž zpráva efektivnější.

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

Referenční data tabulku (deviceId je klíčovou vlastnost):

| deviceId | messageId | deviceLocation |
| --- | --- | --- |
| FXXX | ŘÁDEK\_DAT | EU |
| FRRR | ŘÁDEK\_DAT | USA |

Tabulku dob řady Přehled událostí (po vyrovnání):

| deviceId | messageId | deviceLocation | časové razítko | řada. Míra toku ft3/s | řada. Modul těžba ropy přetížení psi |
| --- | --- | --- | --- | --- | --- |
| FXXX | ŘÁDEK\_DAT | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
| FXXX | ŘÁDEK\_DAT | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
| FRRR | ŘÁDEK\_DAT | USA | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

Vezměte na vědomí následující v předchozím příkladu:

- **DeviceId** sloupec slouží jako záhlaví sloupce pro různá zařízení v firemního vozového. Při pokusu o vytvoření deviceId hodnoty svůj vlastní název vlastnosti by mají omezenou 595 (S1 prostředích) nebo 795 (S2 prostředích), celkový počet zařízení s jinými pět sloupci.

- Nepotřebné vlastnosti se tak předejde, například informace o značku a model, atd. Vzhledem k tomu, že nebude být dotazována v budoucnu, jejich odstranění umožňuje lepší sítě a efektivitu úložiště.

- Referenční data se používá ke snížení počet bajtů přenesených přes síť. Dva atributy **messageId** a **deviceLocation** , jsou připojené pomocí vlastnosti klíče **deviceId**. Tato data jsou spojena s telemetrická data v době příjem příchozích dat a následně uložená v TSI pro zadávání dotazů.

- Dvě vrstvy vnoření se používají, což je maximální množství vnoření nepodporuje TSI. Je důležité, aby se zabránilo hluboko vložené pole.

- Míry jsou odesílány jako samostatné vlastnosti v rámci stejného objektu, vzhledem k tomu, že existuje několik míry. Zde **řady. Tok míra psi** a **řady. Modul těžba ropy přetížení ft3 nebo s** jsou jedinečných sloupců.

### <a name="scenario-several-measures-exist"></a>Scénář: existuje několik opatření

**Doporučení:** odesílat měření jako "typ", "jednotka" a "value" řazené kolekce členů.

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

Referenční Data (vlastnosti klíče je ID zařízení a series.tagId):

| deviceId | series.tagId | messageId | deviceLocation | type | jednotka |
| --- | --- | --- | --- | --- | --- |
| FXXX | pumpRate | ŘÁDEK\_DAT | EU | Míra toku | ft3/s |
| FXXX | oilPressure | ŘÁDEK\_DAT | EU | Modul těžba ropy přetížení | psi |
| FRRR | pumpRate | ŘÁDEK\_DAT | USA | Míra toku | ft3/s |
| FRRR | oilPressure | ŘÁDEK\_DAT | USA | Modul těžba ropy přetížení | psi |

Tabulku dob řady Přehled událostí (po vyrovnání):

| deviceId | series.tagId | messageId | deviceLocation | type | jednotka | časové razítko | Series.Value |
| --- | --- | --- | --- | --- | --- | --- | --- |
| FXXX | pumpRate | ŘÁDEK\_DAT | EU | Míra toku | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 |
| FXXX | oilPressure | ŘÁDEK\_DAT | EU | Modul těžba ropy přetížení | psi | 2018-01-17T01:17:00Z | 34.7 |
| FXXX | pumpRate | ŘÁDEK\_DAT | EU | Míra toku | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 |
| FXXX | oilPressure | ŘÁDEK\_DAT | EU | Modul těžba ropy přetížení | psi | 2018-01-17T01:17:00Z | 49.2 |
| FRRR | pumpRate | ŘÁDEK\_DAT | USA | Míra toku | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
| FRRR | oilPressure | ŘÁDEK\_DAT | USA | Modul těžba ropy přetížení | psi | 2018-01-17T01:18:00Z | 22.2 |

Všimněte si následujících v předchozím příkladu a podobně jako v prvním příkladu:

- sloupce **deviceId** a **series.tagId** sloužit jako záhlaví sloupců pro různá zařízení a značky v firemního vozového. Použití každého jako vlastní atribut by mají omezenou dotaz na 594 (S1 prostředích) nebo 794 (S2 prostředích) Celkový počet zařízení s jinými šesti sloupci.

- z důvodu citovalo v prvním příkladu byly předejde nepotřebné vlastnosti.

- referenční data se používá a snížit počet bajtů přenesených přes síť představení **deviceId**, pro jedinečnou dvojici hodnot z **messageId** a **deviceLocation**. Je použit složený klíč, **series.tagId**, pro jedinečnou dvojici hodnot z **typ** a **jednotce**. Umožňuje složeného klíče **deviceId** a **series.tagId** pár použije k odkazování na čtyři hodnoty: **messageId, deviceLocation, typ,** a **jednotky** . Tato data jsou spojena s telemetrická data v době příjem příchozích dat a následně uložená v TSI pro zadávání dotazů.

- z důvodu citovalo v prvním příkladu se používají dvě vrstvy vnoření.

### <a name="for-both-scenarios"></a>Pro oba scénáře

Pokud je vlastnost s velký počet možných hodnot, je vhodné odeslat jako jedinečných hodnot v rámci jeden sloupec, místo vytvoření nového sloupce pro každou hodnotu. V předchozích dvou příkladech:
  - V prvním příkladu existuje několik vlastností, které mají několik hodnot, proto je vhodný, aby se každý samostatný vlastnost. 
  - V druhém příkladu však uvidíte, že míry nejsou zadány jako jednotlivé vlastnosti, ale místo toho pole hodnot nebo míry v části společné vlastnosti řady. Nový klíč se pošle **tagId** , která vytvoří nový sloupec, **series.tagId** v tabulce plochou. Nové vlastnosti jsou vytvořeny, **typ** a **jednotky**pomocí referenční data, proto se dosáhl brání limit vlastnost.

## <a name="next-steps"></a>Další postup

Tyto pokyny uvedené do praxe, najdete v tématu [syntaxe dotazu Statistika řady čas Azure](/rest/api/time-series-insights/time-series-insights-reference-query-syntax) Další informace o syntaxi dotazu pro TSI data přístup k rozhraní REST API.