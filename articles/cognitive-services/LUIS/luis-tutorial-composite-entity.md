---
title: Kurz složených entit – LUIS
titleSuffix: Azure Cognitive Services
description: Přidáte složené entitu seskupit do jedné entity obsahující extrahované data různých typů. Seskupí dat, klientská aplikace snadno extrahovat souvisejících dat v různých datových typů.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/19/2019
ms.author: diberry
ms.openlocfilehash: 6d54b46ff8b0c8a987653b4fc0344377c0115b58
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560090"
---
# <a name="tutorial-group-and-extract-related-data"></a>Kurz: Seskupení a extrakce souvisejících dat
V tomto kurzu přidáte složené entitu seskupit do jedné entity obsahující extrahované data různých typů. Seskupí dat, klientská aplikace snadno extrahovat souvisejících dat v různých datových typů.

Účelem složený entity je nadřazená entita kategorie pro seskupení souvisejících entit. Informace o existuje jako samostatný entity před vytvořením složeného. 

Složený entita je vhodný pro tento typ dat, protože data:

* Se vztahují k sobě navzájem. 
* Využijte celou řadu typů entit.
* Musí být seskupeny a zpracovány klientskou aplikací jako jediná informace.

**V tomto kurzu se naučíte:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importovat ukázkovou aplikaci
> * Vytvořit záměr
> * Přidání složené entity 
> * Trénování
> * Publikování
> * Zjistit záměry a entity z koncového bodu

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importovat ukázkovou aplikaci

1.  Stáhněte a uložte [soubor JSON aplikace](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/tutorial_list.json) z kurzu seznam entit.

2. Naimportujte soubor JSON do nové aplikace.

3. V části **Manage** (Správa) na kartě **Versions** (Verze) naklonujte verzi a pojmenujte ji `composite`. Klonování představuje skvělý způsob, jak si můžete vyzkoušet různé funkce služby LUIS, aniž by to mělo vliv na původní verzi. Název verze je součástí cesty URL, a proto smí obsahovat jenom znaky, které jsou platné v adresách URL.

## <a name="composite-entity"></a>Složený entity

V této aplikaci je název oddělení definovaný v entitě seznam **oddělení** a obsahuje synonyma. 

Záměr **TransferEmployeeToDepartment** má jako příklad projevy, jak požádat o zaměstnance, aby se přesunul do nového oddělení. 

Příklad projevy pro tento záměr zahrnuje:

|Ukázkové promluvy|
|--|
|přesunout Jan W. Novák do účetního oddělení|
|přenos Jill Novotný z na R & D|
 
Požadavek Move by měl obsahovat název oddělení a jméno zaměstnance. 

## <a name="add-the-personname-prebuilt-entity-to-help-with-common-data-type-extraction"></a>Přidejte předem vytvořenou entitu Person, která bude pomáhat s extrakcí běžných datových typů.

Služba LUIS poskytuje několik předem připravených entit pro extrakci běžných dat. 

1. V  horním navigačním panelu vyberte sestavovat a pak v levé navigační nabídce vyberte **entity** .

1. Vyberte tlačítko **Manage prebuilt entity** (Spravovat předem připravenou entitu).

1. V seznamu předem vytvořených entit vyberte **[Person](luis-reference-prebuilt-person.md)** a potom vyberte Hotovo .

    ![Snímek obrazovky s vybranou možností number (číslo) v dialogovém okně s předem připravenými entitami](./media/luis-tutorial-composite-entity/add-personname-prebuilt-entity.png)

    Tato entita vám pomůže přidat rozpoznávání názvů ke klientské aplikaci.

## <a name="create-composite-entity-from-example-utterances"></a>Vytvoření složené entity z příkladu projevy

1. Vyberte **Intents** (Záměry) v levé navigaci.

1. V seznamu záměry vyberte **TransferEmployeeToDepartment** .

1. V utterance `place John Jackson in engineering`vyberte entitu Person a `John Jackson`potom v rozevíracím seznamu místní nabídky pro následující utterance vyberte **zalamovat v složené entitě** . 

    ![Snímek obrazovky s výběrem složeného balení v dialogovém okně rozevíracího seznamu](./media/luis-tutorial-composite-entity/hr-create-composite-entity-1.png)

