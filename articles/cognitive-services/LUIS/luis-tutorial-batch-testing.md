---
title: 'Kurz: dávkové testování pro vyhledání problémů – LUIS'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu se dozvíte, jak pomocí dávkového testování najít ve vaší aplikaci problémy předpovědi utterance a opravit je.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 68a0016e034f4642c4e4ff166a1456f7ecf1ee3c
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904233"
---
# <a name="tutorial-batch-test-data-sets"></a>Kurz: dávkové test sady dat

V tomto kurzu se dozvíte, jak pomocí dávkového testování najít ve vaší aplikaci problémy předpovědi utterance a opravit je.  

Dávkové testování umožňuje ověřit stav aktivního a trained modelu se známou sadou označených projevy a entit. V dávkovém souboru ve formátu JSON přidejte projevy a nastavte popisky entit, které potřebujete, v rámci utterance. 

Požadavky na dávkové testování:

* Maximálně 1000 projevy na test. 
* Žádné duplicity. 
* Povolené typy entit: jenom ty počítače, které se naučily jednoduché a složené. Dávkové testování je užitečné jenom pro počítače a entity, které se naučily.

Pokud používáte jinou aplikaci než tento kurz, *nepoužívejte ukázkový* projevy již přidaný do záměru. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**Co se v tomto kurzu naučíte:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importovat ukázkovou aplikaci
> * Vytvoření souboru dávkového testu 
> * Spustit test dávky
> * Kontrola výsledků testu
> * Opravit chyby 
> * Test dávky

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importovat ukázkovou aplikaci

Pokračujte s aplikací **HumanResources**, kterou jste vytvořili v posledním kurzu. 

Použijte k tomu následující postup:

1.  Stáhněte si [soubor JSON aplikace](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-review-HumanResources.json) a uložte si ho.

2. Naimportujte soubor JSON do nové aplikace.

3. V části **Manage** (Správa) na kartě **Versions** (Verze) naklonujte verzi a pojmenujte ji `batchtest`. Klonování představuje skvělý způsob, jak si můžete vyzkoušet různé funkce služby LUIS, aniž by to mělo vliv na původní verzi. Název verze je součástí cesty URL, a proto může obsahovat jenom znaky podporované v adresách URL. 

4. Trénujte aplikaci.

## <a name="batch-file"></a>Dávkový soubor

1. Vytvořte `HumanResources-jobs-batch.json` v textovém editoru nebo [si ho stáhněte](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources-jobs-batch.json) . 

2. V dávkovém souboru ve formátu JSON přidejte projevy s **záměrem** , který chcete odhadnout v testu. 

   [!code-json[Add the intents to the batch test file](~/samples-luis/documentation-samples/tutorials/HumanResources-jobs-batch.json "Add the intents to the batch test file")]

## <a name="run-the-batch"></a>Spustit dávku

1. V horním navigačním panelu vyberte **test** . 

