---
title: Metadata s GenerateAnswer API – QnA Maker
titleSuffix: Azure Cognitive Services
description: Nástroj QnA Maker umožňuje přidat metadata ve formě dvojice klíč/hodnota do sad otázek a odpovědí. Tyto informace slouží k filtrování výsledků na dotazy uživatelů a k ukládání dalších informací, lze použít v následných konverzace.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 05/30/2019
ms.author: tulasim
ms.openlocfilehash: 3088d0f161496cfd2e1cb8897cef36365ece9962
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66496961"
---
# <a name="get-a-knowledge-answer-with-the-generateanswer-api-and-metadata"></a>Získání odpovědí znalostní báze s rozhraním GenerateAnswer API a metadat

Předpokládaná odpověď na otázku uživatele, použijte rozhraní API GenerateAnswer. Při publikování znalostní báze těchto informací pomocí tohoto rozhraní API se zobrazí na stránce publikování. Můžete také konfigurovat rozhraní API filtrování odpovědi na základě metadat značek a testování ve znalostní bázi z koncového bodu pomocí parametru řetězce dotazu testu.

Nástroj QnA Maker umožňuje přidat metadata ve formě dvojice klíče a hodnoty do sad otázek a odpovědí. Tyto informace slouží k filtrování výsledků na dotazy uživatelů a k ukládání dalších informací, lze použít v následných konverzace. Další informace najdete v tématu [znalostní báze](../Concepts/knowledge-base.md).

<a name="qna-entity"></a>

## <a name="storing-questions-and-answers-with-a-qna-entity"></a>Ukládání otázky a odpovědi s entitou QnA

Nejprve je důležité pochopit, jak QnA Maker ukládá data otázek a odpovědí. Následující obrázek znázorňuje QnA entity:

