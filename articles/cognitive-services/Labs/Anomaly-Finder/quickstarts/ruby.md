---
title: Jak používat rozhraní API pro vyhledávač anomálií pomocí Ruby - Microsoft Cognitive Services | Dokumentace Microsoftu
description: Získat informace a ukázky kódu můžete rychle začít pracovat pomocí Ruby a rozhraní API pro vyhledávač anomálií ve službě Cognitive Services.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 6eb559f8971583afe9619fb41fe331bd3013bb69
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2018
ms.locfileid: "41987495"
---
# <a name="use-the-anomaly-finder-api-with-ruby"></a>Vyhledávač anomálií rozhraní API pomocí Ruby

Tento článek obsahuje informace a ukázky kódu, které vám pomohou rychle začít používat rozhraní API pro vyhledávač anomálií pomocí Ruby k provedení úlohy získat výsledek detekce anomálií dat časových řad.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-ruby"></a>Zjišťování anomálií body rozhraní API pro vyhledávač anomálií pomocí Ruby 
[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Příklad dat časových řad
V příkladu času datových bodů řady vypadá takto,

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-ruby-example"></a>Analýza dat a získat body anomálií Ruby příklad

Kroky v příkladu jsou následující.

1. Nainstalujte [klienta rest](https://github.com/rest-client/rest-client) spuštěním "nástroje gem rest klient pro instalaci".
2. Uložení níže uvedeného kódu do souboru .rb.
3. Nahraďte `[YOUR_SUBSCRIPTION_KEY]` hodnotu klíčem platné předplatné.
4. Nahradit `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` pomocí příkladu nebo vlastních datových bodů.
5. Spustit a zkontrolovat odpovědi.

```ruby
# https://github.com/rest-client/rest-client
require 'rest_client'

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the subscriptionKey string value with your valid subscription key.
subscription_key = '[YOUR_SUBSCRIPTION_KEY]';

endpoint = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection";

# Replace the request data with your real data.
requestData = '[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]';

header = {
    :content_type => 'application/json',
    :'Ocp-Apim-Subscription-Key' => subscription_key
}

response = RestClient::Request.execute(
    :url => endpoint,
    :method => :post,
    :verify_ssl => true,
    :payload => requestData,
    :header => header)

# You will see the response with anomaly results
puts response.body
```

### <a name="example-response"></a>Příklad odpovědi

Úspěšná odpověď se vrátí ve formátu JSON. Ukázková odpověď vypadá takto.
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [REST API – referenční informace](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
