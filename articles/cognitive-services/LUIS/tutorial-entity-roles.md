---
title: 'Kurz: Kontextová data s rolemi – LUIS'
description: Vyhledejte související data na základě kontextu. Spolu například souvisí počáteční a cílová umístění pro fyzický přesun z jedné budovy a kanceláře do jiné.
ms.topic: tutorial
ms.date: 03/30/2020
ms.openlocfilehash: fdb463896e531619ea7ebe7c384729763dc84138
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475818"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>Kurz: Extrahujte kontextově související data z utterance

V tomto kurzu vyhledáte související části dat na základě kontextu. Například výchozí a cílová místa pro převod z jednoho města do druhého. Mohou být vyžadovány oba údaje, které jsou vzájemně propojeny.

Roli lze použít s libovolným předem sestaveným nebo vlastním typem entity a použita v ukázkových projevech i vzorech.

**V tomto kurzu se dozvíte, jak:**

> [!div class="checklist"]
> * Vytvoření nové aplikace
> * Přidat záměr
> * Získání informací o původu a cíli pomocí rolí
> * Trénování
> * Publikování
> * Získání záměrů a rolí entit z koncového bodu

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="related-data"></a>Související údaje

Tato aplikace určuje, kam má být zaměstnanec přesunut z původního města do cílového města. Používá GeographyV2 předem sestavené entity k identifikaci názvy měst a používá role k určení typů umístění (původ a cíl) v rámci utterance.

Role by měla být použita při data entity extrahovat:

* Souvisí s sebou v kontextu utterance.
* Používá konkrétní volbu slova k označení každé role. Mezi příklady těchto slov patří from/to (z/do), leaving/headed to (odstěhovat/nastěhovat), away from/toward (z kanceláře/do kanceláře).
* Obě role jsou často ve stejném utterance, což umožňuje LUIS učit se z tohoto častého kontextového použití.
* Musí být seskupeny a zpracovány klientskou aplikací jako jediná informace.

## <a name="create-a-new-app"></a>Vytvoření nové aplikace

1. Přihlaste se k [portálu **náhledu** služby LUIS](https://preview.luis.ai).

1. Vyberte + Nová aplikace pro `HumanResources` **konverzaci**, zadejte název a zachovat výchozí jazykovou verzi, **angličtina**. Ponechte prostředek popisu a předpovědi prázdný. Vyberte **Done** (Hotovo).

## <a name="create-an-intent-to-move-employees-between-cities"></a>Vytvoření záměru přesunout zaměstnance mezi městy

Záměr se používá ke klasifikaci projevy uživatele na základě záměru uživatele, určené z textu přirozeného jazyka.

Chcete-li klasifikovat utterance, záměr potřebuje příklady projevy uživatele, které by měly být klasifikovány s tímto záměrem.

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Vyberte **+ Vytvořit**.

1. V automaticky otevíraném dialogovém okně zadejte `MoveEmployeeToCity` a pak vyberte **Done** (Hotovo).

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s dialogovým oknem pro vytvoření nového záměru](./media/tutorial-entity-roles/create-new-intent-move-employee-to-city.png)

1. Přidejte několik příklad projevy k tomuto záměru, který očekáváte, že uživatel požádat.

    |Ukázkové promluvy|
    |--|
    |přesunout John W. Smith odchodu Seattle zamířil do Orlanda|
    |převod Jill Jones ze Seattlu do Káhiry|
    |Místo John Jackson od Tampy, přichází do Atlanty |
    |přesunout Debra Doughtery do Tulsy z Chicaga|
    |mv Jill Jones opouští Káhira zamířil do Tampy|
    |Shift Alice Anderson do Oaklandu z Redmondu|
    |Carl Chamerlin ze San Francisca do Redmondu|
    |Transfer Steve Standish ze San Diega směrem Bellevue |
    |výtah Tanner Thompson z Kansas city a přesunout do Chicaga|

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky aplikace LUIS s novými promluvami v záměru MoveEmployee (Přesunutí zaměstnance)](./media/tutorial-entity-roles/hr-enter-utterances.png)

## <a name="add-prebuilt-entity-geographyv2"></a>Přidání předem sestavené entity geographyV2

