---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: 0677a361e853f778894b6a62a054636e3276b364
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2019
ms.locfileid: "74424409"
---
Tento rychlý Start založený na složeném startu vás provede tím, že získáte odpověď ze znalostní báze.

## <a name="prerequisites"></a>Požadavky

* Poslední [**kudrlinkou**](https://curl.haxx.se/)
* Musíte mít [QnA maker službu](../How-To/set-up-qnamaker-service-azure.md) a mít [znalostní bázi s dotazy a odpověďmi](../Tutorials/create-publish-query-in-portal.md).

## <a name="publish-to-get-endpoint"></a>Publikování pro získání koncového bodu

Až budete připraveni vygenerovat odpověď na otázku ze znalostní báze Knowledge Base, [publikujte](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) znalostní bázi.

## <a name="use-production-endpoint-with-curl"></a>Použití výrobního koncového bodu s kudrlinkou

Když je vaše znalostní báze publikována, zobrazí se na stránce **publikovat** nastavení požadavku HTTP, které vygeneruje odpověď. Karta **kudrlinkou** zobrazuje nastavení požadovaná pro vygenerování odpovědi z nástroje příkazového řádku, [kudrlinkou](https://www.getpostman.com).

[![publikování výsledků](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png)](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png#lightbox)

Pokud chcete vygenerovat odpověď s KUDRLINKOU, proveďte následující kroky:

1. Zkopírujte text na kartě KUDRLINKOU. 
1. Otevřete příkazový řádek nebo terminál a vložte text.
1. Upravte otázku, která bude relevantní pro vaši znalostní bázi. Dejte pozor, abyste neodebrali obsahující kód JSON, který tuto otázku obklopuje.
1. Zadejte příkaz. 
1. Odpověď obsahuje relevantní informace o odpovědi. 

    ```bash
    > curl -X POST https://qnamaker-f0.azurewebsites.net/qnamaker/knowledgebases/1111f8c-d01b-4698-a2de-85b0dbf3358c/generateAnswer -H "Authorization: EndpointKey 111841fb-c208-4a72-9412-03b6f3e55ca1" -H "Content-type: application/json" -d "{'question':'How do I programmatically update my Knowledge Base?'}"
    {
      "answers": [
        {
          "questions": [
            "How do I programmatically update my Knowledge Base?"
          ],
          "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update",
          "score": 100.0,
          "id": 18,
          "source": "Custom Editorial",
          "metadata": [
            {
              "name": "category",
              "value": "api"
            }
          ]
        }
      ]
    }
    ```

## <a name="use-staging-endpoint-with-curl"></a>Použití pracovního koncového bodu s kudrlinkou

Pokud chcete získat odpověď z pracovního koncového bodu, použijte vlastnost text `isTest`.

```json
isTest:true
```


