---
title: 'Rychlý Start: extrakce vytištěného textu – REST, kudrlinkou'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu extrahujete vytištěný text z obrázku pomocí rozhraní API pro počítačové zpracování obrazu s kudrlinkou.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 2f4b201a425075d5be55d831fd23bb1133f70256
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176872"
---
# <a name="quickstart-extract-printed-text-ocr-using-the-computer-vision-rest-api-and-curl"></a>Rychlý Start: extrakce vytištěného textu (OCR) pomocí Počítačové zpracování obrazu REST API a kudrlinkou

V tomto rychlém startu extrahujete vytištěný text pomocí optického rozpoznávání znaků (OCR) z obrázku pomocí REST API Počítačové zpracování obrazu. Pomocí metody [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) můžete detekovat vytištěný text v obrázku a extrahovat rozpoznané znaky do datového proudu znaků, který je k počítači použitelný.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

- Musíte mít [oblé](https://curl.haxx.se/windows).
- Pro Počítačové zpracování obrazu musíte mít klíč předplatného. Bezplatný zkušební klíč si můžete [vyzkoušet Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Případně postupujte podle pokynů v části [Vytvoření účtu Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pro přihlášení k odběru počítačové zpracování obrazu a získání klíče.

## <a name="create-and-run-the-sample-command"></a>Vytvoření a spuštění ukázkového příkazu

Chcete-li vytvořit a spustit ukázku, proveďte následující kroky:

1. Zkopírujte následující příkaz do textového editoru.
1. V případě potřeby proveďte v příkazu následující změny:
    1. Hodnotu `<subscriptionKey>` nahraďte klíčovým předplatným.
    1. Nahraďte první část adresy URL požadavku (`westcentralus`) textem ve vaší vlastní adrese URL koncového bodu.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Volitelně můžete změnit adresu URL obrázku v textu žádosti (`https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\`) na adresu URL jiného obrázku, který se má analyzovat.
1. Otevřete okno příkazového řádku.
1. Vložte příkaz z textového editoru do okna příkazového řádku a potom spusťte příkaz.

```bash
curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.1/ocr?language=unk&detectOrientation=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\"}"
```

## <a name="examine-the-response"></a>Projděte si odpověď.

Ve formátu JSON se vrátí úspěšná odpověď. Ukázková aplikace analyzuje a zobrazuje úspěšnou odpověď v okně příkazového řádku, podobně jako v následujícím příkladu:

```json
{
  "language": "en",
  "orientation": "Up",
  "textAngle": 0,
  "regions": [
    {
      "boundingBox": "21,16,304,451",
      "lines": [
        {
          "boundingBox": "28,16,288,41",
          "words": [
            {
              "boundingBox": "28,16,288,41",
              "text": "NOTHING"
            }
          ]
        },
        {
          "boundingBox": "27,66,283,52",
          "words": [
            {
              "boundingBox": "27,66,283,52",
              "text": "EXISTS"
            }
          ]
        },
        {
          "boundingBox": "27,128,292,49",
          "words": [
            {
              "boundingBox": "27,128,292,49",
              "text": "EXCEPT"
            }
          ]
        },
        {
          "boundingBox": "24,188,292,54",
          "words": [
            {
              "boundingBox": "24,188,292,54",
              "text": "ATOMS"
            }
          ]
        },
        {
          "boundingBox": "22,253,297,32",
          "words": [
            {
              "boundingBox": "22,253,105,32",
              "text": "AND"
            },
            {
              "boundingBox": "144,253,175,32",
              "text": "EMPTY"
            }
          ]
        },
        {
          "boundingBox": "21,298,304,60",
          "words": [
            {
              "boundingBox": "21,298,304,60",
              "text": "SPACE."
            }
          ]
        },
        {
          "boundingBox": "26,387,294,37",
          "words": [
            {
              "boundingBox": "26,387,210,37",
              "text": "Everything"
            },
            {
              "boundingBox": "249,389,71,27",
              "text": "else"
            }
          ]
        },
        {
          "boundingBox": "127,431,198,36",
          "words": [
            {
              "boundingBox": "127,431,31,29",
              "text": "is"
            },
            {
              "boundingBox": "172,431,153,36",
              "text": "opinion."
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Další kroky

Prozkoumejte rozhraní API pro počítačové zpracování obrazu slouží k analýze obrázku, detekci celebrit a orientačních bodů, vytvoření miniatury a extrakci vytištěného a rukopisného textu. Chcete-li rychle experimentovat s rozhraní API pro počítačové zpracování obrazu, vyzkoušejte [konzolu Open API Testing](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Prozkoumat rozhraní API pro počítačové zpracování obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
