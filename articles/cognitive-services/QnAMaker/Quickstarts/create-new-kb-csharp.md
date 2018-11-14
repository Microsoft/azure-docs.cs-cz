---
title: 'Rychlý start: Vytvoření znalostní báze – REST, C# – QnA Maker'
titlesuffix: Azure Cognitive Services
description: Tento rychlý start založený na REST a jazyce C# vás provede procesem vytvoření ukázkové znalostní báze služby QnA Maker prostřednictvím kódu programu. Tato znalostní báze se zobrazí na řídicím panelu Azure účtu rozhraní API služeb Cognitive Services.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 11/6/2018
ms.author: diberry
ms.openlocfilehash: d9040965a0cfaf022bf4cc582cb2aeaa34d04849
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51252441"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-c"></a>Rychlý start: Vytvoření znalostní báze ve službě QnA Maker pomocí jazyka C#

Tento rychlý start vás provede vytvořením a publikováním ukázkové znalostní báze služby QnA Maker pomocí kódu programu. Služba QnA Maker automaticky extrahuje otázky a odpovědi z částečně strukturovaného obsahu, jako jsou třeba časté otázky, ze [zdrojů dat](../Concepts/data-sources-supported.md). Model pro znalostní bázi je definovaný v kódu ve formátu JSON poslaném v těle požadavku rozhraní API. 

Tento rychlý start volá rozhraní API služby QnA Maker:
* [Create KB](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
* [Get Operation Details](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)
* [Publikování](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) 

## <a name="prerequisites"></a>Požadavky

* Nejnovější verze sady [**Visual Studio Community Edition**](https://www.visualstudio.com/downloads/).
* Potřebujete [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pokud chcete získat klíč, vyberte na řídicím panelu **Klíče** v části **Správa prostředků**. 

> [!NOTE] 
> Kompletní soubor(y) řešení jsou k dispozici v [**Azure-Samples/cognitive-services-qnamaker-csharp** úložišti GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/quickstarts/create-and-publish-knowledge-base).

## <a name="create-a-knowledge-base-project"></a>Vytvoření projektu znalostní báze

[!INCLUDE [Create Visual Studio Project](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-create-project.md)] 

## <a name="add-the-required-dependencies"></a>Přidání požadovaných závislostí

V horní části souboru Program.cs nahraďte samostatný příkaz using následujícími řádky, které do projektu přidají potřebné závislosti:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=1-11 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Přidání požadovaných konstant

Do horní části třídy Program přidejte následující konstanty, které umožní přístup ke službě QnA Maker:

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=17-24 "Add the required constants")]

## <a name="add-the-kb-definition"></a>Přidání definice znalostní báze

Za konstanty přidejte následující definici znalostní báze:

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=32-57 "Add the required constants")]

## <a name="add-supporting-functions-and-structures"></a>Přidání podpůrných funkcí a struktur
Přidejte následující blok kódu do třídy Program:

[!code-csharp[Add supporting functions and structures](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=62-82 "Add supporting functions and structures")]

## <a name="add-a-post-request-to-create-kb"></a>Přidání požadavku POST na vytvoření znalostní báze

Následující kód odešle do rozhraní API služby QnA Maker požadavek HTTPS, který vytvoří znalostní bázi. Potom kód přijme odpověď:

[!code-csharp[Add a POST request to create KB](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=91-105 "Add a POST request to create KB")]

Toto volání rozhraní API vrátí odpověď JSON, která v poli hlavičky **Location** obsahuje ID operace. Toto ID operace použijte ke zjištění toho, jestli se znalostí báze úspěšně vytvořila. 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-get-request-to-determine-creation-status"></a>Přidání požadavku GET, který určí stav vytváření

Zkontrolujte stav operace.

[!code-csharp[Add GET request to determine creation status](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=159-170 "Add GET request to determine creation status")]

Toto volání rozhraní API vrátí odpověď ve formátu JSON, která obsahuje i stav operace: 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

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

## <a name="add-createkb-method"></a>Přidání metody CreateKB

Následující metoda vytvoří znalostní bázi a opakovaně zkontroluje stav.  V poli hlavičky odpovědi POST **Location** se vrátí **ID operace** _vytváření_, které se pak použije jako část trasy v požadavku GET. Jelikož může vytvoření znalostní báze nějakou dobu trvat, je potřeba volání pro kontrolu stavu opakovat, dokud vrácený stav neoznámí úspěch nebo neúspěch. Pokud operace proběhne úspěšně, ve vlastnosti **resourceLocation** se vrátí ID znalostní báze. 

[!code-csharp[Add CreateKB method](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=176-237 "Add CreateKB method")]

## <a name="add-the-createkb-method-to-main"></a>Přidání metody CreateKB do metody Main

Změňte metodu Main, aby volala metodu CreateKB:

[!code-csharp[Add CreateKB method](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=239-248 "Add CreateKB method")]

## <a name="build-and-run-the-program"></a>Sestavení a spuštění programu

Sestavte program a spusťte ho. Program automaticky odešle do rozhraní API služby QnA Maker požadavek na vytvoření znalostní báze a dál se bude každých 30 sekund dotazovat na výsledky. Každá odpověď se zobrazí v okně konzoly.

Jakmile se znalostní báze vytvoří, můžete se na ni podívat na portálu služby QnA Maker na stránce [vašich znalostních bází](https://www.qnamaker.ai/Home/MyServices). 


[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
