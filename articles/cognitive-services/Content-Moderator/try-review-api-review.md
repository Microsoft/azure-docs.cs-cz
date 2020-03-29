---
title: Vytváření recenzí moderování pomocí konzoly REST API – Content Moderator
titleSuffix: Azure Cognitive Services
description: Pomocí rozhraní API pro kontrolu obsahu Azure můžete vytvářet grafické nebo textové recenze pro lidské moderování.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: pafarley
ms.openlocfilehash: a9726e41a84926d00d48b51e31f534a3d8c2fe0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "72757145"
---
# <a name="create-human-reviews-rest"></a>Vytvořit lidské recenze (REST)

[Recenze](./review-api.md#reviews) ukládat a zobrazovat obsah pro lidské moderátory posoudit. Když uživatel dokončí kontrolu, výsledky jsou odeslány do zadaného koncového bodu zpětného volání. V této příručce se dozvíte, jak nastavit recenze pomocí revizní ho rozhraní REST API prostřednictvím konzoly rozhraní API. Jakmile porozumíte struktuře rozhraní API, můžete tato volání snadno přenést na libovolnou platformu kompatibilní s rest.

## <a name="prerequisites"></a>Požadavky

- Přihlaste se nebo si vytvořte účet na webu [nástroje Kontrola](https://contentmoderator.cognitive.microsoft.com/) moderátora obsahu.

## <a name="create-a-review"></a>Vytvoření recenze

Chcete-li vytvořit recenzi, přejděte na stránku **[Recenze – vytvoření](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)** referenční stránky rozhraní API a vyberte tlačítko pro oblast klíče (najdete ji v adrese URL koncového bodu na stránce Přihlašovací **údaje** [nástroje Revize).](https://contentmoderator.cognitive.microsoft.com/) Tím se spustí konzola rozhraní API, kde můžete snadno vytvořit a spustit volání rozhraní REST API.

![Recenze – výběr oblasti](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Zadat parametry volání REST

Zadejte hodnoty pro **teamName**a **Ocp-Apim-Subscription-Key**:

- **TeamName**: ID týmu, které jste vytvořili při nastavování účtu [nástroje revize](https://contentmoderator.cognitive.microsoft.com/) (najdete v poli **Id** na obrazovce Pověření nástroje pro kontrolu).
- **Ocp-Apim-Subscription-Key**: Klíč moderátora obsahu. Najdete ji na kartě **Nastavení** [nástroje Revize](https://contentmoderator.cognitive.microsoft.com).

### <a name="enter-a-review-definition"></a>Zadejte definici recenze.

Chcete-li zadat požadavek JSON s následujícími poli, upravte pole **Tělo požadavku:**

- **Metadata**: Vlastní dvojice klíč-hodnota, které mají být vráceny do koncového bodu zpětného volání. Pokud je klíčem krátký kód definovaný v [nástroji revize](https://contentmoderator.cognitive.microsoft.com), zobrazí se jako značka.
- **Obsah**: V případě obsahu obrázku a videa se jedná o řetězec URL odkazující na obsah. Pro textový obsah se jedná o skutečný textový řetězec.
- **ContentId**: Řetězec vlastního identifikátoru. Tento řetězec je předán do rozhraní API a vrácena prostřednictvím zpětného volání. Je užitečné pro připojování vnitřníidentifikátory nebo metadata s výsledky úlohy moderování.
- **CallbackEndpoint**: (Volitelné) Adresa URL pro příjem informací o zpětném volání po dokončení recenze.

Výchozí tělo požadavku zobrazuje příklady různých typů recenzí, které můžete vytvořit:

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

### <a name="submit-your-request"></a>Odešlete svůj požadavek
  
Vyberte **Poslat**. Pokud je operace úspěšná, `200 OK` **je stav odpověď** a v poli Obsah **odpovědi** se zobrazí ID kontroly. Zkopírujte toto ID, které se použije v následujících krocích.

![Recenze – pole Vytvořit obsah odpovědi konzoly zobrazuje ID recenze.](images/test-drive-review-2.PNG)

### <a name="examine-the-new-review"></a>Prozkoumat nový přezkum

V [nástroji revize](https://contentmoderator.cognitive.microsoft.com)vyberte **Zkontrolovat** > **textové**/**video** **obrázku**/(v závislosti na použitém obsahu). Obsah, který jste nahráli, by se měl zobrazit a připravit se na lidskou kontrolu.

![Zkontrolujte obrázek nástroje fotbalového míče](images/test-drive-review-5.PNG)

## <a name="get-review-details"></a>Získat podrobnosti o recenzích

Chcete-li načíst podrobnosti o existující recenzi, přejděte na stránku [recenze – získat](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) referenční kartu API a vyberte tlačítko pro vaši oblast (oblast, ve které je váš klíč spravován).

![Pracovní postup – výběr oblasti](images/test-drive-region.png)

Zadejte parametry volání REST jako ve výše uvedené části. V tomto kroku **reviewId** je jedinečný řetězec ID, který jste obdrželi při vytváření recenze.

![Recenze – vytvoření konzoly Získat výsledky](images/test-drive-review-3.PNG)
  
Vyberte **Poslat**. Pokud je operace úspěšná, `200 OK` **je stav Odpověď** a v poli Obsah **odpovědi** se zobrazí podrobnosti o kontrole ve formátu JSON, například následující:

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

V odpovědi si poznamenejte následující pole:

- **status**
- **reviewerResultTags**: Zobrazí se, pokud byly některé značky ručně přidány týmem pro kontrolu člověka (zobrazeno pole **createdBy).**
- **Metadata**: Zobrazí značky, které byly původně přidány do recenze, než tým pro lidské recenze provedl změny.

## <a name="next-steps"></a>Další kroky

V této příručce jste se naučili, jak vytvořit recenze moderování obsahu pomocí rozhraní REST API. Dále integrujte recenze do scénáře moderování od konce, jako je například kurz [moderování elektronického obchodování.](./ecommerce-retail-catalog-moderation.md)