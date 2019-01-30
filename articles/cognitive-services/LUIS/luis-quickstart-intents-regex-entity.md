---
title: Regulární výraz entity
titleSuffix: Azure Cognitive Services
description: Extrahování konzistentně formátovaných dat z promluvy s využitím entity regulárního výrazu
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 7ce8ba5d35d4c8f8def7b87e96f102c45d39a5d7
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228079"
---
# <a name="tutorial-get-well-formatted-data-from-the-utterance"></a>Kurz: Získejte správně formátovaná data z utterance
V tomto kurzu, vytvořte aplikaci extrahovat data konzistentním formátováním z utterance pomocí **regulárního výrazu** entity.

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

## <a name="regular-expression-entities"></a>Regulární výraz entity

Použití této aplikace entity regulárního výrazu je vyžádá si správně naformátovaný čísla formuláře lidských zdrojů (HR) ze utterance. Když se záměr promluvy vždy určuje pomocí strojového učení, tento konkrétní typ entity se pomocí strojového učení nezískává. 

**Mezi ukázkové promluvy patří:**

|Ukázkové promluvy|
|--|
|Kde je HRF 123456?|
|Autora HRF 123234?|
|HRF 456098 je publikována ve francouzštině?|
|HRF-456098|
|Datum HRF 456098?|
 
Regulární výraz je pro tento typ dat vhodný, když:

* jsou data správně naformátovaná


## <a name="create-a-new-app"></a>Vytvoření nové aplikace

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-intent-for-finding-form"></a>Vytvořit záměr pro vyhledání formuláře

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
    |Kolik verzí hrf 345123 existují?|
    |Who needs to authorize form hrf-123456? (Kdo musí autorizovat hrf-123456?)|
    |How many people need to sign off on hrf-345678? (Kolik lidí musí podepsat hrf-345678?)|
    |hrf-234123 date? (datum hrf-234123?)|
    |author of hrf-546234? (autor hrf-546234?)|
    |title of hrf-456234? (název hrf-456234?)|

    [ ![Snímek obrazovky se stránkou záměru a zvýrazněnými novými promluvami](./media/luis-quickstart-intents-regex-entity/findform-intent.png) ](./media/luis-quickstart-intents-regex-entity/findform-intent.png#lightbox)

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="use-the-regular-expression-entity-for-well-formatted-data"></a>Použít regulární výraz entity pro správně formátovaná data
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

    Protože entity není entita se zjištěné počítače, je entita u projevy a zobrazí na webu služby LUIS ihned po jeho vytvoření.

## <a name="add-example-utterances-to-the-none-intent"></a>Přidání projevů příklad na hodnotu None záměru 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-before-testing-or-publishing"></a>Trénování před testování a publikování aplikace

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Publikujte aplikaci do dotaz z koncového bodu

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Získání předpovědi záměr a entity z koncového bodu

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Na konec adresy URL zadejte `When were HRF-123456 and hrf-234567 published in the last year?`. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. Tato promluva není stejná jako žádná z označených promluv, proto je to dobrý test a měl by se vrátit záměr `FindForm` se dvěma čísly formuláře `HRF-123456` a `hrf-234567`.

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

* [Regulární výraz](luis-concept-entity-types.md#regular-expression-entity) koncepty entity
* [Trénování](luis-how-to-train.md)
* [Jak publikovat](luis-how-to-publish-app.md)
* [Testování v portálu služby LUIS](luis-interactive-test.md)

## <a name="next-steps"></a>Další postup
V tomto kurzu jste vytvořili nový záměr, přidali příklady promluv a pak vytvořili entitu regulárního výrazu k extrahování správně formátovaných data z promluv. Po natrénování a publikování aplikace jste dotazem adresovaným koncovému bodu zjistili záměr a vrátili extrahovaná data.

> [!div class="nextstepaction"]
> [Informace o entitě seznamu](luis-quickstart-intent-and-list-entity.md)

