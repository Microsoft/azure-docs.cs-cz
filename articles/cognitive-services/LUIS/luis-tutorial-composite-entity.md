---
title: 'Kurz: kurz složených entit – LUIS'
titleSuffix: Azure Cognitive Services
description: Přidáte složené entitu seskupit do jedné entity obsahující extrahované data různých typů. Seskupí dat, klientská aplikace snadno extrahovat souvisejících dat v různých datových typů.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: diberry
ms.openlocfilehash: 0e72563f366330f841d1a61ed67956b6314c769a
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893177"
---
# <a name="tutorial-group-and-extract-related-data"></a>Kurz: seskupení a extrakce souvisejících dat
V tomto kurzu přidáte složené entitu seskupit do jedné entity obsahující extrahované data různých typů. Seskupí dat, klientská aplikace snadno extrahovat souvisejících dat v různých datových typů.

Účelem složený entity je nadřazená entita kategorie pro seskupení souvisejících entit. Informace o existuje jako samostatný entity před vytvořením složeného.

Složený entita je vhodný pro tento typ dat, protože data:

* Se vztahují k sobě navzájem.
* Využijte celou řadu typů entit.
* Musí být seskupeny a zpracovány klientskou aplikací jako jediná informace.

[!INCLUDE [Only valid with current portal](includes/old-portal-only.md)]

**Co se v tomto kurzu naučíte:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importovat ukázkovou aplikaci
> * Vytvořit záměr
> * Přidání složené entity
> * Trénování
> * Publikování
> * Získat záměry a entity z koncového bodu

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importovat ukázkovou aplikaci

1.  Stáhněte a uložte [soubor JSON aplikace](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/tutorial_list.json) z kurzu seznam entit.

2. Naimportujte soubor JSON do nové aplikace.

3. V části **Manage** (Správa) na kartě **Versions** (Verze) naklonujte verzi a pojmenujte ji `composite`. Klonování představuje skvělý způsob, jak si můžete vyzkoušet různé funkce služby LUIS, aniž by to mělo vliv na původní verzi. Vzhledem k tomu, že název verze je součástí cesty URL, smí obsahovat jen znaky platné v adresách URL.

## <a name="composite-entity"></a>Složená entita

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

1. V horním navigačním panelu vyberte **sestavovat** a pak v levé navigační nabídce vyberte **entity** .

1. Vyberte tlačítko **Manage prebuilt entity** (Spravovat předem připravenou entitu).

1. V seznamu předem vytvořených entit vyberte **[Person](luis-reference-prebuilt-person.md)** a potom vyberte **Hotovo**.

    ![Snímek obrazovky s vybranou možností number (číslo) v dialogovém okně s předem připravenými entitami](./media/luis-tutorial-composite-entity/add-personname-prebuilt-entity.png)

    Tato entita vám pomůže přidat rozpoznávání názvů ke klientské aplikaci.

## <a name="create-composite-entity-from-example-utterances"></a>Vytvoření složené entity z příkladu projevy

1. Vyberte **Intents** (Záměry) v levé navigaci.

1. V seznamu záměry vyberte **TransferEmployeeToDepartment** .

1. V `place John Jackson in engineering`utterance vyberte entitu Person, `John Jackson`a pak v místní nabídce vyberte v rozevíracím seznamu možnost **zalamovat ze složené entity** pro následující utterance.

    ![Snímek obrazovky s výběrem složeného balení v dialogovém okně rozevíracího seznamu](./media/luis-tutorial-composite-entity/hr-create-composite-entity-1.png)

1. Vyberte poslední entita okamžitě `engineering` v utterance. Panel zelené vykreslením v rámci vybrané slova označující složený entity. V rozbalovací nabídce, zadejte název složený `TransferEmployeeInfo` vyberte ENTER.

    ![Snímek obrazovky s zadáním složeného názvu v rozevíracím seznamu](./media/luis-tutorial-composite-entity/hr-create-composite-entity-2.png)

1. V poli **jaký typ entity chcete vytvořit?** všechna požadovaná pole jsou v seznamu: `personName` a `Department`. Vyberte **Done** (Hotovo). Všimněte si, že předem sestavená entita, person, byla do složené entity přidána. Pokud jste mohli předem připravených entit zobrazí mezi počáteční a koncové tokeny složený entity, složený entity musí obsahovat předem připravených entit. Pokud předem připravených entit nejsou zahrnuty, složený entity není správně předpovědět, ale je každý jednotlivý prvek.

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


## <a name="next-steps"></a>Další kroky

V tomto kurzu vytvořili složený entity k zapouzdření existující entity. To umožňuje klientské aplikaci najít skupinu souvisejících dat v různých datových typů, chcete-li pokračovat v konverzaci. Klientská aplikace pro tuto aplikaci lidských zdrojů může požádat, jaký den a čas přechodu musí začínat a končit. Může se také zeptat na další logistiku přesunutí, jako je třeba fyzický telefon.

> [!div class="nextstepaction"]
> [Opravte nejistotu předpovědi kontrolou Endpoint projevy](luis-tutorial-review-endpoint-utterances.md)
