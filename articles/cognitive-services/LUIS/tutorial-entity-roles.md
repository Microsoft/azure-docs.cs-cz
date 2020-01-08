---
title: 'Kurz: kontextová data s rolemi – LUIS'
titleSuffix: Azure Cognitive Services
description: Najde související data na základě kontextu. Spolu například souvisí počáteční a cílová umístění pro fyzický přesun z jedné budovy a kanceláře do jiné.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: cd646ef061a0be06a9b1a56b72a4f35d9796aa63
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75447887"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>Kurz: extrakce kontextově souvisejících dat z utterance

V tomto kurzu vyhledáte související části dat na základě kontextu. Například zdroj a cílové umístění pro přenos z jednoho města do druhého. Je možné, že obě části dat jsou nutné a jsou vzájemně propojené.

Roli lze použít pro libovolný předem sestavený nebo vlastní typ entity a používá se v obou příkladech projevy a Patterns.

**V tomto kurzu se naučíte:**

> [!div class="checklist"]
> * Vytvoření nové aplikace
> * Přidat záměr
> * Získání informací o původu a cíli pomocí rolí
> * Trénování
> * Publikování
> * Získat záměry a role entit z koncového bodu

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="related-data"></a>Související data

Tato aplikace určuje, kam má být zaměstnanec přesunut od města původu do cílového města. Používá předem vytvořenou entitu GeographyV2 k identifikaci názvů měst a používá role k určení typů umístění (zdroj a cíl) v rámci utterance.

Role by se měla použít při extrakci dat entity:

* Se vzájemně souvisí v kontextu utterance.
* Označuje jednotlivé role pomocí konkrétního výběru slova. Mezi příklady těchto slov patří from/to (z/do), leaving/headed to (odstěhovat/nastěhovat), away from/toward (z kanceláře/do kanceláře).
* Obě role jsou často ve stejném utterance, což umožňuje LUIS získat informace z tohoto častého kontextového použití.
* Musí být seskupeny a zpracovány klientskou aplikací jako jediná informace.

## <a name="create-a-new-app"></a>Vytvoření nové aplikace

1. Přihlaste se k portálu LUIS verze Preview pomocí adresy URL [https://preview.luis.ai](https://preview.luis.ai).

1. Vyberte **vytvořit novou aplikaci**, zadejte název `HumanResources` a zachovejte výchozí jazykovou verzi **Angličtina**. Popis nechejte prázdný.

1. Vyberte **Done** (Hotovo).

## <a name="create-an-intent-to-move-employees-between-cities"></a>Vytvoření záměru pro přesun zaměstnanců mezi městy

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Vyberte **Create new intent** (Vytvořit nový záměr).

1. V automaticky otevíraném dialogovém okně zadejte `MoveEmployeeToCity` a pak vyberte **Done** (Hotovo).

    > [!div class="mx-imgBorder"]
    > ![snímek obrazovky s dialogovým oknem vytvořit nový záměr s](./media/tutorial-entity-roles/create-new-intent-move-employee-to-city.png)

1. Přidejte do záměru ukázkové promluvy.

    |Ukázkové promluvy|
    |--|
    |Přesunutí Jan W. Smith opouští Seattle s Orlandu|
    |přenos Jill Novotný z Seattlu do Cairo|
    |Umístění Jan Jacksonůvch z Tampaů, přicházejících do Brna |
    |přesunout Debra Doughtery na Tulsa z Chicago|
    |MV Jill, který opouští Cairo na Tampa|
    |Posunutí Alice Anderson na Oakland z Redmond|
    |Carl Chamerlin z San Francisco do Redmond|
    |Přenos Steve Standish z San Diegu směrem k Bellevue |
    |přezvednutí Tanner Václav z Kansas City a Shift na Chicago|

    > [!div class="mx-imgBorder"]
    > ![snímek obrazovky LUIS s novým projevy v MoveEmployee záměru](./media/tutorial-entity-roles/hr-enter-utterances.png)

## <a name="add-prebuilt-entity-geographyv2"></a>Přidat předem sestavenou entitu geographyV2

Předem sestavená entita, geographyV2, extrahuje informace o poloze, včetně názvů měst. Vzhledem k tomu, že projevy mají dva názvy měst, které jsou v kontextu vzájemně související, použijte role k extrakci daného kontextu.

1. Vyberte **entity** z navigace na levé straně.

1. Vyberte **Přidat předem vytvořenou entitu**a pak na panelu hledání vyberte `geo` pro filtrování předem sestavených entit.

    > [!div class="mx-imgBorder"]
    > ![přidat předem vytvořenou entitu geographyV2 do aplikace](media/tutorial-entity-roles/add-geographyV2-prebuilt-entity.png)

1. Zaškrtněte políčko a vyberte **Hotovo**.
1. V seznamu **entity** vyberte **geographyV2** a otevřete novou entitu.
1. Přidejte dvě role, `Origin`a `Destination`.

    > [!div class="mx-imgBorder"]
    > ![přidat role do předem připravené](media/tutorial-entity-roles/add-roles-to-prebuilt-entity.png) entit

1. Vyberte **záměry** z navigace na levé straně a pak vyberte záměr **MoveEmployeeToCity** . Všimněte si, že názvy měst jsou označené předem vytvořenou entitou **geographyV2**.
1. Na panelu nástrojů kontextu vyberte **paleta entit**.

    > [!div class="mx-imgBorder"]
    > ![výběr palety entit z panelu nástrojů obsahu](media/tutorial-entity-roles/intent-detail-context-toolbar-select-entity-palette.png)

1. Vyberte předem vytvořenou entitu **geographyV2**a pak vyberte **inspektor entit**.
1. V okně **inspektor entit**vyberte jednu roli, **cíl**. Tím se změní ukazatel myši. Pomocí kurzoru označte text ve všech projevy, který je cílovým umístěním.

    > [!div class="mx-imgBorder"]
    > ![vybrat roli v paletě entit](media/tutorial-entity-roles/entity-palette-select-entity-role.png)


1. Vraťte se do **inspektoru entit**a změňte roli na **počátek**. Pomocí kurzoru označte text ve všech projevy, který představuje původní umístění.

## <a name="add-example-utterances-to-the-none-intent"></a>Přidat příklad projevy k záměru None

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Výuka aplikace, aby se mohly testovat změny záměru

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publikujte aplikaci, aby se Queryable z koncového bodu vyškolený model.

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Získání záměru a předpovědi entit z koncového bodu

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


1. Přejděte na konec adresy URL v panelu adresy a zadejte `Please move Carl Chamerlin from Tampa to Portland`. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. Tento utterance se neshoduje s žádným z označených projevy, takže je dobrým testem a měl by vracet `MoveEmployee` záměrem v extrahované entitě.

    ```json
    {
      "query": "Please move Carl Chamerlin from Tampa to Portland",
      "topScoringIntent": {
        "intent": "MoveEmployeeToCity",
        "score": 0.9706451
      },
      "intents": [
        {
          "intent": "MoveEmployeeToCity",
          "score": 0.9706451
        },
        {
          "intent": "None",
          "score": 0.0307451729
        }
      ],
      "entities": [
        {
          "entity": "tampa",
          "type": "builtin.geographyV2.city",
          "startIndex": 32,
          "endIndex": 36,
          "role": "Origin"
        },
        {
          "entity": "portland",
          "type": "builtin.geographyV2.city",
          "startIndex": 41,
          "endIndex": 48,
          "role": "Destination"
        }
      ]
    }
    ```

    Je předpokládaná správná záměr a pole entity obsahuje zdrojové i cílové role ve vlastnosti odpovídajících **entit** .

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Související informace

* [Koncepty entit](luis-concept-entity-types.md)
* [Koncepty rolí](luis-concept-roles.md)
* [Seznam předem připravených entit](luis-reference-prebuilt-entities.md)
* [Postup výuky](luis-how-to-train.md)
* [Jak publikovat](luis-how-to-publish-app.md)
* [Testování na portálu LUIS](luis-interactive-test.md)
* [Role](luis-concept-roles.md)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili nový záměr a Přidali jste příklad projevy pro kontextové naučená data o původu a cílovém umístění. Jakmile aplikaci vytrénujete a publikujete, klientská aplikace může tyto informace použít k vytvoření lístku přesunu s relevantními informacemi.

> [!div class="nextstepaction"]
> [Další informace o přidání složené entity](luis-tutorial-composite-entity.md)