2. Na panelu na pravé straně vyberte **panel dávkové testování** . 

    [![snímek obrazovky aplikace LUIS s vybraným panelem dávkového testu](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png)](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png#lightbox)

3. Vyberte **importovat datovou sadu**.

    [![obrazovka aplikace LUIS s zvýrazněnou importovanou datovou sadou](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png)](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png#lightbox)

4. Vyberte umístění souboru `HumanResources-jobs-batch.json`.

5. Pojmenujte datovou sadu `intents only` a vyberte **Hotovo**.

    ![Vybrat soubor](./media/luis-tutorial-batch-testing/hr-import-new-dataset-ddl.png)

6. Vyberte tlačítko **Spustit**. 

7. Vyberte **Zobrazit výsledky**.

8. Zkontrolujte výsledky v grafu a v legendě.

    [![obrazovky aplikace LUIS s výsledky dávkového testu](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png)](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png#lightbox)

## <a name="review-batch-results"></a>Kontrola výsledků dávky

V dávkovém grafu se zobrazí čtyři kvadranty výsledků. Napravo od grafu je filtr. Filtr obsahuje záměry a entity. Když vyberete [část grafu](luis-concept-batch-test.md#batch-test-results) nebo bod v rámci grafu, zobrazí se v grafu odpovídající utterance (y). 

Při najetí myší na graf může kolečko myši zvětšit nebo zmenšit displej v grafu. To je užitečné v případě, že je graf v grafu seskupen těsně dohromady. 

Graf je ve čtyřech kvadrantech, přičemž dva z oddílů se zobrazí červeně. **Tyto oddíly se zaměřují na**. 

### <a name="getjobinformation-test-results"></a>Výsledky testů GetJobInformation

Výsledky testu **GetJobInformation** zobrazené ve filtru ukazují, že 2 ze čtyř předpovědi byly úspěšně provedeny. V levém dolním rohu vyberte název **false (negativní** ), aby se projevy pod grafem. 

Pomocí klávesnice CTRL + E přepnete zobrazení popisku, abyste zobrazili přesný text uživatele utterance. 

`Is there a database position open in Los Colinas?` utterance je označena jako _GetJobInformation_ , ale aktuální model utterance jako _ApplyForJob_. 

Existují skoro třikrát tolik příkladů pro **ApplyForJob** než **GetJobInformation**. Tato nestejnoměrnost příkladu projevy se váží ve prospěch záměru **ApplyForJob** , což způsobuje nesprávnou předpověď. 

Všimněte si, že oba záměry mají stejný počet chyb. Nesprávná předpověď v jednom záměru má vliv i na jiný záměr. Oba mají chyby, protože projevy byly nesprávně předpovězené pro jeden záměr a také nesprávně nepředvídatelné pro jiný záměr. 

<a name="fix-the-app"></a>

## <a name="how-to-fix-the-app"></a>Jak opravit aplikaci

Cílem této části je, aby se všechny projevy správně předpovídané pro **GetJobInformation** opravou aplikace. 

Zdánlivou rychlou opravou je přidání těchto dávkových souborů projevy do správného záměru. To není to, co chcete udělat. Chcete, aby LUIS správně předpovědět tyto projevy, aniž byste je museli přidávat jako příklady. 

Můžete se také zajímat o odebrání projevy z **ApplyForJob** , dokud není množství utterance stejné jako **GetJobInformation**. To může opravit výsledky testu, ale by bránilo LUIS v předpovědi tohoto záměru přesně příště. 

Opravou je přidání dalších projevy k **GetJobInformation**. Nezapomeňte změnit délku utterance, volbu slov a uspořádání slov, zatímco stále cílíte na záměr najít informace o úloze, a _nemusíte_ je použít pro úlohu.

### <a name="add-more-utterances"></a>Přidat další projevy

1. Kliknutím na tlačítko **test** v horním navigačním panelu zavřete panel Batch test. 

2. V seznamu záměry vyberte **GetJobInformation** . 

3. Přidejte další projevy, které jsou proměnlivé pro délku, volbu slov a uspořádání slov, a nezapomeňte zahrnout `resume`, `c.v.`a `apply`:

    |Příklad projevy pro záměr **GetJobInformation**|
    |--|
    |Vyžaduje nová úloha ve skladu pro Stocker, že se dá použít s pokračováním?|
    |Kde se v současnosti úlohy střešních krytinují?|
    |Slyšeli jsme, že došlo k lékařskému kódování, které vyžaduje obnovení.|
    |Potřebuji úlohu, která pomáhá školním dětem psát své c.v.s. |
    |Tady je moje obnovení a hledá se nový příspěvek v komunitní škole, který používá počítače.|
    |Jaké pozice jsou k dispozici v podřízeném a domovském místě?|
    |Je v novince k dispozici stůl pro učně?|
    |Moje C.v. ukazuje, že je dobré analyzovat nákupy, rozpočty a ztráty peněz. Existuje něco pro tento typ práce?|
    |Kde se nyní nacházejí vrtné úlohy země?|
    |Jako ovladač EMS jsem pracoval 8 let. Jakékoli nové úlohy?|
    |Nové úlohy zpracování potravin vyžadují aplikaci?|
    |Kolik nových pracovních úloh v Yardu je dostupných?|
    |Existuje nový příspěvek v PERSONÁLNÍm vztahu k práci a jednáním?|
    |Mám hlavní šablony v rámci správy knihoven a archivů. Jakékoli nové pozice?|
    |Existují nějaké neřešte úlohy pro 13 let olds v dnešním městě?|

    Nepište entitu **úlohy** v projevy. V této části kurzu se zaměřuje jenom na předpověď záměrů.

4. Výuku aplikace vyberete tak, že vyberete **vlak** v pravém horním navigačním panelu.

## <a name="verify-the-new-model"></a>Ověření nového modelu

Chcete-li ověřit, zda jsou projevy v dávkovém testu správně předpovězeny, spusťte test dávky znovu.

1. V horním navigačním panelu vyberte **test** . Pokud jsou výsledky dávky stále otevřené, vyberte **zpět k seznamu**.  

1. Klikněte na tlačítko se třemi tečkami (***...***) napravo od názvu dávky a vyberte **Spustit**. Počkejte na dokončení dávkového testu. Všimněte si, že tlačítko **Zobrazit výsledky** je teď zelené. To znamená, že celá dávka byla úspěšně spuštěna.

1. Vyberte **Zobrazit výsledky**. Všechny záměry by měly mít zelené ikony nalevo od názvů záměrů. 

## <a name="create-batch-file-with-entities"></a>Vytvoření dávkového souboru s entitami 

Aby bylo možné ověřit entity v rámci dávkového testu, musí být entity označeny v dávkovém souboru JSON. 

Variace entit pro celkový počet slov ([token](luis-glossary.md#token)) může mít vliv na kvalitu předpovědi. Ujistěte se, že školicí data dodaná do záměru s názvem projevy obsahují celou řadu délek entit. 

Při prvním zápisu a testování dávkových souborů je nejlepší začít s několika projevy a entitami, o kterých víte, že se domníváte, že je možné, že bude nesprávně předpokládaná. To vám pomůže rychle se zaměřit na problematické oblasti. Po otestování záměrů **GetJobInformation** a **ApplyForJob** pomocí několika různých názvů úloh, které nebyly předpovězeny, byl tento soubor testu dávek vyvinut, aby bylo možné zjistit, zda došlo k problému předpovědi s určitými hodnotami entity **úlohy** . 

Hodnota entity **úlohy** , která je k dispozici v projevy testu, je obvykle jedno nebo dvě slova, několik příkladů je více slov. Pokud _vaše vlastní_ aplikace lidských zdrojů má obvykle názvy úloh mnoho slov, příklad projevy s entitou **úlohy** v této aplikaci nebude dobře fungovat.

1. Vytvořte `HumanResources-entities-batch.json` v textovém editoru, jako je [VSCode](https://code.visualstudio.com/) nebo [si ho stáhněte](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources-entities-batch.json) .

2. V dávkovém souboru ve formátu JSON přidejte pole objektů, které obsahují projevy, s **záměrem** , který chcete odhadnout v testu, a také umístění všech entit v utterance. Vzhledem k tomu, že entita je založená na tokenech, nezapomeňte spustit a zastavit každou entitu ve znaku. Nespouštějte ani neukončí utterance v prostoru. Dojde k chybě při importu dávkového souboru.  

   [!code-json[Add the intents and entities to the batch test file](~/samples-luis/documentation-samples/tutorials/HumanResources-entities-batch.json "Add the intents and entities to the batch test file")]


## <a name="run-the-batch-with-entities"></a>Spuštění dávky s entitami

1. V horním navigačním panelu vyberte **test** . 

2. Na panelu na pravé straně vyberte **panel dávkové testování** . 

3. Vyberte **importovat datovou sadu**.

4. Vyberte umístění systému souborů `HumanResources-entities-batch.json` souboru.

5. Pojmenujte datovou sadu `entities` a vyberte **Hotovo**.

6. Vyberte tlačítko **Spustit**. Počkejte na dokončení testu.

7. Vyberte **Zobrazit výsledky**.

## <a name="review-entity-batch-results"></a>Kontrola výsledků dávky entit

Graf se otevře se správně předpovězenými záměry. Posuňte se dolů na filtr na pravé straně, abyste našli předpovědi entit s chybami. 

1. Ve filtru vyberte entitu **úlohy** .

    ![Předpovědi entit chyb ve filtru](./media/luis-tutorial-batch-testing/hr-entities-filter-errors.png)

    Graf se změní a zobrazí předpovědi entit. 

2. V dolním levém kvadrantu grafu vyberte **falešně negativní hodnotu** . Pak použijte ovládací prvek kombinace kláves + E a přepněte se do zobrazení tokenu. 

    [zobrazení tokenu ![předpovědi entit](./media/luis-tutorial-batch-testing/token-view-entities.png)](./media/luis-tutorial-batch-testing/token-view-entities.png#lightbox)
    
    Kontrola projevy pod grafem odhalí konzistentní chybu, když název úlohy zahrnuje `SQL`. V příkladu projevy a v seznamu frází úlohy se SQL používá jenom jednou a jenom jako součást většího názvu úlohy `sql/oracle database administrator`.

## <a name="fix-the-app-based-on-entity-batch-results"></a>Oprava aplikace na základě výsledků dávky entit

Oprava aplikace vyžaduje, aby LUIS správně určila variace úloh SQL. Pro tuto opravu je k dispozici několik možností. 

* Explicitně přidejte další příklad projevy, který používá SQL a označí tato slova jako entitu úlohy. 
* Explicitní přidání dalších úloh SQL do seznamu frází

Tyto úlohy jsou pro vás k disstranu.

Přidáním [vzoru](luis-concept-patterns.md) před tím, než je entita správně předpovězena, nedojde k vyřešení tohoto problému. Důvodem je to, že se vzor neshoduje, dokud nebudou zjištěny všechny entity ve vzoru. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky

Kurz pro vyhledání problémů s aktuálním modelem použil dávkový test. Model byl opraven a znovu testován pomocí dávkového souboru pro ověření správnosti změny.

> [!div class="nextstepaction"]
> [Přečtěte si o vzorcích](luis-tutorial-pattern.md)

