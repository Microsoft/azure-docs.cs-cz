---
title: Jak používat rozhraní API detektoru anomálií na datech časových řad
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak rozpoznat anomálie v datech buď jako dávku, nebo na streamovaných datech.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: aahi
ms.openlocfilehash: ca93de71f64efaf21c78b37b9c9aee193d13b28d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "71840223"
---
# <a name="how-to-use-the-anomaly-detector-api-on-your-time-series-data"></a>Postup: Použití rozhraní API detektoru anomálií na datech časových řad  

Rozhraní [API detektoru anomálií](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) poskytuje dvě metody detekce anomálií. Můžete buď zjistit anomálie jako dávka v celé řadě časů, nebo jako data jsou generována zjištěním anomálií stav nejnovější datový bod. Model detekce vrátí výsledky anomálií spolu s očekávanou hodnotou každého datového bodu a horní a dolní hranice detekce anomálií. Tyto hodnoty můžete použít k vizualizaci rozsahu normálníhodnoty a anomálie v datech.

## <a name="anomaly-detection-modes"></a>Režimy detekce anomálií 

Rozhraní API detektoru anomálií poskytuje režimy detekce: dávkové a streamování.

> [!NOTE]
> Následující adresy URL požadavků musí být kombinovány s příslušným koncovým bodem pro vaše předplatné. Příklad: `https://<your-custom-subdomain>.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/entire/detect`


### <a name="batch-detection"></a>Detekce dávky

Chcete-li zjistit anomálie v celé dávce datových bodů v daném časovém rozsahu, použijte následující identifikátor URI požadavku s daty časových řad: 

`/timeseries/entire/detect`. 

Odesláním dat časových řad najednou rozhraní API vygeneruje model s celou řadou a analyzuje s ním každý datový bod.  

### <a name="streaming-detection"></a>Detekce streamování

Chcete-li průběžně zjišťovat anomálie na streamovaných datech, použijte následující identifikátor URI požadavku s nejnovějším datovým bodem: 

`/timeseries/last/detect'`. 

Odesláním nových datových bodů při jejich generování můžete sledovat data v reálném čase. Model bude generován s datovými body, které odesíláte a rozhraní API určí, zda poslední bod v časové řadě je anomálie.

## <a name="adjusting-lower-and-upper-anomaly-detection-boundaries"></a>Nastavení dolních a horních hranic detekce anomálií

Ve výchozím nastavení se horní a dolní hranice `expectedValue` `upperMargin`detekce `lowerMargin`anomálií počítají pomocí , , a . Pokud požadujete různé hranice, `marginScale` doporučujeme použít pro `upperMargin` nebo `lowerMargin`. Hranice by se vypočítali takto:

|Hranice  |Výpočet  |
|---------|---------|
|`upperBoundary` | `expectedValue + (100 - marginScale) * upperMargin`        |
|`lowerBoundary` | `expectedValue - (100 - marginScale) * lowerMargin`        |

Následující příklady ukazují výsledek rozhraní API detektoru anomálií při různých citlivostech.

### <a name="example-with-sensitivity-at-99"></a>Příklad s citlivostí při 99

![Výchozí citlivost](../media/sensitivity_99.png)

### <a name="example-with-sensitivity-at-95"></a>Příklad s citlivostí při 95

![Citlivost 99](../media/sensitivity_95.png)

### <a name="example-with-sensitivity-at-85"></a>Příklad s citlivostí při 85

![Citlivost 85](../media/sensitivity_85.png)

## <a name="next-steps"></a>Další kroky

* [Co je rozhraní API Detektoru anomálií?](../overview.md)
* [Úvodní příručka: Detekce anomálií v datech časových řad pomocí rozhraní REST API detektoru anomálií](../quickstarts/detect-data-anomalies-csharp.md)
