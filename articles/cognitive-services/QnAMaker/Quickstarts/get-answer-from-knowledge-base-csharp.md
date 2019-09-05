---
title: 'Rychlý start: Získat odpověď ze znalostní báze Knowledge Base – C# REST,-QnA maker'
titleSuffix: Azure Cognitive Services
description: Tento C# rychlý Start založený na REST vás provede tím, že vám prostřednictvím programu získá odpověď z znalostní báze.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: ead314e4c889b79892857eb07ecdd63377131b12
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376354"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-c"></a>Rychlý start: Získejte odpovědi na otázku ze znalostní báze.C#

V tomto rychlém startu se dozvíte, jak programově získat odpověď z publikované QnA Maker znalostní báze. Znalostní báze obsahuje otázky a odpovědi ze [zdrojů dat](../Concepts/data-sources-supported.md) , jako jsou nejčastější dotazy. [Otázka](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) se odešle do služby QnA maker. [Odpověď](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) obsahuje horní předpokládanou odpověď. 


## <a name="prerequisites"></a>Požadavky

* Nejnovější verze sady [**Visual Studio Community Edition**](https://www.visualstudio.com/downloads/).
* Potřebujete [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Chcete-li načíst kód Product key, vyberte **klíče** pod **správy prostředků** v řídicím panelu Azure pro prostředek nástroje QnA Maker. 
* **Publikování** nastavení stránky. Pokud nemáte publikovanou znalostní bázi, vytvořte prázdnou znalostní bázi a pak importujte znalostní bázi na stránce **Nastavení** a pak klikněte na publikovat. [Tuto základní znalostní bázi](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv)si můžete stáhnout a použít. 

    Nastavení stránky publikovat zahrnuje hodnotu POST Route, hodnotu Host a hodnotu EndpointKey. 

    ![Nastavení publikování](../media/qnamaker-quickstart-get-answer/publish-settings.png)

Kód pro tento rychlý Start je v [https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/quickstarts/get-answer) úložišti. 

## <a name="create-a-knowledge-base-project"></a>Vytvoření projektu znalostní báze

1. Otevřete Visual Studio 2019 Community Edition.
1. Vytvořte nový projekt Konzolová aplikace (.NET Core) a pojmenujte projekt QnaMakerQuickstart. U zbývajících nastavení přijměte výchozí hodnoty.

## <a name="add-the-required-dependencies"></a>Přidání požadovaných závislostí

V horní části souboru Program.cs nahraďte jediný příkaz using následujícími řádky, aby bylo možné přidat nezbytné závislosti do projektu:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=1-3 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Přidání požadovaných konstant

V horní `Program` části třídy v `Main`rámci přidejte požadované konstanty pro přístup k QnA maker. Tyto hodnoty jsou po publikování znalostní báze na stránce **publikovat** . 

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=14-30 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-answer"></a>Přidat požadavek POST k odeslání otázky a získat odpověď

Následující kód provede požadavek HTTPS na rozhraní API služby QnA Maker k odeslání otázky do znalostní báze a obdrží odpověď:

[!code-csharp[Add a POST request to send question to knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=32-57 "Add a POST request to send question to knowledge base")]

Hodnota hlavičky obsahuje řetězec `EndpointKey`. `Authorization` 

Přečtěte si další informace o [žádosti](../how-to/metadata-generateanswer-usage.md#generateanswer-request) a [odpovědi](../how-to/metadata-generateanswer-usage.md#generateanswer-response). 

## <a name="build-and-run-the-program"></a>Sestavení a spuštění programu

Sestavte a spusťte program ze sady Visual Studio. Automaticky se odešle požadavek rozhraní API nástroje QnA Maker a vytiskne se do okna konzoly.

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)] 

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
