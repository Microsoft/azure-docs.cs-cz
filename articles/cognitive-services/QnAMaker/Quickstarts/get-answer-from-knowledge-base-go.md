---
title: 'Úvodní příručka: Získejte odpověď ze znalostní báze - REST, Go - QnA Maker'
description: Tento rychlý start založený na go REST vás provede získáním odpovědi z znalostní báze programově.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 37a8925f8460e448dce3b66354f5181fe103a6a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851792"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-go"></a>Úvodní příručka: Získejte odpovědi na otázku z znalostní báze Go

Tento rychlý start vás provede programově získáním odpovědi z publikované znalostní báze QnA Maker. Znalostní báze obsahuje otázky a odpovědi ze [zdrojů dat,](../Concepts/knowledge-base.md) jako jsou časté dotazy. [Otázka](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) je odeslána službě QnA Maker. [Odpověď](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) obsahuje nejlépe předpovídanou odpověď.

[Ukázka referenční dokumentace](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime) | [Sample](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/blob/master/documentation-samples/quickstarts/get-answer/get-answer.go)

## <a name="prerequisites"></a>Požadavky

* [Go 1.10.1](https://golang.org/dl/)
* [Kód visual studia](https://code.visualstudio.com/)
* Musíte mít [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pokud chcete klíč načíst, vyberte **klíče** v části **Správa prostředků** na řídicím panelu Azure pro prostředek QnA Maker.
* **Publikovat** nastavení stránky. Pokud nemáte publikovanou znalostní bázi, vytvořte prázdnou znalostní bázi, importujte znalostní bázi na stránce **Nastavení** a poté ji publikujte. Tuto [základní znalostní bázi](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv)si můžete stáhnout a použít .

    Nastavení stránky publikování zahrnuje hodnotu postupu POST, hodnotu hostitele a hodnotu EndpointKey.

    ![Publish settings (Nastavení publikování)](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-go-file"></a>Vytvoření souboru Go

Otevřete VSCode a vytvořte nový soubor s názvem `get-answer.go` a přidejte následující třídu:

```Go
package main

func main() {

}
```

## <a name="add-the-required-dependencies"></a>Přidání požadovaných závislostí

Nad `main` funkcí v horní části `get-answer.go` souboru přidejte do projektu nezbytné závislosti:

[!code-go[Add the required dependencies](~/samples-qnamaker-go/documentation-samples/quickstarts/get-answer/get-answer.go?range=3-9 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Přidání požadovaných konstant

V horní části `main` funkce přidejte požadované konstanty pro přístup k QnA Maker. Tyto hodnoty jsou na stránce **Publikovat** po publikování znalostní báze.

[!code-go[Add the required constants](~/samples-qnamaker-go/documentation-samples/quickstarts/get-answer/get-answer.go?range=17-33 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-answer"></a>Přidání požadavku POST k odeslání otázky a získání odpovědi

Následující kód provede požadavek HTTPS na rozhraní API qnA maker u odesílání otázky do znalostní báze a obdrží odpověď:

[!code-go[Add a POST request to send question to knowledge base](~/samples-qnamaker-go/documentation-samples/quickstarts/get-answer/get-answer.go?range=35-48 "Add a POST request to send question to knowledge base")]

Hodnota `Authorization` záhlaví obsahuje řetězec `EndpointKey`.

Přečtěte si další informace o [žádosti](../how-to/metadata-generateanswer-usage.md#generateanswer-request) a [odpovědi](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

## <a name="build-and-run-the-program"></a>Sestavení a spuštění programu

Vytvořte a spusťte program z příkazového řádku. Automaticky odešle požadavek do rozhraní QnA Maker API a poté se vytiskne do okna konzoly.

1. Vytvořte soubor:

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