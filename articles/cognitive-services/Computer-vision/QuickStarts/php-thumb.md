---
title: 'Rychlý start: Vygenerování miniatury – REST, PHP – Počítačové zpracování obrazu'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu vygenerujete ve službách Cognitive Services pomocí počítačového zpracování obrazu s PHP miniaturu obrázku.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: dc2d5c4e5a1a9a098ad8b70b6b7a280c4dd02299
ms.sourcegitcommit: 3d0295a939c07bf9f0b38ebd37ac8461af8d461f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2018
ms.locfileid: "43841892"
---
# <a name="quickstart-generate-a-thumbnail---rest-php---computer-vision"></a>Rychlý start: Vygenerování miniatury – REST, PHP – Počítačové zpracování obrazu

V tomto rychlém startu vygenerujete pomocí počítačového zpracování obrazu miniaturu obrázku.

## <a name="prerequisites"></a>Požadavky

Abyste mohli počítačové zpracování obrazu použít, potřebujete klíč předplatného. Přečtěte si, [jak klíče předplatného získat](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="get-thumbnail-request"></a>Žádost Get Thumbnail

Miniaturu obrázku můžete vygenerovat pomocí [metody Get Thumbnail](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb). Zadáte výšku a šířku, které se mohou od poměru stran vstupního obrázku lišit. Počítačové zpracování obrazu použije chytré oříznutí, které inteligentně identifikuje oblast zájmu a na základě této oblasti vygeneruje souřadnice oříznutí.

Pokud chcete spustit ukázku, postupujte takto:

1. Zkopírujte do editoru následující kód.
1. Místo `<Subscription Key>` použijte platný klíč předplatného.
1. V případě potřeby změňte `uriBase` na umístění, kde jste získali klíče předplatného.
1. Volitelně můžete nastavit `imageUrl` na obrázek, který chcete analyzovat.
1. Uložte soubor s příponou `.php`.
1. Otevřete soubor v okně prohlížeče s podporou PHP.

Tento příklad používá balíček [HTTP_Request2](http://pear.php.net/package/HTTP_Request2) PHP5.

```php
<html>
<head>
    <title>Get Thumbnail Sample</title>
</head>
<body>
<?php
// Replace <Subscription Key> with a valid subscription key.
$ocpApimSubscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to obtain
// your subscription keys. For example, if you obtained your subscription keys
// from westus, replace "westcentralus" in the URL below with "westus".
$uriBase = 'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/';

$imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg';

require_once 'HTTP/Request2.php';

$request = new Http_Request2($uriBase . 'generateThumbnail');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',
    'Ocp-Apim-Subscription-Key' => $ocpApimSubscriptionKey
);
$request->setHeader($headers);

$parameters = array(
    // Request parameters
    'width' => '100',  // Width of the thumbnail.
    'height' => '100', // Height of the thumbnail.
    'smartCropping' => 'true',
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

## <a name="get-thumbnail-response"></a>Odpověď metody Get Thumbnail

Úspěšná odpověď obsahuje binární kód miniatury obrázku. Pokud požadavek selže, bude odpověď obsahovat chybový kód a zprávu, která vám pomůže určit, co se nepovedlo.

## <a name="next-steps"></a>Další kroky

Prozkoumejte rozhraní API pro počítačové zpracování obrazu používané pro analýzu obrázku, zjišťování celebrit a památek, vytvoření miniatury a extrahování tištěného a ručně psaného textu. Pokud chcete rychle vyzkoušet rozhraní API pro počítačové zpracování obrazu, vyzkoušejte [testovací konzolu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Prozkoumat rozhraní API pro počítačové zpracování obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
