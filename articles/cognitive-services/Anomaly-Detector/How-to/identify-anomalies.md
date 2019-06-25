---
title: Jak používat rozhraní API detekce anomálií na vašich datech časových řad
description: Zjistěte, jak detekovat anomálie v datech v dávce nebo na streamovaných datech.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 63ede8fe90d5c19c2473ffb315bf6096599ffb9c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "61432304"
---
# <a name="how-to-use-the-anomaly-detector-api-on-your-time-series-data"></a>Postup: Použití rozhraní API detektor anomálií ve vašich datech časových řad  

[Rozhraní API detekce anomálií](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) nabízí dvě metody pro detekci anomálií. Buď můžete detekovat anomálie v dávce v celém vašem čase řady, nebo pomocí detekce anomálií stav poslední datový bod je generovaná data. Detekce modelu vrátí výsledky anomálií spolu s očekávanou hodnotu datových bodů a anomálií horní a dolní hranice zjišťování. Tyto hodnoty můžete použít k vizualizaci řadu běžných hodnot a anomálie v datech.

## <a name="anomaly-detection-modes"></a>Režim detekce anomálií 

Poskytuje rozhraní API detekce anomálií detekce režimy: služby batch a streamování.

> [!NOTE]
> Následující žádost, u které adresy URL musí být kombinován se příslušné koncový bod pro vaše předplatné. Příklad: `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/entire/detect`


### <a name="batch-detection"></a>Zjišťování služby batch

K detekci anomálií v rámci služby batch datových bodů za dané časové období, použijte následující identifikátor URI požadavku s daty časových řad: 

`/timeseries/entire/detect`. 

Odesláním najednou daty časových řad, bude rozhraní API pro generování modelu pomocí celé řady a analyzovat datových bodů s ním.  

### <a name="streaming-detection"></a>Zjišťování datových proudů

Průběžně zjišťovat anomálie na streamovaných datech, použijte následující identifikátor URI požadavku s nejnovější datového bodu: 

`/timeseries/last/detect'`. 

Odesláním nové datové body, jako je vygenerovat, můžete sledovat data v reálném čase. Vygeneruje se vzor s datovými body, které odesíláte a rozhraní API se určit, zda nejnovější bod v časové řadě anomálie.

## <a name="adjusting-lower-and-upper-anomaly-detection-boundaries"></a>Nastavení hranic detekce anomálií nižším a vyšším

Ve výchozím nastavení, horní a dolní hranice pro detekci anomálií vypočítají na základě `expectedValue`, `upperMargin`, a `lowerMargin`. Pokud požadujete různé hranice, doporučujeme použití `marginScale` k `upperMargin` nebo `lowerMargin`. Hranice se vypočítá následovně:

|Hranice  |Výpočet  |
|---------|---------|
|`upperBoundary` | `expectedValue + (100 - marginScale) * upperMargin`        |
|`lowerBoundary` | `expectedValue - (100 - marginScale) * lowerMargin`        |

Následující příklady ukazují výsledek rozhraní API detekce anomálií v různých citlivost.

### <a name="example-with-sensitivity-at-99"></a>Příklad s citlivosti na 99

![Výchozí citlivosti](../media/sensitivity_99.png)

### <a name="example-with-sensitivity-at-95"></a>Příklad s citlivosti na 95

![99 citlivosti](../media/sensitivity_95.png)

### <a name="example-with-sensitivity-at-85"></a>Příklad s citlivosti na 85

![85 citlivosti](../media/sensitivity_85.png)

## <a name="next-steps"></a>Další kroky

* [Co je API detekce anomálií?](../overview.md)
* [Rychlé zprovoznění: Detekovat anomálie ve vašich datech časových řad pomocí rozhraní REST API detekce anomálií](../quickstarts/detect-data-anomalies-csharp.md)