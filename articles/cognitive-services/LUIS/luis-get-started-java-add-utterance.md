---
title: Změna, trénovat na základě modelu aplikace Java
titleSuffix: Language Understanding - Azure Cognitive Services
description: V tomto rychlém startu pro Javu přidáte příklady promluv do aplikace domácí automatizace a budete aplikaci trénovat.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: e8bf2c2e7f4f37325fa741d190d99b617e1bcc0c
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54847905"
---
# <a name="quickstart-change-model-using-java"></a>Rychlý start: Změnit model pomocí Javy 

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [Quickstart prerequisites for endpoint](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* [Knihovna GSON JSON od Googlu](https://github.com/google/gson).

[!INCLUDE [Quickstart note about code repository](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>Soubor JSON s ukázkovými promluvami

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>Vytvoření kódu rychlého startu

1. Přidejte do souboru s názvem `AddUtterances.java` závislosti Java.

   [!code-java[Java Dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=23-26 "Java Dependencies")]

2. Vytvořte třídu `AddUtterances`. Tato třída bude obsahovat všechny následující fragmenty kódu.

    ```Java
    public class AddUtterances {
        // Insert code here
    }
    ```

3. Přidejte do třídy konstanty LUIS. Zkopírujte následující kód a použijte vlastní klíč pro vytváření, ID aplikace a ID verze.

   [!code-java[LUIS-based IDs](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=33-44 "LUIS-based IDs")]

4. Přidejte metodu volání do rozhraní API služby LUIS do třídy AddUtterances. 

   [!code-java[HTTP request to LUIS](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=46-168 "HTTP request to LUIS")]

5. Přidejte metodu volání pro odpověď HTTP z rozhraní API služby LUIS do třídy AddUtterances.

   [!code-java[HTTP response from LUIS](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=170-202 "HTTP response from LUIS")]

6. Přidejte zpracování výjimek do třídy AddUtterances. 

   [!code-java[Exception Handling](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=205-243 "Exception Handling")]

7. Přidejte hlavní funkci do třídy AddUtterances.

   [!code-java[Add main function](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=245-278 "Add main function")]

## <a name="build-code"></a>Vytvoření kódu

Zkompilujte AddUtterance se závislostmi.

```console
> javac -classpath gson-2.8.2.jar AddUtterances.java
```

## <a name="run-code"></a>Spuštění kódu
Zavoláním `AddUtterance` bez argumentů se do aplikace přidají promluvy LUIS, ale nepoužijí se k trénování.

```console
> java -classpath .;gson-2.8.2.jar AddUtterances
```

Tento příkazový řádek ukazuje výsledky volání rozhraní API pro přidávání promluv. 

[!INCLUDE [Quickstart response from API calls](../../../includes/cognitive-services-luis-qs-change-model-json-results.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků
Jakmile tento rychlý start dokončíte, odeberte všechny soubory, které jste v něm vytvořili. 

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"] 
> [Sestavení aplikace LUIS prostřednictvím kódu programu](luis-tutorial-node-import-utterances-csv.md) 