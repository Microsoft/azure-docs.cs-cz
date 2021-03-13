---
title: 'Model výuky: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat modul **vlakového modelu** v Azure Machine Learning ke školení modelu klasifikace nebo regrese.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/10/2021
ms.openlocfilehash: 77927472dae6c8e7e6fddacf9088b479636edd37
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2021
ms.locfileid: "103224318"
---
# <a name="train-model-module"></a>Modul vlakového modelu

Tento článek popisuje modul v Návrháři Azure Machine Learning.

Tento modul slouží ke školení modelu klasifikace nebo regrese. Školení proběhne po definování modelu a nastavení jeho parametrů a vyžaduje označení dat. Pomocí **výukového modelu** můžete také přeškolovat stávající model s novými daty. 

## <a name="how-the-training-process-works"></a>Jak proces školení funguje

V Azure Machine Learning je vytváření a používání modelu strojového učení obvykle proces tří kroků. 

1. Nakonfigurujete model, výběrem konkrétního typu algoritmu a definováním jeho parametrů nebo parametrů. Vyberte některý z následujících typů modelů: 

    + **Klasifikační** modely založené na neuronovéch sítích, rozhodovacích stromů a doménových strukturách a dalších algoritmech.
    + **Regresní** modely, které mohou zahrnovat standardní lineární regresi nebo které používají jiné algoritmy, včetně neuronovéch sítí a bayesovského rozhodování regrese.  

2. Poskytněte datovou sadu, která je označena a má data kompatibilní s algoritmem. Připojte data i model k **proškolování modelu**.

    Jaké školení vytváří konkrétní binární formát, iLearner, který zapouzdřuje statistické vzorce získané z dat. Tento formát nejde změnit přímo ani číst. Tento vycvičený model ale může používat jiný modul. 
    
    Můžete také zobrazit vlastnosti modelu. Další informace najdete v části s výsledky.

3. Po dokončení školení použijte školicí model s jedním z [modulů pro vyhodnocování](./score-model.md)a předpovědi se na nová data.

## <a name="how-to-use-train-model"></a>Jak používat model výuky 
    
1. Přidejte modul **vlakového modelu** do kanálu.  Tento modul můžete najít v kategorii **Machine Learning** . Rozbalte položku **vlak** a přetáhněte do svého kanálu modul **vlakového modelu** .
  
1.  Na levém vstupu připojte nev nevýukovém režimu. Připojte datovou sadu ke správnému vstupu **modelu výuky**.

    Datová sada školení musí obsahovat sloupec popisku. Všechny řádky bez popisků jsou ignorovány.
  
1.  U **sloupce popisek** klikněte na **Upravit sloupec** v pravém panelu modulu a vyberte jeden sloupec obsahující výsledky, které model může použít pro školení.
  
    - Pro problémy s klasifikací musí sloupec popisku obsahovat buď hodnoty **kategorií** nebo **diskrétní** hodnoty. Některé příklady mohou být hodnocení typu Ano/bez, kód klasifikace choroby nebo název nebo skupina pro příjem.  Pokud vyberete sloupec noncategorical, modul během školení vrátí chybu.
  
    -   V případě regresních problémů musí sloupec popisku obsahovat **Číselná** data, která představují proměnnou odpovědi. V ideálním případě číselná data představují souvislý rozsah. 
    
    Příkladem může být skóre úvěrového rizika, předpokládaný čas k selhání pevného disku nebo předpokládaný počet volání centra volání v daném dni nebo čase.  Pokud nevyberete číselný sloupec, může se zobrazit chyba.
  
    -   Pokud neurčíte, který sloupec popisku použít, Azure Machine Learning se pokusí odvodit, který je příslušným sloupcem popisku, pomocí metadat datové sady. Pokud se sloupec vybere jako špatný, můžete ho opravit pomocí voliče sloupců.
  
    > [!TIP] 
    > Pokud máte potíže s použitím voliče sloupců, přečtěte si článek [Výběr sloupců v datové sadě](./select-columns-in-dataset.md) pro tipy. Popisuje několik běžných scénářů a tipů k používání **pravidel s pravidly** a možnostmi **názvu** .
  
1.  Odešlete kanál. Pokud máte velké množství dat, může to chvíli trvat.

    > [!IMPORTANT] 
    > Pokud máte sloupec ID, který má ID každého řádku, nebo textový sloupec, který obsahuje příliš mnoho jedinečných hodnot, může dojít k chybě **modelu výuky** , například "počet jedinečných hodnot ve sloupci:" {column_name} "je větší než povolený.
    >
    > Důvodem je to, že sloupec dosáhl prahové hodnoty jedinečných hodnot a příčinou může být nedostatek paměti. Pomocí možnosti [Upravit metadata](edit-metadata.md) můžete tento sloupec označit jako **nejasná funkce** a nebude se používat při výuce, nebo [extrahovat N-gram funkcí z modulu textu](extract-n-gram-features-from-text.md) do předběžného zpracování textového sloupce. Další podrobnosti o chybě najdete v [kódu chyby návrháře](././designer-error-codes.md) .

## <a name="model-interpretability"></a>Interpretace modelu

Interpretace modelu nabízí možnost pochopit model ML a prezentovat základní základnu pro rozhodování způsobem srozumitelným pro lidi.

Modul **vlakového modelu** v současné době podporuje [použití balíčku pro vyhodnocení pro vysvětlení modelů ml](https://docs.microsoft.com/azure/machine-learning/how-to-machine-learning-interpretability-aml#generate-feature-importance-values-via-remote-runs). Podporují se tyto integrované algoritmy:

- Lineární regrese
- Regrese neurální sítě
- Logistická regrese se dvěma třídami
- Support Vector Machine (SVM) se dvěma třídami
- Rozhodovací doménová struktura s více třídami

Chcete-li vygenerovat vysvětlení modelu, můžete vybrat **hodnotu true** v rozevíracím seznamu **vysvětlení modelu** v modulu výuka modelu. Ve výchozím nastavení je v modulu **vlakového modelu** nastaveno na hodnotu false. Všimněte si, že generování vysvětlení vyžaduje dodatečné výpočetní náklady.

![Snímek obrazovky znázorňující zaškrtávací políčko pro vysvětlení modelu](./media/module/train-model-explanation-checkbox.png)

Po dokončení kanálu můžete navštívit kartu **vysvětlení** v pravém podokně modulu **výuka modelu** a prozkoumat výkon modelu, datovou sadu a důležitost funkcí.

![Snímek obrazovky znázorňující grafy vysvětlující model](./media/module/train-model-explanations-tab.gif)

Další informace o použití vysvětlení modelů v Azure Machine Learning najdete v článku o [interpretaci modelů ml](https://docs.microsoft.com/azure/machine-learning/how-to-machine-learning-interpretability-aml#generate-feature-importance-values-via-remote-runs).

## <a name="results"></a>Výsledky

Po vyškolení modelu:


+ Pokud chcete model použít v jiných kanálech, vyberte modul a na kartě **výstup** v pravém panelu vyberte ikonu **Registrovat datovou sadu** . K uloženým modelům získáte přístup v paletě modulu v části **datové sady**.

+ Pokud chcete model použít při předvídání nových hodnot, připojte ho k modulu [skóre modelu](./score-model.md) spolu s novými vstupními daty.


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 