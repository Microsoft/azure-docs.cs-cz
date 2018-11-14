---
title: 'Rychlý start: Vytvoření znalostní báze – REST, Java – QnA Maker'
titlesuffix: Azure Cognitive Services
description: Tento rychlý start založený na REST a Javě vás provede procesem vytvoření ukázkové znalostní báze služby QnA Maker prostřednictvím kódu programu. Tato znalostní báze se zobrazí na řídicím panelu Azure účtu rozhraní API služeb Cognitive Services.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 11/06/2018
ms.author: diberry
ms.openlocfilehash: 47a900f6877355fb45481d7b04052387ab3619cf
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51229590"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-java"></a>Rychlý start: Vytvoření znalostní báze ve službě QnA Maker pomocí Javy

Tento rychlý start vás provede vytvořením ukázkové znalostní báze služby QnA Maker pomocí kódu programu. Služba QnA Maker automaticky extrahuje otázky a odpovědi z částečně strukturovaného obsahu, jako jsou třeba časté otázky, ze [zdrojů dat](../Concepts/data-sources-supported.md). Model pro znalostní bázi je definovaný v kódu ve formátu JSON poslaném v těle požadavku rozhraní API. 

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-java-repo-note.md)]

## <a name="create-a-knowledge-base-file"></a>Vytvoření souboru pro znalostní bázi 

Vytvořte soubor s názvem `CreateKB.java`.

## <a name="add-the-required-dependencies"></a>Přidání požadovaných závislostí

Na začátek souboru `CreateKB.java` přidejte následující řádky na přidání potřebných závislostí do projektu:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=1-5 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Přidání požadovaných konstant
Za předcházející požadované závislosti přidejte do třídy `CreateKB` požadované konstanty pro přístup ke službě QnA Maker. Nahraďte hodnotu proměnné `subscriptionKey` vlastním klíčem služby QnA Maker. Na konec třídy není potřeba přidávat ukončovací složenou závorku, ta je v konečném fragmentu kódu na konci tohoto rychlého startu. 

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=26-34 "Add the required constants")]


## <a name="add-the-kb-model-definition-classes"></a>Přidání tříd definice modelu znalostní báze
Za konstanty přidejte do třídy `CreateKB` následující třídy a funkce pro serializaci objektu definice modelu do formátu JSON.

[!code-java[Add the KB model definition classes](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=36-80 "Add the KB model definition classes")]

## <a name="add-supporting-functions"></a>Přidání podpůrných funkcí

Dále do třídy `CreateKB` přidejte následující podpůrné funkce.

1. Přidejte následující funkci pro tisk souboru JSON v čitelném formátu:    

    [!code-java[Add the PrettyPrint function](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=82-87 "Add the KB model definition classes")]

2. Přidejte následující třídu pro správu odpovědi HTTP:

    [!code-java[Add class to manage the HTTP response](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=89-97 "Add class to manage the HTTP response")]

3. Přidejte následující metodu pro vytvoření požadavku POST na rozhraní API služby QnA Maker. `Ocp-Apim-Subscription-Key` je klíč služby QnA Maker používaný k ověřování. 

    [!code-java[Add POST method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=99-121 "Add POST method")]

4. Přidejte následující metodu pro vytvoření požadavku GET na rozhraní API služby QnA Maker.

    [!code-java[Add GET method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=123-137 "Add GET method")]

## <a name="add-a-method-to-create-the-kb"></a>Přidání metody pro vytvoření znalostní báze
Přidejte následující metodu pro vytvoření znalostní báze zavoláním metody Post. 

[!code-java[Add CreateKB method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=139-144 "Add CreateKB method")]

Toto volání rozhraní API vrátí odpověď ve formátu JSON, která obsahuje i ID operace. Toto ID operace použijte ke zjištění toho, jestli se znalostí báze úspěšně vytvořila. 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-a-method-to-get-status"></a>Přidání metody pro získání stavu
Přidejte následující metodu pro kontrolu stavu vytváření. 

[!code-java[Add GetStatus method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=146-150 "Add GetStatus method")]

Volání opakujte, dokud neskočí úspěchem nebo neúspěchem: 

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-a-main-method"></a>Přidání metody main
Metoda main vytvoří znalostní bázi a pak se dotazuje na stav. V poli hlavičky odpovědi POST **Location** se vrátí **ID operace** vytváření, které se pak použije jako část trasy v požadavku GET. **Smyčka `while` kontroluje stav, dokud není dokončený. 

[!code-java[Add main method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=152-191 "Add main method")]
 
## <a name="compile-and-run-the-program"></a>Kompilace a spuštění programu

1. Ujistěte se, že adresář `./libs` obsahuje knihovnu gson. Na příkazovém řádku zkompilujte soubor `CreateKB.java`:

    ```bash
    javac -cp ".;libs/*" CreateKB.java
    ```

2. Spusťte program zadáním následujícího příkazu na příkazovém řádku. Program pošle požadavek na vytvoření znalostní báze do rozhraní API služby QnA Maker a pak se bude dotazovat na výsledky každých 30 sekund. Každá odpověď se zobrazí v okně konzoly.

    ```base
    java -cp ",;libs/*" CreateKB
    ```

Jakmile se znalostní báze vytvoří, můžete se na ni podívat na portálu služby QnA Maker na stránce [vašich znalostních bází](https://www.qnamaker.ai/Home/MyServices).    

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)