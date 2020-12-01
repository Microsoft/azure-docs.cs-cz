---
title: 'Rychlý Start: publikování znalostní báze, REST, přejít-QnA Maker'
description: Díky tomu bude rychlý Start založený na REST publikovat vaši znalostní bázi a vytvoří koncový bod, který se dá volat ve vaší aplikaci nebo ve robotovi chatu.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: how-to
ms.openlocfilehash: d876be45789041cddba269784a9c1e62e5907bc2
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352248"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-go"></a>Rychlý start: Publikování znalostní báze ve službě QnA Maker pomocí jazyka Go

Tento rychlý Start založený na REST vás provede programově publikováním znalostní báze (KB). Publikování nabídne nejnovější verzi znalostní báze do vyhrazeného indexu služby Azure Kognitivní hledání a vytvoří koncový bod, který se dá volat ve vaší aplikaci nebo robotovi pro chat.

Tento rychlý start volá rozhraní API služby QnA Maker:
* [Publikování](/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) –toto rozhraní API nevyžaduje v těle požadavku žádné informace.

## <a name="prerequisites"></a>Požadavky

* [Go 1.10.1](https://golang.org/dl/)
* Musíte mít [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pokud chcete načíst svůj klíč a koncový bod (včetně názvu prostředku), vyberte pro prostředek v Azure Portal **rychlý Start** .

* V adrese URL parametru řetězce dotazu bylo nalezeno QnA Maker znalostní báze (KB) `kbid` , jak je uvedeno níže.

    ![ID znalostní báze ve službě QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Pokud znalostní bázi ještě nemáte, můžete si vytvořit ukázkovou znalostní bázi a použít ji v tomto rychlém startu. Přečtěte si o [vytvoření nové znalostní báze](create-new-kb-csharp.md).

> [!NOTE]
> Kompletní soubory řešení jsou k dispozici v [úložišti GitHub **Azure-Samples/vnímání-Services-qnamakerem-přejít**](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-go-file"></a>Vytvoření souboru přejít

Otevřete VSCode a vytvořte nový soubor s názvem `publish-kb.go` .

## <a name="add-the-required-dependencies"></a>Přidání požadovaných závislostí

Na začátek souboru `publish-kb.go` přidejte následující řádky k přidání potřebných závislostí do projektu:

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/publish-kb.go" id="dependencies":::

## <a name="create-the-main-function"></a>Vytvoření funkce main

Po požadovaných závislostech přidejte následující třídu:

```Go
package main

func main() {

}
```

## <a name="add-post-request-to-publish-kb"></a>Přidejte požadavek POST pro publikování znalostní báze

Přidejte následující kód, který vytvoří požadavek HTTPS rozhraní API služby QnA Maker k publikování znalostní báze a obdrží odpověď:

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/publish-kb.go" id="main":::

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

Po publikování znalostní báze budete potřebovat [adresu URL koncového bodu, aby se vygenerovala odpověď](./get-answer-from-knowledge-base-go.md).

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)