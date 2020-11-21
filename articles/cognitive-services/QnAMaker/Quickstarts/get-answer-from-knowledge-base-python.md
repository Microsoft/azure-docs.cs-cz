---
title: 'Rychlý Start: získání odpovědi z znalostní báze – REST, Python-QnA Maker'
description: Tento rychlý Start založený na REST Python vás provede tím, že vám prostřednictvím programu získá odpověď z znalostní báze.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-python
ms.topic: how-to
ms.openlocfilehash: 29e6f3634ab723cbcba948d6f8c35ac66732c2f9
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95023646"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-python"></a>Rychlý Start: získání odpovědí na otázku ze znalostní báze pomocí Pythonu

V tomto rychlém startu se dozvíte, jak programově získat odpověď z publikované QnA Maker znalostní báze. Znalostní báze obsahuje otázky a odpovědi ze [zdrojů dat](../Concepts/knowledge-base.md) , jako jsou nejčastější dotazy. [Otázka](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) se odešle do služby QnA maker. [Odpověď](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) obsahuje horní předpokládanou odpověď.

[Referenční dokumentace](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker4.0/Runtime)  |  [Ukázka](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/get-answer/get-answer-3x.py)

## <a name="prerequisites"></a>Požadavky

* [Python 3,6 nebo vyšší](https://www.python.org/downloads/)
* [Visual Studio Code](https://code.visualstudio.com/)
* Musíte mít [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pokud chcete načíst svůj klíč, na řídicím panelu Azure pro prostředek QnA Maker vyberte **klíče** pod **správou prostředků** .
* **Publikování** nastavení stránky. Pokud nemáte publikovanou znalostní bázi, vytvořte prázdnou znalostní bázi a pak importujte znalostní bázi na stránce **Nastavení** a pak klikněte na publikovat. [Tuto základní znalostní bázi](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv)si můžete stáhnout a použít.

    Nastavení stránky publikovat zahrnuje hodnotu POST Route, hodnotu Host a hodnotu EndpointKey.

    ![Publish settings (Nastavení publikování)](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-python-file"></a>Vytvoření souboru Pythonu

Otevřete VSCode a vytvořte nový soubor s názvem `get-answer-3x.py` .

## <a name="add-the-required-dependencies"></a>Přidání požadovaných závislostí

V horní části `get-answer-3x.py` souboru přidejte nezbytné závislosti do projektu:

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/query-kb.py" id="dependencies":::

<!--TBD - reword this following paragraph -->

Hostitel a trasa se liší od toho, jak se zobrazují na stránce **publikování** . Důvodem je to, že knihovna Pythonu nepovoluje žádné směrování v hostiteli. Směrování, které se zobrazí na stránce **publikovat** jako součást hostitele, se přesunulo do trasy.

## <a name="add-the-required-constants"></a>Přidání požadovaných konstant

Přidejte požadované konstanty pro přístup k QnA Maker. Tyto hodnoty jsou po publikování znalostní báze na stránce **publikovat** .

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/query-kb.py" id="constants":::

## <a name="add-a-post-request-to-send-question-and-get-an-answer"></a>Přidat požadavek POST k odeslání otázky a získat odpověď

Následující kód provede požadavek HTTPS na rozhraní API služby QnA Maker k odeslání otázky do znalostní báze a obdrží odpověď:

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/query-kb.py" id="main":::

`Authorization`Hodnota hlavičky obsahuje řetězec `EndpointKey` .

## <a name="run-the-program"></a>Spuštění programu

Spusťte tento program z příkazového řádku. Požadavek bude automaticky odesílat do rozhraní API služby QnA Maker a pak bude vytištěn v okně konzoly.

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
