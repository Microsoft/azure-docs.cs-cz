---
title: Získání záměru, Java
titleSuffix: Language Understanding - Azure Cognitive Services
description: V tomto rychlém startu Java použijte k určení záměru uživatele z textu lze aplikaci dostupnou LUIS veřejné.
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: c55721e803b85c536067a90019f75dd5aa0f7845
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53085344"
---
# <a name="quickstart-get-intent-using-java"></a>Rychlý start: Získání záměru pomocí Javy

V tomto rychlém startu budete do koncového bodu služby LUIS předávat promluvy a získávat zpět záměr a entity.

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Požadavky

* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* [Visual Studio Code](https://code.visualstudio.com/)
* ID veřejné aplikace: df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Získání klíče LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Získání záměru prostřednictvím prohlížeče

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Získání záměru prostřednictvím kódu programu 

Pomocí Javy můžete získat přístup ke stejným výsledkům, jako jste viděli v okně prohlížeče v předchozím kroku. 

1. Zkopírujte následující kód a vytvořte třídu v souboru s názvem `LuisGetRequest.java`:

   [!code-java[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/java/call-endpoint.java)]

2. Nahraďte hodnotu proměnné `YOUR-KEY` klíčem služby LUIS.

3. Kompilujte program Javy pomocí `javac -cp ":lib/*" LuisGetRequest.java`. 

4. Spusťte aplikaci klávesou `java -cp ":lib/*" LuisGetRequest.java`. Zobrazí se stejný JSON, jako jste viděli dříve v okně prohlížeče.

    ![Okno konzoly se zobrazeným výsledkem JSON ze služby LUIS](./media/luis-get-started-java-get-intent/console-turn-on.png)
    
## <a name="luis-keys"></a>Klíče služby LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstraňte soubor Javy. 

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Přidání projevů](luis-get-started-java-add-utterance.md)