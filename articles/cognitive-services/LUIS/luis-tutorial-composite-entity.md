---
title: Složený entity"
titleSuffix: Azure Cognitive Services
description: Přidáte složené entitu seskupit do jedné entity obsahující extrahované data různých typů. Seskupí dat, klientská aplikace snadno extrahovat souvisejících dat v různých datových typů.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 43d72b88c96b45939cb394a26881b1272e250819
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55217250"
---
# <a name="tutorial-group-and-extract-related-data"></a>Kurz: Seskupení a extrakce souvisejících dat
V tomto kurzu přidáte složené entitu seskupit do jedné entity obsahující extrahované data různých typů. Seskupí dat, klientská aplikace snadno extrahovat souvisejících dat v různých datových typů.

Účelem složený entity je nadřazená entita kategorie pro seskupení souvisejících entit. Informace o existuje jako samostatný entity před vytvořením složeného. Je podobné hierarchické entity, ale může obsahovat různé typy entit. 

Složený entita je vhodný pro tento typ dat, protože data:

* Se vztahují k sobě navzájem. 
* Využijte celou řadu typů entit.
* Musí být seskupeny a zpracovány klientskou aplikací jako jediná informace.

**V tomto kurzu se naučíte:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importovat ukázková aplikace
> * Vytvořit záměr
> * Přidání složené entity 
> * Trénování
> * Publikování
> * Zjistit záměry a entity z koncového bodu

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importovat ukázková aplikace

1.  Stáhněte a uložte [souboru JSON aplikace](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/tutorial_list.json) z kurzu seznam entit.

2. Naimportujte soubor JSON do nové aplikace.

3. V části **Manage** (Správa) na kartě **Versions** (Verze) naklonujte verzi a pojmenujte ji `composite`. Klonování představuje skvělý způsob, jak si můžete vyzkoušet různé funkce služby LUIS, aniž by to mělo vliv na původní verzi. Název verze je součástí cesty URL, a proto smí obsahovat jenom znaky, které jsou platné v adresách URL.

## <a name="composite-entity"></a>Složený entity

V této aplikaci název oddělení je definovaný v **oddělení** seznam entit a zahrnuje synonyma. 

**TransferEmployeeToDepartment** má záměr projevů příklad požádat o zaměstnanec přesunout do jiného oddělení. 

Příklad projevy záměru zahrnují:

|Ukázkové promluvy|
|--|
|Přesunout Jan Macek W. z účetního oddělení|
|Jill Jones z přenosu pro výzkum a vývoj|
 
Žádost o přesunutí musí obsahovat název oddělení a jméno zaměstnance. 

## <a name="add-the-personname-prebuilt-entity-to-help-with-common-data-type-extraction"></a>Přidat PersonName předem připravených entit pomoci při extrakci typu common data

Služba LUIS poskytuje několik předem připravených entit pro extrakci běžných dat. 

1. Vyberte **sestavení** v horním navigačním panelu vyberte **entity** z levé navigační nabídce.

1. Vyberte tlačítko **Manage prebuilt entity** (Spravovat předem připravenou entitu).

1. Vyberte **[PersonName](luis-reference-prebuilt-person.md)** ze seznamu předem připravených entit vyberte **provádí**.

    ![Snímek obrazovky s vybranou možností number (číslo) v dialogovém okně s předem připravenými entitami](./media/luis-tutorial-composite-entity/add-personname-prebuilt-entity.png)

    Tato entita vám pomůžou přidat název rozpoznávání do klientské aplikace.

## <a name="create-composite-entity-from-example-utterances"></a>Vytvoření složeného entity z příkladu projevy

1. Vyberte **Intents** (Záměry) v levé navigaci.

1. Vyberte **TransferEmployeeToDepartment** ze seznamu záměry.

1. V první utterance vyberte entitu personName `John Jackson`a pak vyberte **Start obtékání složený entity** v seznamu místní nabídky pro následující utterance:

    `place John Jackson in engineering`

1. Vyberte poslední entita okamžitě `engineering` v utterance. Panel zelené vykreslením v rámci vybrané slova označující složený entity. V rozbalovací nabídce, zadejte název složený `TransferEmployeeInfo` vyberte ENTER. 

1. V **jaký typ entity chcete vytvořit?**, všechna pole, vyžaduje se v seznamu: `personName` a `Department`. Vyberte **Done** (Hotovo). 

    Všimněte si, že předem připravených entit personName, byl přidán do složeného entity. Pokud jste mohli předem připravených entit zobrazí mezi počáteční a koncové tokeny složený entity, složený entity musí obsahovat předem připravených entit. Pokud předem připravených entit nejsou zahrnuty, složený entity není správně předpovědět, ale je každý jednotlivý prvek.

## <a name="label-example-utterances-with-composite-entity"></a>Projevy příklad popisku s složený entity


1. V každé utterance příklad vyberte entitu úplně vlevo, by měla být v složeného. Potom vyberte **zabalit do složeného entity**.

1. Vyberte poslední entita složený potom **TransferEmployeeInfo** v místní nabídce. 

1. Ověřte, že všechny projevy v záměr jsou označeny složený entity. 

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Trénování aplikace, takže můžete otestovat změny k příslušnému záměru 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publikování aplikace, tedy dotazovatelné z koncového bodu trénovaného modelu

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Získání předpovědi záměr a entity z koncového bodu 

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

* [Seznam entit kurz](luis-quickstart-intents-only.md)
* [Entita složený](luis-concept-entity-types.md) koncepční informace
* [Trénování](luis-how-to-train.md)
* [Jak publikovat](luis-how-to-publish-app.md)
* [Testování v portálu služby LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Další postup

V tomto kurzu vytvořili složený entity k zapouzdření existující entity. To umožňuje klientské aplikaci najít skupinu souvisejících dat v různých datových typů, chcete-li pokračovat v konverzaci. Klientská aplikace pro tuto aplikaci lidských zdrojů může požádat, jaký den a čas přechodu musí začínat a končit. Také může požádat o další logistiky přesunout třeba fyzické telefonu. 

> [!div class="nextstepaction"] 
> [Zjistěte, jak přidat jednoduchou entitu se seznamem fráze](luis-quickstart-primary-and-secondary-data.md)  