Předem vytvořená **entita, geographyV2**, extrahuje informace o poloze, včetně názvů měst. Vzhledem k tomu, že projevy mají dva názvy měst, vztahující se k sobě navzájem v kontextu, použijte role k extrahování tohoto kontextu.

1. Z navigace na levé straně vyberte **entity.**

1. Vyberte + Přidat předem `geo` **vytvořenou entitu**, pak zadejte do vyhledávacího řádku a filtrujte předem vytvořené entity.

    > [!div class="mx-imgBorder"]
    > ![Přidání předem sestavené entity geographyV2 do aplikace](media/tutorial-entity-roles/add-geographyV2-prebuilt-entity.png)

1. Zaškrtněte políčko a vyberte **Hotovo**.

## <a name="add-roles-to-prebuilt-entity"></a>Přidání rolí do předem sestavené entity

1. V seznamu **Entity** vyberte **geographyV2** otevřít novou entitu.
1. Chcete-li přidat **+** roli, vyberte a `Origin`přidejte následující dvě role: a `Destination`.

    > [!div class="mx-imgBorder"]
    > ![Přidání rolí do předem sestavené entity](media/tutorial-entity-roles/add-roles-to-prebuilt-entity.png)

## <a name="label-entity-roles-in-example-utterances"></a>Role entity popisků v ukázkových projevech

1. Z navigace na levé straně vyberte **Záměry** **záměru MoveEmployeeToCity.** Všimněte si, že názvy měst jsou označeny předem sestavenou **entitou geographyV2**.
1. V pruhu nástrojů kontextu vyberte **paletu entit** s _ikonou tužky_.

    > [!div class="mx-imgBorder"]
    > ![Vybrat paletu entit z panelu nástrojů Obsah](media/tutorial-entity-roles/intent-detail-context-toolbar-select-entity-palette.png)

1. Vyberte předem vytvořenou entitu **geographyV2**a vyberte **inspektor entity**.
1. V **inspektoru Entity**vyberte jednu roli, **cíl**. Tím se změní kurzor myši. Pomocí kurzoru označte text ve všech projevech, které jsou cílovým umístěním.

    > [!div class="mx-imgBorder"]
    > ![Vybrat roli v paletě entit](media/tutorial-entity-roles/entity-palette-select-entity-role.png)


1. Vraťte se do **inspektoru Entity**, změňte roli na **Origin**. Pomocí kurzoru označte text ve všech projevech, které jsou výchozím umístěním.

## <a name="add-example-utterances-to-the-none-intent"></a>Přidání ukázkových promluv do záměru None

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Trénování aplikace tak, aby změny záměru mohly být testovány

Chcete-li aplikaci trénovat, vyberte **možnost Vlak**. Školení platí změny, jako jsou nové entity a popisované projevy, aktivní model.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Publikování aplikace pro přístup z koncového bodu HTTP

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]


## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Získání záměru a predikce entit z koncového bodu

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]


1. Přejděte na konec adresy URL v _YOUR_QUERY_HERE_ adresním `Please move Carl Chamerlin from Tampa to Portland`řádku a nahraďte YOUR_QUERY_HERE .

Tento utterance není stejný jako kterýkoli z popisek projevy, takže je `MoveEmployee` dobrý test a by měl vrátit záměr s entita extrahované.

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

    The correct intent is predicted and the entities array has both the origin and destination roles in the corresponding **entities** property.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Související informace

* [Koncepty entit](luis-concept-entity-types.md)
* [Koncepce rolí](luis-concept-roles.md)
* [Předem sestavený seznam entit](luis-reference-prebuilt-entities.md)
* [Jak trénovat](luis-how-to-train.md)
* [Jak publikovat](luis-how-to-publish-app.md)
* [Jak testovat na portálu LUIS](luis-interactive-test.md)
* [Role](luis-concept-roles.md)

## <a name="next-steps"></a>Další kroky

Tento kurz vytvořil nový záměr a přidal příklad projevy pro kontextově nabytá data původu a cílových míst. Jakmile aplikaci vytrénujete a publikujete, klientská aplikace může tyto informace použít k vytvoření lístku přesunu s relevantními informacemi.

> [!div class="nextstepaction"]
> [Další informace o přidání složené entity](luis-tutorial-composite-entity.md)
