---
title: Rychlý start k doménovému modelu s využitím rozhraní API pro počítačové zpracování obrazu s PHP | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: V tomto rychlém startu použijete doménový model k identifikaci památek v obrázku pomocí počítačového zpracování obrazu s PHP ve službách Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 335065b45781dd2712f5416fb03a2f8726182472
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "43769322"
---
# <a name="quickstart-use-a-domain-model---rest-php"></a>Rychlý start: Použití doménového modelu – REST, PHP

V tomto rychlém startu použijete doménový model k identifikaci památek nebo celebrit v obrázku pomocí počítačového zpracování obrazu.

## <a name="prerequisites"></a>Požadavky

Abyste mohli počítačové zpracování obrazu použít, potřebujete klíč předplatného. Přečtěte si, [jak klíče předplatného získat](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="recognize-landmark-request"></a>Žádost Recognize Landmark

Pomocí [metody Recognize Domain Specific Content](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) můžete identifikovat konkrétní sadu objektů v obrázku. Momentálně jsou k dispozici dva modely specifické pro doménu: _celebrities_ a _landmarks_.

Pokud chcete spustit ukázku, postupujte takto:

1. Zkopírujte do editoru následující kód.
1. Místo `<Subscription Key>` použijte platný klíč předplatného.
1. V případě potřeby změňte `uriBase` na umístění, kde jste získali klíče předplatného.
1. Volitelně můžete `imageUrl` nastavit na obrázek, který chcete analyzovat.
1. Volitelně můžete `domain` nastavit na `celebrities` a použít model Celebrities.
1. Uložte soubor s příponou `.php`.
1. Soubor otevřete v okně prohlížeče s podporou PHP.

Následující příklad identifikuje památku v obrázku.

Tento příklad používá balíček [HTTP_Request2](http://pear.php.net/package/HTTP_Request2) PHP5.

```php
<html>
<head>
    <title>Analyze Domain Model Sample</title>
</head>
<body>
<?php
// Replace <Subscription Key> with a valid subscription key.
$ocpApimSubscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to obtain
// your subscription keys. For example, if you obtained your subscription keys
// from westus, replace "westcentralus" in the URL below with "westus".
$uriBase = 'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/';

// Change 'landmarks' to 'celebrities' to use the Celebrities model.
$domain = 'landmarks';

$imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/2/23/Space_Needle_2011-07-04.jpg';

require_once 'HTTP/Request2.php';

$request = new Http_Request2($uriBase . 'models/' . $domain . '/analyze');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',
    'Ocp-Apim-Subscription-Key' => $ocpApimSubscriptionKey
);
$request->setHeader($headers);

$parameters = array(
    // Request parameters
    'model' => $domain
);
$url->setQueryVariables($parameters);

$request->setMethod(HTTP_Request2::METHOD_POST);

// Request body parameters
$body = json_encode(array('url' => $imageUrl));

// Request body
$request->setBody($body);

try
{
    $response = $request->send();
    echo "<pre>" .
        json_encode(json_decode($response->getBody()), JSON_PRETTY_PRINT) . "</pre>";
}
catch (HttpException $ex)
{
    echo "<pre>" . $ex . "</pre>";
}
?>
</body>
</html>
```

## <a name="recognize-landmark-response"></a>Odpověď metody Recognize Landmark

Úspěšná odpověď se vrátí ve formátu JSON, například:

```json
{
    "result": {
        "landmarks": [
            {
                "name": "Space Needle",
                "confidence": 0.9998177886009216
            }
        ]
    },
    "requestId": "4d26587b-b2b9-408d-a70c-1f8121d84b0d",
    "metadata": {
        "height": 4132,
        "width": 2096,
        "format": "Jpeg"
    }
}
```

## <a name="next-steps"></a>Další kroky

Prozkoumejte rozhraní API pro počítačové zpracování obrazu používané pro analýzu obrázku, zjišťování celebrit a památek, vytvoření miniatury a extrahování tištěného a ručně psaného textu. Pokud chcete rychle vyzkoušet rozhraní API pro počítačové zpracování obrazu, vyzkoušejte [testovací konzolu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Prozkoumat rozhraní API pro počítačové zpracování obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
