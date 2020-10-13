---
title: 'Rychlý start: Vytvoření znalostní báze – REST, Go – QnA Maker'
description: Tento rychlý start založený na REST a jazyce Go vás provede procesem vytvoření ukázkové znalostní báze služby QnA Maker prostřednictvím kódu programu. Tato znalostní báze se zobrazí na řídicím panelu Azure účtu rozhraní API služeb Cognitive Services.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: how-to
ms.openlocfilehash: be314ad74fbb58709f5707e84b193f8ca69b47d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91777709"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-go"></a>Rychlý start: Vytvoření znalostní báze ve službě QnA Maker pomocí jazyka Go

Tento rychlý start vás provede vytvořením ukázkové znalostní báze služby QnA Maker pomocí kódu programu. Služba QnA Maker automaticky extrahuje otázky a odpovědi z částečně strukturovaného obsahu, jako jsou třeba časté otázky, ze [zdrojů dat](../Concepts/knowledge-base.md). Model pro znalostní bázi je definovaný v kódu ve formátu JSON poslaném v těle požadavku rozhraní API.

Tento rychlý start volá rozhraní API služby QnA Maker:
* [Create KB](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [Získat podrobnosti operace](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[Referenční dokumentace](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)  |  [Ukázka přechodu](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/blob/master/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Požadavky

* [Go 1.10.1](https://golang.org/dl/)
* Musíte mít [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pokud chcete načíst svůj klíč a koncový bod (včetně názvu prostředku), vyberte pro prostředek v Azure Portal **rychlý Start** .

## <a name="create-a-knowledge-base-go-file"></a>Vytvoření souboru Go pro znalostní bázi

Vytvořte soubor s názvem `create-new-knowledge-base.go`.

## <a name="add-the-required-dependencies"></a>Přidání požadovaných závislostí

Na začátek souboru `create-new-knowledge-base.go` přidejte následující řádky k přidání potřebných závislostí do projektu:

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="dependencies":::

## <a name="add-the-kb-model-definition"></a>Přidejte definici modelu znalostní báze
Za konstanty přidejte následující definici modelu znalostní báze. Model po definici provádí převod na řetězec.

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="model":::

## <a name="add-supporting-structures-and-functions"></a>Přidání podpůrných struktur a funkcí

V dalším kroku přidejte následující podpůrné funkce.

1. Přidejte strukturu pro odpověď HTTP:

    :::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="response":::

1. Přidejte následující metodu pro zpracování požadavku POST na rozhraní API služby QnA Maker. V tomto rychlém startu se požadavek POST používá k odeslání definice znalostní báze do služby QnA Maker.

    :::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="post":::

1. Přidejte následující metodu pro zpracování požadavku GET na rozhraní API služby QnA Maker. V tomto rychlém startu se požadavek GET používá ke kontrole stavu operace vytváření.

    :::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="get":::

## <a name="add-function-to-create-kb"></a>Přidání funkce pro vytvoření znalostní báze

Přidejte následující funkce pro vytvoření požadavku HTTP POST na vytvoření znalostní báze. **ID operace** _Vytvoření_ se vrátí v **umístění**pole hlavičky odpovědi post a pak se použije jako součást trasy v žádosti o získání. `Ocp-Apim-Subscription-Key` je klíč služby QnA Maker používaný k ověřování.

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="create_kb":::

Toto volání rozhraní API vrátí odpověď ve formátu JSON, která obsahuje i ID operace. Toto ID operace použijte ke zjištění toho, jestli se znalostí báze úspěšně vytvořila.

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

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="get_status":::

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

Následující funkce je hlavní funkce, která vytvoří znalostní bázi a pak opakuje kontroly stavu. Protože vytvoření znalostní báze může nějakou dobu trvat, je třeba volání na kontrolu stavu opakovat, dokud vrácený stav nebude úspěch nebo neúspěch.

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="main":::


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
> [Reference k rozhraní REST API služby QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
