---
title: 'Kurz: Regulární výraz entity – LUIS'
titleSuffix: Azure Cognitive Services
description: Extrahování konzistentně formátovaných dat z promluvy s využitím entity regulárního výrazu
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: diberry
ms.openlocfilehash: db60d87b441836f38309166318dd1778ce828483
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307590"
---
# <a name="tutorial-get-well-formatted-data-from-the-utterance"></a>Kurz: Získat dobře formátovaná data z utterance
V tomto kurzu vytvoříte aplikaci, která extrahuje konzistentně formátovaná data z utterance pomocí entity **regulárního výrazu** .

**V tomto kurzu se naučíte:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Vytvoření nové aplikace 
> * Přidat záměr
> * Přidání entity regulárního výrazu 
> * Trénování
> * Publikování
> * Zjistit záměry a entity z koncového bodu

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="regular-expression-entities"></a>Entity regulárních výrazů

Pomocí entity regulárních výrazů této aplikace je možné vyžádat si ve správném formátu utterance čísla lidských zdrojů (HR). Když se záměr promluvy vždy určuje pomocí strojového učení, tento konkrétní typ entity se pomocí strojového učení nezískává. 

**Mezi ukázkové promluvy patří:**

|Ukázkové promluvy|
|--|
|Kde je HRF-123456?|
|Kdo vytvořil HRF-123234?|
|HRF-456098 se zveřejňuje ve francouzštině?|
|HRF-456098|
|Datum HRF-456098?|
 
Regulární výraz je pro tento typ dat vhodný, když:

* jsou data správně naformátovaná


## <a name="create-a-new-app"></a>Vytvoření nové aplikace

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-intent-for-finding-form"></a>Vytvořit záměr pro hledání formuláře

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Vyberte **Create new intent** (Vytvořit nový záměr). 

1. V automaticky otevíraném dialogovém okně zadejte `FindForm` a pak vyberte **Done** (Hotovo). 

    ![Snímek obrazovky s dialogovým oknem Create new intent (Vytvořit nový záměr) s textem Utilities (Nástroje) ve vyhledávacím poli](./media/luis-quickstart-intents-regex-entity/create-new-intent-ddl.png)

1. Přidejte do záměru ukázkové promluvy.

    |Ukázkové promluvy|
    |--|
    |What is the URL for hrf-123456? (Jaká je adresa URL formuláře hrf-123456?)|
    |Where is hrf-345678? (Kde je hrf-345678?)|
    |When was hrf-456098 updated? (Kdy se aktualizoval formulář hrf-456098?)|
    |Did John Smith update hrf-234639 last week? (Aktualizoval John Smith minulý týden hrf-234639?)|
    |Kolik verzí HRF-345123?|
    |Who needs to authorize form hrf-123456? (Kdo musí autorizovat hrf-123456?)|
    |How many people need to sign off on hrf-345678? (Kolik lidí musí podepsat hrf-345678?)|
    |hrf-234123 date? (datum hrf-234123?)|
    |author of hrf-546234? (autor hrf-546234?)|
    |title of hrf-456234? (název hrf-456234?)|

    [![Snímek stránky záměru s novým projevy zvýrazněnou](./media/luis-quickstart-intents-regex-entity/findform-intent.png)](./media/luis-quickstart-intents-regex-entity/findform-intent.png#lightbox)

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="use-the-regular-expression-entity-for-well-formatted-data"></a>Použití entity regulárního výrazu pro dobře formátovaná data
Entita regulárního výrazu, který hledá shodu s číslem formuláře, je `hrf-[0-9]{6}`. Tento regulární výraz hledá shodu se znaky literálů `hrf-`, ale ignoruje velikost a kulturní varianty. Hledá shodu s číslicemi 0–9 o délce přesně 6 číslic.

HRF je zkratka pro `human resources form`.

Služba LUIS promluvu při přidání do záměru tokenizuje. Tokenizací těchto promluv se přidají mezery před a za spojovník: `Where is HRF - 123456?`. Regulární výraz se použije na promluvu v nezpracované podobě ještě před tím, než se tokenizuje. Vzhledem k tomu, že se regulární výraz používá na _nezpracovanou_ podobu, nemusí se starat o hranice slov. 

Podle následujících kroků vytvořte entitu regulárního výrazu, která službě LUIS poskytne informace o formátu HRF-number:

1. Na levém panelu vyberte **Entities** (Entity).

1. Na stránce Entities (Entity) vyberte tlačítko **Create new entity** (Vytvořit novou entitu). 

1. V automaticky otevíraném okně zadejte název nové entity `HRF-number`, jako typ entity vyberte **RegEx** (Regulární výraz), jako hodnotu `hrf-[0-9]{6}`RegEx**zadejte** a pak vyberte **Done** (Hotovo).

    ![Snímek obrazovky s automaticky otevíraným dialogovým oknem s nastavením vlastností nové entity](./media/luis-quickstart-intents-regex-entity/create-regex-entity.png)

1. V levé nabídce vyberte **Intents** (Záměry) a pak záměr **FindForm** (Vyhledat formulář), aby se v promluvách zobrazil označený regulární výraz. 

    [![Snímek obrazovky s promluvami s označenou stávající entitou a vzorem regulárního výrazu](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png)](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png#lightbox)

    Vzhledem k tomu, že entita není entitou získanou počítačem, entita se použije na projevy a zobrazí se na webu LUIS, jakmile se vytvoří.

## <a name="add-example-utterances-to-the-none-intent"></a>Přidat příklad projevy k záměru None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-before-testing-or-publishing"></a>Výuka aplikace před testováním nebo publikováním

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Publikování aplikace pro dotaz z koncového bodu

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Získání záměru a předpovědi entit z koncového bodu

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Přejděte na konec adresy URL v adrese a zadejte následující utterance:

    `When were HRF-123456 and hrf-234567 published in the last year?`

    Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. Tato promluva není stejná jako žádná z označených promluv, proto je to dobrý test a měl by se vrátit záměr `FindForm` se dvěma čísly formuláře `HRF-123456` a `hrf-234567`.

    ```json
    {
      "query": "When were HRF-123456 and hrf-234567 published in the last year?",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9988884
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.9988884
        },
        {
          "intent": "None",
          "score": 0.00204812363
        }
      ],
      "entities": [
        {
          "entity": "hrf-123456",
          "type": "HRF-number",
          "startIndex": 10,
          "endIndex": 19
        },
        {
          "entity": "hrf-234567",
          "type": "HRF-number",
          "startIndex": 25,
          "endIndex": 34
        }
      ]
    }
    ```

    Pomocí entity regulárního výrazu služba LUIS extrahuje pojmenovaná data, což usnadňuje programové použití v klientské aplikaci, která přijímá odpověď JSON.


## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Související informace

* Koncepty entit [regulárního výrazu](luis-concept-entity-types.md#regular-expression-entity)
* [Postup výuky](luis-how-to-train.md)
* [Jak publikovat](luis-how-to-publish-app.md)
* [Testování na portálu LUIS](luis-interactive-test.md)

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste vytvořili nový záměr, přidali příklady promluv a pak vytvořili entitu regulárního výrazu k extrahování správně formátovaných data z promluv. Po natrénování a publikování aplikace jste dotazem adresovaným koncovému bodu zjistili záměr a vrátili extrahovaná data.

> [!div class="nextstepaction"]
> [Informace o entitě seznamu](luis-quickstart-intent-and-list-entity.md)

