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
ms.openlocfilehash: 5788f17f2724a0354a1db506971c2343c1800f01
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266392"
---
# <a name="use-batch-testing-to-find-prediction-accuracy-issues"></a>Dávky k testování používejte k vyhledání problémů přesnost předpovědi

Tento kurz ukazuje, jak dávky k testování používejte najít utterance předpovědi problémy.  

V tomto kurzu se naučíte:

> [!div class="checklist"]
* Vytvořte dávkový soubor testu 
* Spuštění dávky testu
* Zkontrolujte výsledky testů
* Opravte chyby pro záměry
* Testování dávky

## <a name="prerequisites"></a>Požadavky

> [!div class="checklist"]
> * V tomto článku budete také potřebovat [LUIS][LUIS] účet, aby bylo možné vytvořit aplikace LEOŠ.

> [!Tip]
> Pokud již nemáte předplatné, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/).

## <a name="create-new-app"></a>Vytvoření nové aplikace
Tento článek používá předem domény HomeAutomation. Předkompilované domény má záměry, entit a utterances řízení HomeAutomation zařízení, jako jsou indikátory. Vytvořit aplikaci, přidejte doménu, školení a publikování.

1. V [LUIS] webu, vytvořte novou aplikaci tak, že vyberete **vytvořit novou aplikaci** na **MyApps** stránky. 

    ![Vytvoření nové aplikace](./media/luis-tutorial-batch-testing/create-app-1.png)

2. Zadejte název `Batchtest-HomeAutomation` v dialogovém okně.

    ![Zadejte název aplikace.](./media/luis-tutorial-batch-testing/create-app-2.png)

3. Vyberte **předem domén** v levém dolním rohu. 

    ![Vyberte předem domény](./media/luis-tutorial-batch-testing/prebuilt-domain-1.png)

4. Vyberte **přidat doménu** pro HomeAutomation.

    ![Přidání domény HomeAutomation](./media/luis-tutorial-batch-testing/prebuilt-domain-2.png)

5. Vyberte **Train** v pravém horním navigačním panelu.

    ![Kliknutím na tlačítko Train](./media/luis-tutorial-batch-testing/train-button.png)

