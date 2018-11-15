---
title: 'Kurz: Vytvoření, publikování a odpovědi ze znalostní báze – REST, C# – QnA Maker'
titleSuffix: Azure Cognitive Services
description: Tento kurz založený na rozhraní REST vás provede programovým vytvořením a publikováním znalostní báze a následným zodpovězením otázky ze znalostní báze.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.technology: qna-maker
ms.topic: tutorial
ms.date: 10/29/2018
ms.author: diberry
ms.openlocfilehash: 013b74165afe6eafee337bd00ca88b1034ad5662
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2018
ms.locfileid: "51299555"
---
# <a name="tutorial-using-c-create-knowledge-base-then-answer-question"></a>Kurz: Vytvoření znalostní báze a následné zodpovězení otázky pomocí jazyka C#

Tento kurz vás provede programovým vytvořením a publikováním znalostní báze a následným zodpovězením otázky zákazníka s využitím znalostní báze. 

> [!div class="checklist"]
* Vytvoření znalostní báze 
* Kontrola stavu vytváření
* Trénování a publikování znalostní báze
* Získání informací o koncových bodech
* Dotazování znalostní báze pomocí cURL


Tento rychlý start volá rozhraní API služby QnA Maker:

* [Vytvoření znalostní báze](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
* [Get Operation Details](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)
* [Získání podrobností o znalostní bázi](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/knowledgebases_getknowledgebasedetails) 
* [Získání koncových bodů znalostní báze](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/endpointkeys_getendpointkeys)
* [Publikování](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) 

## <a name="prerequisites"></a>Požadavky

* Nejnovější verze sady [**Visual Studio Community Edition**](https://www.visualstudio.com/downloads/).
* Potřebujete [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pokud chcete získat klíč, vyberte na řídicím panelu **Klíče** v části **Správa prostředků**. 

> [!NOTE] 
> Kompletní soubor(y) řešení jsou k dispozici v [**Azure-Samples/cognitive-services-qnamaker-csharp** úložišti GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base).

## <a name="create-a-knowledge-base-project"></a>Vytvoření projektu znalostní báze

[!INCLUDE [Create Visual Studio Project](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-create-project.md)] 

## <a name="add-the-required-dependencies"></a>Přidání požadovaných závislostí

V horní části souboru Program.cs nahraďte samostatný příkaz _using_ následujícími řádky, které přidají k projektu potřebné závislosti:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=1-11 "Add the required dependencies")]

## <a name="add-a-kbdetails-class"></a>Přidání třídy KBDetails
Mezi závorky oboru názvů přidejte následující třídu KBDetails. Tato třída umožňuje knihovně NewtonSoft deserializovat odpověď JSON do třídy jazyka C#.

[!code-csharp[Add a KBDetails class](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=15-26 "Add a KBDetails class")]

## <a name="add-the-required-constants"></a>Přidání požadovaných konstant

Do horní části třídy Program přidejte následující konstanty, které umožní přístup ke službě QnA Maker:

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=30-57 "Add the required constants")]

## <a name="add-the-kb-definition"></a>Přidání definice znalostní báze

Za konstanty přidejte následující definici modelu znalostní báze:

[!code-csharp[Add the KB definition](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=59-85 "Add the KB definition")]

## <a name="add-supporting-functions-and-structures"></a>Přidání podpůrných funkcí a struktur
Přidejte následující blok kódu do třídy Program:

[!code-csharp[Add supporting functions and structures](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=87-123 "Add supporting functions and structures")]

## <a name="add-a-post-request-to-create-kb"></a>Přidání požadavku POST na vytvoření znalostní báze

Následující kód odešle do rozhraní API služby QnA Maker požadavek HTTPS, který vytvoří znalostní bázi. Potom kód přijme odpověď:

[!code-csharp[Add a POST request to create KB](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=124-141 "Add a POST request to create KB")]

Toto volání rozhraní API vrátí odpověď ve formátu JSON, která obsahuje i ID operace. Program později toto ID operace použije ke zjištění toho, jestli se znalostí báze úspěšně vytvořila. 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-get-request-to-determine-creation-status"></a>Přidání požadavku GET, který určí stav vytváření

Zkontrolujte stav operace vytváření.

[!code-csharp[Add GET request to determine creation status](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=142-151 "Add GET request to determine creation status")]

Toto volání rozhraní API vrátí odpověď ve formátu JSON, která obsahuje i stav operace: 

