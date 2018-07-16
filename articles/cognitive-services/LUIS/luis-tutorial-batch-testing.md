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
ms.date: 07/06/2018
ms.author: v-geberr
ms.openlocfilehash: b695783c6d68876d39482ed5abec24f45087603d
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054858"
---
# <a name="improve-app-with-batch-test"></a>Vylepšit aplikaci s testováním služby batch

Tento kurz ukazuje, jak najít utterance předpovědi problémy pomocí služby batch testu.  

V tomto kurzu se naučíte:

<!-- green checkmark -->
> [!div class="checklist"]
* Vytvořte dávkový soubor testu 
* Spuštění testu služby batch
* Kontrola výsledků testu
* Opravte chyby pro příkazy
* Opětovné testování služby batch

Pro účely tohoto článku potřebujete bezplatný účet [LUIS](luis-reference-regions.md#luis-website), abyste mohli vytvořit svou aplikaci LUIS.

## <a name="before-you-begin"></a>Než začnete
Pokud nemáte aplikaci lidských zdrojů [zkontrolujte projevy koncový bod](luis-tutorial-review-endpoint-utterances.md) kurzu [importovat](luis-how-to-start-new-app.md#import-new-app) JSON do nové aplikace ve [LUIS](luis-reference-regions.md#luis-website) webu. Aplikaci k importování najdete v úložišti [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-review-HumanResources.json) na Githubu.

Pokud chcete zachovat původní aplikaci pro lidské zdroje, naklonujte verzi na stránce [Settings](luis-how-to-manage-versions.md#clone-a-version) (Nastavení) a pojmenujte ji `batchtest`. Klonování představuje skvělý způsob, jak si můžete vyzkoušet různé funkce služby LUIS, aniž by to mělo vliv na původní verzi. 

## <a name="purpose-of-batch-testing"></a>Účelem testování služby batch
Testování služby batch můžete ověřit stav modelu známé sadu testů projevy a označené jako entity. V souboru ve formátu JSON služby batch přidejte projevy a nastavte popisky entity, které potřebujete předpovědět uvnitř utterance. 

Doporučený testovací strategie pro LUIS používá tři samostatné sady dat: Příklad projevy modelu, projevy testovací služby batch a projevy koncový bod k dispozici. Pro účely tohoto kurzu Ujistěte se, že nepoužíváte projevy z obou příklad projevy (přidá k záměru) nebo projevy koncový bod. 

K ověření vaší služby batch test projevy proti příklad projevy a koncový bod projevy [exportovat](luis-how-to-start-new-app.md#export-app) aplikace a [Stáhnout](luis-how-to-start-new-app.md#export-endpoint-logs) protokol dotazu. Porovnání utterance příkladu aplikace a projevy dotazu protokolu do testu projevy služby batch. 

Požadavky pro testování služby batch:

* 1000 projevy na test. 
* Žádné duplicity. 
* Povolené typy entit: jednoduché a složené.

## <a name="create-a-batch-file-with-utterances"></a>Vytvořte dávkový soubor se projevy
1. Vytvoření `HumanResources-jobs-batch.json` v textovém editoru, jako [VSCode](https://code.visualstudio.com/). 

2. V souboru ve formátu JSON batch přidání projevů s **záměr** chcete předpokládané v testu. 

    ```JSON
    [
        {
        "text": "Are there any janitorial jobs currently open?",
        "intent": "GetJobInformation",
        "entities": []
        },
        {
        "text": "I would like a fullstack typescript programming with azure job",
        "intent": "GetJobInformation",
        "entities": []
        },
        {
        "text": "Is there a database position open in Los Colinas?",
        "intent": "GetJobInformation",
        "entities": []
        },
        {
        "text": "Can I apply for any database jobs with this resume?",
        "intent": "GetJobInformation",
        "entities": []
        }
    ]
    ```

## <a name="run-the-batch"></a>Spusťte dávku

1. Vyberte **Test** v horním navigačním panelu. 

    [ ![Snímek obrazovky LUIS aplikace s testem zvýrazněných v horní, pravé navigační panel](./media/luis-tutorial-batch-testing/hr-first-image.png)](./media/luis-tutorial-batch-testing/hr-first-image.png#lightbox)

2. Vyberte **Batch testování panel** na pravé straně panelu. 

    [ ![Aplikace LUIS snímek obrazovky s panelem test Batch zvýrazněnou](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png)](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png#lightbox)

3. Vyberte **datové sady importu**.

    [ ![Aplikace LUIS snímek obrazovky s datovou sadou Import zvýrazněnou](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png)](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png#lightbox)

4. Vyberte umístění systému souboru `HumanResources-jobs-batch.json` souboru.

5. Zadejte název datové sady `intents only` a vyberte **provádí**.

    ![Výběr souboru](./media/luis-tutorial-batch-testing/hr-import-new-dataset-ddl.png)

6. Vyberte tlačítko **Spustit**. Počkejte, dokud se provádí test.

    [ ![Aplikace LUIS snímek obrazovky s spuštěný zvýrazněnou](./media/luis-tutorial-batch-testing/hr-run-button.png)](./media/luis-tutorial-batch-testing/hr-run-button.png#lightbox)

7. Vyberte **zobrazit výsledky**.

8. Zkontrolujte výsledky v grafu a legend.

    [ ![Snímek obrazovky LUIS aplikace s výsledky testů služby batch](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png)](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png#lightbox)

## <a name="review-batch-results"></a>Zkontrolujte výsledky služby batch
Batch graf zobrazuje čtyři kvadrantech výsledky. Napravo od grafu je filtr. Ve výchozím nastavení je nastaven filtr na první záměr v seznamu. Filtr obsahuje všechny záměry a pouze jednoduché, hierarchické (pouze nadřazené) a složené entity. Když vyberete graf nebo bodu v rámci tohoto grafu, přidružené utterance(s) zobrazení pod grafem. 

Když najede myší na graf, kolečko myši můžete zvětšit nebo zmenšit zobrazení v grafu. To je užitečné, když existuje mnoho bodů v grafu úzce společně v clusteru. 

Graf je v čtyři kvadrantech dva oddíly zobrazí červeně. **Toto jsou oddíly pro zaměření na**. 

### <a name="applyforjob-test-results"></a>Výsledky testu ApplyForJob
**ApplyForJob** zobrazit výsledky testů zobrazené ve filtru, 1 čtyři predikcí bylo úspěšné. Vyberte název **falešně pozitivní** nad horním pravém kvadrantu zobrazíte projevy pod grafem. 

![Služba LUIS projevy testovací služby batch](./media/luis-tutorial-batch-testing/hr-applyforjobs-false-positive-results.png)

Tři projevy měl hlavní záměr **ApplyForJob**. Záměr uvedenými v dávkovém souboru, má nižší skóre. Proč k tomu? Co se týče možnosti aplikace word a uspořádání word jsou velmi úzce souvisí dvou příkazů. Kromě toho jsou skoro tři třikrát tolik příklady **ApplyForJob** než **GetJobInformation**. Tato nerovnosti příklad projevy váží **ApplyForJob** upřednostnit na záměr. 

Všimněte si, že oba záměry mají stejný počet chyb: 

![Služba LUIS batch test filtrování chyb](./media/luis-tutorial-batch-testing/hr-intent-error-count.png)

Utterance odpovídající vrchního bodu v **falešně pozitivní** oddíl je `Can I apply for any database jobs with this resume?`. Slovo `resume` pouze se použil **ApplyForJob**. 

Ostatní dva body v grafu má mnohem nižší skóre pro chybný záměr, což znamená, že jsou blíž ke správné záměr. 

## <a name="fix-the-app-based-on-batch-results"></a>Oprava aplikace na základě výsledků služby batch
Cílem této části je mít tři projevy, které byly správně předpovědět pro **ApplyForJob** k správně předpovědět pro **GetJobInformation**, po vyřešení aplikace. 

Přidání projevů soubor tyto služby batch na správné záměr je zdánlivě rychlé opravy. To je, co nechcete dělat ale. Chcete, aby služba LUIS správně předpovědět těchto projevů bez nutnosti přidávat jako příklady. 

Může vás také zajímat o odebrání projevy z **ApplyForJob** dokud množství utterance je stejný jako **GetJobInformation**. Který lze pravděpodobně vyřešit výsledky testů, ale bude bránit LUIS z přesně předpovídat tohoto záměru příště. 

První opravu, je přidat další projevy do **GetJobInformation**. Druhý opravy je snížení váhu slovo `resume` směrem k **ApplyForJob** záměr. 

### <a name="add-more-utterances-to-getjobinformation"></a>Přidat další projevy do **GetJobInformation**
1. Zavřít panel dávky testů tak, že vyberete **testování** tlačítko v horním navigačním panelu. 

    [ ![Snímek obrazovky služby LUIS se zvýrazněným tlačítkem testu](./media/luis-tutorial-batch-testing/hr-close-test-panel.png)](./media/luis-tutorial-batch-testing/hr-close-test-panel.png#lightbox)

2. Vyberte **GetJobInformation** ze seznamu záměry. 

    [ ![Snímek obrazovky služby LUIS se zvýrazněným tlačítkem testu](./media/luis-tutorial-batch-testing/hr-select-intent-to-fix-1.png)](./media/luis-tutorial-batch-testing/hr-select-intent-to-fix-1.png#lightbox)

3. Přidat další projevy, které jsou různé délky, volba slov a uspořádání slova, nezapomeňte zahrnout podmínky `resume` a `c.v.`:

    ```JSON
    Is there a new job in the warehouse for a stocker?
    Where are the roofing jobs today?
    I heard there was a medical coding job that requires a resume.
    I would like a job helping college kids write their c.v.s. 
    Here is my resume, looking for a new post at the community college using computers.
    What positions are available in child and home care?
    Is there an intern desk at the newspaper?
    My C.v. shows I'm good at analyzing procurement, budgets, and lost money. Is there anything for this type of work?
    Where are the earth drilling jobs right now?
    I've worked 8 years as an EMS driver. Any new jobs?
    New food handling jobs?
    How many new yard work jobs are available?
    Is there a new HR post for labor relations and negotiations?
    I have a masters in library and archive management. Any new positions?
    Are there any babysitting jobs for 13 year olds in the city today?
    ```

4. Trénování aplikace tak, že vyberete **Train** v pravém horním navigačním panelu.

## <a name="verify-the-fix-worked"></a>Ověření opravy pracoval
Pokud chcete ověřit, že jsou správně předpovědět projevy v testu služby batch, znovu spusťte test služby batch.

1. Vyberte **Test** v horním navigačním panelu. Pokud výsledky batch jsou stále otevřen, vyberte **zpět do seznamu**.  

2. Vyberte tři tečky (***...*** ) tlačítko vpravo od názvu služby batch a vyberte **spouštět datovou sadu**. Počkejte, dokud se provádí test služby batch. Všimněte si, že **zobrazit výsledky** je nyní zelené tlačítko. To znamená, že celý batch proběhla úspěšně.

3. Vyberte **zobrazit výsledky**. Příkazů by měly mít zelené ikony nalevo od názvu záměru. 

    [ ![Snímek obrazovky služby LUIS se zvýrazněným tlačítkem výsledky služby batch](./media/luis-tutorial-batch-testing/hr-batch-test-intents-no-errors.png)](./media/luis-tutorial-batch-testing/hr-batch-test-intents-no-errors.png#lightbox)


## <a name="what-has-this-tutorial-accomplished"></a>Co má provést v tomto kurzu?
Tato aplikace přesnost předpovědi zvýšila o nalezení chyby v dávce a opravu modelu tak, že přidáte další příklad projevy správné záměr a školení. 

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už aplikaci LUIS nepotřebujete, odstraňte ji. V nabídce vlevo nahoře vyberte **Moje aplikace**. Vyberte tři tečky **...**  napravo od názvu aplikace v seznamu aplikací vyberte **odstranit**. V automaticky otevíraném dialogovém okně **Delete app?** (Odstranit aplikaci?) vyberte **Ok**.


## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o vzorech](luis-tutorial-pattern.md)

