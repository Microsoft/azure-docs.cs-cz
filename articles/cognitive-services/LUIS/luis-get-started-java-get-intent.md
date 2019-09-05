---
title: 'Rychlý start: Získat záměr, Java – LUIS'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu Java použijte k určení záměru uživatele z textu lze aplikaci dostupnou LUIS veřejné.
author: diberry
manager: nitinme
services: cognitive-services
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: d38802119ed9761b1baf497a9524c430eefda89a
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70375506"
---
# <a name="quickstart-get-intent-using-java"></a>Rychlý start: Získání záměru pomocí jazyka Java

V tomto rychlém startu budete do koncového bodu služby LUIS předávat promluvy a získávat zpět záměr a entity.

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Požadavky

* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* [Visual Studio Code](https://code.visualstudio.com/) nebo vaše oblíbené integrované vývojové prostředí (IDE)
* ID veřejné aplikace: df67dcdb-c37d-46af-88e1-8b97951ca1c2

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Získání klíče LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Získání záměru prostřednictvím prohlížeče

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Získání záměru prostřednictvím kódu programu

Pomocí Javy můžete získat přístup ke stejným výsledkům, jako jste viděli v okně prohlížeče v předchozím kroku. Nezapomeňte do svého projektu přidat knihovny Apache.

1. Zkopírujte následující kód a vytvořte třídu v souboru s názvem `LuisGetRequest.java`:

   [!code-java[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/java/call-endpoint.java)]

2. Nahraďte hodnotu proměnné `YOUR-KEY` klíčem služby LUIS.

3. Nahraďte cestou k souboru a zkompilujte program Java z příkazového řádku: `javac -cp .;<FILE_PATH>\* LuisGetRequest.java`.

4. Nahraďte cestou k souboru a spusťte aplikaci z příkazového řádku: `java -cp .;<FILE_PATH>\* LuisGetRequest.java`. Zobrazí se stejný JSON, jako jste viděli dříve v okně prohlížeče.

    ![Okno konzoly se zobrazeným výsledkem JSON ze služby LUIS](./media/luis-get-started-java-get-intent/console-turn-on.png)
    
## <a name="luis-keys"></a>Klíče služby LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstraňte soubor/složku projektu Java.

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Přidání projevů](luis-get-started-java-add-utterance.md)
