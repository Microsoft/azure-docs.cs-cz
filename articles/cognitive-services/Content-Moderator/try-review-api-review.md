---
title: Vytvoření moderování kontroly pomocí rozhraní REST API konzoly – Content Moderator
titlesuffix: Azure Cognitive Services
description: Azure revizi rozhraní API Content Moderatoru použijte k vytvoření image nebo text revize pro lidské moderování.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/18/2019
ms.author: sajagtap
ms.openlocfilehash: 254269ccedc92b9dfc164cc4665a8a8513682773
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60607503"
---
# <a name="create-human-reviews-rest"></a>Vytvoření recenze prováděné lidmi (REST)

[Revize](./review-api.md#reviews) uložit a zobrazit obsah pro lidské moderátory k vyhodnocení. Přezkoumání dokončení uživatele výsledky se posílají do koncového bodu zadané zpětného volání. V této příručce se dozvíte, jak nastavit revize používat revize rozhraní REST API přes konzolu pro rozhraní API. Jakmile porozumíte strukturu rozhraní API, můžete snadno port těchto volání na jakoukoli platformu REST kompatibilní.

## <a name="prerequisites"></a>Požadavky

- Přihlaste se nebo si vytvořte účet v Content Moderatoru [nástroj pro recenze](https://contentmoderator.cognitive.microsoft.com/) lokality.

## <a name="create-a-review"></a>Vytvořit kontrolu

Vytváření kontroly, přejděte na **[zkontrolovat – vytvoření](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)** rozhraní API odkazovat na stránku a klikněte na tlačítko pro vaše klíčové oblasti (zjistíte ji v adresu URL koncového bodu na **pověření** stránky nástroje [nástroj pro recenze](https://contentmoderator.cognitive.microsoft.com/)). Spustí se rozhraní API konzoly, kde můžete snadno vytvořit a spustit volání rozhraní REST API.

![Kontrola - Get oblast výběru](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Zadejte parametry volání REST

Zadejte hodnoty pro **teamName**, a **Ocp-Apim-Subscription-Key**:

- **teamName**: ID týmu, který jste vytvořili při nastavování vašeho [nástroj pro recenze](https://contentmoderator.cognitive.microsoft.com/) účtu (součástí **Id** pole na obrazovce nástroje zkontrolujte přihlašovací údaje).
- **Ocp-Apim-Subscription-Key**: Content Moderator klíč. Tento nástroj naleznete na **nastavení** karty [nástroj pro recenze](https://contentmoderator.cognitive.microsoft.com).

### <a name="enter-a-review-definition"></a>Zadejte definici revize

Upravit **text žádosti** pole k zadání požadavku JSON u následujících polí:

- **Metadata**: Vlastní páry klíč hodnota má být vrácen do zpětného volání koncového bodu. Pokud klíč je krátký kód, který je definován v [nástroj pro recenze](https://contentmoderator.cognitive.microsoft.com), zobrazí se jako značku.
- **Obsahu**: V případě obsah obrázků a videa Toto je řetězec adresy URL odkazující na obsah. Pro textový obsah jedná se o řetězec vlastní text.
- **ContentId**: Vlastní identifikátor řetězce. Tento řetězec je předán rozhraní API a vrátí přes zpětného volání. Je užitečné pro přidružení k výsledky úlohy moderování interní identifikátory nebo metadata.
- **CallbackEndpoint**: (Volitelné) Adresa URL získat informace zpětného volání při dokončení kontroly.

Výchozí text požadavku jsou uvedeny příklady různých typů kontrol, které můžete vytvořit:

```json
[Image]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Image",
    "Content": "<Content Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Text]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Text",
    "Content": "<Your Text Content>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Video]
[
  {
    "VideoFrames":[
      {
          "Id": "<Frame Id>",
          "Timestamp": "<Frame Timestamp",
          "FrameImage":"<Frame Image URL",
          "Metadata": [
            {
              "Key": "<Key>",
              "Value": "<Value"
            }
          ],
          "ReviewerResultTags": [
          ]
    ], 
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      },
      //For encrypted Videos
        {
          "Key": "protectedType",
          "Value": "AES or FairPlay or Widevine or Playready"
        },
        {
          "Key": "authenticationToken",
          "Value": "your viewtoken(In case of Video Indexer AES encryption type, this value is viewtoken from breakdown json)"
        },
      //For FairPlay encrypted type video include certificateUrl as well
        {
          "Key": "certificateUrl",
          "Value": "your certificate url"
        }
    ],
    "Type": "Video",
    "Content": "<Stream Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>",
    [Optional]
    "Timescale": "<Timescale of the video>
  }
]
```

### <a name="submit-your-request"></a>Odeslání žádosti
  
Vyberte **Poslat**. Pokud je operace úspěšná, **stav odpovědi** je `200 OK`a **obsah odpovědi** zobrazí ID revize. Zkopírujte toto ID se má použít v následujících krocích.

![Projděte si – vytvoření konzoly odpovědi obsahu pole se zobrazí ID revize](images/test-drive-review-2.PNG)

### <a name="examine-the-new-review"></a>Prozkoumat nové kontroly

V [nástroj pro recenze](https://contentmoderator.cognitive.microsoft.com)vyberte **revize** > **Image**/**Text** / **Video** (v závislosti na tom, jaký obsah jste použili). By se měla zobrazit obsah, který jste nahráli, připravené pro recenze prováděné lidmi.

![Zkontrolujte nástroj image soccer koule](images/test-drive-review-5.PNG)

## <a name="get-review-details"></a>Získat podrobnosti o kontrole

K načtení podrobností o stávající revizi, přejděte na [zkontrolovat – získání](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) rozhraní API odkazovat na stránku a klikněte na tlačítko pro vaši oblast (oblasti, ve kterém je spravována klíč).

![Pracovní postup - výběr oblasti Get](images/test-drive-region.png)

Zadejte parametry volání REST stejně jako v předchozím oddílu. V tomto kroku **reviewId** je jedinečný řetězec ID jste získali při vytváření kontroly.

![Projděte si – vytvoření konzoly získat výsledky](images/test-drive-review-3.PNG)
  
Vyberte **Poslat**. Pokud je operace úspěšná, **stav odpovědi** je `200 OK`a **obsah odpovědi** zobrazí podrobnosti o kontrole ve formátu JSON, jako je následující:

```json
{  
  "reviewId":"201712i46950138c61a4740b118a43cac33f434",
  "subTeam":"public",
  "status":"Complete",
  "reviewerResultTags":[  
    {  
      "key":"a",
      "value":"False"
    },
    {  
      "key":"r",
      "value":"True"
    },
    {  
      "key":"sc",
      "value":"True"
    }
  ],
  "createdBy":"<teamname>",
  "metadata":[  
    {  
      "key":"sc",
      "value":"true"
    }
  ],
  "type":"Image",
  "content":"https://reviewcontentprod.blob.core.windows.net/<teamname>/IMG_201712i46950138c61a4740b118a43cac33f434",
  "contentId":"0",
  "callbackEndpoint":"<callbackUrl>"
}
```

Poznamenejte si následující pole v odpovědi:

- **status**
- **reviewerResultTags**: Tím se zobrazí v případě, že všechny značky ručně přidat tým recenze (zobrazí **createdBy** pole).
- **metadata**: To ukazuje značky, které byly původně přidaný v revizi před změnami tým vytvořil recenze prováděné lidmi.

## <a name="next-steps"></a>Další postup

V této příručce zjistili, jak vytvořit moderování obsahu kontroly pomocí rozhraní REST API. V dalším kroku integrovat revize scénáři moderování začátku do konce, jako [E-commerce moderování](./ecommerce-retail-catalog-moderation.md) kurzu.