---
title: Získat záměr pomocí volání RESTC#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: e6ae9590cee3a2ddc3b8e121161fcf84815da28a
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838498"
---
## <a name="prerequisites"></a>Předpoklady

* [Visual Studio Community 2017](https://visualstudio.microsoft.com/vs/community/)
* Programovací jazyk C# (je součástí sady VS Community 2017)
* ID veřejné aplikace: df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Získání klíče LUIS

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-programmatically"></a>Získání záměru prostřednictvím kódu programu

K získání stejných výsledků, jaké jste viděli v okně prohlížeče v předchozí části, použijte jazyk C# a dotaz na rozhraní [API](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) koncového bodu predikce. 

1. V sadě Visual Studio vytvořte novou konzolovou aplikaci. 

    ![Vytvoření nové konzolové aplikace v aplikaci Visual Studio](../media/luis-get-started-cs-get-intent/visual-studio-console-app.png)

2. V projektu sady Visual Studio v Průzkumníku řešení vyberte **Přidat odkaz** a potom na kartě Sestavení vyberte **System.Web**.

    ![Vyberte Přidat odkaz a pak na kartě sestavení vyberte System. Web.](../media/luis-get-started-cs-get-intent/add-system-dot-web-to-project.png)

3. Soubor Program.cs přepište následujícím kódem:
    
   [!code-csharp[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/csharp/Program.cs)]

4. Nahraďte hodnotu `YOUR_KEY` klíčem služby LUIS.

5. Vytvořte a spusťte konzolovou aplikaci. Zobrazí se stejný JSON, jako jste viděli dříve v okně prohlížeče.

    ![Okno konzoly se zobrazeným výsledkem JSON ze služby LUIS](../media/luis-get-started-cs-get-intent/console-turn-on.png)



## <a name="luis-keys"></a>Klíče služby LUIS

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Jakmile tento rychlý start dokončíte, zavřete projekt sady Visual Studio a odeberte adresář projektů ze systému souborů. 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidání promluv a trénování s C#](../luis-get-started-cs-add-utterance.md)