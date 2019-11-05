---
title: 'Kurz: kurz složených entit – LUIS'
titleSuffix: Azure Cognitive Services
description: Přidejte složenou entitu, která bude seskupit extrahovaná data různých typů do jedné obsahující entity. Díky sdružování dat může klientská aplikace snadno extrahovat související data v různých datových typech.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: adb8941fd60a955a44a04717958c5203b721639a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498980"
---
# <a name="tutorial-group-and-extract-related-data"></a>Kurz: seskupení a extrakce souvisejících dat
V tomto kurzu přidejte složenou entitu, která bude seskupit extrahovaná data různých typů do jedné obsahující entity. Díky sdružování dat může klientská aplikace snadno extrahovat související data v různých datových typech.

Účelem složené entity je seskupit související entity do nadřazené entity kategorie. Informace existují jako samostatné entity před vytvořením složeného typu. 

Složená entita je pro tento typ dat vhodná, protože data:

* Jsou vzájemně propojené. 
* Použijte nejrůznější typy entit.
* Musí být seskupeny a zpracovány klientskou aplikací jako jediná informace.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**V tomto kurzu se naučíte:**

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

3. V části **Manage** (Správa) na kartě **Versions** (Verze) naklonujte verzi a pojmenujte ji `composite`. Klonování představuje skvělý způsob, jak si můžete vyzkoušet různé funkce služby LUIS, aniž by to mělo vliv na původní verzi. Název verze je součástí cesty URL, a proto smí obsahovat jenom znaky, které jsou platné v adresách URL.

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

1. Pak hned vyberte poslední entitu `engineering` v utterance. Pod vybranými slovy se vykreslí zelený pruh, který označuje složenou entitu. V místní nabídce zadejte složený název `TransferEmployeeInfo` pak vyberte Enter. 

    ![Snímek obrazovky s zadáním složeného názvu v rozevíracím seznamu](./media/luis-tutorial-composite-entity/hr-create-composite-entity-2.png)

1. V poli **jaký typ entity chcete vytvořit?** všechna požadovaná pole jsou v seznamu: `personName` a `Department`. Vyberte **Done** (Hotovo). Všimněte si, že předem sestavená entita, person, byla do složené entity přidána. Pokud se vám může zobrazit předem sestavená entita mezi počátečním a koncovým tokenem složené entity, musí složené entity obsahovat tyto předem připravené entity. Pokud nejsou zahrnuty předem připravené entity, není složená entita správně předpovězena, ale každý jednotlivý prvek je.

    ![Snímek obrazovky s zadáním složeného názvu v rozevíracím seznamu](./media/luis-tutorial-composite-entity/hr-create-composite-entity-3.png)

## <a name="label-example-utterances-with-composite-entity"></a>Příklad popisku projevy se složenou entitou

1. V každém příkladu utterance vyberte entitu nejvíce vlevo, která by měla být ve složeném. Pak vyberte možnost **zalamovat ve složené entitě**.

1. Vyberte poslední slovo v složené entitě a potom v místní nabídce vyberte **TransferEmployeeInfo** . 

1. Ověřte, že všechny projevy v záměru jsou označeny složenou entitou. 

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Výuka aplikace, aby se mohly testovat změny záměru 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publikujte aplikaci, aby se Queryable z koncového bodu vyškolený model.

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Získání záměru a předpovědi entit z koncového bodu 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Na konec adresy URL zadejte `Move Jill Jones to DevOps`. Poslední parametr řetězce dotazu je `q`a dotaz utterance. 

    Vzhledem k tomu, že se jedná o ověření, že kompozitní je extrahováno správně, může test buď zahrnovat existující vzorový utterance nebo nový utterance. Dobrým testem je zahrnutí všech podřízených entit do složené entity.

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

   Tento utterance vrací pole složených entit. Každé entitě je uveden typ a hodnota. Chcete-li najít větší přesnost pro každou podřízenou entitu, použijte kombinaci typu a hodnoty ze složené položky pole a vyhledejte odpovídající položku v poli entity.  

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Související informace

* [Kurz pro výpis entit](luis-quickstart-intents-only.md)
* Koncepční informace o [složených entitách](luis-concept-entity-types.md)
* [Postup výuky](luis-how-to-train.md)
* [Jak publikovat](luis-how-to-publish-app.md)
* [Testování na portálu LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili složenou entitu pro zapouzdření stávajících entit. Tím umožníte, aby klientská aplikace najít skupinu souvisejících dat v různých datových typech, aby pokračovala v konverzaci. Klientská aplikace pro tuto aplikaci pro lidské prostředky by mohla požádat o den a čas, kdy přesun musí začít a končit. Může se také zeptat na další logistiku přesunutí, jako je třeba fyzický telefon. 

> [!div class="nextstepaction"] 
> [Naučte se, jak přidat jednoduchou entitu se seznamem frází.](luis-quickstart-primary-and-secondary-data.md)  
