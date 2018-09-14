---
title: Analýza textu v přirozeném jazyce ve službě Language Understanding (LUIS) pomocí C# – Azure Cognitive Services | Microsoft Docs
description: V tomto rychlém startu použijete dostupnou veřejnou aplikaci LUIS ke zjištění záměru uživatele z textu konverzace. Pomocí C# odešlete záměr uživatele jako text do koncového bodu předpovědi HTTP veřejné aplikace. Služba LUIS použije v koncovém bodě model veřejné aplikace k analýze smyslu textu v přirozeném jazyce, zjištění celkového záměru a extrakci dat, která jsou relevantní pro doménu subjektu aplikace.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/23/2018
ms.author: diberry
ms.openlocfilehash: 676546a215bbb8964f1cb2d26ae0fb9fd2ed9289
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "43769201"
---
# <a name="quickstart-analyze-text-using-c"></a>Rychlý start: Analýza textu pomocí C#

[!include[Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Požadavky

* [Visual Studio Community 2017](https://visualstudio.microsoft.com/vs/community/)
* Programovací jazyk C# (je součástí sady VS Community 2017)
* ID veřejné aplikace: df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Získání klíče LUIS

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="analyze-text-with-browser"></a>Analýza textu pomocí prohlížeče

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="analyze-text-with-c"></a>Analýza textu pomocí C# 

K získání stejných výsledků, jaké jste viděli v okně prohlížeče v předchozí části, použijte jazyk C# a dotaz na rozhraní [API](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) koncového bodu predikce. 

1. V sadě Visual Studio vytvořte novou konzolovou aplikaci. 

    ![Přístup do nabídky nastavení uživatelů služby LUIS](media/luis-get-started-cs-get-intent/visual-studio-console-app.png)

2. V projektu sady Visual Studio v Průzkumníku řešení vyberte **Přidat odkaz** a potom na kartě Sestavení vyberte **System.Web**.

    ![Přístup do nabídky nastavení uživatelů služby LUIS](media/luis-get-started-cs-get-intent/add-system-dot-web-to-project.png)

3. Soubor Program.cs přepište následujícím kódem:
    
   [!code-csharp[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/csharp/Program.cs)]

4. Nahraďte hodnotu `YOUR_KEY` klíčem služby LUIS.

5. Vytvořte a spusťte konzolovou aplikaci. Zobrazí se stejný JSON, jako jste viděli dříve v okně prohlížeče.

    ![Okno konzoly se zobrazeným výsledkem JSON ze služby LUIS](./media/luis-get-started-cs-get-intent/console-turn-on.png)

## <a name="luis-keys"></a>Klíče služby LUIS

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Jakmile tento rychlý start dokončíte, zavřete projekt sady Visual Studio a odeberte adresář projektů ze systému souborů. 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidání promluv a trénování s C#](luis-get-started-cs-add-utterance.md)