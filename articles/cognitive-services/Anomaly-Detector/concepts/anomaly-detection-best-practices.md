---
title: Osvědčené postupy při používání rozhraní API Detektoru anomálií
description: Další informace o osvědčených postupech při zjišťování anomálií s rozhraním API detekce anomálií.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 1ad4a67d7737733e4c910d3495be29860769f27e
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477813"
---
# <a name="best-practices-for-using-the-anomaly-detector-api"></a>Doporučené postupy pro používání rozhraní API detekce anomálií

Rozhraní API detekce anomálií je služba detekce anomálií bezstavové. Může být ovlivněno přesnost a výkon svých výsledků:

* Jak je připravit data časových řad.
* Parametry rozhraní API detekce anomálií, které byly použity.
* Počet datových bodů ve vaší žádosti rozhraní API. 

Pomocí tohoto článku se dozvíte o osvědčené postupy při používání rozhraní API pro získání nejlepších výsledků pro vaše data. 

## <a name="when-to-use-batch-entire-or-latest-last-point-anomaly-detection"></a>Použití služby batch (celá) nebo poslední (naposledy) bodu detekce anomálií

Koncový bod zjišťování služby batch API detekce anomálií vám umožní detekovat anomálie přes celou dobu data řady. V tomto režimu detekce jeden statistické model je vytvořit a použít pro každý bod v datové sadě. Pokud má vaše časové řady následující charakteristiky, doporučujeme použít zjišťování služby batch a zobrazit náhled dat v jednom volání rozhraní API.

* Sezónní časové řady, s občasné anomálie.
* Plochý trend časové řadě, s občasné provozní špičky a vyhrazené IP adresy. 

Nedoporučujeme používat pro detekci anomálií služby batch pro data v reálném čase, monitorování nebo pomocí datech časových řad, který nemá nad charakteristiky. 

* Zjišťování služby batch vytvoří a použije jenom jeden model, se provádí zjišťování pro každý bod v rámci celé řady. Je-li změnit čas trendy data řady směrem nahoru a dolů bez sezonnost, některé body (vyhrazené IP adresy a provozní špičky v datech) mohou chybět modelem. Podobně nemusí některé body změn, které jsou méně důležité než těch, které jsou dále v sadě dat počítá jako natolik závažné, má být zahrnut do modelu.

* Zjišťování služby batch je pomalejší než zjišťování anomálií stav poslední bod při monitorování dat v reálném čase z důvodu počet bodů analyzován.

Ke sledování dat v reálném čase, doporučujeme, abyste zjišťování anomálií stav pouze nejnovější data bodu. Použitím průběžně poslední bod zjišťování, streamování dat monitorování lze efektivněji a přesně.

Následující příklad popisuje dopad, který tyto režimy zjišťování může mít na výkon. První obrázek ukazuje výsledek průběžně zjišťování anomálií stav nejnovějšího bodu podél 28 dříve zobrazenou datových bodů. Červená body jsou anomálie.

![Obrázek znázorňující detekce anomálií pomocí posledního bodu](../media/last.png)

Níže je sada dat pomocí služby batch pro detekci anomálií. Model sestavený pro operaci ignoroval několik anomálie, označen obdélníků.

![Obrázek znázorňující detekce anomálií pomocí služby batch – metoda](../media/entire.png)

## <a name="data-preparation"></a>Příprava dat

Rozhraní API detekce anomálií přijímá časové řady na žádost o objekt JSON ve formátu data. Časové řady může být jakékoli číselných dat zaznamenaných v průběhu času v sekvenčním pořadí. Koncový bod rozhraní API detekce anomálií, a to kvůli zvýšení výkonu rozhraní API můžete poslat windows daty časových řad. Minimální počet datových bodů, které můžete odeslat je 12 a maximální hodnota je 8640 body. [Členitost](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) je definován jako vašich dat se definuje na rychlost. 

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

Pokud vaše data se definuje na nestandardní časový interval, můžete použít tak, že přidáte `customInterval` atribut ve vaší žádosti. Například pokud řady je vzorkováno každých 5 minut, můžete přidat následující k žádosti JSON:

```json
{
    "granularity" : "minutely", 
    "customInterval" : 5
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

## <a name="next-steps"></a>Další postup

* [Co je API detekce anomálií?](../overview.md)
* [Rychlé zprovoznění: Detekovat anomálie ve vašich datech časových řad pomocí rozhraní REST API detekce anomálií](../quickstarts/detect-data-anomalies-csharp.md)
