---
title: 'Úvodní příručka: Publikování znalostní báze REST, Go - QnA Maker'
description: Tento rychlý start založený na go REST publikuje znalostní bázi a vytvoří koncový bod, který lze volat ve vaší aplikaci nebo chatovacím robotovi.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 4ce655bdc7a913ecb281ce8a75e7ec4f2009a2ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851693"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-go"></a>Rychlý start: Publikování znalostní báze ve službě QnA Maker pomocí jazyka Go

Tento rychlý start založený na rest vás provede programovým publikováním znalostní báze (KB). Publikování odešle nejnovější verzi znalostní báze do vyhrazeného indexu Azure Cognitive Search a vytvoří koncový bod, který lze volat ve vaší aplikaci nebo chatovacím robotu.

Tento rychlý start volá rozhraní API služby QnA Maker:
* [Publikování](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) –toto rozhraní API nevyžaduje v těle požadavku žádné informace.

## <a name="prerequisites"></a>Požadavky

* [Go 1.10.1](https://golang.org/dl/)
* Musíte mít [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pokud chcete načíst klíč a koncový bod (který obsahuje název prostředku), vyberte **Rychlý start** pro váš prostředek na webu Azure Portal.

* ID znalostní báze QnA Maker (KB) nalezené v adrese URL v parametru řetězce dotazu, `kbid` jak je znázorněno níže.

    ![ID znalostní báze ve službě QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Pokud znalostní bázi ještě nemáte, můžete si vytvořit ukázkovou znalostní bázi a použít ji v tomto rychlém startu. Přečtěte si o [vytvoření nové znalostní báze](create-new-kb-csharp.md).

> [!NOTE]
> Kompletní soubor řešení jsou k dispozici v [ **azure-samples/cognitive-services-qnamaker-go** úložiště GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-go-file"></a>Vytvoření souboru Go

Otevřete VSCode a vytvořte nový soubor s názvem `publish-kb.go`.

## <a name="add-the-required-dependencies"></a>Přidání požadovaných závislostí

Na začátek souboru `publish-kb.go` přidejte následující řádky k přidání potřebných závislostí do projektu:

[!code-go[Add the required dependencies](~/samples-qnamaker-go/documentation-samples/quickstarts/publish-knowledge-base/publish-kb.go?range=3-7 "Add the required dependencies")]

## <a name="create-the-main-function"></a>Vytvoření hlavní funkce

Po požadovaných závislostech přidejte následující třídu:

```Go
package main

func main() {

}
```

## <a name="add-required-constants"></a>Přidání požadovaných konstant

Uvnitř **hlavního**


 přidejte požadované konstanty pro přístup k QnA Makeru. Nahraďte hodnoty vlastními.

[!code-go[Add the required constants](~/samples-qnamaker-go/documentation-samples/quickstarts/publish-knowledge-base/publish-kb.go?range=16-20 "Add the required constants")]

## <a name="add-post-request-to-publish-kb"></a>Přidejte požadavek POST pro publikování znalostní báze

Po požadovaných konstantách přidejte následující kód, který do rozhraní API qnA makeru vytvoří požadavek HTTPS, aby publikoval znalostní bázi a obdrží odpověď:

[!code-go[Add a POST request to publish KB](~/samples-qnamaker-go/documentation-samples/quickstarts/get-answer/get-answer.go?range=35-48 "Add a POST request to publish KB")]

Volání API vrátí v případě úspěšného publikování stav 204, přičemž tělo odpovědi bude prázdné. Kód v případě odpovědi se stavem 204 přidá obsah.

V případě jakékoli jiné odpověď se vrátí tato odpověď beze změny.

## <a name="build-and-run-the-program"></a>Sestavení a spuštění programu

Zadáním následujícího příkazu soubor zkompilujte. Příkazový řádek nevrací žádné informace o úspěšném sestavení.

```bash
go build publish-kb.go
```

Spusťte program zadáním následujícího příkazu na příkazovém řádku. Odešle požadavek na rozhraní API QnA Maker publikovat KB, pak vytisknout 204 pro úspěch nebo chyby.

```bash
./publish-kb
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Další kroky

Po publikování znalostní báze potřebujete [adresu URL koncového bodu ke generování odpovědi](./get-answer-from-knowledge-base-go.md).

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)