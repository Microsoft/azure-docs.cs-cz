---
title: Vytvoření kontrol moderování pomocí konzoly REST API – Content Moderator
titleSuffix: Azure Cognitive Services
description: Pomocí rozhraní API služby Azure Content Moderator prověřit rozhraní API můžete vytvářet Image nebo textové recenze pro moderování lidí.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: pafarley
ms.openlocfilehash: 479c7c455f07d098edd327196803e85df24dfb6d
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905126"
---
# <a name="create-human-reviews-api-console"></a>Vytváření lidských kontrolorů (konzola API)

[Kontroluje](./review-api.md#reviews) ukládání a zobrazování obsahu pro Moderátoři pro lidské moderování k vyhodnocení. Když uživatel dokončí revizi, výsledky se odešlou do zadaného koncového bodu zpětného volání. V této příručce se dozvíte, jak nastavit recenze pomocí rozhraní API pro kontrolu REST prostřednictvím konzoly API. Jakmile pochopíte strukturu rozhraní API, můžete tato volání snadno přenést na libovolnou platformu kompatibilní s REST.

## <a name="prerequisites"></a>Předpoklady

- Přihlaste se nebo vytvořte účet na webu [Nástroje pro kontrolu](https://contentmoderator.cognitive.microsoft.com/) Content moderator.

## <a name="create-a-review"></a>Vytvořit recenzi

Chcete-li vytvořit revizi, klikněte na stránku **[recenze – vytvořit](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)** rozhraní API a vyberte tlačítko pro vaši oblast klíče (Toto můžete najít v adrese URL koncového bodu na stránce s **přihlašovacími údaji** [Nástroje pro kontrolu](https://contentmoderator.cognitive.microsoft.com/)). Spustí se konzola rozhraní API, kde můžete snadno sestavit a spustit REST API volání.

![Revize – získat výběr oblasti](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Zadejte parametry volání REST

Zadejte hodnoty pro **Členové týmu** a **OCP-APIM-Subscription-Key**:

- ID **týmu**: ID týmu, které jste vytvořili při nastavování účtu [Nástroje pro kontrolu](https://contentmoderator.cognitive.microsoft.com/) (najdete ho v poli **ID** na obrazovce s přihlašovacími údaji nástroje pro kontrolu).
- **OCP-APIM-Subscription-Key**: váš Content moderator klíč. Najdete ho na kartě **Nastavení** [Nástroje pro kontrolu](https://contentmoderator.cognitive.microsoft.com).

### <a name="enter-a-review-definition"></a>Zadejte definici revize.

Upravte pole **Text žádosti** , aby se ZADAL požadavek JSON s následujícími poli:

- **Metadata**: vlastní páry klíč-hodnota, které se mají vrátit do koncového bodu zpětného volání. Pokud je klíč krátkým kódem, který je definován v [nástroji pro revizi](https://contentmoderator.cognitive.microsoft.com), zobrazí se jako značka.
- **Obsah**: v případě obrázku a obsahu videa se jedná o řetězec adresy URL odkazující na obsah. V případě textového obsahu je to skutečný textový řetězec.
- ID **obsahu: vlastní** řetězec identifikátoru. Tento řetězec je předán rozhraní API a vrácen prostřednictvím zpětného volání. Je vhodný pro přidružení interních identifikátorů nebo metadat k výsledkům úlohy moderování.
- **CallbackEndpoint**: (volitelné) adresa URL pro příjem informací o zpětném volání po dokončení revize.

Výchozí text požadavku ukazuje příklady různých typů revizí, které můžete vytvořit:

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
  
Vyberte **Odeslat**. Pokud je operace úspěšná, **stav odpovědi** je `200 OK` a v poli **obsah odpovědi** se zobrazí ID revize. Zkopírujte toto ID, které chcete použít v následujících krocích.

![Revize – pole vytvořit obsah odpovědi konzoly zobrazí ID revize.](images/test-drive-review-2.PNG)

### <a name="examine-the-new-review"></a>Prozkoumejte novou kontrolu

V [nástroji pro revize](https://contentmoderator.cognitive.microsoft.com)vyberte **zkontrolovat**  >  **Image** / **Text** / **video** text obrázek (v závislosti na obsahu, který jste použili). Obsah, který jste nahráli, by měl být připravený k lidské kontrole.

![Obrázek nástroje pro kontrolu fotbalového míče](images/test-drive-review-5.PNG)

## <a name="get-review-details"></a>Získat podrobnosti o kontrole

Pokud chcete načíst podrobnosti o existující revizi, klikněte na stránku [recenze – získat](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) rozhraní API a vyberte tlačítko pro vaši oblast (oblast, ve které je klíč spravovaný).

![Pracovní postup – získat výběr oblasti](images/test-drive-region.png)

Zadejte parametry volání REST jako v předchozí části. Pro tento krok je **reviewId** jedinečným řetězcem ID, který jste obdrželi při vytváření revize.

![Kontrola – vytvoření výsledků získání konzoly](images/test-drive-review-3.PNG)
  
Vyberte **Odeslat**. Pokud je operace úspěšná, **stav odpovědi** je a v `200 OK` poli **obsah odpovědi** se zobrazí podrobnosti o kontrole ve formátu JSON, jako je například následující:

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

- **stav**
- **reviewerResultTags**: Tato položka se zobrazí, pokud byly některé značky přidány pomocí týmu pro lidské revize (zobrazuje se pole **CreatedBy** ).
- **metadata**: zobrazuje značky, které byly původně přidány do revize, před provedením změny v týmu pro lidské revize.

## <a name="next-steps"></a>Další kroky

V této příručce jste zjistili, jak pomocí REST API vytvořit recenze Moderování obsahu. Dále Integrujte recenze do kompletního scénáře moderování, jako je například kurz pro [moderování elektronického obchodování](./ecommerce-retail-catalog-moderation.md) .