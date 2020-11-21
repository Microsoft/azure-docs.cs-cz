---
title: 'Rychlý Start: získání odpovědi z znalostní báze – REST, přejít na QnA Maker'
description: Tento rychlý Start založený na REST vás provede tím, že vám pomůžeme získat odpověď ze znalostní báze prostřednictvím kódu programu.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: how-to
ms.openlocfilehash: 4cfd6aee6e8321322f73ce14b470d007bd0bfad9
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025921"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-go"></a>Rychlý Start: získání odpovědí na otázku ze znalostní báze se službou přejít

V tomto rychlém startu se dozvíte, jak programově získat odpověď z publikované QnA Maker znalostní báze. Znalostní báze obsahuje otázky a odpovědi ze [zdrojů dat](../Concepts/knowledge-base.md) , jako jsou nejčastější dotazy. [Otázka](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) se odešle do služby QnA maker. [Odpověď](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) obsahuje horní předpokládanou odpověď.

[Referenční dokumentace](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker4.0/Runtime)  |  [Ukázka](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/blob/master/documentation-samples/quickstarts/get-answer/get-answer.go)

## <a name="prerequisites"></a>Požadavky

* [Go 1.10.1](https://golang.org/dl/)
* [Visual Studio Code](https://code.visualstudio.com/)
* Musíte mít [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pokud chcete načíst svůj klíč, na řídicím panelu Azure pro prostředek QnA Maker vyberte **klíče** pod **správou prostředků** .
* **Publikování** nastavení stránky. Pokud nemáte publikovanou znalostní bázi, vytvořte prázdnou znalostní bázi a pak importujte znalostní bázi na stránce **Nastavení** a pak klikněte na publikovat. [Tuto základní znalostní bázi](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv)si můžete stáhnout a použít.

    Nastavení stránky publikovat zahrnuje hodnotu POST Route, hodnotu Host a hodnotu EndpointKey.

    ![Publish settings (Nastavení publikování)](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-go-file"></a>Vytvoření souboru přejít

Otevřete VSCode a vytvořte nový soubor s názvem `get-answer.go` a přidejte následující třídu:

```Go
package main

func main() {

}
```

## <a name="add-the-required-dependencies"></a>Přidání požadovaných závislostí

Nad `main` funkci v horní části `get-answer.go` souboru přidejte nezbytné závislosti do projektu:

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/query-kb.go" id="dependencies":::

## <a name="add-a-post-request-to-send-question-and-get-answer"></a>Přidat požadavek POST k odeslání otázky a získat odpověď

Následující kód provede požadavek HTTPS na rozhraní API služby QnA Maker k odeslání otázky do znalostní báze a obdrží odpověď:

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/query-kb.go" id="main":::

`Authorization`Hodnota hlavičky obsahuje řetězec `EndpointKey` .

Přečtěte si další informace o [žádosti](../how-to/metadata-generateanswer-usage.md#generateanswer-request) a [odpovědi](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

## <a name="build-and-run-the-program"></a>Sestavení a spuštění programu

Sestavte a spusťte program z příkazového řádku. Požadavek bude automaticky odesílat do rozhraní API služby QnA Maker a pak bude vytištěn v okně konzoly.

1. Sestavte soubor:

    ```bash
    go build get-answer.go
    ```

1. Spusťte soubor:

    ```bash
    ./get-answer
    ```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]


[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
