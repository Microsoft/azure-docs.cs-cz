---
title: Získání záměru pomocí volání REST v jazyce Java
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 7199dfaaa476e4be27929010b76a6e0544857bdb
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838537"
---
## <a name="prerequisites"></a>Předpoklady

* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* [Visual Studio Code](https://code.visualstudio.com/) nebo vaše oblíbené integrované vývojové prostředí (IDE)
* ID veřejné aplikace: df67dcdb-c37d-46af-88e1-8b97951ca1c2

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Získání klíče LUIS

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-programmatically"></a>Získání záměru prostřednictvím kódu programu

Pomocí Javy můžete získat přístup ke stejným výsledkům, jako jste viděli v okně prohlížeče v předchozím kroku. Nezapomeňte do svého projektu přidat knihovny Apache.

1. Zkopírujte následující kód a vytvořte třídu v souboru s názvem `LuisGetRequest.java`:

   [!code-java[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/java/call-endpoint.java)]

2. Nahraďte hodnotu proměnné `YOUR-KEY` klíčem služby LUIS.

3. Nahraďte cestou k souboru a zkompilujte program Java z příkazového řádku: `javac -cp .;<FILE_PATH>\* LuisGetRequest.java`.

4. Nahraďte cestou k souboru a spusťte aplikaci z příkazového řádku: `java -cp .;<FILE_PATH>\* LuisGetRequest.java`. Zobrazí se stejný JSON, jako jste viděli dříve v okně prohlížeče.

    ![Okno konzoly se zobrazeným výsledkem JSON ze služby LUIS](../media/luis-get-started-java-get-intent/console-turn-on.png)
    


## <a name="luis-keys"></a>Klíče služby LUIS

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Jakmile tento rychlý start dokončíte, zavřete projekt sady Visual Studio a odeberte adresář projektů ze systému souborů. 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidání projevy a výukového programu pomocí Java](../luis-get-started-java-add-utterance.md)