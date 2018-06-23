---
title: Použití rozhraní API vyhledávací anomálií s Ruby - kognitivní služby Microsoft | Microsoft Docs
description: Get informace a ukázky kódu můžete rychle začít, Ruby a rozhraní API vyhledávací anomálií v kognitivní služby.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: ca4754514ba5012f7e9e28981d0869d174561fb3
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343012"
---
# <a name="use-the-anomaly-finder-api-with-ruby"></a>Vyhledávací anomálií rozhraní API pomocí Ruby

Tento článek obsahuje informace a ukázky kódu, které vám pomohou rychle začít, pomocí rozhraní API vyhledávací anomálií Ruby k provedení úlohy získání výsledek detekce anomálií data časové řady.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-ruby"></a>Získávání anomálií body s rozhraním API vyhledávací anomálií pomocí Ruby 
[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Příklad data časové řady
Příkladem řady datových bodů času je následujícím způsobem [!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-ruby-example"></a>Analýza dat a získat body anomálií Ruby příklad

Takto vypadají kroky v příkladu.

1. Nainstalujte [klienta rest](https://github.com/rest-client/rest-client) spuštěním "gem instalace rest klienta".
2. Uložte jako soubor .rb níže uvedeného kódu.
3. Nahraďte `[YOUR_SUBSCRIPTION_KEY]` hodnotu s klíč platné předplatné.
4. Nahraďte `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` s příklad nebo vlastních datových bodů.
5. Spuštění a zkontrolujte odpovědi.

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

Úspěšná odpověď se vrátí ve formátu JSON. Ukázková odpověď je následující.
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [REST API – referenční informace](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
