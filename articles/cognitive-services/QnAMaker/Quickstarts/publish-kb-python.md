---
title: 'Rychlý Start: publikování znalostní báze, REST, Pythonu QnA Maker'
description: Tento rychlý Start založený na REST Python publikuje vaši znalostní bázi a vytvoří koncový bod, který se dá volat ve vaší aplikaci nebo v robotovi pro chat.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-python
ms.topic: how-to
ms.openlocfilehash: 3900d817de55795539dd3f16d33896626ebda2d8
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351125"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-python"></a>Rychlý start: Publikování znalostní báze ve službě QnA Maker pomocí Pythonu

Tento rychlý Start založený na REST vás provede programově publikováním znalostní báze (KB). Publikování nabídne nejnovější verzi znalostní báze do vyhrazeného indexu služby Azure Kognitivní hledání a vytvoří koncový bod, který se dá volat ve vaší aplikaci nebo robotovi pro chat.

Tento rychlý Start volá QnA Maker rozhraní REST API:
* [Publikování](/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) –toto rozhraní API nevyžaduje v těle požadavku žádné informace.

## <a name="prerequisites"></a>Požadavky

* [Python 3.7](https://www.python.org/downloads/)
* Musíte mít [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pokud chcete načíst svůj klíč a koncový bod (včetně názvu prostředku), vyberte pro prostředek v Azure Portal **rychlý Start** .
* V adrese URL parametru řetězce dotazu bylo nalezeno QnA Maker znalostní báze (KB) `kbid` , jak je uvedeno níže.

    ![ID znalostní báze ve službě QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Pokud znalostní bázi ještě nemáte, můžete si vytvořit ukázkovou znalostní bázi a použít ji v tomto rychlém startu. Přečtěte si o [vytvoření nové znalostní báze](./create-publish-knowledge-base.md).

> [!NOTE]
> Kompletní soubory řešení jsou k dispozici v [úložišti GitHub **Azure-Samples/vnímání-Services-qnamakerem-Python**](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-knowledge-base-python-file"></a>Vytvoření souboru Pythonu pro znalostní bázi

Vytvořte soubor s názvem `publish-kb-3x.py`.

## <a name="add-the-required-dependencies"></a>Přidání požadovaných závislostí

Na začátek souboru `publish-kb-3x.py` přidejte následující řádky k přidání potřebných závislostí do projektu:

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/publish-kb.py" id="dependencies":::

## <a name="add-required-constants"></a>Přidání požadovaných konstant

Za předcházející požadované závislosti přidejte požadované konstanty pro přístup ke službě QnA Maker. Nahraďte hodnoty vlastními.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/publish-kb.py" id="constants":::

## <a name="add-post-request-to-publish-knowledge-base"></a>Přidat požadavek POST pro publikování znalostní báze

Po požadovaných konstantách přidejte následující kód, který vytvoří požadavek HTTPS rozhraní API služby QnA Maker k publikování znalostní báze a obdrží odpověď:

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/publish-kb.py" id="main":::

Volání API vrátí v případě úspěšného publikování stav 204, přičemž tělo odpovědi bude prázdné. Kód v případě odpovědi se stavem 204 přidá obsah.

V případě jakékoli jiné odpověď se vrátí tato odpověď beze změny.

## <a name="build-and-run-the-program"></a>Sestavení a spuštění programu

Spusťte program zadáním následujícího příkazu na příkazovém řádku. Pošle požadavek rozhraní API služby QnA Maker k publikování znalostní báze a pak vytiskněte 204 pro úspěch nebo chyby.

```bash
python publish-kb-3x.py
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Další kroky

Po publikování znalostní báze budete potřebovat [adresu URL koncového bodu, aby se vygenerovala odpověď](./get-answer-from-knowledge-base-python.md).

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)

[Přehled služby QnA Maker](../Overview/overview.md)