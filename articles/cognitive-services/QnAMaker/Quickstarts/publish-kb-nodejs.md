---
title: 'Rychlý start: Publikování znalostní báze, REST, Node. js – QnA Maker'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu Node.js provede programově publikování znalostní bázi (KB). Publikování odešle nejnovější verzi znalostní báze do vyhrazeného indexu Azure Search a vytvoří koncový bod, který je možné volat v aplikaci nebo chatovacím robotu.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: d47359f92a2cebef10514b3746fbf32f7e952132
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562916"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-nodejs"></a>Rychlý start: Publikování znalostní báze v QnA Maker pomocí Node. js

V tomto rychlém startu založené na protokolu REST provede programově publikování znalostní bázi (KB). Publikování odešle nejnovější verzi znalostní báze do vyhrazeného indexu Azure Search a vytvoří koncový bod, který je možné volat v aplikaci nebo chatovacím robotu.

Tento rychlý start volá rozhraní API služby QnA Maker:
* [Publikování](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) –toto rozhraní API nevyžaduje v těle požadavku žádné informace.

## <a name="prerequisites"></a>Požadavky

* [Node.js 6+](https://nodejs.org/en/download/)
* Potřebujete [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pokud chcete získat klíč, vyberte na řídicím panelu **Klíče** v části **Správa prostředků**. 
* ID znalostní báze služby QnA Maker, které najdete v adrese URL v parametru kbid řetězce dotazu, jak vidíte níže.

    ![ID znalostní báze ve službě QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Pokud ještě nemáte znalostní bázi Knowledge Base, můžete si vytvořit ukázku pro použití v tomto rychlém startu: [Vytvořte novou znalostní bázi](create-new-kb-nodejs.md).


> [!NOTE] 
> Kompletní soubory řešení jsou k dispozici v [úložišti GitHub **Azure-Samples/vnímání-Services-qnamakerem-NodeJS** ](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/publish-knowledge-base-short).

## <a name="create-a-knowledge-base-nodejs-file"></a>Vytvoření souboru Node.js znalostní báze

Vytvořte soubor s názvem `publish-knowledge-base.js`.

## <a name="add-required-dependencies"></a>Přidání požadovaných závislostí

Na začátek souboru `publish-knowledge-base.js` přidejte následující řádky k přidání potřebných závislostí do projektu:

[!code-nodejs[Add the dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base-short/publish-knowledge-base.js?range=1-3 "Add the dependencies")]

## <a name="add-required-constants"></a>Přidání požadovaných konstant

Za předcházející požadované závislosti přidejte požadované konstanty pro přístup ke službě QnA Maker. Nahraďte hodnoty vlastními.

[!code-nodejs[Add required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base-short/publish-knowledge-base.js?range=11-14 "Add required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>Přidání požadavku POST k publikování znalostní báze

Za požadované konstanty přidejte následující kód, který odešle požadavek HTTPS API nástroje QnA Maker na publikování znalostní báze a přijímat odpovědi:

[!code-nodejs[Add a POST request to publish knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base-short/publish-knowledge-base.js?range=16-47 "Add a POST request to publish knowledge base")]

Volání API vrátí v případě úspěšného publikování stav 204, přičemž tělo odpovědi bude prázdné. Kód v případě odpovědi se stavem 204 přidá obsah.

V případě jakékoli jiné odpověď se vrátí tato odpověď beze změny.

## <a name="run-the-program"></a>Spuštění programu

Sestavte program a spusťte ho. Automaticky se odešle požadavek rozhraní API nástroje QnA Maker publikování znalostní báze a odpověď je zobrazeny v okně konzoly.

Po publikování znalostní báze v ní můžete vyhledávat pomocí dotazů z koncového bodu prostřednictvím klientské aplikace nebo chatovacího robota. 

```bash
node publish-knowledge-base.js
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Další postup

Po publikování znalostní báze, je nutné [adresu URL koncového bodu pro generování odpovědi](../Tutorials/create-publish-answer.md#generating-an-answer). 

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
