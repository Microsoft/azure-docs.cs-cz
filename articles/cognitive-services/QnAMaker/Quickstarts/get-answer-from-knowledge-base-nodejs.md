---
title: 'Úvodní příručka: Získejte odpověď ze znalostní báze REST, Node.js - QnA Maker'
description: Tento rychlý start založený na souboru Node.js rest vás provede získáním odpovědi ze znalostní báze programově.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 9031c320b055c0a7191655d6531eaa31d9cc8bb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851726"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-nodejs"></a>Úvodní příručka: Získejte odpovědi na otázku ze znalostní báze node.js

Tento rychlý start vás provede programově získáním odpovědi z publikované znalostní báze QnA Maker. Znalostní báze obsahuje otázky a odpovědi ze [zdrojů dat,](../Concepts/knowledge-base.md) jako jsou časté dotazy. [Otázka](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) je odeslána službě QnA Maker. [Odpověď](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) obsahuje nejlépe předpovídanou odpověď.

[Ukázka referenční dokumentace](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime) | [Sample](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/get-answer/get-answer.js)

## <a name="prerequisites"></a>Požadavky

* [Node.js](https://nodejs.org/en/download/)
* [Kód visual studia](https://code.visualstudio.com/)
* Musíte mít [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pokud chcete klíč načíst, vyberte **klíče** v části **Správa prostředků** na řídicím panelu Azure pro prostředek QnA Maker.
* **Publikovat** nastavení stránky. Pokud nemáte publikovanou znalostní bázi, vytvořte prázdnou znalostní bázi, importujte znalostní bázi na stránce **Nastavení** a poté ji publikujte. Tuto [základní znalostní bázi](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv)si můžete stáhnout a použít .

    Nastavení stránky publikování zahrnuje hodnotu postupu POST, hodnotu hostitele a hodnotu EndpointKey.

    ![Publish settings (Nastavení publikování)](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-nodejs-file"></a>Vytvoření souboru Node.js

Otevřete VSCode a vytvořte nový soubor s názvem `get-answer.js`.

## <a name="add-the-required-dependencies"></a>Přidání požadovaných závislostí

V horní části `get-answer.js` souboru přidejte potřebné závislosti do projektu:

[!code-nodejs[Add the required dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=1-4 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Přidání požadovaných konstant

Dále přidejte požadované konstanty pro přístup k QnA Maker. Tyto hodnoty jsou na stránce **Publikovat** po publikování znalostní báze.

[!code-nodejs[Add the required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=6-22 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-an-answer"></a>Přidání žádosti POST k odeslání otázky a získání odpovědi

Následující kód provede požadavek HTTPS na rozhraní API qnA maker u odesílání otázky do znalostní báze a obdrží odpověď:

[!code-nodejs[Add a POST request to send question to knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=24-49 "Add a POST request to send question to knowledge base")]

Hodnota `Authorization` záhlaví obsahuje řetězec `EndpointKey`.

## <a name="install-the-dependencies"></a>Instalace závislostí

Nainstalujte závislosti z příkazového řádku:

```bash
npm install request request-promise
```

## <a name="run-the-program"></a>Spuštění programu

Spusťte program z příkazového řádku. Automaticky odešle požadavek do rozhraní QnA Maker API a poté se vytiskne do okna konzoly.

Spusťte soubor:

```bash
node get-answer.js
```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]

Přečtěte si další informace o [žádosti](../how-to/metadata-generateanswer-usage.md#generateanswer-request) a [odpovědi](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)