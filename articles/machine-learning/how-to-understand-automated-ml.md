---
title: Vyhodnotit výsledky experimentů AutoML
titleSuffix: Azure Machine Learning
description: Přečtěte si, jak zobrazit a vyhodnotit grafy a metriky pro každý z automatizovaných testů strojového učení.
services: machine-learning
author: aniththa
ms.author: anumamah
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.date: 10/09/2020
ms.topic: conceptual
ms.custom: how-to, contperfq2, automl
ms.openlocfilehash: fcbe0fc5049f6e892f80f048a885c75420bc636e
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93359081"
---
# <a name="evaluate-automated-machine-learning-experiment-results"></a>Vyhodnotit automatizované výsledky experimentování ve strojovém učení

V tomto článku se dozvíte, jak zobrazit a vyhodnotit výsledky automatizovaného strojového učení, automatizovaného ML a experimentů. Tyto experimenty se skládají z několika spuštění, kde každé spuštění vytvoří model. Pro usnadnění vyhodnocení jednotlivých modelů automaticky generuje automatizovaná ML metriky výkonu a grafy specifické pro váš typ experimentu. 

Například automatizované ML poskytuje různé grafy pro klasifikace a regresní modely. 

|Classification|Regrese
|---|---|
|<li> [Konfuzní matice](#confusion-matrix) <li>[Přesnost – graf odvolání](#precision-recall-chart) <li> [Provozní charakteristiky přijímače (nebo ROC)](#roc) <li> [Zvednutí křivky](#lift-curve)<li> [Křivka zisků](#gains-curve)<li> [Graf kalibrace](#calibration-plot) | <li> [Předpověď oproti hodnotě true](#pvt) <li> [Histogram zbytků](#histo)|

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si napřed bezplatný účet. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes

* Vytvořte experiment pro automatizované spuštění strojového učení, a to buď pomocí sady SDK, nebo v Azure Machine Learning Studiu.

    * Použití sady SDK k sestavení [modelu klasifikace](how-to-auto-train-remote.md) nebo [regresního modelu](tutorial-auto-train-models.md)
    * Pomocí nástroje [Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md) můžete vytvořit model klasifikace nebo regrese tím, že nahrajete příslušná data.

## <a name="view-run-results"></a>Zobrazit výsledky spuštění

Po dokončení automatizovaného experimentu machine learningu můžete v pracovním prostoru Machine Learning najít historii běhů prostřednictvím služby [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md). 

V případě experimentů sady SDK můžete stejné výsledky zobrazit během spuštění, když použijete `RunDetails` [widget Jupyter](/python/api/azureml-widgets/azureml.widgets?preserve-view=true&view=azure-ml-py).

Následující kroky a animace ukazují, jak zobrazit historii spuštění a metriky výkonu a grafy konkrétního modelu v studiu.

![Postup zobrazení historie spuštění a metriky výkonu modelu a grafů](./media/how-to-understand-automated-ml/view-run-metrics-ui.gif)

Zobrazení historie spuštění a metriky výkonu modelu a grafů v studiu: 

1. [Přihlaste se ke studiu](https://ml.azure.com/) a přejděte k pracovnímu prostoru.
1. V levém panelu pracovního prostoru vyberte možnost **spuštění**.
1. V seznamu experimentů vyberte tu, kterou chcete prozkoumat.
1. V dolní tabulce vyberte **běh**.
1. Na kartě **modely** vyberte **název algoritmu** pro model, který chcete prozkoumat.
1. Na kartě **metriky** vyberte metriky a grafy, které chcete pro daný model vyhodnotit. 


<a name="classification"></a> 

## <a name="classification-performance-metrics"></a>Metriky výkonu klasifikace

Následující tabulka shrnuje metriky výkonu modelu, které automatizované ML vypočítá pro každý model klasifikace vygenerovaný pro váš experiment. 

Metrika|Popis|Výpočet|Další parametry
--|--|--|--
AUC_macro| AUC je oblast pod křivkou s provozní charakteristikou přijímače. Makro je aritmetická střední hodnota AUC pro každou třídu.  | [Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | Average = "Macro"|
AUC_micro| AUC je oblast pod křivkou s provozní charakteristikou přijímače. Hodnota mikro je vypočítána globálně kombinací skutečných kladných hodnot a falešně pozitivních hodnot z každé třídy.| [Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | Average = "mikro"|
AUC_weighted  | AUC je oblast pod křivkou s provozní charakteristikou přijímače. Váže se aritmetický průměr skóre pro každou třídu, vážená o počet hodnot true instance v každé třídě.| [Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|Average = vážený
accuracy|Přesnost je procento předpokládaných popisků, které přesně odpovídají skutečným popiskům. |[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Žádné|
average_precision_score_macro|Průměrná přesnost shrnuje křivku pro odvolání přesnosti jako vážený průměr přesností dosaženého při každé prahové hodnotě, přičemž se zvýší počet odvolání z předchozí prahové hodnoty použité jako váha. Makro je aritmetický průměr skóre každé třídy přesnosti.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Average = "Macro"|
average_precision_score_micro|Průměrná přesnost shrnuje křivku pro odvolání přesnosti jako vážený průměr přesností dosaženého při každé prahové hodnotě, přičemž se zvýší počet odvolání z předchozí prahové hodnoty použité jako váha. Hodnota mikro je vypočítána globálně kombinací pravdivé kladné hodnoty a falešně pozitivních hodnot při každém přerušení.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Average = "mikro"|
average_precision_score_weighted|Průměrná přesnost shrnuje křivku pro odvolání přesnosti jako vážený průměr přesností dosaženého při každé prahové hodnotě, přičemž se zvýší počet odvolání z předchozí prahové hodnoty použité jako váha. Vážená průměrná hodnota průměrného skóre přesnosti pro každou třídu, vážená o počet true instancí v každé třídě.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Average = vážený|
balanced_accuracy|Vyvážená přesnost je aritmetickým průměrem odvolání jednotlivých tříd.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Average = "Macro"|
f1_score_macro|Známkou F1 je harmonický průměr přesnosti a odvolání. Makro je aritmetický průměr skóre F1 pro každou třídu.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Average = "Macro"|
f1_score_micro|Známkou F1 je harmonický průměr přesnosti a odvolání. Hodnota mikro je vypočítávána globálně vynásobením celkového počtu skutečných kladných hodnot, falešně negativních hodnot a falešně pozitivních hodnot.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Average = "mikro"|
f1_score_weighted|Známkou F1 je harmonický průměr přesnosti a odvolání. Vážený průměr podle četnosti třídy F1 pro každou třídu|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Average = vážený|
log_loss|Jedná se o funkci ztráty použitou v rámci (MULTINOMIAL) logistické regrese a rozšíření, jako jsou například sítě neuronové, definované jako negativní protokol – pravděpodobnost hodnot true pro pravděpodobnostní třídění předpovědi. V případě jedné ukázky s true Label YT v {0,1} a odhadované pravděpodobnosti YP, že YT = 1 je ztráta protokolu-log P (yt&#124;YP) =-(yt log (YP) + (1-YT) log (1-YP)).|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Žádné|
norm_macro_recall|Normalizované odvolání makra představuje normalizované odvolání makra, takže náhodný výkon má skóre 0 a dokonalý výkon má skóre 1. Toho je dosaženo norm_macro_recall: = (recall_score_macro-R)/(1-R), kde R je očekávaná hodnota recall_score_macro pro náhodný předpovědi (tj. R = 0,5 pro binární klasifikaci a R = (1/C) pro problémy klasifikace C-Class).|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Average = "Macro" |
precision_score_macro|Přesnost je procentuální podíl přesně předpokládaných prvků, které jsou správně označeny. Makro je aritmetický průměr přesnosti pro každou třídu.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Average = "Macro"|
precision_score_micro|Přesnost je procentuální podíl přesně předpokládaných prvků, které jsou správně označeny. Hodnota mikro se vypočítává globálně pomocí inventarizace celkových skutečných hodnot a falešně pozitivních hodnot.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Average = "mikro"|
precision_score_weighted|Přesnost je procentuální podíl přesně předpokládaných prvků, které jsou správně označeny. Váže se aritmetický průměr pro každou třídu, vážený podle počtu skutečných instancí v každé třídě.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Average = vážený|
recall_score_macro|Odvolání je procento správně označených prvků určité třídy. Makro je aritmetický průměr odvolání každé třídy.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Average = "Macro"|
recall_score_micro|Odvolání je procento správně označených prvků určité třídy. Hodnota mikro se vypočítává globálně pomocí inventarizace celkových skutečných hodnot, falešně negativních hodnot a falešně pozitivních hodnot.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Average = "mikro"|
recall_score_weighted|Odvolání je procento správně označených prvků určité třídy. Váže se aritmetický průměr pro každou třídu, vážený podle počtu skutečných instancí v každé třídě.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Average = vážený|
weighted_accuracy|Vážená přesnost je přesnost, při které se váha předaná jednotlivým příkladům rovná poměru skutečných instancí v této skutečné třídě v tomto příkladu.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight je vektor, který se rovná podílu třídy pro každý prvek v cíli.|

### <a name="binary-vs-multiclass-metrics"></a>Binární a vícevrstvé metriky

Automatizované ML nerozlišuje mezi binárními a více třídami metriky. Stejné metriky ověřování jsou hlášeny, zda má datová sada dvě třídy nebo více než dvě třídy. Některé metriky jsou však určeny pro klasifikaci s více třídami. Při použití na binární datovou sadu tyto metriky nepovažují žádnou třídu za `true` třídu, jak je možné očekávat. Metriky, které jsou jasně určeny pro více tříd, jsou s příponou `micro` , `macro` nebo `weighted` . Mezi příklady patří,,, `average_precision_score` `f1_score` `precision_score` `recall_score` a `AUC` .

Například namísto výpočtu odvolání jako `tp / (tp + fn)` je průměrně vyvolaná hodnota ve více třídách ( `micro` , `macro` nebo `weighted` ) v obou třídách binární datové sady klasifikace. To je ekvivalentní k výpočtu odvolání pro `true` třídu a `false` třídu samostatně a pak přebírání průměru dvou.

## <a name="confusion-matrix"></a>Konfuzní matice

Nejasná matice popisuje výkon klasifikačního modelu. Každý řádek zobrazí v datové sadě instance hodnoty true nebo actual, přičemž každý sloupec představuje instance třídy, která byla předpovězena modelem. 

U každé nejasné matrice zobrazuje automatizovaná paleta frekvence všech předpokládaných popisků (sloupců) porovnaných k hodnotě true Label (řádek). Čím tmavší je barva, tím vyšší je počet v této konkrétní části matice. 

### <a name="what-does-a-good-model-look-like"></a>Co vypadá dobrý model?

Nejasná matice porovnává skutečnou hodnotu datové sady s předpovězenými hodnotami, které model poskytl. Z tohoto důvodu mají modely strojového učení vyšší přesnost, pokud model má většinu hodnot po diagonále, což znamená, že model předpovídá správnou hodnotu. V případě, že model má nerovnováhu tříd, vyhledá vyměněný model pomocí matrice.

#### <a name="example-1-a-classification-model-with-poor-accuracy"></a>Příklad 1: klasifikační model s nízkou přesností
![Klasifikační model s nízkou přesností](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

#### <a name="example-2-a-classification-model-with-high-accuracy"></a>Příklad 2: model klasifikace s vysokou přesností 
![Klasifikační model s vysokou přesností](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)

##### <a name="example-3-a-classification-model-with-high-accuracy-and-high-bias-in-model-predictions"></a>Příklad 3: klasifikační model s vysokou přesností a vysokým posunem v modelu předpovědi
![Klasifikační model s vysokou přesností a vysokým posunem v modelu předpovědi](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-biased-model.png)

<a name="precision-recall-chart"></a>

## <a name="precision-recall-chart"></a>Přesnost – graf odvolání

Křivka s přesností na odvolání zobrazuje vztah mezi přesností a vrácením z modelu. Pojem přesnost představuje možnost, že model bude správně označovat všechny instance. Odvolání představuje schopnost klasifikátoru najít všechny instance konkrétního popisku.

Pomocí tohoto grafu můžete porovnat křivky přesnosti a odvolání pro každý model, abyste zjistili, který model má přijatelný vztah mezi přesností a odvoláním konkrétního obchodního problému. Tento graf znázorňuje průměrnou přesnost v makru – odvolání, průměrnou přesnost při odvolání a vrácení přesnosti přidružené ke všem třídám pro model. 

**Makro – průměr** počítá metriku nezávisle na každé třídě a pak bere průměr a současně zpracovává všechny třídy. **Střední** hodnota však agreguje příspěvky všech tříd pro výpočet průměru. Mikroprůměr je vhodnější, pokud je v datové sadě přítomna nerovnováha tříd.

### <a name="what-does-a-good-model-look-like"></a>Co vypadá dobrý model?
V závislosti na cíli podnikového problému se může způsobit, že se křivka s ideální přesností a odvolání může lišit. 

##### <a name="example-1-a-classification-model-with-low-precision-and-low-recall"></a>Příklad 1: klasifikační model s nízkou přesností a nízkým navrácením
![Klasifikační model s nízkou přesností a nízkým navrácením](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

##### <a name="example-2-a-classification-model-with-100-precision-and-100-recall"></a>Příklad 2: klasifikační model s ~ 100% přesností a ~ 100% Recalling 
![Vysoká přesnost a vrácení modelu klasifikace](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)

<a name="roc"></a>

## <a name="roc-chart"></a>Graf ROC

Provozní charakteristika (nebo ROC) přijímače je graf správně klasifikovaných popisků vs. nesprávně klasifikované popisky pro konkrétní model. Křivka ROC může být méně informativní při výuce modelů u datových sad s vysokou nevyrovnanou třídou, protože většina třídy může drowN příspěvek z minoritních tříd.

Oblast v grafu ROC můžete vizualizovat jako poměr správně klasifikovaných vzorků. Pokročilý uživatel grafu ROC může nahlížet nad rámec oblasti pod křivkou a získat Intuition pro pravdivé kladné a falešně pozitivní míry jako funkci prahové hodnoty klasifikace nebo hranice rozhodnutí.

### <a name="what-does-a-good-model-look-like"></a>Co vypadá dobrý model?
Nejlepší model je křivka ROC, která přistupuje k levému hornímu rohu s 100% true a hodnotou 0% falešně pozitivní hodnotou. Náhodný model se zobrazí jako plochý čára od levého dolního rohu k pravému hornímu rohu. Horší než náhodné by byly DIP pod řádkem y = x.

#### <a name="example-1-a-classification-model-with-low-true-labels-and-high-false-labels"></a>Příklad 1: klasifikační model s dolními popisky s nízkou hodnotou a horními nepravdivými popisky
![Klasifikační model s dolními popisky s nízkou hodnotou a horními nepravdivými popisky](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

#### <a name="example-2-a-classification-model-with-high-true-labels-and-low-false-labels"></a>Příklad 2: klasifikační model s horními popisky a popisky s nízkou hodnotou false

![klasifikační model s horními popisky s hodnotou true a s dolními falešnými popisky](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)


<a name="lift-curve"></a>

## <a name="lift-chart"></a>Nazvednutí grafu

Nazvednutí grafů vyhodnotí výkon modelů klasifikace. Graf výtahu ukazuje, kolikrát je model lépe vykonává v porovnání s náhodným modelem. Díky tomu získáte relativní výkon, který vezme v úvahu skutečnost, že klasifikace je těžší při zvýšení počtu tříd. Náhodný model nesprávně předpovídá větší zlomek vzorků z datové sady s deseti třídami v porovnání s datovou sadou se dvěma třídami.

Můžete porovnat výtah modelu sestavený automaticky s Azure Machine Learning do směrného plánu (náhodný model), chcete-li zobrazit hodnotu pro konkrétní model.

### <a name="what-does-a-good-model-look-like"></a>Co vypadá dobrý model?

Lepší model provádění bude mít křivku zvednutí, která je vyšší v grafu a dále od směrného plánu. 

#### <a name="example-1-a-classification-model-that-performs-poorly-compared-to-a-random-selection-model"></a>Příklad 1: klasifikační model, který se v porovnání s modelem náhodného výběru provádí špatně
![Model klasifikace, který je horší než model náhodného výběru](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)

#### <a name="example-2-a-classification-model-that-performs-better-than-a-random-selection-model"></a>Příklad 2: model klasifikace, který provádí lepší, než model náhodného výběru
![Model klasifikace, který zajišťuje lepší](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)

<a name="gains-curve"></a>

## <a name="cumulative-gains-chart"></a>Graf kumulativních zisků

Kumulativní graf kurzů vyhodnocuje výkon klasifikačního modelu podle každé části dat. Pro každý percentil sady dat graf znázorňuje, kolik dalších vzorků bylo přesně klasifikovaných v porovnání s modelem, který je vždy nesprávný. Tyto informace poskytují další způsob, jak si prohlédnout výsledky v doprovodném grafu výtahu.

Graf kumulativních zisků vám pomůže vybrat přerušení klasifikace pomocí procenta, které odpovídá požadovanému zisku z modelu. Pokud chcete zobrazit procentuální podíl vzorků, které byly správně klasifikovány na základě každého stupně spolehlivosti, můžete porovnat kumulativní graf nárůstu na účaří (nesprávný model).

#### <a name="what-does-a-good-model-look-like"></a>Co vypadá dobrý model?

Podobně jako u grafu výtahu je čím vyšší vaše křivka kumulativních zisků nad základní hodnotou, což je lepší model. Kromě toho se povýšení křivky kumulativních zisků nachází v levém horním rohu grafu, což je větší zisk, který váš model dosahuje oproti směrnému plánu. 

##### <a name="example-1-a-classification-model-with-minimal-gain"></a>Příklad 1: klasifikační model s minimálním ziskem
![klasifikační model s minimálním ziskem](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)

##### <a name="example-2-a-classification-model-with-significant-gain"></a>Příklad 2: klasifikační model s významným ziskem
![Klasifikační model s významným ziskem](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

<a name="calibration-plot"></a>

## <a name="calibration-chart"></a>Graf kalibrace

Graf kalibrace zobrazuje spolehlivost prediktivního modelu. Zobrazuje vztah mezi předpovězenou pravděpodobností a skutečnou pravděpodobností, kde "pravděpodobnost" představuje pravděpodobnost, že konkrétní instance patří do nějakého popisku.

Pro všechny problémy s klasifikací můžete zkontrolovat kalibrační čáru pro střední průměr, průměr makra a každou třídu v daném prediktivním modelu.

**Makro – průměr** vypočítá metriku nezávisle na každé třídě a pak vypočítá průměr a zpracuje všechny třídy rovnoměrně. **Střední** hodnota však agreguje příspěvky všech tříd pro výpočet průměru. 

### <a name="what-does-a-good-model-look-like"></a>Co vypadá dobrý model?
Dobře kalibrovaný model zarovnává se čárou y = x, kde správně předpovídá pravděpodobnost, že vzorky patří do jednotlivých tříd. Model s nadjistým využitím předpovídání pravděpodobností téměř nula a jedna, zřídka nejisté o třídě jednotlivých vzorků.

#### <a name="example-1-a-well-calibrated-model"></a>Příklad 1: dobře kalibrovaný model
![ dobře kalibrovaný model](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

#### <a name="example-2-an-over-confident-model"></a>Příklad 2: model s nadjistým využitím
![Model s vysokou jistotou](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)


<a name="regression"></a> 

## <a name="regression-performance-metrics"></a>Metriky výkonu regrese

Následující tabulka shrnuje metriky výkonu modelu, které automatizované ML vypočítá pro každou regresi nebo model prognózy, který je vygenerovaný pro váš experiment. 

|Metrika|Popis|Výpočet|Další parametry
--|--|--|--|
explained_variance|Vysvětlení odchylky je poměr, ke kterému chcete vytvořit matematické modely pro variaci dané datové sady. Jedná se o procento snížení odchylky původních dat s odchylkou chyb. Pokud je střední hodnota chyb 0, rovná se součiniteli určení (viz r2_score níže).|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Žádné|
r2_score|R ^ 2 je koeficient stanovení nebo procento snížení počtu kvadratických chyb v porovnání se základním modelem, který má za následek výstup středníku. |[Výpočet](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Žádné|
spearman_correlation|Korelace Spearman je neparametrová míra monotonicity vztahu mezi dvěma datovými sadami. Na rozdíl od korelace Pearsonova nepředpokládá korelace Spearman, že obě datové sady jsou normálně distribuovány. Podobně jako u jiných korelačních korelace se tato hodnota liší v rozsahu-1 až + 1 s 0, což neimplikuje žádnou korelaci. Korelace-1 nebo + 1 implikuje přesný vztah monotónní. Kladné korelace znamenají, že při zvýšení hodnoty x, takže ano. Záporná korelace implikuje za následek, že při zvyšování hodnoty x se sníží hodnota y.|[Výpočet](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Žádné|
mean_absolute_error|Střední absolutní chyba je očekávaná hodnota absolutní hodnoty rozdíl mezi cílem a předpovědí.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Žádné|
normalized_mean_absolute_error|Normalizovaná střední chyba znamená absolutní chybu dělenou rozsahem dat.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Rozdělit podle rozsahu dat|
median_absolute_error|Střední absolutní chyba je medián všech absolutních rozdílů mezi cílem a předpovědi. Tato ztráta je robustní pro odlehlé hodnoty.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Žádné|
normalized_median_absolute_error|Normalizovaná absolutní chyba mediánu je absolutní chyba mediánu dělená rozsahem dat.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Rozdělit podle rozsahu dat|
root_mean_squared_error|Kořenová střední Chyba je druhá odmocnina očekávaného čtvercového rozdílu mezi cílem a předpovědí.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Žádné|
normalized_root_mean_squared_error|Normalizovaný základní Průměrná chyba je kořenová chyba, která je dělená rozsahem dat.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Rozdělit podle rozsahu dat|
root_mean_squared_log_error|Hodnota původní střední Chyba protokolu je druhá odmocnina očekávaného čtvercového logaritmu chyby.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Žádné|
normalized_root_mean_squared_log_error|Normalizovaná chyba na čtvercovém středním významu protokolu je chyba v kořenovém středním významu protokolu dělená rozsahem dat|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Rozdělit podle rozsahu dat|

<a name="pvt"></a>

## <a name="predicted-vs-true-chart"></a>Předpokládaný vs. pravdivý graf

Předpovězené vs. hodnota true zobrazuje vztah mezi předpokládanou hodnotou a korelaci pravdivé hodnoty pro problém regrese. 

Po každém spuštění můžete zobrazit předpokládaný a pravdivý graf pro každý regresní model. Za účelem ochrany ochrany osobních údajů jsou hodnoty rozdělený dohromady a velikost každé přihrádky se v dolní části oblasti grafu zobrazuje jako pruhový graf. Prediktivní model můžete porovnat s plochým barevným nádechem, na kterém se zobrazují okraje chyb, oproti ideální hodnotě, kde by měl model být.

### <a name="what-does-a-good-model-look-like"></a>Co vypadá dobrý model?
Tento graf lze použít k měření výkonu modelu jako bližšího k řádku y = x, což jsou předpovězené hodnoty, což je lepší výkon prediktivního modelu.

#### <a name="example-1-a-regression-model-with-low-performance"></a>Příklad 1: regresní model s nízkým výkonem
![Regresní model s nízkou přesností v předpovědi](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

#### <a name="example-2-a-regression-model-with-high-performance"></a>Příklad 2: regresní model s vysokým výkonem
![Regresní model s vysokou přesností ve svém předpovědi](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)

<a name="histo"></a> 

## <a name="histogram-of-residuals-chart"></a>Histogram grafu zbytků

Automatizované ML automaticky poskytuje graf reziduálního grafu pro zobrazení distribuce chyb v předpovědi regresního modelu. Reziduální je rozdíl mezi předpověď a skutečnou hodnotou ( `y_pred - y_true` ). 

### <a name="what-does-a-good-model-look-like"></a>Co vypadá dobrý model?
Chcete-li zobrazit marži chyby s nízkou špičkou, histogram zbytku by měl být vytvořen jako křivka špičky na střed nula.

#### <a name="example-1-a-regression-model-with-bias-in-its-errors"></a>Příklad 1: regresní model s posunem v jeho chybách
![SA regresní model s posunem v jeho chybách](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

#### <a name="example-2-a-regression-model-with-a-more-even-distribution-of-errors"></a>Příklad 2: regresní model s větší distribucí chyb
![Regresní model s větší rovnoměrné distribucí chyb](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

<a name="explain-model"></a>

## <a name="model-interpretability-and-feature-importance"></a>Závažnost modelu a důležitost funkcí
Automatizované ML poskytuje řídicí panel pro vyhodnocení pro vaše běhy na strojovém učení.

Další informace o povolení funkcí pro interpretaci najdete v tématu věnovaném [interpretaci: vysvětlení modelů v automatizovaném strojovém učení](how-to-machine-learning-interpretability-automl.md).

> [!NOTE]
> ForecastTCN model není aktuálně podporován klientem vysvětlení. Tento model nevrátí řídicí panel vysvětlení, pokud je vrácen jako nejlepší model, a nepodporuje spuštění vysvětlení na vyžádání.

## <a name="next-steps"></a>Další kroky

+ Přečtěte si další informace o [automatizovaném ml](concept-automated-ml.md) v Azure Machine Learning.
+ Vyzkoušejte si vzor [automatizovaného strojového učení s vysvětlením](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) ukázkových poznámkových bloků.