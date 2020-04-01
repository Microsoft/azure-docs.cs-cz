---
title: 'Kurz: Kurz složené entity – LUIS'
description: V tomto kurzu přidejte složenou entitu do svazku extrahovaných dat různých typů do jedné obsahující entity. Sdružováním dat může klientská aplikace snadno extrahovat související data v různých datových typech.
ms.topic: tutorial
ms.date: 03/31/2020
ms.openlocfilehash: 5b8185a56c54ec92ce8ceaf1cd029dd31f6e709c
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478686"
---
# <a name="tutorial-group-and-extract-related-data"></a>Kurz: Seskupení a extrakce souvisejících dat
V tomto kurzu přidejte složenou entitu do svazku extrahovaných dat různých typů do jedné obsahující entity. Sdružováním dat může klientská aplikace snadno extrahovat související data v různých datových typech.

Účelem složené entity je seskupit související entity do entity nadřazené kategorie. Informace existují jako samostatné entity před vytvořením složené.

Složená entita je vhodná pro tento typ dat, protože data:

* Jsou spolu příbuzní.
* Použijte různé typy entit.
* Musí být seskupeny a zpracovány klientskou aplikací jako jediná informace.

**V tomto kurzu se dozvíte, jak:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importovat ukázkové aplikace
> * Vytvořit záměr
> * Přidání složené entity
> * Trénování
> * Publikování
> * Zjistit záměry a entity z koncového bodu

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importovat ukázkové aplikace

1.  Stáhněte a uložte [soubor JSON aplikace](
https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/tutorial_list.json?raw=true) z kurzu entity Seznam.

2. Importujte JSON do nové aplikace pomocí [portálu LUIS](https://www.luis.ai).

3. V části **Manage** (Správa) na kartě **Versions** (Verze) naklonujte verzi a pojmenujte ji `composite`. Klonování představuje skvělý způsob, jak si můžete vyzkoušet různé funkce služby LUIS, aniž by to mělo vliv na původní verzi. Název verze je součástí cesty URL, a proto smí obsahovat jenom znaky, které jsou platné v adresách URL.

## <a name="composite-entity"></a>Složená entita

V této aplikaci je název oddělení definován v entitě seznamu **oddělení** a zahrnuje synonyma.

**PřevodEmployeeToDepartment** záměr má příklad projevy požádat zaměstnance přesunout do nového oddělení.

Příklad projevy pro tento záměr patří:

|Ukázkové promluvy|
|--|
|přesunout Johna W. Smithe do účetního oddělení|
|převod Jill Jones z R&D|

Žádost o přesunutí by měla obsahovat název oddělení a jméno zaměstnance.

## <a name="add-the-personname-prebuilt-entity-to-help-with-common-data-type-extraction"></a>Přidání předem sestavené entity PersonName, která vám pomůže s běžným extrakcí datového typu

Služba LUIS poskytuje několik předem připravených entit pro extrakci běžných dat.

1. Z horní navigace vyberte **Build** a pak v levé navigační nabídce vyberte **Entity.**

1. Vyberte tlačítko **Manage prebuilt entity** (Spravovat předem připravenou entitu).

1. Vyberte **[PersonName](luis-reference-prebuilt-person.md)** ze seznamu předem vytvořených entit a vyberte **Hotovo**.

    ![Snímek obrazovky s vybranou možností number (číslo) v dialogovém okně s předem připravenými entitami](./media/luis-tutorial-composite-entity/add-personname-prebuilt-entity.png)

    Tato entita vám pomůže přidat rozpoznávání názvů do klientské aplikace.

## <a name="create-composite-entity-from-example-utterances"></a>Vytvoření složené entity z ukázkových promluv

1. Vyberte **Intents** (Záměry) v levé navigaci.

1. V seznamu záměrů vyberte **Možnost Převéstzaměstnancedooddělení.**

1. V utterance `place John Jackson in engineering`vyberte entitu `John Jackson`personName a pak **vyberte zalamovat ve složené entitě** v seznamu rozbalovacích nabídek pro následující utterance.

    ![Snímek obrazovky s výběrem obtékání složených v rozevíracím dialogovém okně](./media/luis-tutorial-composite-entity/hr-create-composite-entity-1.png)

1. Pak okamžitě vyberte `engineering` poslední entitu v utterance. Pod vybranými slovy označujícími složenou entitu je nakreslen zelený pruh. V rozbalovací nabídce zadejte `TransferEmployeeInfo` složený název a vyberte enter.

    ![Snímek obrazovky s zadáním složeného názvu v rozevíracím dialogovém okně](./media/luis-tutorial-composite-entity/hr-create-composite-entity-2.png)

1. **V poli Jaký typ entity chcete vytvořit?** `personName` `Department` Vyberte **Done** (Hotovo). Všimněte si, že předem sestavená entita, personName, byla přidána do složené entity. Pokud byste mohli mít předem sestavenou entitu mezi počátečními a koncovými tokeny složené entity, musí složená entita obsahovat tyto předem sestavené entity. Pokud předem sestavené entity nejsou zahrnuty, složená entita není správně předpovězena, ale každý jednotlivý prvek je.

    ![Snímek obrazovky s zadáním složeného názvu v rozevíracím dialogovém okně](./media/luis-tutorial-composite-entity/hr-create-composite-entity-3.png)

## <a name="label-example-utterances-with-composite-entity"></a>Popiskové příkladové projevy s složenou entitou

1. V každém příkladu utterance vyberte entitu nejvíce vlevo, která by měla být ve složeném. Pak vyberte **Obtékat ve složené entitě**.

1. Vyberte poslední slovo ve složené entitě a z rozbalovací nabídky vyberte **Možnost PřevéstZaměstnanceInfo.**

1. Ověřte, zda jsou všechny projevy v záměru označeny složenou entitou.

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Trénování aplikace tak, aby změny záměru mohly být testovány

Chcete-li aplikaci trénovat, vyberte **možnost Vlak**. Školení platí změny, jako jsou nové entity a popisované projevy, aktivní model.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Publikování aplikace pro přístup z koncového bodu HTTP

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Získání záměru a predikce entit z koncového bodu

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Na konec adresy URL zadejte `Move Jill Jones to DevOps`. Poslední parametr řetězce dotazu je `q`, což je dotaz promluvy.

    Vzhledem k tomu, že tento test je ověřit složený je extrahován správně, test může buď existující ukázkový utterance nebo nové utterance. Dobrým testem je zahrnout všechny podřízené entity ve složené entitě.

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

   Tato utterance vrátí pole složené entity. Každá entita má typ a hodnotu. Chcete-li najít větší přesnost pro každou podřízenou entitu, použijte kombinaci typu a hodnoty z položky složeného pole k nalezení odpovídající položky v poli entit.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Související informace

* [Kurz entity seznamu](luis-quickstart-intents-only.md)
* Rámcové informace o složené [entitě](luis-concept-entity-types.md)
* [Jak trénovat](luis-how-to-train.md)
* [Jak publikovat](luis-how-to-publish-app.md)
* [Jak testovat na portálu LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Další kroky

Tento kurz vytvořil složenou entitu pro zapouzdření existujících entit. To umožňuje klientské aplikaci najít skupinu souvisejících dat v různých datových typech pokračovat v konverzaci. Klientská aplikace pro tuto aplikaci lidských zdrojů se může zeptat, jaký den a čas musí přesun začít a skončit. Mohlo by se také zeptat na další logistiku pohybu, jako je fyzický telefon.

> [!div class="nextstepaction"]
> [Oprava nejistých předpovědí kontrolou promluv koncového bodu](luis-tutorial-review-endpoint-utterances.md)
