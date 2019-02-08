---
title: 'Rychlý start: Získat odpověď ze znalostní báze knowledge base - REST, Go – QnA Maker'
titlesuffix: Azure Cognitive Services
description: V tomto rychlém startu založené na protokolu REST přejít provede získat odpověď ze znalostní báze, prostřednictvím kódu programu.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 11/19/2018
ms.author: diberry
ms.openlocfilehash: e411047e68f13ca24e8937bddbccc261b72bc0d1
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55868408"
---
# <a name="get-answers-to-a-question-from-a-knowledge-base-with-go"></a>Získejte odpovědi na dotaz zadaný ze znalostní báze s využitím Go

Tento rychlý start vás provede prostřednictvím kódu programu získávání odpovědi z publikovaných znalostní báze QnA Maker. Služba QnA Maker automaticky extrahuje otázky a odpovědi z částečně strukturovaného obsahu, jako jsou třeba časté otázky, ze [zdrojů dat](../Concepts/data-sources-supported.md). V textu požadavku rozhraní API se odešle dotaz ve formátu JSON. 

## <a name="prerequisites"></a>Požadavky

* [Go 1.10.1](https://golang.org/dl/)
* [Visual Studio Code](https://code.visualstudio.com/)
* Potřebujete [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Chcete-li načíst kód Product key, vyberte **klíče** pod **správy prostředků** v řídicím panelu Azure pro prostředek nástroje QnA Maker. 
* **Publikování** stránce nastavení. Pokud nemáte publikované znalostní báze, vytvořte prázdný znalostní báze a potom importujte znalostní báze na **nastavení** stránce a potom publikovat. Můžete stáhnout a použít [tento základní znalostní báze](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv). 

    Stránka nastavení publikování zahrnují hodnoty trasy příspěvek, hodnota hostitele a EndpointKey hodnoty. 

    ![Nastavení publikování](../media/qnamaker-quickstart-get-answer/publish-settings.png)

Kód v tomto rychlém startu se [ https://github.com/Azure-Samples/cognitive-services-qnamaker-Go ](https://github.com/Azure-Samples/cognitive-services-qnamaker-Go/tree/master/documentation-samples/quickstarts/get-answer) úložiště. 

## <a name="create-a-go-file"></a>Vytvořte soubor Go

Otevřete VSCode a vytvořte nový soubor s názvem `get-answer.go` a přidejte následující třídu:

```Go
package main

func main() {

}
```

## <a name="add-the-required-dependencies"></a>Přidání požadovaných závislostí

Výše `main` funkce, v horní části `get-answer.go` potřebné závislosti přidejte do projektu:

[!code-go[Add the required dependencies](~/samples-qnamaker-go/documentation-samples/quickstarts/get-answer/get-answer.go?range=3-9 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Přidání požadovaných konstant

V horní části `main` funkci, přidejte požadované konstanty na používání nástroje QnA Maker. Tyto hodnoty jsou na **publikovat** stránce po publikování znalostní báze. 

[!code-go[Add the required constants](~/samples-qnamaker-go/documentation-samples/quickstarts/get-answer/get-answer.go?range=17-33 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-answer"></a>Přidat požadavek POST odeslat dotaz a získejte odpověď

Následující kód odešle požadavek HTTPS API nástroje QnA Maker odeslat dotaz do znalostní báze a přijímat odpovědi:

[!code-go[Add a POST request to send question to knowledge base](~/samples-qnamaker-go/documentation-samples/quickstarts/get-answer/get-answer.go?range=35-48 "Add a POST request to send question to knowledge base")]

`Authorization` Hodnotu hlavičky obsahuje řetězec `EndpointKey `. 

## <a name="build-and-run-the-program"></a>Sestavení a spuštění programu

Sestavit a spustit program z příkazového řádku. Automaticky se odešle požadavek rozhraní API nástroje QnA Maker a vytiskne se do okna konzoly.

1. Sestavení souboru:

    ```bash
    go build get-answer.go
    ```

1. Spusťte soubor:

    ```bash
    ./get-answer
    ```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)] 


[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
