---
title: 'Rychlý Start: získání odpovědi z znalostní báze – REST, C# – QnA Maker'
description: Tento rychlý Start založený na REST C# vás provede tím, že vám prostřednictvím programu získá odpověď z znalostní báze.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-csharp
ms.topic: how-to
ms.openlocfilehash: 547b3377d8c4404026e35c949ea7ccb7b243365c
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777626"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-c"></a>Rychlý Start: získání odpovědí na otázku ze znalostní báze pomocí jazyka C #

V tomto rychlém startu se dozvíte, jak programově získat odpověď z publikované QnA Maker znalostní báze. Znalostní báze obsahuje otázky a odpovědi ze [zdrojů dat](../Concepts/knowledge-base.md) , jako jsou nejčastější dotazy. [Otázka](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) se odešle do služby QnA maker. [Odpověď](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) obsahuje horní předpokládanou odpověď.

[Referenční dokumentace](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime)  |  [Ukázka](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs)

## <a name="prerequisites"></a>Předpoklady

* Nejnovější verze sady [**Visual Studio Community Edition**](https://www.visualstudio.com/downloads/).
* Musíte mít [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pokud chcete načíst svůj klíč, na řídicím panelu Azure pro prostředek QnA Maker vyberte **klíče** pod **správou prostředků** .
* **Publikování** nastavení stránky. Pokud nemáte publikovanou znalostní bázi, vytvořte prázdnou znalostní bázi a pak importujte znalostní bázi na stránce **Nastavení** a pak klikněte na publikovat. [Tuto základní znalostní bázi](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv)si můžete stáhnout a použít.

    Nastavení stránky publikovat zahrnuje hodnotu POST Route, hodnotu Host a hodnotu EndpointKey.

    ![Publish settings (Nastavení publikování)](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-knowledge-base-project"></a>Vytvoření projektu znalostní báze

1. Otevřete Visual Studio 2019 Community Edition.
1. Vytvořte nový projekt Konzolová aplikace (.NET Core) a pojmenujte projekt QnaMakerQuickstart. U zbývajících nastavení přijměte výchozí hodnoty.

## <a name="add-the-required-dependencies"></a>Přidání požadovaných závislostí

V horní části souboru Program.cs nahraďte jediný příkaz using následujícími řádky, aby bylo možné přidat nezbytné závislosti do projektu:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/query-kb.cs" id="dependencies":::

## <a name="add-the-required-constants"></a>Přidání požadovaných konstant

V horní části třídy v `Program` rámci `Main` přidejte požadované konstanty pro přístup k QnA maker. Tyto hodnoty jsou po publikování znalostní báze na stránce **publikovat** .

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/query-kb.cs" id="constants":::

## <a name="add-a-post-request-to-send-question-and-get-answer"></a>Přidat požadavek POST k odeslání otázky a získat odpověď

Následující kód provede požadavek HTTPS na rozhraní API služby QnA Maker k odeslání otázky do znalostní báze a obdrží odpověď:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/query-kb.cs" id="post":::

`Authorization`Hodnota hlavičky obsahuje řetězec `EndpointKey` .

Přečtěte si další informace o [žádosti](../how-to/metadata-generateanswer-usage.md#generateanswer-request) a [odpovědi](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

## <a name="build-and-run-the-program"></a>Sestavení a spuštění programu

Sestavte a spusťte program ze sady Visual Studio. Požadavek bude automaticky odesílat do rozhraní API služby QnA Maker a pak bude vytištěn v okně konzoly.

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
