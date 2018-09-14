---
title: Analýza textu v přirozeném jazyce ve službě Language Understanding (LUIS) pomocí Node.js – Cognitive Services – Azure Cognitive Services | Microsoft Docs
description: V tomto rychlém startu použijete dostupnou veřejnou aplikaci LUIS ke zjištění záměru uživatele z textu konverzace. Pomocí Node.js odešlete záměr uživatele jako text do koncového bodu předpovědi HTTP veřejné aplikace. Služba LUIS použije v koncovém bodě model veřejné aplikace k analýze smyslu textu v přirozeném jazyce, zjištění celkového záměru a extrakci dat, která jsou relevantní pro doménu subjektu aplikace.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/23/2018
ms.author: diberry
ms.openlocfilehash: d7067041ae8e413675de3c95ca4ba0c1b987f47a
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "43769206"
---
# <a name="quickstart-analyze-text-using-nodejs"></a>Rychlý start: Analýza textu pomocí Node.js

[!include[Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Požadavky

* Programovací jazyk [Node.js](https://nodejs.org/) 
* [Visual Studio Code](https://code.visualstudio.com/)
* ID veřejné aplikace: df67dcdb-c37d-46af-88e1-8b97951ca1c2


> [!NOTE] 
> Kompletní řešení Node.js je dostupné v [úložišti **LUIS-Samples** na GitHubu](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/analyze-text/node).

## <a name="get-luis-key"></a>Získání klíče LUIS

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="analyze-text-with-browser"></a>Analýza textu pomocí prohlížeče

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="analyze-text-with-nodejs"></a>Analýza textu pomocí Node.js

Pomocí Node.js můžete získat přístup ke stejným výsledkům, jako jste viděli v okně prohlížeče v předchozím kroku.

1. Zkopírujte následující fragment kódu:

   [!code-nodejs[Console app code that calls a LUIS endpoint for Node.js](~/samples-luis/documentation-samples/quickstarts/analyze-text/node/call-endpoint.js)]

2. Vytvořte soubor `.env` s následujícím textem nebo nastavte tyto proměnné v systémovém prostředí:

    ```CMD
    LUIS_APP_ID=df67dcdb-c37d-46af-88e1-8b97951ca1c2
    LUIS_ENDPOINT_KEY=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
    ```

3. Nastavte proměnnou prostředí `LUIS_ENDPOINT_KEY` na svůj klíč.

4. Spuštěním následujícího příkazu v příkazovém řádku nainstalujte závislosti: `npm install`.

5. Spusťte kód pomocí `npm start`. Zobrazí se stejné hodnoty, jako jste viděli dříve v okně prohlížeče.

## <a name="luis-keys"></a>Klíče služby LUIS

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstraňte soubor Node.js.

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Přidání projevů](luis-get-started-node-add-utterance.md)