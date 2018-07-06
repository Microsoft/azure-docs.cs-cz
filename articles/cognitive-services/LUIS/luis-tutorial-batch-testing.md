---
title: Dávky k testování používejte ke zlepšení LUIS předpovědi | Microsoft Docs
titleSuffix: Azure
description: Zátěžový batch test, zkontrolujte výsledky a zlepšit LUIS předpovědi s změny.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/19/2018
ms.author: v-geberr
ms.openlocfilehash: 4a5ace10c171d17235051c5bd666526318829fd7
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37867337"
---
# <a name="use-batch-testing-to-find-prediction-accuracy-issues"></a>Najít problémy, přesnost předpovědi pomocí služby batch testu

Tento kurz ukazuje, jak najít utterance předpovědi problémy pomocí služby batch testu.  

V tomto kurzu se naučíte:

> [!div class="checklist"]
* Vytvořte dávkový soubor testu 
* Spuštění testu služby batch
* Kontrola výsledků testu
* Opravte chyby pro příkazy
* Opětovné testování služby batch

## <a name="prerequisites"></a>Požadavky

> [!div class="checklist"]
> * V tomto článku budete také potřebovat [LUIS][LUIS] účet, aby bylo možné vytvořit aplikace LEOŠ.

> [!Tip]
> Pokud ještě nemáte předplatné, si můžete zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/).

## <a name="create-new-app"></a>Vytvoření nové aplikace
Tento článek používá předem připravených domény HomeAutomation. Předem připravená doména má záměrů, entit a projevy pro řízení HomeAutomation zařízení, jako jsou světla. Vytvořit aplikaci, přidat doménu, trénování a publikování.

1. V [LUIS] webu, vytvořte novou aplikaci tak, že vyberete **vytvořit novou aplikaci** na **MyApps** stránky. 

    ![Vytvoření nové aplikace](./media/luis-tutorial-batch-testing/create-app-1.png)

2. Zadejte název `Batchtest-HomeAutomation` v dialogovém okně.

    ![Zadejte název aplikace](./media/luis-tutorial-batch-testing/create-app-2.png)

3. Vyberte **předem připravených domén** v levém dolním rohu. 

    ![Vyberte doménu, předem připravené](./media/luis-tutorial-batch-testing/prebuilt-domain-1.png)

4. Vyberte **přidat doménu** pro HomeAutomation.

    ![Přidat doménu HomeAutomation](./media/luis-tutorial-batch-testing/prebuilt-domain-2.png)

5. Vyberte **Train** v horním pravém navigačním panelu.

    ![Tlačítko pro výběr trénování](./media/luis-tutorial-batch-testing/train-button.png)

