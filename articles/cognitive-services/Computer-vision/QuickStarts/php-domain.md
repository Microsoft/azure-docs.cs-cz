---
title: 'Rychlý start: Obsah specifický pro doménu image - REST, PHP'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu použijete doménový model k identifikaci památek na obrázku pomocí rozhraní API pro počítačové zpracování obrazu a PHP.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 19aa1874a93c4a52f6fc5ff75848c1ca6815db1e
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53581330"
---
# <a name="quickstart-recognize-domain-specific-content-using-the-rest-api-and-php-with-computer-vision"></a>Rychlý start: Rozpoznávání obsahu specifického pro doménu pomocí rozhraní REST API a PHP s pro počítačové zpracování obrazu

V tomto rychlém startu použijete doménový model k identifikaci památek nebo (volitelně) celebrit na vzdáleně uloženém obrázku pomocí rozhraní REST API počítačového zpracování obrazu. K rozpoznávání obsahu na obrázku pomocí metody [Recognize Domain Specific Content](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) můžete využít model specifický pro doménu.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

- Musíte mít nainstalovaný [PHP](https://secure.php.net/downloads.php).
- Musíte mít nainstalovaný [Pear](https://pear.php.net).
- Musíte mít klíč předplatného pro počítačové zpracování obrazu. Abyste získali klíč předplatného, přejděte k tématu [Jak získat klíče předplatného](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="create-and-run-the-sample"></a>Vytvoření a spuštění ukázky

Pokud chcete vytvořit a spustit ukázku, postupujte takto:

1. Nainstalujte balíček PHP5 [`HTTP_Request2`](https://pear.php.net/package/HTTP_Request2).
   1. Otevřete okno příkazového řádku jako správce.
   1. Spusťte následující příkaz:

      ```console
      pear install HTTP_Request2
      ```

   1. Po úspěšné instalaci balíčku zavřete okno příkazového řádku.

1. Zkopírujte do textového editoru následující kód.
1. Proveďte v kódu na příslušných místech následující změny:
    1. Hodnotu `subscriptionKey` nahraďte klíčem předplatného.
    1. Hodnotu `uriBase` nahraďte adresou URL koncového bodu metody [Recognize Domain Specific Content](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) z oblasti Azure, kde jste získali klíče předplatného, pokud je to potřeba.
    1. Volitelně můžete hodnotu `imageUrl` nahradit adresou URL jiného obrázku, který chcete analyzovat.
    1. Volitelně můžete hodnotu parametru požadavku `domain` nahradit hodnotou `celebrites`, pokud chcete místo doménového modelu `landmarks` použít doménový model `celebrities`.
1. Uložte kód jako soubor s příponou `.php`. Například, `use-domain-model.php`.
1. Otevřete okno prohlížeče s podporou PHP.
1. Přetáhněte daný soubor do okna prohlížeče.

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

## <a name="examine-the-response"></a>Prozkoumání odpovědi

Úspěšná odpověď se vrátí ve formátu JSON. Ukázkový web provede analýzu a zobrazí úspěšnou odpověď v okně prohlížeče, podobně jako v následujícím příkladu:

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

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už tuto ukázku nepotřebujete, odstraňte soubor a pak odinstalujte balíček PHP5 `HTTP_Request2`. Odinstalaci balíčku proveďte takto:

1. Otevřete okno příkazového řádku jako správce.
2. Spusťte následující příkaz:

   ```console
   pear uninstall HTTP_Request2
   ```

3. Po úspěšné odinstalaci balíčku zavřete okno příkazového řádku.

## <a name="next-steps"></a>Další postup

Prozkoumejte rozhraní API pro počítačové zpracování obrazu, které se používá pro analýzu obrázku, zjišťování celebrit a památek, vytvoření miniatury a extrahování tištěného a ručně psaného textu. Pokud chcete rychle vyzkoušet rozhraní API pro počítačové zpracování obrazu, vyzkoušejte [testovací konzolu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Prozkoumat rozhraní API pro počítačové zpracování obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
