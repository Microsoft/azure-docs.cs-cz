---
title: Použití rozhraní API vyhledávací anomálií s cURL - kognitivní služby Microsoft | Microsoft Docs
description: Získání informací o vám pomohou rychle začít, pomocí cURL a rozhraní API vyhledávací anomálií v kognitivní služby.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 3c1d791b8c0478715b4ffa93cd7dfa43f9be4586
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343009"
---
# <a name="use-the-anomaly-finder-api-with-curl"></a>Rozhraní API vyhledávací anomálií pomocí cURL

Tento článek obsahuje informace a ukázky kódu, které vám pomohou rychle začít, pomocí rozhraní API vyhledávací anomálií cURL k provedení úlohy získání anomálií výsledek data časové řady.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-the-anomaly-finder-api-using-curl"></a>Získání bodů anomálií s rozhraním API vyhledávací anomálií pomocí cURL 

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Příklad data časové řady

Následuje příklad času řadu datových bodů.

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-curl-example"></a>Analýza dat a získat body anomálií příklad cURL

Takto vypadají kroky v příkladu.

1. Nahraďte `[YOUR_SUBSCRIPTION_KEY]` hodnotu s klíč platné předplatné.
2. Nahraďte `[YOUR_REGION]` a použít tak umístění, kde získat klíče pro předplatné.
3. Nahraďte `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` s příklad nebo vlastních datových bodů.
4. Spuštění a zkontrolujte odpovědi.

```cURL

curl -v -X POST "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection"
-H "Content-Type: application/json"
-H "Ocp-Apim-Subscription-Key: [YOUR_SUBSCRIPTION_KEY]"
--data-ascii "[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]" 

```

### <a name="example-response"></a>Příklad odpovědi
Úspěšná odpověď se vrátí ve formátu JSON. Příklad odpovědi je následující: [!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [REST API – referenční informace](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
