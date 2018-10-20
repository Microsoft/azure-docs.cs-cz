---
title: Jak používat rozhraní API pro vyhledávač anomálií s využitím PHP – Microsoft Cognitive Services | Dokumentace Microsoftu
description: Získat informace a ukázky kódu můžete rychle začít používat vyhledávač anomálií s PHP ve službě Cognitive Services.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.component: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: cb149d7a0a1eef2c096a3562f5d0256deca19527
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49458067"
---
# <a name="use-the-anomaly-finder-api-with-php"></a>Vyhledávač anomálií rozhraní API pomocí PHP

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Tento článek obsahuje informace a ukázky kódu, které vám pomohou rychle začít používat rozhraní API pro vyhledávač anomálií s PHP k provedení úlohy získání výsledku anomálií dat časové řady.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-php"></a>Získání bodů anomálií s využitím PHP API vyhledávač anomálií
[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Příklad dat časových řad
Následuje příklad dat časových řad.
[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-php-example"></a>Analýza dat a získat body anomálií příklad PHP
1. Hodnotu `[YOUR_SUBSCRIPTION_KEY]` nahraďte platným klíčem předplatného.
2. Nahradit `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` pomocí příkladu nebo vlastních datových bodů.
3. Spustit a zkontrolovat odpovědi.

```PHP
<?php
# This sample uses the Apache HTTP client from HTTP components (http://hc.apache.org/httpcomponents-client-ga/)
require_once 'HTTP/Request2.php';

$request = new HTTP_Request2('https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection');
$url = $request->getUrl();

$requestData = '[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]';

$headers = array(
    # Request headers
    'Content-Type' => 'application/json',
    # NOTE: Replace the "Ocp-Apim-Subscription-Key" value with a valid subscription key.
    'Ocp-Apim-Subscription-Key' => '[YOUR_SUBSCRIPTION_KEY]',
);

$request->setHeader($headers);

$request->setMethod(HTTP_Request2::METHOD_POST);

# Request body
$request->setBody($requestData);

try
{
    $response = $request->send();
    echo $response->getBody();
}
catch (HttpException $ex)
{
    echo $ex;
}
?>
```

### <a name="example-response"></a>Příklad odpovědi

Úspěšná odpověď se vrátí ve formátu JSON. Ukázková odpověď vypadá takto.
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [REST API – referenční informace](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