![Nástroj QnA Entity](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Každá entita QnA má jedinečný a trvalý ID. ID je možné provést aktualizace na konkrétní entitu QnA.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Získání odpovědí předpovědi s rozhraním API GenerateAnswer

Použití rozhraní API GenerateAnswer v váš Bot nebo aplikaci k dotazování znalostní báze se dotaz uživatele získat nejlepší shodu ze sady otázek a odpovědí.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Publikování na získání GenerateAnswer koncového bodu 

Po publikování znalostní báze, buď z [portál QnA Maker](https://www.qnamaker.ai), nebo pomocí [API](https://go.microsoft.com/fwlink/?linkid=2092179), můžete získat podrobné GenerateAnswer koncového bodu.

Získat podrobnosti o vašich koncových bodů:
1. Přihlaste se k webu [https://www.qnamaker.ai](https://www.qnamaker.ai).
1. V **Moje znalostních bází**, klikněte na **zobrazit kód** pro znalostní báze.
    ![Moje znalostních bází](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. Získáte podrobnosti o vašich GenerateAnswer koncového bodu.

    ![Podrobnosti o koncovém bodu](../media/qnamaker-how-to-metadata-usage/view-code.png)

Můžete také získat podrobnosti o vašem koncového bodu z **nastavení** kartu znalostní báze.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>Konfigurační požadavek GenerateAnswer

Volání GenerateAnswer pomocí požadavku HTTP POST. Ukázkový kód, který ukazuje, jak volat GenerateAnswer, najdete v článku [rychlých startů](../quickstarts/csharp.md).

**Adresa URL požadavku** má následující formát: 

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

|Vlastnost požadavku HTTP|Name|Type|Účel|
|--|--|--|--|
|Parametr trasa adresy URL|ID znalostní báze|string|Identifikátor GUID pro znalostní báze.|
|Parametr trasa adresy URL|Hostitel koncového bodu QnA maker|string|Název hostitele koncového bodu nasazené ve vašem předplatném Azure. Toto je k dispozici na stránce nastavení po publikování znalostní báze. |
|Záhlaví|Content-Type|string|Typ média textu odeslaného do rozhraní API. Výchozí hodnota je: "|
|Záhlaví|Autorizace|string|Klíče vašeho koncového bodu (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Tělo POST|JSON – objekt|JSON|Dotaz s nastavením|


Text JSON má několik nastavení:

|Vlastnost text JSON|Požaduje se|Type|Účel|
|--|--|--|--|
|`question`|Vyžaduje|string|Uživatel dotaz k odeslání do znalostní báze.|
|`top`|Volitelné|integer|Číslo seřazený výsledků, které chcete zahrnout do výstupu. Výchozí hodnota je 1.|
|`userId`|Volitelné|string|Jedinečné ID k identifikaci uživatele. Toto ID se zaznamená do protokolů chatu.|
|`scoreThreshold`|Volitelné|integer|Vrátí se pouze odpovědi s jistotou skóre nad touto prahovou hodnotou. Výchozí hodnota je 0.|
|`isTest`|Volitelné|Boolean|Pokud nastavena na hodnotu true, vrátí výsledky z `testkb` indexu vyhledávání místo publikované indexu.|
|`strictFilters`|Volitelné|string|Je-li zadána, říká QnA Maker vrátit pouze odpovědi, které mají zadanou metadat. Použití `none` znamená, odpověď by měla mít žádné filtry metadat. |
|`RankerType`|Volitelné|string|Pokud je zadán jako `QuestionOnly`, informuje nástroj QnA Maker k vyhledání pouze dotazy. Pokud není zadaný, hledá QnA Maker otázek a odpovědí.

Příklad text JSON vypadá takto:

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>Vlastnosti GenerateAnswer odpovědi

Úspěšná odpověď vrací stav 200 a odpověď ve formátu JSON. 

|Vlastnost odpovědi (seřazené podle skóre)|Účel|
|--|--|
|skóre|Hodnocení 0 až 100.|
|Id|Jedinečné ID přiřazené k odpovědi.|
|Dotazy|Dotazy poskytnutých uživatelem.|
|Odpověď|Odpověď na dotaz.|
|source|Název zdroje, ze kterého byla odpověď extrahovat nebo uložit znalostní báze knowledge base.|
|zprostředkovatele identity|Metadata přidružená k odpovědi.|
|metadata.name|Název metadat. (maximální délka řetězce: 100, povinné)|
|metadata.Value|Hodnota metadat. (maximální délka řetězce: 100, povinné)|


```json
{
    "answers": [
        {
            "score": 28.54820341616869,
            "Id": 20,
            "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
            "source": "Custom Editorial",
            "questions": [
                "How can I integrate LUIS with QnA Maker?"
            ],
            "metadata": [
                {
                    "name": "category",
                    "value": "api"
                }
            ]
        }
    ]
}
```

<a name="metadata-example"></a>

## <a name="using-metadata-allows-you-to-filter-answers-by-custom-metadata-tags"></a>Používání metadat vám umožní filtrovat odpovědi ve vlastní metadatové značky

Přidání metadat umožňuje filtrovat podle značky těchto metadat odpovědi. Přidat sloupec metadat z **možnosti zobrazení** nabídky. Přidat metadata do znalostní báze po kliknutí na metadata **+** ikona pro přidání páru metadat. Tento pár se skládá z jednoho klíče a jedna hodnota.

![Přidání metadat](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtrování výsledků s strictFilters pro značky metadat

Zvažte otázku uživatele "Když se tento hotelu zavřít?" kde je implicitní záměr pro restaurace "Paradise."

Protože výsledky se vyžaduje jenom pro restaurace "Paradise", můžete nastavit filtr ve volání GenerateAnswer metadat "Restaurace Name", následujícím způsobem.

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [
      {
        "name": "restaurant",
        "value": "paradise"
      }]
}
```

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Použití výsledky otázky a odpovědi k zajištění kontextu konverzace

Odpověď GenerateAnswer obsahuje odpovídající informace metadat sady odpovídající otázek a odpovědí. Tyto informace můžete použít v klientské aplikaci k ukládání kontextu předchozí konverzace pro použití v pozdější konverzace. 

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

## <a name="match-questions-only-by-text"></a>Odpovídají pouze dotazy podle textu

Nástroj QnA Maker se ve výchozím nastavení, prohledá otázek a odpovědí. Pokud chcete prohledávat pouze dotazy, generovat odpověď, použijte `RankerType=QuestionOnly` v textu POST GenerateAnswer požadavku.

Můžete prohledávat publikované kb pomocí `isTest=false`, nebo v kb test pomocí `isTest=true`.

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}
```

## <a name="next-steps"></a>Další postup

Stránka publikovat také obsahuje informace pro generování odpovědi pomocí [Postman](../Quickstarts/get-answer-from-kb-using-postman.md) a [cURL](../Quickstarts/get-answer-from-kb-using-curl.md). 

> [!div class="nextstepaction"]
> [Vytvoření znalostní báze](./create-knowledge-base.md)