## <a name="batch-test-criteria"></a>Kritéria testovací batch
Testování batch můžete otestovat až 1 000 utterances najednou. Dávky nesmí obsahovat duplikáty. [Export](create-new-app.md#export-app) aplikaci chcete-li zobrazit seznam aktuální utterances.  

Test strategií LUIS používá tři samostatné sady dat: model utterances, utterances testovací batch a utterances koncový bod. V tomto kurzu Ujistěte se, že nepoužíváte utterances z buď utterances modelu (Přidat do záměrem) nebo utterances koncový bod. 

Nepoužívejte žádné utterances již v aplikaci pro batch test:

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

## <a name="create-a-batch-to-test-intent-prediction-accuracy"></a>Vytvoření dávky k testování přesnost předpovědi záměrné
1. Vytvoření `homeauto-batch-1.json` v textovém editoru, jako [VSCode](https://code.visualstudio.com/). 

2. Přidat utterances s **záměr** chcete předpokládaných v testu. V tomto kurzu, chcete-li jednoduchý, proveďte utterances `HomeAutomation.TurnOn` a `HomeAutomation.TurnOff` a přepněte `on` a `off` textu v utterances. Pro `None` záměr, přidat pár utterances, které nejsou součástí [domény](luis-glossary.md#domain) oblasti (předmět). 

    Chcete-li pochopit, jak se výsledky testů batch korelovat dávku JSON, přidejte pouze šest záměry.

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

    ![Vyberte v navigačním panelu testu](./media/luis-tutorial-batch-testing/test-1.png)

2. Vyberte **dávky testování panel** na pravé straně panelu. 

    ![Vyberte testovací panely Batch](./media/luis-tutorial-batch-testing/test-2.png)

3. Vyberte **datovou sadu Import**.

    ![Vyberte Import datové sady](./media/luis-tutorial-batch-testing/test-3.png)

4. Vyberte umístění systému souborů `homeauto-batch-1.json` souboru.

5. Název datové sady `set 1`.

    ![Výběr souboru](./media/luis-tutorial-batch-testing/test-4.png)

6. Vyberte tlačítko **Spustit**. Počkejte na dokončení testu.

    ![Vyberte spustit](./media/luis-tutorial-batch-testing/test-5.png)

7. Vyberte **zobrazte výsledky**.

    ![Zobrazte výsledky](./media/luis-tutorial-batch-testing/test-6.png)

8. Zkontrolujte výsledky v a legendu grafu.

    ![Výsledky batch](./media/luis-tutorial-batch-testing/batch-result-1.png)

## <a name="review-batch-results"></a>Zkontrolujte výsledky batch
Výsledky batch jsou dvě části. V horní části obsahuje a legendu grafu. V dolní části utterances zobrazí, když vyberete název oblasti grafu.

Všechny chyby jsou označeny červenou barvu. Graf je v čtyři části, se dvěma části zobrazí červeně. **Tyto jsou oddíly zaměřit se na**. 

Horní pravé části označuje test nesprávně předpovědět existence záměr nebo entity. Spodní levé části označuje, že test nesprávně předpovědět absence záměr nebo entity.

### <a name="homeautomationturnoff-test-results"></a>Výsledky testu HomeAutomation.TurnOff
V legendě, vyberte `HomeAutomation.TurnOff` záměr. Ikona s zelenou úspěch nalevo od názvu má v legendě. Nejsou žádné chyby pro tento záměr. 

![Výsledky batch](./media/luis-tutorial-batch-testing/batch-result-1.png)

### <a name="homeautomationturnon-and-none-intents-have-errors"></a>HomeAutomation.TurnOn a žádná záměry chybami
Dvou tříd Intent mít chyby, což znamená, že testovací předpovědi neodpovídal očekávání souboru batch. Vyberte `None` záměrné v legendě ke kontrole první chyba. 

![Žádné záměrné](./media/luis-tutorial-batch-testing/none-intent-failures.png)

Chyby se v grafu v částech red: **False kladné** a **False záporné**. Vyberte **False záporné** název oddílu v grafu pro zobrazení se nezdařilo utterances pod grafem. 

![False záporné selhání](./media/luis-tutorial-batch-testing/none-intent-false-negative.png)

Selhání utterance, `help` byl očekáván jako `None` záměr ale test předpovědět `HomeAutomation.TurnOn` záměr.  

Existují dvě selhání, jeden v HomeAutomation.TurnOn a jeden v žádné. Obě byla způsobena utterance `help` protože nesplnila se očekává v žádné a bylo v případě neočekávané shody pro HomeAutomation.TurnOn záměr. 

Chcete-li zjistit důvod, proč `None` utterances selhávají, zkontrolujte utterances aktuálně v `None`. 

## <a name="review-none-intents-utterances"></a>Zkontrolujte žádné záměrné je utterances

1. Zavřít **Test** panely výběrem **Test** tlačítko v horním navigačním panelu. 

2. Vyberte **sestavení** z panelu horním navigačním panelu. 

3. Vyberte **žádné** záměrné ze seznamu tříd Intent.

4. Vyberte ovládací prvek + E získat token zobrazení utterances 
    
    |Žádné záměrné je utterances|Předpověď skóre|
    |--|--|
    |"snížit teploty pro mi prosím"|0.44|
    |"dim kuchyně indikátory na 25."|0.43|
    |"nižší svazku"|0.46|
    |"zapnout Internetu v mé ložnici prosím"|0.28|

## <a name="fix-none-intents-utterances"></a>Opravte žádné záměrné je utterances
    
Všechny utterances v `None` jsou by měl být mimo doménu aplikace. Tyto utterances jsou relativní vzhledem k HomeAutomation, tak, aby byly v nesprávný záměr. 

LUIS také poskytuje 50 % utterances menší než (<.50) skóre předpovědi. Pokud se podíváte na utterances v ostatních dvě záměrů, zobrazí mnohem vyšší skóre předpovědi. Když LUIS má nízkou skóre pro příklad utterances, které je dobrá indikace toho utterances jsou pro LUISmatoucí mezi aktuální záměr a dalších tříd Intent. 

Opravit aplikaci aktuálně v utterances `None` záměr potřebujete přesunout do správné záměr a `None` záměr musí nové, odpovídající záměry. 

Tři utterances v `None` záměr mají nižší automatizace nastavení zařízení. Například používají slova `dim`, `lower`, nebo `decrease`. Čtvrtý utterance zobrazí dotaz, chcete-li na Internetu. Vzhledem k tomu, že všechny čtyři utterances o zapnutím nebo změna síle na zařízení se musí přesunout do `HomeAutomation.TurnOn` záměr. 

Toto je pouze jeden řešení. Můžete také vytvořit nové záměr `ChangeSetting` přesunout utterances pomocí dimenze, snížit a snížit do této nové záměr. 

## <a name="fix-the-app-based-on-batch-results"></a>Opravit aplikaci na základě výsledků batch
Přesunout čtyři utterances k `HomeAutomation.TurnOn` záměr. 

1. Zaškrtněte políčko výše v seznamu utterance tak, že jsou vybrané všechny utterances. 

2. V **přiřazení záměr** rozevíracího seznamu, vyberte `HomeAutomation.TurnOn`. 

    ![Přesunout utterances](./media/luis-tutorial-batch-testing/move-utterances.png)

    Po čtyři utterances jsou přiřazeny, utterance seznam pro `None` je prázdný.

3. Přidejte nové čtyři způsoby pro záměrné žádnou:

    ```
    "fish"
    "dogs"
    "beer"
    "pizza"
    ```

    Tyto utterances výborný jsou mimo doménu HomeAutomation. Při zadávání jednotlivých utterance sledujte skóre pro ni. Skóre může být nízkou nebo i velmi nízkou (s červeným rámečkem kolem něj). Po cvičení aplikace v kroku 8, bude skóre mnohem vyšší. 

7. Odebrat všechny popisky výběrem blue popisek v utterance a vyberte **odebrání štítku**.

8. Vyberte **Train** v pravém horním navigačním panelu. Skóre každé utterance je mnohem vyšší. Všech výsledků `None` záměr by měla být vyšší než.80 nyní. 

## <a name="verify-the-fix-worked"></a>Ověřte opravu práce na incidentu
Abyste ověřili, že jsou pro správně předpovědět utterances v testu batch **žádné** záměr, znovu spusťte batch test.

1. Vyberte **Test** v horním navigačním panelu. 

2. Vyberte **dávky testování panel** na pravé straně panelu. 

3. Vyberte se třemi tečkami (...) vpravo od názvu batch a vyberte **spustit datovou sadu**. Počkejte na dokončení testu batch.

    ![Spustit datové sady](./media/luis-tutorial-batch-testing/run-dataset.png)

4. Vyberte **zobrazte výsledky**. Záměry by měly mít zelené ikony nalevo od záměrné názvy. S vhodný filtr, nastavte na `HomeAutomation.Turnoff` záměr, vyberte zeleným dot v horním pravém panelu nejblíže k středu grafu. Název utterance se zobrazí v následující tabulce grafu. Skóre `breezeway off please` velmi nízké. Volitelné aktivita je přidat další utterances do cílem zvýšit tento skóre. 

    ![Spustit datové sady](./media/luis-tutorial-batch-testing/turnoff-low-score.png)

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

4. If the list of datasets is not visible, select **Back to list**. Select the three dots (...) at the end of `Set 2` and select `Run Dataset`. Wait for the test to complete.

5. Select **See results** to review the test results.

6. 
-->
## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o příklad utterances](luis-how-to-add-example-utterances.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions