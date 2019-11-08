---
title: 'Rychlý Start: publikování znalostní báze, REST, přejít-QnA Maker'
titleSuffix: Azure Cognitive Services
description: Díky tomu bude rychlý Start založený na REST publikovat vaši znalostní bázi a vytvoří koncový bod, který se dá volat ve vaší aplikaci nebo ve robotovi chatu.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/02/2019
ms.author: diberry
ms.openlocfilehash: 45ce9720a840c6bc82487ed574b6577304eff4a8
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73794097"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-go"></a>Rychlý start: Publikování znalostní báze ve službě QnA Maker pomocí jazyka Go

Tento rychlý Start založený na REST vás provede programově publikováním znalostní báze (KB). Publikování nabídne nejnovější verzi znalostní báze do vyhrazeného indexu služby Azure Kognitivní hledání a vytvoří koncový bod, který se dá volat ve vaší aplikaci nebo robotovi pro chat.

Tento rychlý start volá rozhraní API služby QnA Maker:
* [Publikování](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) –toto rozhraní API nevyžaduje v těle požadavku žádné informace.

## <a name="prerequisites"></a>Požadavky

* [Go 1.10.1](https://golang.org/dl/)
* Potřebujete [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pokud chcete načíst svůj klíč a koncový bod (včetně názvu prostředku), vyberte pro prostředek v Azure Portal **rychlý Start** .

* ID znalostní báze služby QnA Maker, které najdete v adrese URL v parametru kbid řetězce dotazu, jak vidíte níže.

    ![ID znalostní báze ve službě QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Pokud znalostní bázi ještě nemáte, můžete si vytvořit ukázkovou znalostní bázi a použít ji v tomto rychlém startu. Přečtěte si o [vytvoření nové znalostní báze](create-new-kb-csharp.md).

> [!NOTE] 
> Kompletní soubory řešení jsou k dispozici v [úložišti GitHub **Azure-Samples/vnímání-Services-qnamakerem-přejít** ](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-go-file"></a>Vytvoření souboru přejít

Otevřete VSCode a vytvořte nový soubor s názvem `publish-kb.go`.

## <a name="add-the-required-dependencies"></a>Přidání požadovaných závislostí

Na začátek souboru `publish-kb.go` přidejte následující řádky k přidání potřebných závislostí do projektu:

[!code-go[Add the required dependencies](~/samples-qnamaker-go/documentation-samples/quickstarts/publish-knowledge-base/publish-kb.go?range=3-7 "Add the required dependencies")]

## <a name="create-the-main-function"></a>Vytvoření funkce main

Po požadovaných závislostech přidejte následující třídu:

```Go
package main

func main() {

}
```

## <a name="add-required-constants"></a>Přidání požadovaných konstant

Uvnitř **Hlavní**


 funkce, přidejte požadované konstanty pro přístup k QnA Maker. Nahraďte hodnoty vlastními.

[!code-go[Add the required constants](~/samples-qnamaker-go/documentation-samples/quickstarts/publish-knowledge-base/publish-kb.go?range=16-20 "Add the required constants")]

## <a name="add-post-request-to-publish-kb"></a>Přidejte požadavek POST pro publikování znalostní báze

Po požadovaných konstantách přidejte následující kód, který vytvoří požadavek HTTPS rozhraní API služby QnA Maker k publikování znalostní báze a obdrží odpověď:

[!code-go[Add a POST request to publish KB](~/samples-qnamaker-go/documentation-samples/quickstarts/get-answer/get-answer.go?range=35-48 "Add a POST request to publish KB")]

Volání API vrátí v případě úspěšného publikování stav 204, přičemž tělo odpovědi bude prázdné. Kód v případě odpovědi se stavem 204 přidá obsah.

V případě jakékoli jiné odpověď se vrátí tato odpověď beze změny.

## <a name="build-and-run-the-program"></a>Sestavení a spuštění programu

Zadáním následujícího příkazu soubor zkompilujte. Příkazový řádek nevrací žádné informace o úspěšném sestavení.

```bash
go build publish-kb.go
```

Spusťte program zadáním následujícího příkazu na příkazovém řádku. Pošle požadavek rozhraní API služby QnA Maker k publikování KB a pak vytiskněte 204 pro úspěch nebo chyby.

```bash
./publish-kb
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Další kroky

Po publikování znalostní báze budete potřebovat [adresu URL koncového bodu, aby se vygenerovala odpověď](../Tutorials/create-publish-answer.md#generating-an-answer). 

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
