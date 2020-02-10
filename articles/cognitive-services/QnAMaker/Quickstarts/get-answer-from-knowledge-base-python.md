---
title: 'Rychlý Start: získání odpovědi z znalostní báze – REST, Python-QnA Maker'
description: Tento rychlý Start založený na REST Python vás provede tím, že vám prostřednictvím programu získá odpověď z znalostní báze.
ms.topic: quickstart
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCHANGE-20200128
ms.openlocfilehash: 7fe1ed140c53f93f2bd00847c5652906de998147
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2020
ms.locfileid: "77109702"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-python"></a>Rychlý Start: získání odpovědí na otázku ze znalostní báze pomocí Pythonu

V tomto rychlém startu se dozvíte, jak programově získat odpověď z publikované QnA Maker znalostní báze. Znalostní báze obsahuje otázky a odpovědi ze [zdrojů dat](../Concepts/knowledge-base.md) , jako jsou nejčastější dotazy. [Otázka](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) se odešle do služby QnA maker. [Odpověď](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) obsahuje horní předpokládanou odpověď.

[Referenční dokumentace](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime) | [Ukázka](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/get-answer/get-answer-3x.py)

## <a name="prerequisites"></a>Předpoklady

* [Python 3,6 nebo vyšší](https://www.python.org/downloads/)
* [Visual Studio Code](https://code.visualstudio.com/)
* Musíte mít [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pokud chcete načíst svůj klíč, na řídicím panelu Azure pro prostředek QnA Maker vyberte **klíče** pod **správou prostředků** .
* **Publikování** nastavení stránky. Pokud nemáte publikovanou znalostní bázi, vytvořte prázdnou znalostní bázi a pak importujte znalostní bázi na stránce **Nastavení** a pak klikněte na publikovat. [Tuto základní znalostní bázi](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv)si můžete stáhnout a použít.

    Nastavení stránky publikovat zahrnuje hodnotu POST Route, hodnotu Host a hodnotu EndpointKey.

    ![Nastavení publikování](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-python-file"></a>Vytvoření souboru Pythonu

Otevřete VSCode a vytvořte nový soubor s názvem `get-answer-3x.py`.

## <a name="add-the-required-dependencies"></a>Přidání požadovaných závislostí

V horní části souboru `get-answer-3x.py` přidejte do projektu nezbytné závislosti:

[!code-python[Add the required dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/get-answer/get-answer-3x.py?range=1-2 "Add the required dependencies")]

<!--TBD - reword this following paragraph -->

Hostitel a trasa se liší od toho, jak se zobrazují na stránce **publikování** . Důvodem je to, že knihovna Pythonu nepovoluje žádné směrování v hostiteli. Směrování, které se zobrazí na stránce **publikovat** jako součást hostitele, se přesunulo do trasy.

## <a name="add-the-required-constants"></a>Přidejte požadované konstanty

Přidejte požadované konstanty pro přístup k QnA Maker. Tyto hodnoty jsou po publikování znalostní báze na stránce **publikovat** .

[!code-python[Add the required constants](~/samples-qnamaker-python/documentation-samples/quickstarts/get-answer/get-answer-3x.py?range=5-25 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-an-answer"></a>Přidat požadavek POST k odeslání otázky a získat odpověď

Následující kód provede požadavek HTTPS na rozhraní API služby QnA Maker k odeslání otázky do znalostní báze a obdrží odpověď:

[!code-python[Add a POST request to send question to knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/get-answer/get-answer-3x.py?range=27-48 "Add a POST request to send question to knowledge base")]

Hodnota hlavičky `Authorization` zahrnuje `EndpointKey`řetězce.

## <a name="run-the-program"></a>Spuštění programu

Spusťte program z příkazového řádku. Automaticky se odešle požadavek rozhraní API nástroje QnA Maker a vytiskne se do okna konzoly.

Spusťte soubor:

```bash
python get-answer-3x.py
```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]

Přečtěte si další informace o [žádosti](../how-to/metadata-generateanswer-usage.md#generateanswer-request) a [odpovědi](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
