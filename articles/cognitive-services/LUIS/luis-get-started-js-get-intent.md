---
title: Analýza textu v přirozeném jazyce ve službě Language Understanding (LUIS) pomocí JavaScriptu – Cognitive Services – Azure Cognitive Services | Microsoft Docs
description: V tomto rychlém startu použijete dostupnou veřejnou aplikaci LUIS ke zjištění záměru uživatele z textu konverzace. Pomocí JavaScriptu odešlete záměr uživatele jako text do koncového bodu předpovědi HTTP veřejné aplikace. Služba LUIS použije v koncovém bodě model veřejné aplikace k analýze smyslu textu v přirozeném jazyce, zjištění celkového záměru a extrakci dat, která jsou relevantní pro doménu subjektu aplikace.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/23/2018
ms.author: diberry
ms.openlocfilehash: 10210c3759611a77c4430a97896a19a6b97b9fa9
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "43769208"
---
# <a name="quickstart-analyze-text-using-javascript"></a>Rychlý start: Analýza textu pomocí JavaScriptu

[!include[Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="prerequisites"></a>Požadavky

* [Visual Studio Code](https://code.visualstudio.com/)
* ID veřejné aplikace: df67dcdb-c37d-46af-88e1-8b97951ca1c2


## <a name="get-luis-key"></a>Získání klíče LUIS

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="analyze-text-with-browser"></a>Analýza textu pomocí prohlížeče

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="analyze-text-with-javascript"></a>Analýza textu pomocí JavaScriptu 

Pomocí JavaScriptu můžete získat přístup ke stejným výsledkům, jako jste viděli v okně prohlížeče v předchozím kroku. 

1. Zkopírujte následující kód a uložte ho do souboru HTML:

   [!code-html[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/javascript/call-endpoint.html)]

2. Otevřete soubor v prohlížeči. Zadejte klíč koncového bodu služby LUIS do formuláře a vyberte **Odeslat**.

    ![Ukázka kódu HTML zobrazeného v prohlížeči s výsledky služby LUIS pro aplikaci domácí automatizace](./media/luis-get-started-js-get-intent/html-results.png)

    Výsledky se zobrazí pod formulářem. 

## <a name="luis-keys"></a>Klíče služby LUIS

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstraňte soubor JavaScriptu.

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Přidání projevů](luis-get-started-javascript-add-utterance.md)