## <a name="batch-test-criteria"></a>Testovací kritérium služby batch
Testování služby batch můžete otestovat až 1 000 projevy najednou. Dávka by neměl mít duplicitní položky. [Export](create-new-app.md#export-app) aplikace, chcete-li zobrazit seznam aktuální projevy.  

Test strategií LUIS používá tři samostatné sady dat: model utterances, utterances testovací batch a utterances koncový bod. Pro účely tohoto kurzu Ujistěte se, že nepoužíváte projevy z obou modelů projevy (přidá k záměru) nebo projevy koncový bod. 

Nepoužívejte všechny projevy již v aplikaci pro batch test:

```
'breezeway on please',
'change temperature to seventy two degrees',
'coffee bar on please',
'decrease temperature for me please',
'dim kitchen lights to 25 .',
'fish pond off please',
'fish pond on please',
'illuminate please',
'living room lamp on please',
'living room lamps off please',
'lock the doors for me please',
'lower your volume',
'make camera 1 off please',
'make some coffee',
'play dvd',
'set lights bright',
'set lights concentrate',
'set lights out bedroom',
'shut down my work computer',
'silence the phone',
'snap switch fan fifty percent',
'start master bedroom light .',
'theater on please',
'turn dimmer off',
'turn off ac please',
'turn off foyer lights',
'turn off living room light',
'turn off staircase',
'turn off venice lamp',
'turn on bathroom heater',
'turn on external speaker',
'turn on my bedroom lights .',
'turn on the furnace room lights',
'turn on the internet in my bedroom please',
'turn on thermostat please',
'turn the fan to high',
'turn thermostat on 70 .' 
```

## <a name="create-a-batch-to-test-intent-prediction-accuracy"></a>Vytvoření služby batch k otestování přesnost předpovědi záměru
1. Vytvoření `homeauto-batch-1.json` v textovém editoru, jako [VSCode](https://code.visualstudio.com/). 

2. Přidání projevů s **záměr** chcete předpokládané v testu. Pro účely tohoto kurzu, aby byl jednoduchý, využít projevy `HomeAutomation.TurnOn` a `HomeAutomation.TurnOff` a přepnout `on` a `off` textu v projevy. Pro `None` záměr, přidejte do ní několik projevy, které nejsou součástí [domény](luis-glossary.md#domain) (oblastí). 

    Chcete-li pochopit, jak korelaci výsledků testů batch do služby batch JSON, přidejte pouze šest tříd Intent.

    ```JSON
    [
        {
          "text": "lobby on please",
          "intent": "HomeAutomation.TurnOn",
          "entities": []
        },
        {
          "text": "change temperature to seventy one degrees",
          "intent": "HomeAutomation.TurnOn",
          "entities": []
        },
        {
          "text": "where is my pizza",
          "intent": "None",
          "entities": []
        },
        {
          "text": "help",
          "intent": "None",
          "entities": []
        },
        {
          "text": "breezeway off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": []
        },
        {
          "text": "coffee bar off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": []
        }
    ]
    ```

## <a name="run-the-batch"></a>Spusťte dávku
1. Vyberte **Test** v horním navigačním panelu. 

    ![V navigačním panelu vyberte Test](./media/luis-tutorial-batch-testing/test-1.png)

2. Vyberte **Batch testování panel** na pravé straně panelu. 

    ![Vyberte test panelu služby Batch](./media/luis-tutorial-batch-testing/test-2.png)

3. Vyberte **datové sady importu**.

    ![Vyberte Import datové sady](./media/luis-tutorial-batch-testing/test-3.png)

4. Vyberte umístění systému souboru `homeauto-batch-1.json` souboru.

5. Zadejte název datové sady `set 1`.

    ![Výběr souboru](./media/luis-tutorial-batch-testing/test-4.png)

6. Vyberte tlačítko **Spustit**. Počkejte, dokud se provádí test.

    ![Výběrem spuštění](./media/luis-tutorial-batch-testing/test-5.png)

7. Vyberte **zobrazit výsledky**.

    ![Zobrazit výsledky](./media/luis-tutorial-batch-testing/test-6.png)

8. Zkontrolujte výsledky v grafu a legend.

    ![Výsledky služby batch](./media/luis-tutorial-batch-testing/batch-result-1.png)

## <a name="review-batch-results"></a>Zkontrolujte výsledky služby batch
Výsledky batch jsou ve dvou částech. V horní části obsahuje a legendy grafu. V dolní části zobrazí projevy, když vyberete název oblasti grafu.

Chyby jsou označeny červenou barvu. Graf je do čtyř oddílů, dva oddíly zobrazí červeně. **Toto jsou oddíly pro zaměření na**. 

Horní pravé části označuje testu správně předpovědět existence záměr nebo entity. V dolní části levého označuje, že test nesprávně předpovědět absence záměr nebo entity.

### <a name="homeautomationturnoff-test-results"></a>Výsledky testu HomeAutomation.TurnOff
V legendě, vyberte `HomeAutomation.TurnOff` záměr. Ikona s zelenou úspěch nalevo od názvu má v legendě. Nejsou žádné chyby tohoto záměru. 

![Výsledky služby batch](./media/luis-tutorial-batch-testing/batch-result-1.png)

### <a name="homeautomationturnon-and-none-intents-have-errors"></a>HomeAutomation.TurnOn a žádné záměry došlo k chybám
Další dva příkazy mají chyby, což znamená, že test predikcí neodpovídal očekávání dávkového souboru. Vyberte `None` záměru v legendě, čímž zkontrolujte první chyba. 

![Žádný záměru](./media/luis-tutorial-batch-testing/none-intent-failures.png)

Selhání se zobrazí v grafu v části red: **falešně pozitivní** a **falešně negativní**. Vyberte **falešně negativní** název oddílu v grafu pro zobrazení neúspěšných projevy pod grafem. 

![Falešné chyby záporná](./media/luis-tutorial-batch-testing/none-intent-false-negative.png)

Utterance selhání `help` byl očekáván jako `None` záměr, ale test předpovědět `HomeAutomation.TurnOn` záměr.  

Existují dva selhání, jeden v HomeAutomation.TurnOn a druhý v žádné. Obě byly způsobeny utterance `help` protože nesplnila očekávání v žádné a bylo neočekávané hledat HomeAutomation.TurnOn záměr. 

Chcete-li zjistit, proč `None` projevy selhávají, zkontrolujte projevy, které jsou aktuálně ve `None`. 

## <a name="review-none-intents-utterances"></a>Kontrola žádný záměru uživatele projevy

1. Zavřít **testovací** panel tak, že vyberete **Test** tlačítko na horním navigačním panelu. 

2. Vyberte **sestavení** na horním navigačním panelu. 

3. Vyberte **žádný** záměru ze seznamu záměry.

4. Vyberte ovládací prvek + E, jeho token zobrazení projevy 
    
    |Žádný záměru uživatele projevy|Skóre předpovědi|
    |--|--|
    |"snížit teploty pro mě prosím"|0.44|
    |"dimenze kuchyně indikátory 25."|0.43|
    |"snížit hlasitost"|0.46|
    |"zapnout Internetu v mé ložnici prosím"|0,28|

## <a name="fix-none-intents-utterances"></a>Opravit žádné záměru uživatele projevy
    
V jakékoli projevy `None` jsou by měl být mimo doménu aplikace. Tyto projevy jsou relativní vzhledem k HomeAutomation, tak, aby byly v nesprávné záměr. 

LUIS také poskytuje 50 % utterances menší než (<.50) skóre předpovědi. Pokud podíváte na projevy ve dvou příkazů, uvidíte mnohem vyšší skóre předpovědi. Když LUIS má nízkou skóre pro příklad utterances, které je dobrá indikace toho utterances jsou pro LUISmatoucí mezi aktuální záměr a dalších tříd Intent. 

Chcete-li vyřešit aplikace projevy, které jsou aktuálně ve `None` záměr musí přesunout do správné záměr a `None` záměr potřebuje nové, odpovídající záměry. 

Tři projevy v `None` záměr jsou určené k snížení automatizaci nastavení zařízení. Například používají slova `dim`, `lower`, nebo `decrease`. Čtvrtý utterance vyzve k zapnutí nastavení v Internetu. Protože jsou všechny čtyři projevy o zapnutí nebo změnit úroveň výkonu na zařízení, by měl být přesunut do `HomeAutomation.TurnOn` záměr. 

Toto je jenom jedno řešení. Můžete také vytvořit nové záměr `ChangeSetting` přesunout projevy pomocí dim, snížit a snížit do této nové záměr. 

## <a name="fix-the-app-based-on-batch-results"></a>Oprava aplikace na základě výsledků služby batch
Přesunout čtyři projevy do `HomeAutomation.TurnOn` záměr. 

1. Proto jsou vybrány všechny projevy, zaškrtněte políčko nad seznamem utterance. 

2. V **záměr přiřazení** rozevíracího seznamu, vyberte `HomeAutomation.TurnOn`. 

    ![Přesunout projevy](./media/luis-tutorial-batch-testing/move-utterances.png)

    Až čtyři projevy jsou přiřazeny, utterance seznamu pro `None` záměr je prázdný.

3. Přidejte čtyři nové příkazy pro záměru žádná:

    ```
    "fish"
    "dogs"
    "beer"
    "pizza"
    ```

    Tyto projevy jsou jednoznačně mimo doménu HomeAutomation. Při vkládání jednotlivých utterance, podívejte se na toto skóre pro něj. Skóre může být, střední nebo dokonce i velmi nízký (s červeným rámečkem okolo něj). Po trénování aplikace v kroku 8, bude toto skóre mnohem vyšší. 

7. Odebrat všechny popisky tak, že vyberete modrého popisku utterance a vyberte **odebrat popisek**.

8. Vyberte **Train** v horním pravém navigačním panelu. Skóre každé utterance je mnohem vyšší. Všechny výsledky pro `None` záměr by měla být vyšší než.80 nyní. 

## <a name="verify-the-fix-worked"></a>Ověření opravy pracoval
Pokud chcete ověřit, že jsou projevy v testu batch správně předpovědět pro **žádný** záměr, znovu spusťte testovací služby batch.

1. Vyberte **Test** v horním navigačním panelu. 

2. Vyberte **Batch testování panel** na pravé straně panelu. 

3. Vyberte tři tečky (***...*** ) tlačítko vpravo od názvu služby batch a vyberte **spouštět datovou sadu**. Počkejte, dokud se provádí test služby batch.

    ![Spuštění datové sady](./media/luis-tutorial-batch-testing/run-dataset.png)

4. Vyberte **zobrazit výsledky**. Příkazů by měly mít zelené ikony nalevo od názvu záměru. S nastavena na správný filtr `HomeAutomation.Turnoff` záměr, vyberte zelený tečky v horním pravém panelu nejbližší ve středu grafu. Název utterance se zobrazí v tabulce pod grafem. Skóre `breezeway off please` je velmi nízký. Volitelné aktivita je přidání další projevů k příslušnému záměru zvýšit Toto skóre. 

    ![Spuštění datové sady](./media/luis-tutorial-batch-testing/turnoff-low-score.png)

<!--
    The Entities section of the legend may have errors. That is the next thing to fix.

## Create a batch to test entity detection
1. Create `homeauto-batch-2.json` in a text editor such as [VSCode](https://code.visualstudio.com/). 

2. Utterances have entities identified with `startPos` and `endPost`. These two elements identify the entity before [tokenization](luis-glossary.md#token), which happens in some [cultures](luis-supported-languages.md#tokenization) in LUIS. If you plan to batch test in a tokenized culture, learn how to [extract](luis-concept-data-extraction.md#tokenized-entity-returned) the non-tokenized entities.

    Copy the following JSON into the file:

    ```JSON
    [
        {
          "text": "lobby on please",
          "intent": "HomeAutomation.TurnOn",
          "entities": [
            {
              "entity": "HomeAutomation.Room",
              "startPos": 0,
              "endPos": 4
            }
          ]
        },
        {
          "text": "change temperature to seventy one degrees",
          "intent": "HomeAutomation.TurnOn",
          "entities": [
            {
              "entity": "HomeAutomation.Operation",
              "startPos": 7,
              "endPos": 17
            }
          ]
        },
        {
          "text": "where is my pizza",
          "intent": "None",
          "entities": []
        },
        {
          "text": "help",
          "intent": "None",
          "entities": []
        },
        {
          "text": "breezeway off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": [
            {
              "entity": "HomeAutomation.Room",
              "startPos": 0,
              "endPos": 9
            }
          ]
        },
        {
          "text": "coffee bar off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": [
            {
              "entity": "HomeAutomation.Device",
              "startPos": 0,
              "endPos": 10
            }
          ]
        }
      ]
    ```

3. Import the batch file, following the [same instructions](#run-the-batch) as the first import, and name the dataset `set 2`. Run the test.

## Possible entity errors
Since the intents in the right-side filter of the test panel still pass the test, this section focuses on correct entity identification. 

Entity testing is diferrent than intents. An utterance will have only one top scoring intent, but it may have several entities. An utterance's entity may be correctly identified, may be incorrectly identified as an entity other than the one in the batch test, may overlap with other entities, or not identified at all. 

## Review entity errors
1. Select `HomeAutomation.Device` in the filter panel. The chart changes to show a single false positive and several true negatives. 

2. Select the False positive section name. The utterance for this chart point is displayed below the chart. The labeled intent and the predicted intent are the same, which is consistent with the test -- the intent prediction is correct. 

    The issue is that the HomeAutomation.Device was detected but the batch expected HomeAutomation.Room for the utterance "coffee bar off please". `Coffee bar` could be a room or a device, depending on the environment and context. As the model designer, you can either enforce the selection as `HomeAutomation.Room` or change the batch file to use `HomeAutomation.Device`. 

    If you want to reinforce that coffee bar is a room, you nee to add an utterances to LUIS that help LUIS decide a coffee bar is a room. 

    The most direct route is to add the utterance to the intent but that to add the utterance for every entity detection error is not the machine-learned solution. Another fix would be to add an utterance with `coffee bar`.

## Add utterance to help extract entity
1. Select the **Test** button on the top navigation to close the batch test panel.

2. On the `HomeAutomation.TurnOn` intent, add the utterance, `turn coffee bar on please`. The uttterance should have all three entities detected after you select enter. 

3. Select **Train** on the top navigation panel. Wait until training completes successfully.

3. Select **Test** on the top navigation panel to open the Batch testing pane again. 

4. If the list of datasets is not visible, select **Back to list**. Select the ellipsis (***...***) button at the end of `Set 2` and select `Run Dataset`. Wait for the test to complete.

5. Select **See results** to review the test results.

6. 
-->
## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o příklad projevy](luis-how-to-add-example-utterances.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions