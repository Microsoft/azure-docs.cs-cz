---
title: 'Rychlý start: Aktualizace znalostní báze – REST, Node.js – QnA Maker'
titleSuffix: Azure Cognitive Services
description: Tento rychlý start vás provede programovou aktualizací stávající znalostní báze QnA Maker.  Tento soubor JSON umožňuje aktualizovat znalostní bázi přidáním nových zdrojů dat, změnou zdrojů dat nebo odstraněním zdrojů dat.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 10/02/2018
ms.author: diberry
ms.openlocfilehash: 3bbc55b3bb064b2cf4b140a395e99209b71a5ce1
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816208"
---
# <a name="quickstart-update-a-qna-maker-knowledge-base-in-nodejs"></a>Rychlý start: Aktualizace znalostní báze QnA Maker v Node.js

Tento rychlý start vás provede programovou aktualizací stávající znalostní báze QnA Maker.  Tento soubor JSON umožňuje aktualizovat znalostní bázi přidáním nových zdrojů dat, změnou zdrojů dat nebo odstraněním zdrojů dat.

Toto rozhraní API je ekvivalentní úpravě a následnému použití tlačítka **Save and train** (Uložit a natrénovat) na portálu služby QnA Maker.

Tento rychlý start volá rozhraní API služby QnA Maker:
* [Update](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600) – Model pro znalostní bázi je definován v kódu ve formátu JSON poslaném v těle požadavku rozhraní API. 
* [Get Operation Details](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)

## <a name="prerequisites"></a>Požadavky

* [Node.js 6+](https://nodejs.org/en/download/)
* Musíte mít [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pokud chcete získat klíč, vyberte na řídicím panelu **Klíče** v části **Správa prostředků**. 
* ID znalostní báze služby QnA Maker, které najdete v adrese URL v parametru kbid řetězce dotazu, jak vidíte níže.

    ![ID znalostní báze ve službě QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

Pokud znalostní bázi ještě nemáte, můžete si vytvořit ukázkovou znalostní bázi a použít ji v tomto rychlém startu. Přečtěte si o [vytvoření nové znalostní báze](create-new-kb-nodejs.md).

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-nodejs-repo-note.md)]

## <a name="create-a-knowledge-base-nodejs-file"></a>Vytvořte soubor Node.js znalostní báze

Vytvořte soubor s názvem `update-knowledge-base.js`.

## <a name="add-the-required-dependencies"></a>Přidejte požadované závislosti

Na začátek souboru `update-knowledge-base.js` přidejte následující řádky k přidání potřebných závislostí do projektu:

[!code-nodejs[Add the dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=1-4 "Add the dependencies")]

## <a name="add-required-constants"></a>Přidejte požadované konstanty
Za předcházející požadované závislosti přidejte požadované konstanty pro přístup ke službě QnA Maker. Nahraďte hodnotu proměnné `subscriptionKey` vlastním klíčem služby QnA Maker. 

[!code-nodejs[Add required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=10-17 "Add required constants")]

## <a name="add-knowledge-base-id"></a>Přidejte ID znalostní báze

Za předchozí konstanty přidejte ID znalostní báze a přidejte ho k cestě:

[!code-nodejs[Add knowledge base ID](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=19-23 "Add knowledge base ID")]

## <a name="add-the-kb-update-model-definition"></a>Přidejte definici modelu aktualizace znalostní báze

Za konstanty přidejte následující definici aktualizace znalostní báze. Definice aktualizace má tři části:

* add
* update
* delete

Jednotlivé části je možné použít ve stejném požadavku na rozhraní API. 

[!code-nodejs[Add the KB update definition](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=25-53 "Add the KB update definition")]


## <a name="add-supporting-functions"></a>Přidejte podpůrné funkce

V dalším kroku přidejte následující podpůrné funkce.

1. Přidejte následující funkci pro tisk souboru JSON v čitelném formátu:

   [!code-nodejs[Add supporting functions, step 1](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=55-58 "Add supporting functions, step 1")]

2. Přidejte následující funkce pro správu odpovědi HTTP a získání stavu operace vytvoření:

   [!code-nodejs[Add supporting functions, step 2](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=60-82 "Add supporting functions, step 2")]

## <a name="add-patch-request-to-update-kb"></a>Přidejte požadavek PATCH, který aktualizuje znalostní bázi

Přidejte následující funkce pro vytvoření požadavku HTTP PATCH k aktualizaci znalostní báze. `Ocp-Apim-Subscription-Key` je klíč služby QnA Maker používaný k ověřování.

[!code-nodejs[Add PATCH request to update KB](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=84-111 "Add PATCH request to update KB")]

Toto volání rozhraní API vrátí odpověď ve formátu JSON, která obsahuje i ID operace. ID operace je nezbytné k vyžádání stavu, pokud operace není dokončená.

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-10-02T01:23:00Z",
  "lastActionTimestamp": "2018-10-02T01:23:00Z",
  "userId": "335c3841df0b42cdb00f53a49d51a89c",
  "operationId": "e7be3897-88ff-44e5-a06c-01df0e05b78c"
}
```

## <a name="add-get-request-to-determine-operation-status"></a>Přidejte požadavek GET, který určí stav operace

Zkontrolujte stav operace.
    
[!code-nodejs[Add GET request to determine operation status](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=113-137 "Add GET request to determine operation status")]

Toto volání rozhraní API vrátí odpověď ve formátu JSON, která obsahuje i stav operace: 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

Volání opakujte, dokud neskočí úspěchem nebo neúspěchem: 

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-updatekb-method"></a>Přidejte metodu update_kb

Následující metoda aktualizuje znalostní bázi a opakovaně kontroluje stav. Protože vytvoření znalostní báze může nějakou dobu trvat, je třeba volání na kontrolu stavu opakovat, dokud vrácený stav nebude úspěch nebo neúspěch.

[!code-nodejs[Add update_kb method](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=139-169 "Add update_kb method")]

## <a name="run-the-program"></a>Spuštění programu

Spusťte program zadáním následujícího příkazu na příkazovém řádku. Program pošle požadavek na aktualizaci znalostní báze do rozhraní API služby QnA Maker a pak se bude dotazovat na výsledky každých 30 sekund. Každá odpověď se zobrazí v okně konzoly.

```bash
node update-knowledge-base.js
```

Jakmile se znalostní báze aktualizuje, můžete se na ni podívat na portálu služby QnA Maker na stránce [vašich znalostních bází](https://www.qnamaker.ai/Home/MyServices). 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)