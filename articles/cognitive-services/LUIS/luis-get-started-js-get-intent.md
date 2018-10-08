---
title: Rychlý start pro Javascript – předpověď záměru – LUIS
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu použijete dostupnou veřejnou aplikaci LUIS ke zjištění záměru uživatele z textu konverzace. Pomocí JavaScriptu odešlete záměr uživatele jako text do koncového bodu předpovědi HTTP veřejné aplikace. Služba LUIS použije v koncovém bodě model veřejné aplikace k analýze smyslu textu v přirozeném jazyce, zjištění celkového záměru a extrakci dat, která jsou relevantní pro doménu subjektu aplikace.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: cbbf0d2388f83f4e38f0a63b6bc181d4a24e0aa0
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47039476"
---
# <a name="quickstart-get-intent-using-javascript"></a>Rychlý start: Získání záměru pomocí Javascriptu

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="prerequisites"></a>Požadavky

* [Visual Studio Code](https://code.visualstudio.com/)
* ID veřejné aplikace: df67dcdb-c37d-46af-88e1-8b97951ca1c2


## <a name="get-luis-key"></a>Získání klíče LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Získání záměru prostřednictvím prohlížeče

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Získání záměru prostřednictvím kódu programu

Pomocí JavaScriptu můžete získat přístup ke stejným výsledkům, jako jste viděli v okně prohlížeče v předchozím kroku. 

1. Zkopírujte následující kód a uložte ho do souboru HTML:

   [!code-html[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/javascript/call-endpoint.html)]

2. Otevřete soubor v prohlížeči. Zadejte klíč koncového bodu služby LUIS do formuláře a vyberte **Odeslat**.

    ![Ukázka kódu HTML zobrazeného v prohlížeči s výsledky služby LUIS pro aplikaci domácí automatizace](./media/luis-get-started-js-get-intent/html-results.png)

    Výsledky se zobrazí pod formulářem. 

## <a name="luis-keys"></a>Klíče služby LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstraňte soubor JavaScriptu.

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Přidání projevů](luis-get-started-javascript-add-utterance.md)
