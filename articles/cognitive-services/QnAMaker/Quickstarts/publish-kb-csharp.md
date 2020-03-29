---
title: 'Úvodní příručka: Publikování znalostní báze REST, C# – QnA Maker'
description: Tento rychlý start založený na c# REST publikuje znalostní bázi a vytvoří koncový bod, který lze volat ve vaší aplikaci nebo chatu robota.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 0a2f64795d82928e33a10c7e9d162f2333cdf7bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851704"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-c"></a>Rychlý start: Publikování znalostní báze ve službě QnA Maker pomocí jazyka C#

Tento rychlý start založený na rest vás provede programovým publikováním znalostní báze (KB). Publikování odešle nejnovější verzi znalostní báze do vyhrazeného indexu Azure Cognitive Search a vytvoří koncový bod, který lze volat ve vaší aplikaci nebo chatovacím robotu.

Tento rychlý start volá rozhraní API služby QnA Maker:
* [Publikování](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) –toto rozhraní API nevyžaduje v těle požadavku žádné informace.

## <a name="prerequisites"></a>Požadavky

* Nejnovější verze sady [**Visual Studio Community Edition**](https://www.visualstudio.com/downloads/).
* Musíte mít [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pokud chcete načíst klíč a koncový bod (který obsahuje název prostředku), vyberte **Rychlý start** pro váš prostředek na webu Azure Portal.
* ID znalostní báze QnA Maker (KB) nalezené v adrese URL v parametru řetězce dotazu, `kbid` jak je znázorněno níže.

    ![ID znalostní báze ve službě QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Pokud znalostní bázi ještě nemáte, můžete si vytvořit ukázkovou znalostní bázi a použít ji v tomto rychlém startu. Přečtěte si o [vytvoření nové znalostní báze](create-new-kb-csharp.md).

> [!NOTE]
> Kompletní soubor řešení jsou k dispozici v [ **azure-samples/cognitive-services-qnamaker-csharp** GitHub úložiště](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-knowledge-base-project"></a>Vytvoření projektu znalostní báze

1. Otevřete edici Visual Studio 2019 Community Edition.
1. Vytvořte nový projekt **konzolové aplikace (.NET Core)** a pojmenujte projekt `QnaMakerQuickstart`. U zbývajících nastavení přijměte výchozí hodnoty.

## <a name="add-required-dependencies"></a>Přidání požadovaných závislostí

V horní části souboru Program.cs nahraďte samostatný příkaz using následujícími řádky, které do projektu přidají potřebné závislosti:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=1-2 "Add the required dependencies")]

## <a name="add-required-constants"></a>Přidání požadovaných konstant

Do třídy **Program** přidejte požadované konstanty pro přístup k QnA Maker.

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=8-34 "Add the required constants")]

## <a name="add-the-main-method-to-publish-the-knowledge-base"></a>Přidání hlavní metody k publikování znalostní báze

Po požadovaných konstantách přidejte následující kód, který do rozhraní API qnA makeru vytvoří požadavek HTTPS, aby publikoval znalostní bázi a obdrží odpověď:

[!code-csharp[Add HTTP Post request and response](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=36-56 "Add HTTP Post request and response")]

Volání API vrátí v případě úspěšného publikování stav 204, přičemž tělo odpovědi bude prázdné.

## <a name="build-and-run-the-program"></a>Sestavení a spuštění programu

Sestavte a spusťte program. Automaticky odešle požadavek do rozhraní API qnA maker u publikování znalostní báze a odpověď se vytiskne do okna konzoly.

Po publikování znalostní báze v ní můžete vyhledávat pomocí dotazů z koncového bodu prostřednictvím klientské aplikace nebo chatovacího robota.

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Další kroky

Po publikování znalostní báze potřebujete [adresu URL koncového bodu ke generování odpovědi](./get-answer-from-knowledge-base-csharp.md).

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)