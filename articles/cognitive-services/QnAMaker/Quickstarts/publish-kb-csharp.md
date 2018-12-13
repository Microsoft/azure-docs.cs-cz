---
title: Publikování znalostní báze knowledge base, REST,C#
titleSuffix: QnA Maker- Azure Cognitive Services
description: To C# založené na protokolu REST rychlý start vás provede s publikování znalostní báze, která odesílá na nejnovější verzi znalostní báze otestované vyhrazené index Azure Search představující publikované znalostní báze. Také se přitom vytvoří koncový bod, který je možné volat v aplikaci nebo chatovacím robotu.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 11/18/2018
ms.author: diberry
ms.openlocfilehash: 0fef4a22179cafbce40868c86e3bceabcebe26a9
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53094368"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-c"></a>Rychlý start: Publikování znalostní báze ve službě QnA Maker pomocí jazyka C#

V tomto rychlém startu založené na protokolu REST provede programově publikování znalostní bázi (KB). Publikování odešle nejnovější verzi znalostní báze do vyhrazeného indexu Azure Search a vytvoří koncový bod, který je možné volat v aplikaci nebo chatovacím robotu.

Tento rychlý start volá rozhraní API služby QnA Maker:
* [Publikování](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) –toto rozhraní API nevyžaduje v těle požadavku žádné informace.

## <a name="prerequisites"></a>Požadavky

* Nejnovější verze sady [**Visual Studio Community Edition**](https://www.visualstudio.com/downloads/).
* Potřebujete [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pokud chcete získat klíč, vyberte na řídicím panelu **Klíče** v části **Správa prostředků**. 
* ID znalostní báze služby QnA Maker, které najdete v adrese URL v parametru kbid řetězce dotazu, jak vidíte níže.

    ![ID znalostní báze ve službě QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Pokud znalostní bázi ještě nemáte, můžete si vytvořit ukázkovou znalostní bázi a použít ji v tomto rychlém startu. Přečtěte si o [vytvoření nové znalostní báze](create-new-kb-csharp.md).

> [!NOTE] 
> Kompletní soubor(y) řešení jsou k dispozici v [**Azure-Samples/cognitive-services-qnamaker-csharp** úložišti GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-knowledge-base-project"></a>Vytvoření projektu znalostní báze

1. Otevřete sadu Visual Studio 2017 Community Edition.
1. Vytvořte nový projekt **Konzolová aplikace (.Net Core)** a dejte mu název `QnaMakerQuickstart`. U zbývajících nastavení přijměte výchozí hodnoty.

## <a name="add-required-dependencies"></a>Přidání požadovaných závislostí

V horní části souboru Program.cs nahraďte samostatný příkaz using následujícími řádky, které do projektu přidají potřebné závislosti:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=1-2 "Add the required dependencies")]

## <a name="add-required-constants"></a>Přidání požadovaných konstant

V **hlavní** metodu, přidejte požadované konstanty na používání nástroje QnA Maker. Nahraďte hodnoty vlastními.

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=11-14 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>Přidání požadavku POST k publikování znalostní báze

Za požadované konstanty přidejte následující kód, který odešle požadavek HTTPS API nástroje QnA Maker na publikování znalostní báze a přijímat odpovědi:

[!code-csharp[Add HTTP Post request and response](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=16-29&dedent=8 "Add HTTP Post request and response")]

Volání API vrátí v případě úspěšného publikování stav 204, přičemž tělo odpovědi bude prázdné. 
 
## <a name="build-and-run-the-program"></a>Sestavení a spuštění programu

Sestavte program a spusťte ho. Automaticky se odešle požadavek rozhraní API nástroje QnA Maker publikování znalostní báze a odpověď je zobrazeny v okně konzoly.

Po publikování znalostní báze v ní můžete vyhledávat pomocí dotazů z koncového bodu prostřednictvím klientské aplikace nebo chatovacího robota. 

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Další postup

Po publikování znalostní báze, je nutné [adresu URL koncového bodu pro generování odpovědi](../Tutorials/create-publish-answer.md#generating-an-answer). 

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
