---
title: Získání záměru Node.js
titleSuffix: Language Understanding - Azure Cognitive Services
description: V tomto rychlém startu použijete dostupnou veřejnou aplikaci LUIS ke zjištění záměru uživatele z textu konverzace. Pomocí Node.js odešlete záměr uživatele jako text do koncového bodu předpovědi HTTP veřejné aplikace.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 12/07/2018
ms.author: diberry
ms.openlocfilehash: 92e10b1f4ec8be1dc67ff449df32ef76e365b5f2
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53162660"
---
# <a name="quickstart-get-intent-using-nodejs"></a>Rychlý start: Get záměr pomocí Node.js

V tomto rychlém startu budete do koncového bodu služby LUIS předávat promluvy a získávat zpět záměr a entity.

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Požadavky

* Programovací jazyk [Node.js](https://nodejs.org/) 
* [Visual Studio Code](https://code.visualstudio.com/)
* ID veřejné aplikace: df67dcdb-c37d-46af-88e1-8b97951ca1c2


> [!NOTE] 
> Kompletní řešení Node.js je k dispozici [ **LUIS-Samples** úložiště GitHub](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/analyze-text/node).

## <a name="get-luis-key"></a>Získání klíče LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Získání záměru prostřednictvím prohlížeče

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Získání záměru prostřednictvím kódu programu

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

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstraňte soubor Node.js.

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Přidání projevů](luis-get-started-node-add-utterance.md)
