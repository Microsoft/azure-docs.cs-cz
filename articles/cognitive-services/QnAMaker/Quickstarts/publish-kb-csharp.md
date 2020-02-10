---
title: 'Rychlý Start: publikování znalostní báze, REST C# ,-QnA maker'
titleSuffix: Azure Cognitive Services
description: Tento C# rychlý Start založený na REST publikuje vaši znalostní bázi a vytvoří koncový bod, který se dá volat ve vaší aplikaci nebo robotovi chatu.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/08/2020
ms.author: diberry
ms.openlocfilehash: 33cc4caa8b5901177699368e2f31908951910a6f
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2020
ms.locfileid: "77109572"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-c"></a>Rychlý start: Publikování znalostní báze ve službě QnA Maker pomocí jazyka C#

V tomto rychlém startu založené na protokolu REST provede programově publikování znalostní bázi (KB). Publikování nabídne nejnovější verzi znalostní báze do vyhrazeného indexu služby Azure Kognitivní hledání a vytvoří koncový bod, který se dá volat ve vaší aplikaci nebo robotovi pro chat.

Tento rychlý start volá rozhraní API služby QnA Maker:
* [Publikování](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) –toto rozhraní API nevyžaduje v těle požadavku žádné informace.

## <a name="prerequisites"></a>Předpoklady

* Nejnovější verze sady [**Visual Studio Community Edition**](https://www.visualstudio.com/downloads/).
* Musíte mít [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pokud chcete načíst svůj klíč a koncový bod (včetně názvu prostředku), vyberte pro prostředek v Azure Portal **rychlý Start** .
* V adrese URL v parametru řetězce dotazu `kbid` QnA Maker ID znalostní báze (KB), jak je znázorněno níže.

    ![ID znalostní báze ve službě QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Pokud znalostní bázi ještě nemáte, můžete si vytvořit ukázkovou znalostní bázi a použít ji v tomto rychlém startu. Přečtěte si o [vytvoření nové znalostní báze](create-new-kb-csharp.md).

> [!NOTE]
> Kompletní soubory řešení jsou k dispozici v [úložišti GitHub **Azure-Samples/vnímání-Services-qnamakerem-CSharp** ](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-knowledge-base-project"></a>Vytvoření projektu znalostní báze

1. Otevřete Visual Studio 2019 Community Edition.
1. Vytvořte nový projekt **Konzolová aplikace (.NET Core)** a pojmenujte projekt `QnaMakerQuickstart`. U zbývajících nastavení přijměte výchozí hodnoty.

## <a name="add-required-dependencies"></a>Přidání požadovaných závislostí

V horní části souboru Program.cs nahraďte samostatný příkaz using následujícími řádky, které do projektu přidají potřebné závislosti:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=1-2 "Add the required dependencies")]

## <a name="add-required-constants"></a>Přidání požadovaných konstant

Do třídy **program** přidejte požadované konstanty pro přístup k QnA maker.

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=8-34 "Add the required constants")]

## <a name="add-the-main-method-to-publish-the-knowledge-base"></a>Přidejte metodu Main pro publikování znalostní báze.

Za požadované konstanty přidejte následující kód, který odešle požadavek HTTPS API nástroje QnA Maker na publikování znalostní báze a přijímat odpovědi:

[!code-csharp[Add HTTP Post request and response](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=36-56 "Add HTTP Post request and response")]

Volání API vrátí v případě úspěšného publikování stav 204, přičemž tělo odpovědi bude prázdné.

## <a name="build-and-run-the-program"></a>Sestavení a spuštění programu

Sestavte program a spusťte ho. Automaticky se odešle požadavek rozhraní API nástroje QnA Maker publikování znalostní báze a odpověď je zobrazeny v okně konzoly.

Po publikování znalostní báze v ní můžete vyhledávat pomocí dotazů z koncového bodu prostřednictvím klientské aplikace nebo chatovacího robota.

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Další kroky

Po publikování znalostní báze budete potřebovat [adresu URL koncového bodu, aby se vygenerovala odpověď](./get-answer-from-knowledge-base-csharp.md).

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