1. Vyberte poslední entita okamžitě `engineering` v utterance. Panel zelené vykreslením v rámci vybrané slova označující složený entity. V rozbalovací nabídce, zadejte název složený `TransferEmployeeInfo` vyberte ENTER. 

    ![Snímek obrazovky s zadáním složeného názvu v rozevíracím seznamu](./media/luis-tutorial-composite-entity/hr-create-composite-entity-2.png)

1. V poli **jaký typ entity chcete vytvořit?** se v seznamu zobrazí všechna požadovaná pole: `personName` a. `Department` Vyberte **Done** (Hotovo). Všimněte si, že předem sestavená entita, person, byla do složené entity přidána. Pokud jste mohli předem připravených entit zobrazí mezi počáteční a koncové tokeny složený entity, složený entity musí obsahovat předem připravených entit. Pokud předem připravených entit nejsou zahrnuty, složený entity není správně předpovědět, ale je každý jednotlivý prvek.

    ![Snímek obrazovky s zadáním složeného názvu v rozevíracím seznamu](./media/luis-tutorial-composite-entity/hr-create-composite-entity-3.png)

## <a name="label-example-utterances-with-composite-entity"></a>Projevy příklad popisku s složený entity

1. V každé utterance příklad vyberte entitu úplně vlevo, by měla být v složeného. Potom vyberte **zabalit do složeného entity**.

1. Vyberte poslední slovo v složené entitě a potom v místní nabídce vyberte **TransferEmployeeInfo** . 

1. Ověřte, že všechny projevy v záměr jsou označeny složený entity. 

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Výuka aplikace, aby se mohly testovat změny záměru 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publikujte aplikaci, aby se Queryable z koncového bodu vyškolený model.

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Získání záměru a předpovědi entit z koncového bodu 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Na konec adresy URL zadejte `Move Jill Jones to DevOps`. Poslední parametr querystring `q`, dotaz utterance. 

    Ověřte, zda že je správně extrahován složeného je tento test, test může obsahovat buď existující utterance ukázka nebo nové utterance. Dobrá se zahrnou všechny podřízené entity složený entity.

    ```json
    {
      "query": "Move Jill Jones to DevOps",
      "topScoringIntent": {
        "intent": "TransferEmployeeToDepartment",
        "score": 0.9882747
      },
      "intents": [
        {
          "intent": "TransferEmployeeToDepartment",
          "score": 0.9882747
        },
        {
          "intent": "None",
          "score": 0.00925369747
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "builtin.personName",
          "startIndex": 5,
          "endIndex": 14
        },
        {
          "entity": "devops",
          "type": "Department",
          "startIndex": 19,
          "endIndex": 24,
          "resolution": {
            "values": [
              "Development Operations"
            ]
          }
        },
        {
          "entity": "jill jones to devops",
          "type": "TransferEmployeeInfo",
          "startIndex": 5,
          "endIndex": 24,
          "score": 0.9607566
        }
      ],
      "compositeEntities": [
        {
          "parentType": "TransferEmployeeInfo",
          "value": "jill jones to devops",
          "children": [
            {
              "type": "builtin.personName",
              "value": "jill jones"
            },
            {
              "type": "Department",
              "value": "devops"
            }
          ]
        }
      ]
    }
    ```

   Tato utterance vrátí pole složený entity. Každá entita je uveden typ a hodnotu. Najít další přesnosti pro každé podřízené entity, použijte kombinaci typu a hodnoty z položky složený pole Najít odpovídající položku v poli entity.  

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Související informace

* [Kurz pro výpis entit](luis-quickstart-intents-only.md)
* Koncepční informace o [složených entitách](luis-concept-entity-types.md)
* [Postup výuky](luis-how-to-train.md)
* [Jak publikovat](luis-how-to-publish-app.md)
* [Testování na portálu LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Další postup

V tomto kurzu vytvořili složený entity k zapouzdření existující entity. To umožňuje klientské aplikaci najít skupinu souvisejících dat v různých datových typů, chcete-li pokračovat v konverzaci. Klientská aplikace pro tuto aplikaci lidských zdrojů může požádat, jaký den a čas přechodu musí začínat a končit. Může se také zeptat na další logistiku přesunutí, jako je třeba fyzický telefon. 

> [!div class="nextstepaction"] 
> [Zjistěte, jak přidat jednoduchou entitu se seznamem fráze](luis-quickstart-primary-and-secondary-data.md)  
