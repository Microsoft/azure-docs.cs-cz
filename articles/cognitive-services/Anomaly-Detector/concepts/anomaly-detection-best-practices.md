---
title: Osvědčené postupy při používání rozhraní API detekce anomálií
description: Další informace o osvědčených postupech při zjišťování anomálií s rozhraním API detekce anomálií.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 467ac4e475a1c23e25b62c76cfbc959e7ed49465
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484029"
---
# <a name="best-practices-for-using-the-anomaly-detector-api"></a>Doporučené postupy pro používání rozhraní API detekce anomálií

Rozhraní API detekce anomálií je služba detekce anomálií bezstavové. Může být ovlivněno přesnost a výkon svých výsledků:

* Jak je připravit data časových řad.
* Parametry rozhraní API detekce anomálií, které byly použity.
* Počet datových bodů ve vaší žádosti rozhraní API. 

Pomocí tohoto článku se dozvíte o osvědčené postupy při používání rozhraní API pro získání nejlepších výsledků pro vaše data. 

## <a name="data-preparation"></a>Příprava dat

Rozhraní API detekce anomálií přijímá časové řady na žádost o objekt JSON ve formátu data. Časové řady může být jakékoli číselných dat zaznamenaných v průběhu času v sekvenčním pořadí. Koncový bod rozhraní API detekce anomálií, a to kvůli zvýšení výkonu rozhraní API můžete poslat windows daty časových řad. Minimální počet datových bodů, které můžete odeslat je 12 a maximální hodnota je 8640 body. 

Datových bodů poslaných do rozhraní API detekce anomálií musí mít platné časové razítko koordinovaný univerzální čas (UTC) a číselnou hodnotu. 

```json
{
    "granularity": "daily",
    "series": [
      {
        "timestamp": "2018-03-01T00:00:00Z",
        "value": 32858923
      },
      {
        "timestamp": "2018-03-02T00:00:00Z",
        "value": 29615278
      },
    ]
}
```

### <a name="missing-data-points"></a>Chybějící datové body

Chybějící datové body jsou běžné ve rovnoměrně distribuovaných čas řady datových sad, zejména těch, které jsou s jemnou rozlišovací schopnost (interval drobné příklady. For example, data vzorkováno každých několik minut). Chybí méně než 10 % očekávaný počet bodů ve vašich datech by neměla mít negativní dopad na vaše výsledky zjišťování. Zvažte vyplnění mezer v datech podle jeho vlastnosti, jako je nahrazování datové body z předchozích období, lineární interpolace nebo klouzavý průměr.

### <a name="aggregate-distributed-data"></a>Agregační distribuovaných dat

Rozhraní API detekce anomálií funguje nejlépe na rovnoměrně distribuovaných časové řady. Pokud vaše data jsou distribuovaná náhodně, můžete by ho agregovat jednotku času, například po minutách, hodinových nebo denních třeba.

## <a name="anomaly-detection-on-data-with-seasonal-patterns"></a>Detekce anomálií na datech s sezónní vzory

Pokud víte, že data časových řad se sezónním vzoru (jeden, který se nachází v pravidelných intervalech), můžete zlepšit přesnost a doba odezvy rozhraní API. 

Určení `period` při vytváření vaší žádosti JSON může snížit latenci detekce anomálií až o 50 %. `period` Je celé číslo, které určuje zhruba kolik datových bodů řady čas potřebný k opakováním vzoru. Například by mít časové řady s jeden datový bod za den `period` jako `7`, a bude mít časové řady s jedním bodem za hodinu (se stejným vzorem týdně) `period` z `7*24`. Pokud si nejste jisti vzorů vaše data, není nutné tento parametr zadán.

Nejlepších výsledků dosáhnete, poskytují 4 `period`uživatele za datový bod a další. Například hodinová data s týdenní opakování výše popsaným způsobem by měly poskytnout 673 datových bodů v textu požadavku (`7 * 24 * 4 + 1`).

### <a name="sampling-data-for-real-time-monitoring"></a>Data vzorkování pro monitorování v reálném čase

Pokud streamování dat se definuje na krátkou dobu (například několika sekund nebo minut), odesílání doporučený počet datových bodů může být delší než rozhraní API detekce anomálií maximální počet povolených (8640 datových bodů). Pokud se vaše data zobrazí stabilní sezónním vzoru, vezměte v úvahu odesílá vzorek dat časových řad na větší časový interval, jako jsou hodiny. Vzorkování dat tímto způsobem můžete také výrazně zlepšit dobu odezvy rozhraní API. 

## <a name="next-steps"></a>Další kroky

* [Co je API detekce anomálií?](../overview.md)
* [Rychlé zprovoznění: Detekovat anomálie ve vašich datech časových řad pomocí rozhraní REST API detekce anomálií](../quickstarts/detect-data-anomalies-csharp.md)
