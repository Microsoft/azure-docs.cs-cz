---
title: 'Rychlý start: Získání odpovědí ze znalostní báze knowledge base - REST, C# – QnA Maker'
titlesuffix: Azure Cognitive Services
description: To C# založené na protokolu REST rychlý start vás provede s získat odpověď ze znalostní báze, prostřednictvím kódu programu.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 2d011dffa4db68a0cdf6ac3415ff1316906a4e87
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "57883997"
---
# <a name="get-answers-to-a-question-from-a-knowledge-base-with-c"></a>Získejte odpovědi na dotaz zadaný ze znalostní báze sC#

Tento rychlý start vás provede prostřednictvím kódu programu získávání odpovědi z publikovaných znalostní báze QnA Maker. Znalostní báze obsahuje otázky a odpovědi z [zdroje dat](../Concepts/data-sources-supported.md) například nejčastější dotazy. [Otázku](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) se odesílají službě nástroje QnA Maker. [Odpovědi](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) zahrnuje horní předpovědět odpověď. 


## <a name="prerequisites"></a>Požadavky

* Nejnovější verze sady [**Visual Studio Community Edition**](https://www.visualstudio.com/downloads/).
* Potřebujete [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Chcete-li načíst kód Product key, vyberte **klíče** pod **správy prostředků** v řídicím panelu Azure pro prostředek nástroje QnA Maker. 
* **Publikování** stránce nastavení. Pokud nemáte publikované znalostní báze, vytvořte prázdný znalostní báze a potom importujte znalostní báze na **nastavení** stránce a potom publikovat. Můžete stáhnout a použít [tento základní znalostní báze](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv). 

    Stránka nastavení publikování zahrnují hodnoty trasy příspěvek, hodnota hostitele a EndpointKey hodnoty. 

    ![Nastavení publikování](../media/qnamaker-quickstart-get-answer/publish-settings.png)

Kód v tomto rychlém startu se [ https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp ](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/quickstarts/get-answer) úložiště. 

## <a name="create-a-knowledge-base-project"></a>Vytvoření projektu znalostní báze

1. Otevřete sadu Visual Studio 2017 Community Edition.
1. Vytvoření nového projektu aplikace konzoly (.NET Core) a název projektu QnaMakerQuickstart. U zbývajících nastavení přijměte výchozí hodnoty.

## <a name="add-the-required-dependencies"></a>Přidání požadovaných závislostí

V horní části souboru Program.cs nahraďte jedné potřebné závislosti přidejte do projektu pomocí příkazu s následujícími řádky:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=1-3 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Přidání požadovaných konstant

V horní části `Program` uvnitř třídy `Main`, přidejte požadované konstanty na používání nástroje QnA Maker. Tyto hodnoty jsou na **publikovat** stránce po publikování znalostní báze. 

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=14-30 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-answer"></a>Přidat požadavek POST odeslat dotaz a získejte odpověď

Následující kód odešle požadavek HTTPS API nástroje QnA Maker odeslat dotaz do znalostní báze a přijímat odpovědi:

[!code-csharp[Add a POST request to send question to knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=32-57 "Add a POST request to send question to knowledge base")]

`Authorization` Hodnotu hlavičky obsahuje řetězec `EndpointKey `. 

Další informace o [požadavek](../how-to/metadata-generateanswer-usage.md#generateanswer-request) a [odpovědi](../how-to/metadata-generateanswer-usage.md#generateanswer-response). 

## <a name="build-and-run-the-program"></a>Sestavení a spuštění programu

Sestavte a spusťte program ze sady Visual Studio. Automaticky se odešle požadavek rozhraní API nástroje QnA Maker a vytiskne se do okna konzoly.

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)] 

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