```JSON
{
  "operationState": "Running",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
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
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-createkb-method"></a>Přidání metody CreateKB

Následující metoda zapouzdřuje volání pro vytvoření znalostní báze a kontrolu stavu.  V poli hlavičky odpovědi POST **Location** se vrátí **ID operace** _vytváření_, které se pak použije jako část trasy v požadavku GET. Jelikož může vytvoření znalostní báze nějakou dobu trvat, je potřeba volání pro kontrolu stavu opakovat, dokud vrácený stav neoznámí úspěch nebo neúspěch. Pokud operace proběhne úspěšně, ve vlastnosti **resourceLocation** se vrátí ID znalostní báze. 

[!code-csharp[Add GET request to determine creation status](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=152-227 "Add GET request to determine creation status")]

## <a name="add-publish-method"></a>Přidání metody publikování

Po úspěšném vytvoření znalostní báze ji publikujte. Možná jste očekávali volání rozhraní API pro trénování. To v této verzi není potřeba. 

Následující kód odešle do rozhraní API služby QnA Maker požadavek HTTPS, který publikuje znalostní bázi. Potom kód přijme odpověď:

[!code-csharp[Add publish method](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=228-259 "Add publish method")]

Volání API vrátí v případě úspěšného publikování stav 204, přičemž tělo odpovědi bude prázdné. Kód rychlého startu přidá pro odpovědi 204 text, abyste viděli výsledek.

V případě jakékoli jiné odpověď se vrátí tato odpověď beze změny.

## <a name="generating-an-answer"></a>Generování odpovědi
Aby program mohl získat přístup ke znalostní bázi za účelem odeslání otázky a přijetí nejlepší odpovědi, potřebuje _hostitele koncového bodu_ z rozhraní API pro podrobnosti o znalostní bázi a _klíč primárního koncového bodu_ z rozhraní API pro koncové body. Tyto metody společně s metodou pro vygenerování odpovědi najdete v následujících částech. 

Následující tabulka ukazuje, jak se tyto údaje používají k vytvoření identifikátoru URI:

|Šablona identifikátoru URI pro vygenerování odpovědi|
|--|
|https://**NÁZEV_HOSTITELE**.azurewebsites.net/qnamaker/knowledgebases/**ID_ZNALOSTNÍ_BÁZE**/generateAnswer|

_Primární koncový bod_ se předává jako hlavička pro ověření požadavku na vygenerování odpovědi:

|Název hlavičky|Hodnota hlavičky|
|--|--|
|Autorizace|`Endpoint ` + **primární koncový bod**<br>Příklad: `Endpoint xxxxxxx`<br>Všimněte si mezery mezi textem `Endpoint` a hodnotou primárního koncového bodu. 

V textu požadavku se musí předávat správný kód JSON:

```JSON
{
    question: "What languages does QnA Maker support?"
}
```

## <a name="get-kb-details"></a>Získání podrobností o znalostní bázi
Přidejte následující metodu pro získání podrobností o znalostní bázi. Mezi tyto podrobnosti patří název hostitele znalostní báze. Název hostitele je název webové služby Azure QnA Maker, který jste zadali při vytváření prostředku služby QnA Maker. 

[!code-csharp[Get KB Details](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=260-273 "Add publish method")]

Toto volání rozhraní API vrátí odpověď JSON: 

```JSON
{
  "id": "ZZZ31e19-cba7-48d1-8594-5c4297ecc9c1",
  "hostName": "https://qnamaker-s0-s.azurewebsites.net",
  "lastAccessedTimestamp": "2018-10-11T18:10:05Z",
  "lastChangedTimestamp": "2018-10-11T18:09:37Z",
  "lastPublishedTimestamp": "2018-10-11T18:10:15Z",
  "name": "QnA Maker FAQ from quickstart",
  "userId": "AAAc3841df0b42cdb00f53a49d51a89c",
  "urls": [
    "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs",
    "https://docs.microsoft.com/en-us/bot-framework/resources-bot-framework-faq"
  ],
  "sources": [
    "Custom Editorial"
  ]
}
```

## <a name="get-kb-endpoints"></a>Získání koncových bodů znalostní báze
Přidejte následující metodu pro získání primárních koncových bodů služby QnA Maker. Tyto koncové body nejsou vázané na znalostní bázi. Jsou platné pro všechny znalostní báze přidružené ke klíčům prostředku služby QnA Maker z webu Azure Portal.  

[!code-csharp[Get KB Endpoints](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=274-289 "Get KB Endpoints")]

Toto volání rozhraní API vrátí odpověď JSON: 

```JSON
{
  "primaryEndpointKey": "AAAb5719-e2f7-4a33-937d-7a3b4736a1be",
  "secondaryEndpointKey": "BBBcba78-c1d2-4166-b98f-c77255aefaba",
  "installedVersion": "4.2.0",
  "lastStableVersion": "4.2.0"
}
```

## <a name="get-an-answer"></a>Získání odpovědi
Přidejte následující metodu pro získání odpovědi na otázku uživatele. 

[!code-csharp[Get Answer](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=290-315 "Get Answer")]

Toto volání rozhraní API vrátí odpověď JSON: 

```JSON
{
  "answers": [
    {
      "questions": [
        "Does QnA Maker support non-English languages?"
      ],
      "answer": "See more details about [supported languages](https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/overview/languages-supported).\n\n\nIf you have content from multiple languages, be sure to create a separate service for each language.",
      "score": 82.19,
      "id": 11,
      "source": "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs",
      "metadata": []
    }
  ]
}
```

## <a name="main-method"></a>Metoda Main
Metoda Main ukazuje synchronní volání pro vytvoření, publikování a vygenerování odpovědi. 

[!code-csharp[Main method](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=316-337 "Main method")]

## <a name="build-and-run-the-program"></a>Sestavení a spuštění programu

Sestavte program a spusťte ho. 

Jakmile se znalostní báze vytvoří, můžete se na ni podívat na portálu služby QnA Maker na stránce [vašich znalostních bází](https://www.qnamaker.ai/Home/MyServices). Když víte, jak používat rozhraní API pro generování odpovědí, můžete toto rozhraní API používat s jakýmkoli jazykem nebo rozhraním pro požadavky HTTP. 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
