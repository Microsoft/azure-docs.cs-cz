---
title: 'Rychlý start: Vytvoření znalostní báze – REST, Go – QnA Maker'
titlesuffix: Azure Cognitive Services
description: Tento rychlý start založený na REST a jazyce Go vás provede procesem vytvoření ukázkové znalostní báze služby QnA Maker prostřednictvím kódu programu. Tato znalostní báze se zobrazí na řídicím panelu Azure účtu rozhraní API služeb Cognitive Services.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 11/06/2018
ms.author: diberry
ms.openlocfilehash: a1f477dd02e048a3bfb77463c2d9857ee32fb8fb
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51235305"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-go"></a>Rychlý start: Vytvoření znalostní báze ve službě QnA Maker pomocí jazyka Go

Tento rychlý start vás provede vytvořením ukázkové znalostní báze služby QnA Maker pomocí kódu programu. Služba QnA Maker automaticky extrahuje otázky a odpovědi z částečně strukturovaného obsahu, jako jsou třeba časté otázky, ze [zdrojů dat](../Concepts/data-sources-supported.md). Model pro znalostní bázi je definovaný v kódu ve formátu JSON poslaném v těle požadavku rozhraní API. 

Tento rychlý start volá rozhraní API služby QnA Maker:
* [Create KB](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
* [Get Operation Details](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)

## <a name="prerequisites"></a>Požadavky

* [Go 1.10.1](https://golang.org/dl/)
* Potřebujete [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pokud chcete získat klíč, vyberte na řídicím panelu **Klíče** v části **Správa prostředků**. 

## <a name="create-a-knowledge-base-go-file"></a>Vytvoření souboru Go pro znalostní bázi

Vytvořte soubor s názvem `create-new-knowledge-base.go`.

## <a name="add-the-required-dependencies"></a>Přidání požadovaných závislostí

Na začátek souboru `create-new-knowledge-base.go` přidejte následující řádky na přidání potřebných závislostí do projektu:

[!code-go[Add the required dependencies](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=1-11 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Přidejte požadované konstanty
Za předcházející požadované závislosti přidejte požadované konstanty pro přístup ke službě QnA Maker. Nahraďte hodnotu proměnné `subscriptionKey` vlastním klíčem služby QnA Maker.

[!code-go[Add the required constants](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=13-20 "Add the required constants")]

## <a name="add-the-kb-model-definition"></a>Přidejte definici modelu znalostní báze
Za konstanty přidejte následující definici modelu znalostní báze. Model po definici provádí převod na řetězec.

[!code-go[Add the KB model definition](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=22-44 "Add the KB model definition")]

## <a name="add-supporting-structures-and-functions"></a>Přidání podpůrných struktur a funkcí

V dalším kroku přidejte následující podpůrné funkce.

1. Přidejte strukturu pro požadavek HTTP:

    [!code-go[Add the structure for an HTTP request](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=46-49 "Add the structure for an HTTP request")]

2. Přidejte následující metodu pro zpracování požadavku POST na rozhraní API služby QnA Maker. V tomto rychlém startu se požadavek POST používá k odeslání definice znalostní báze do služby QnA Maker.

    [!code-go[Add the POST method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=51-66 "Add the POST method")]

3. Přidejte následující metodu pro zpracování požadavku GET na rozhraní API služby QnA Maker. V tomto rychlém startu se požadavek GET používá ke kontrole stavu operace vytváření. 

    [!code-go[Add the GET method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=68-83 "Add the GET method")]

## <a name="add-function-to-create-kb"></a>Přidání funkce pro vytvoření znalostní báze

Přidejte následující funkce pro vytvoření požadavku HTTP POST na vytvoření znalostní báze. V poli hlavičky odpovědi POST **Location** se vrátí **ID operace** _vytváření_, které se pak použije jako část trasy v požadavku GET. `Ocp-Apim-Subscription-Key` je klíč služby QnA Maker používaný k ověřování. 

[!code-go[Add the create_kb method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=85-97 "Add the create_kb method")]

Toto volání rozhraní API vrátí odpověď JSON, která v poli hlavičky **Location** obsahuje ID operace. Toto ID operace použijte ke zjištění toho, jestli se znalostí báze úspěšně vytvořila. 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-function-to-get-status"></a>Přidání funkce pro získání stavu

Přidejte následující funkci na vytvoření požadavku HTTP GET, který zkontroluje stav operace. `Ocp-Apim-Subscription-Key` je klíč služby QnA Maker používaný k ověřování. 

[!code-go[Add the check_status method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=99-108 "Add the check_status method")]

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
## <a name="add-main-function"></a>Přidání funkce main

Následující funkce je hlavní funkce, která vytvoří znalostní bázi a pak opakuje kontroly stavu. Jelikož může vytvoření znalostní báze nějakou dobu trvat, je potřeba volání pro kontrolu stavu opakovat, dokud vrácený stav neoznámí úspěch nebo neúspěch.

[!code-go[Add the main method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=110-140 "Add the main method")]


## <a name="compile-the-program"></a>Kompilace programu
Zadáním následujícího příkazu soubor zkompilujte. Příkazový řádek nevrací žádné informace o úspěšném sestavení.

```bash
go build create-new-knowledge-base.go
```

## <a name="run-the-program"></a>Spuštění programu

Spusťte program zadáním následujícího příkazu na příkazovém řádku. Program pošle požadavek na vytvoření znalostní báze do rozhraní API služby QnA Maker a pak se bude dotazovat na výsledky každých 30 sekund. Každá odpověď se zobrazí v okně konzoly.

```bash
go run create-new-knowledge-base
```

Jakmile se znalostní báze vytvoří, můžete se na ni podívat na portálu služby QnA Maker na stránce [vašich znalostních bází](https://www.qnamaker.ai/Home/MyServices). 

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)