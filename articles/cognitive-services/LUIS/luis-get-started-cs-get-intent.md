---
title: Získání záměru,C#
titleSuffix: Language Understanding - Azure Cognitive Services
description: V tomto C# rychlý start, použít k dispozici veřejné aplikace LUIS k určení záměru uživatele z konverzační textu.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 6d72299e4350c472ef552fa6afd96e48fd1869b9
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55876960"
---
# <a name="quickstart-get-intent-using-c"></a>Rychlý start: Získání záměru pomocíC#

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Požadavky

* [Visual Studio Community 2017](https://visualstudio.microsoft.com/vs/community/)
* Programovací jazyk C# (je součástí sady VS Community 2017)
* ID veřejné aplikace: df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Získání klíče LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Získání záměru prostřednictvím prohlížeče

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Získání záměru prostřednictvím kódu programu

K získání stejných výsledků, jaké jste viděli v okně prohlížeče v předchozí části, použijte jazyk C# a dotaz na rozhraní [API](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) koncového bodu predikce. 

1. V sadě Visual Studio vytvořte novou konzolovou aplikaci. 

    ![Vytvořte novou konzolovou aplikaci v sadě Visual Studio](media/luis-get-started-cs-get-intent/visual-studio-console-app.png)

2. V projektu sady Visual Studio v Průzkumníku řešení vyberte **Přidat odkaz** a potom na kartě Sestavení vyberte **System.Web**.

    ![Vyberte Přidat odkaz a pak vyberte na kartě sestavení System.Web](media/luis-get-started-cs-get-intent/add-system-dot-web-to-project.png)

3. Soubor Program.cs přepište následujícím kódem:
    
   [!code-csharp[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/csharp/Program.cs)]

4. Nahraďte hodnotu `YOUR_KEY` klíčem služby LUIS.

5. Vytvořte a spusťte konzolovou aplikaci. Zobrazí se stejný JSON, jako jste viděli dříve v okně prohlížeče.

    ![Okno konzoly se zobrazeným výsledkem JSON ze služby LUIS](./media/luis-get-started-cs-get-intent/console-turn-on.png)

## <a name="luis-keys"></a>Klíče služby LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Jakmile tento rychlý start dokončíte, zavřete projekt sady Visual Studio a odeberte adresář projektů ze systému souborů. 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Přidání promluv a trénování s C#](luis-get-started-cs-add-utterance.md)
