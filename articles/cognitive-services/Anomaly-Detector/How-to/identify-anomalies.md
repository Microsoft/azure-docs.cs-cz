---
title: Jak používat rozhraní API pro detekci anomálií v datech časových řad
titleSuffix: Azure Cognitive Services
description: Naučte se detekovat anomálie ve vašich datech buď jako dávku, nebo na streamovaná data.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: mbullwin
ms.openlocfilehash: 74f891ba7f5b400b5782565e670539167f4e2464
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97703428"
---
# <a name="how-to-use-the-anomaly-detector-api-on-your-time-series-data"></a>Postupy: použití rozhraní API pro detekci anomálií v datech časových řad  

[Rozhraní API detektoru anomálií](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) nabízí dvě metody detekce anomálií. Anomálie můžete detekovat jako dávku v rámci celé řady, nebo když data vygenerujete zjištěním stavu anomálií nejnovějšího datového bodu. Model detekce vrátí výsledky anomálií spolu s očekávanou hodnotou jednotlivých datových bodů a horní a dolní hranice detekce anomálií. Tyto hodnoty můžete použít k vizualizaci rozsahu běžných hodnot a anomálií v datech.

## <a name="anomaly-detection-modes"></a>Režimy detekce anomálií 

Rozhraní API detektoru anomálií poskytuje režimy detekce: Batch a streamování.

> [!NOTE]
> Následující adresy URL musí být kombinovány s příslušným koncovým bodem pro vaše předplatné. Příklad: `https://<your-custom-subdomain>.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/entire/detect`


### <a name="batch-detection"></a>Zjišťování dávky

K detekci anomálií v rámci dávky datových bodů v daném časovém rozsahu použijte následující identifikátor URI žádosti s daty časových řad: 

`/timeseries/entire/detect`. 

Když odesíláte data časových řad najednou, rozhraní API vygeneruje model pomocí celé řady a analyzuje každý datový bod.  

### <a name="streaming-detection"></a>Detekce streamování

Pokud chcete průběžně zjišťovat anomálie při streamování dat, použijte následující identifikátor URI žádosti s vaším nejnovějším datovým bodem: 

`/timeseries/last/detect'`. 

Odesláním nových datových bodů při jejich generování můžete monitorovat data v reálném čase. Model bude vygenerován s datovými body, které odesíláte, a rozhraní API určí, zda je poslední bod v časové řadě anomálií.

## <a name="adjusting-lower-and-upper-anomaly-detection-boundaries"></a>Úprava hranic detekce dolních a horních anomálií

Ve výchozím nastavení se horní a dolní hranice pro detekci anomálií počítají pomocí `expectedValue` , `upperMargin` a `lowerMargin` . Pokud požadujete jiné hranice, doporučujeme použít `marginScale` na `upperMargin` nebo `lowerMargin` . Hranice by se vypočítaly takto:

|Překračuje  |Výpočet  |
|---------|---------|
|`upperBoundary` | `expectedValue + (100 - marginScale) * upperMargin`        |
|`lowerBoundary` | `expectedValue - (100 - marginScale) * lowerMargin`        |

Následující příklady znázorňují výsledek rozhraní API detektoru anomálií v různých Sensitivities.

### <a name="example-with-sensitivity-at-99"></a>Příklad s citlivostí v 99

![Výchozí Citlivost](../media/sensitivity_99.png)

### <a name="example-with-sensitivity-at-95"></a>Příklad s citlivostí v 95

![99 Citlivost](../media/sensitivity_95.png)

### <a name="example-with-sensitivity-at-85"></a>Příklad s citlivostí v 85

![85 Citlivost](../media/sensitivity_85.png)

## <a name="next-steps"></a>Další kroky

* [Co je rozhraní API Detektoru anomálií?](../overview.md)
* [Rychlý Start: zjištění anomálií v datech časových řad pomocí detektoru anomálií](../quickstarts/client-libraries.md)
