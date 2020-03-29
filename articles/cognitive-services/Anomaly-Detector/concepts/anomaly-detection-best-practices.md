---
title: Osvědčené postupy při používání rozhraní API Detektoru anomálií
titleSuffix: Azure Cognitive Services
description: Další informace o osvědčených postupech při zjišťování anomálií pomocí rozhraní API detektoru anomálií.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 9407f2fc9375765efb6eb9688b3ebfeef24ba90a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67721619"
---
# <a name="best-practices-for-using-the-anomaly-detector-api"></a>Doporučené postupy pro používání rozhraní API detektoru anomálií

Rozhraní API detektoru anomálií je služba detekce anomálií bez stavů. Přesnost a výkon jeho výsledků může být ovlivněna:

* Jak jsou připravena data vašich časových řad.
* Parametry rozhraní API detektoru anomálií, které byly použity.
* Počet datových bodů v žádosti o rozhraní API. 

V tomto článku se dozvíte o doporučených postupech pro používání rozhraní API, které vám zpřístupní nejlepší výsledky pro vaše data. 

## <a name="when-to-use-batch-entire-or-latest-last-point-anomaly-detection"></a>Kdy použít detekci anomálií dávky (celé) nebo poslední (poslední) bodové anomálie

Koncový bod detekce dávek rozhraní API detektoru anomálií umožňuje detekovat anomálie prostřednictvím dat řady celé časy. V tomto režimu zjišťování je vytvořen jeden statistický model a použije se pro každý bod v sadě dat. Pokud vaše časové řady má níže uvedené charakteristiky, doporučujeme použít detekci dávek k zobrazení náhledu dat v jednom volání rozhraní API.

* Sezónní časová řada s občasnými anomáliemi.
* Plochý trend časové řady, s občasnými hroty / poklesy. 

Nedoporučujeme používat detekci anomálií dávek pro monitorování dat v reálném čase nebo je používat na datech časových řad, která nemají výše uvedené charakteristiky. 

* Detekce dávky vytvoří a použije pouze jeden model, detekce pro každý bod se provádí v kontextu celé řady. Pokud se data časových řad trendy nahoru a dolů bez sezónnosti, některé body změny (poklesy a špičky v datech) může být vynechány modelem. Podobně některé body změny, které jsou méně významné než ty později v sadě dat nemusí být započítány jako dostatečně významné, aby mohly být začleněny do modelu.

* Detekce dávek je pomalejší než zjišťování stavu anomálií posledního bodu při monitorování dat v reálném čase z důvodu počtu analyzovaných bodů.

Pro monitorování dat v reálném čase doporučujeme zjistit stav anomálií pouze vašeho nejnovějšího datového bodu. Neustálým používáním nejnovější detekce bodů lze monitorování dat streamování provádět efektivněji a přesněji.

Následující příklad popisuje dopad těchto režimů zjišťování na výkon. První obrázek ukazuje výsledek neustálého zjišťování stavu anomálií nejnovější bod podél 28 dříve viděných datových bodů. Červené body jsou anomálie.

![Obrázek znázorňující detekci anomálií pomocí nejnovějšího bodu](../media/last.png)

Níže je stejná sada dat pomocí detekce anomálií dávky. Model vytvořený pro operaci ignoroval několik anomálií označených obdélníky.

![Obrázek znázorňující detekci anomálií pomocí dávkové metody](../media/entire.png)

## <a name="data-preparation"></a>Příprava dat

Rozhraní API detektoru anomálií přijímá data časových řad formátované do objektu požadavku JSON. Časová řada může být libovolná číselná data zaznamenaná v průběhu času v sekvenčním pořadí. Můžete odeslat okna dat časové řady do koncového bodu rozhraní API detektoru anomálií ke zlepšení výkonu rozhraní API. Minimální počet datových bodů, které můžete odeslat, je 12 a maximální počet je 8640 bodů. [Rozlišovací schopnost](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) je definována jako rychlost, při které jsou data vzorkována. 

Datové body odeslané do rozhraní API detektoru anomálií musí mít platné časové razítko koordinovaného světového času (UTC) a číselnou hodnotu. 

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

Pokud jsou data vzorkována v nestandardním časovém intervalu, `customInterval` můžete je zadat přidáním atributu do požadavku. Pokud je například vaše řada vzorkována každých 5 minut, můžete do požadavku JSON přidat následující:

```json
{
    "granularity" : "minutely", 
    "customInterval" : 5
}
```

### <a name="missing-data-points"></a>Chybějící datové body

Chybějící datové body jsou běžné v rovnoměrně distribuovaných datových sadách časových řad, zejména těch s jemnou granularitou (Malý interval vzorkování. Například data vzorkovaná každých několik minut). Chybějící méně než 10 % očekávaného počtu bodů v datech by nemělo mít negativní dopad na výsledky zjišťování. Zvažte vyplnění mezer v datech na základě jejich charakteristik, jako je nahrazení datových bodů z dřívějšího období, lineární interpolace nebo klouzavý průměr.

### <a name="aggregate-distributed-data"></a>Agregovaná distribuovaná data

Rozhraní API detektoru anomálií funguje nejlépe v rovnoměrně distribuovaných časových řadách. Pokud jsou data distribuována náhodně, měli byste je agregovat podle jednotky času, například za minutu, každou hodinu nebo denně.

## <a name="anomaly-detection-on-data-with-seasonal-patterns"></a>Detekce anomálií u dat se sezónními vzory

Pokud víte, že data časových řad má sezónní vzor (ten, který se vyskytuje v pravidelných intervalech), můžete zlepšit přesnost a dobu odezvy rozhraní API. 

Určení `period` při vytváření požadavku JSON může snížit latenci detekce anomálií až o 50 %. Je `period` celé číslo, které určuje zhruba kolik datových bodů časové řady trvá opakovat vzorek. Například časové řady s jedním datovým bodem `period` `7`za den by měly as a časové řady s jedním `period` `7*24`bodem za hodinu (se stejným týdenním vzorem) by měly . Pokud si nejste jisti vzory dat, nemusíte tento parametr zadávat.

Pro dosažení nejlepších `period`výsledků zadejte 4 's v hodnotě datového bodu, plus další. Například hodinová data s týdenním vzorem, jak je popsáno výše,`7 * 24 * 4 + 1`by měla poskytnout 673 datových bodů v těle požadavku ( ).

### <a name="sampling-data-for-real-time-monitoring"></a>Vzorkovací data pro monitorování v reálném čase

Pokud jsou data streamování vzorkována v krátkém intervalu (například sekundy nebo minuty), může odeslání doporučeného počtu datových bodů překročit maximální povolený počet rozhraní API detektoru anomálií (8640 datových bodů). Pokud vaše data ukazují stabilní sezónní vzor, zvažte odeslání vzorku dat časových řad ve větším časovém intervalu, například hodin. Vzorkování dat tímto způsobem může také výrazně zlepšit dobu odezvy rozhraní API. 

## <a name="next-steps"></a>Další kroky

* [Co je rozhraní API Detektoru anomálií?](../overview.md)
* [Úvodní příručka: Detekce anomálií v datech časových řad pomocí rozhraní REST API detektoru anomálií](../quickstarts/detect-data-anomalies-csharp.md)
