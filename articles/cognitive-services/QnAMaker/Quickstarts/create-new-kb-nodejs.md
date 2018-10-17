---
title: 'Rychlý start: Vytvoření znalostní báze – REST, Node.js – QnA Maker'
description: Tento rychlý start vás provede procesem vytvoření ukázkové znalostní báze služby QnA Maker prostřednictvím kódu programu. Tato znalostní báze se zobrazí na řídicím panelu Azure účtu rozhraní API služby QnA Maker.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 10/02/2018
ms.author: diberry
ms.openlocfilehash: f0375affa547f657ae36de71901298047359cae2
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2018
ms.locfileid: "48884912"
---
# <a name="quickstart-create-a-qna-maker-knowledge-base-in-nodejs"></a>Rychlý start: Vytvoření znalostní báze QnA Maker v Node.js

Tento rychlý start vás provede programovým vytvořením ukázkové znalostní báze služby QnA Maker. Služba QnA Maker automaticky extrahuje otázky a odpovědí z částečně strukturovaného obsahu, jako jsou například časté otázky, ze [zdrojů dat](../Concepts/data-sources-supported.md). Model pro znalostní bázi je definován v kódu ve formátu JSON poslaném v těle požadavku rozhraní API. 

Tento rychlý start volá rozhraní API služby QnA Maker:
* [Vytvořit znalostní bázi](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
* [Získat podrobnosti operace](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)

## <a name="prerequisites"></a>Požadavky

* [Node.js 6+](https://nodejs.org/en/download/)
* Musíte mít [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pokud chcete získat klíč, vyberte na řídicím panelu **Klíče** v části **Správa prostředků**. 

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-nodejs-repo-note.md)]

## <a name="create-a-knowledge-base-nodejs-file"></a>Vytvořte soubor Node.js znalostní báze

Vytvořte soubor s názvem `create-new-knowledge-base.js`.

## <a name="add-the-required-dependencies"></a>Přidejte požadované závislosti

Na začátek souboru `create-new-knowledge-base.js` přidejte následující řádky na přidání potřebných závislostí do projektu:

[!code-nodejs[Add the dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=1-4 "Add the dependencies")]

## <a name="add-the-required-constants"></a>Přidejte požadované konstanty
Za předcházející požadované závislosti přidejte požadované konstanty pro přístup ke službě QnA Maker. Nahraďte hodnotu proměnné `subscriptionKey` vlastním klíčem služby QnA Maker.

[!code-nodejs[Add the required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=10-19 "Add the required constants")]

## <a name="add-the-kb-model-definition"></a>Přidejte definici modelu znalostní báze

Za konstanty přidejte následující definici modelu znalostní báze. Model po definici provádí převod na řetězec.

[!code-nodejs[Add the KB model definition](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=21-51 "Add the KB model definition")]

## <a name="add-supporting-functions"></a>Přidejte podpůrné funkce

V dalším kroku přidejte následující podpůrné funkce.

1. Přidejte následující funkci na tisk JSON v čitelném formátu:

   [!code-nodejs[Add supporting functions, step 1](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=53-56 "Add supporting functions, step 1")]

2. Přidejte následující funkce pro správu odpovědi HTTP:

   [!code-nodejs[Add supporting functions, step 2](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=58-80 "Add supporting functions, step 2")]

## <a name="add-functions-to-create-kb"></a>Přidejte funkce pro vytvoření znalostní báze

Přidejte následující funkce pro vytvoření požadavku HTTP POST na vytvoření znalostní báze. `Ocp-Apim-Subscription-Key` je klíč služby QnA Maker používaný k ověřování. 

[!code-nodejs[POST Request to API](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=82-109 "POST Request to API")]

Toto volání rozhraní API vrátí odpověď ve formátu JSON, která obsahuje i ID operace. Toto ID operace použijte ke zjištění, jestli byla daná znalostí báze úspěšně vytvořena. 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-functions-to-determine-creation-status"></a>Přidejte funkce ke zjištění stavu vytvoření

Přidejte následující funkci na vytvoření požadavku HTTP GET, který zkontroluje stav operace. `Ocp-Apim-Subscription-Key` je klíč služby QnA Maker používaný k ověřování. 

[!code-nodejs[GET Request to API](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=111-135 "GET Request to API")]

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

## <a name="add-create-kb-function"></a>Přidejte funkci create-kb

Následující funkce je hlavní funkce, která vytvoří znalostní bázi a pak opakuje kontroly stavu. Protože vytvoření znalostní báze může nějakou dobu trvat, je třeba volání na kontrolu stavu opakovat, dokud vrácený stav nebude úspěch nebo neúspěch.

[!code-nodejs[Add create-kb function](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=137-167 "Add create-kb function")]

## <a name="run-the-program"></a>Spuštění programu

Spusťte program zadáním následujícího příkazu na příkazovém řádku. Program pošle požadavek na vytvoření znalostní báze do rozhraní API služby QnA Maker a pak se bude dotazovat na výsledky každých 30 sekund. Každá odpověď je zobrazena v okně konzoly.

```bash
node create-new-knowledge-base.js
```

Jakmile se znalostní báze vytvoří, můžete se na ni podívat na portálu služby QnA Maker na stránce [vašich znalostních bází](https://www.qnamaker.ai/Home/MyServices). 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
