---
title: Vysvětlení automatizovaných výsledků na ML
titleSuffix: Azure Machine Learning
description: Naučte se zobrazovat a pochopit grafy a metriky pro každé z automatizovaných běhů strojového učení.
services: machine-learning
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 93695e0bbcb81a570519a6f74cfdeab4ef85f076
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489390"
---
# <a name="understand-automated-machine-learning-results"></a>Vysvětlení automatizovaných výsledků strojového učení
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak zobrazit a pochopit grafy a metriky pro jednotlivé běhy automatizovaného strojového učení. 

Další informace:
+ [Metriky, grafy a křivky pro modely klasifikace](#classification)
+ [Metriky, grafy a grafy pro regresní modely](#regression)
+ [Závažnost modelu a důležitost funkcí](#explain-model)

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes

* Vytvořte experiment pro automatizované spuštění strojového učení, a to buď pomocí sady SDK, nebo v Azure Machine Learning Studiu.

    * Použití sady SDK k sestavení [modelu klasifikace](how-to-auto-train-remote.md) nebo [regresního modelu](tutorial-auto-train-models.md)
    * Pomocí nástroje [Azure Machine Learning Studio](how-to-create-portal-experiments.md) můžete vytvořit model klasifikace nebo regrese tím, že nahrajete příslušná data.

## <a name="view-the-run"></a>Zobrazit běh

Po spuštění automatizovaného experimentu machine learningu se v pracovním prostoru Machine Learning dá najít historie běhů. 

1. Přejděte do svého pracovního prostoru.

1. V levém panelu pracovního prostoru vyberte **experimenty**.

   ![Snímek nabídky experimentů](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. V seznamu experimentů vyberte tu, kterou chcete prozkoumat.

   [seznam experimentů ![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. V dolní tabulce vyberte **číslo spuštění**.

   [Spuštění experimentu![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png))

1. V tabulce iterace vyberte **číslo iterace** pro model, který chcete prozkoumat.

   [model experimentování ![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

Stejné výsledky se zobrazí také při spuštění, když použijete [pomůcku `RunDetails`Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).

## <a name="classification"></a>Výsledky klasifikace

Thee následující metriky a grafy jsou k dispozici pro každý model klasifikace, který sestavíte pomocí možností automatizovaného strojového učení Azure Machine Learning

+ [Metriky](#classification-metrics)
+ [Nejasná matice](#confusion-matrix)
+ [Přesnost – graf odvolání](#precision-recall-chart)
+ [Provozní charakteristiky přijímače (nebo ROC)](#roc)
+ [Zvednutí křivky](#lift-curve)
+ [Křivka zisků](#gains-curve)
+ [Graf kalibrace](#calibration-plot)

### <a name="classification-metrics"></a>Metriky klasifikace

Následující metriky jsou uloženy v každé iteraci spuštění pro úlohu klasifikace.

|Metrika|Popis|Výpočet|Další parametry
--|--|--|--|
AUC_Macro| AUC je oblast pod křivkou s provozní charakteristikou přijímače. Makro je aritmetická střední hodnota AUC pro každou třídu.  | [Kalkulační](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | Average = "Macro"|
AUC_Micro| AUC je oblast pod křivkou s provozní charakteristikou přijímače. Hodnota mikro je vypočítána globálně kombinací skutečných kladných hodnot a falešně pozitivních hodnot z každé třídy.| [Kalkulační](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | Average = "mikro"|
AUC_Weighted  | AUC je oblast pod křivkou s provozní charakteristikou přijímače. Váže se aritmetický průměr skóre pro každou třídu, vážená o počet hodnot true instance v každé třídě.| [Kalkulační](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|Average = vážený
accuracy|Přesnost je procento předpokládaných popisků, které přesně odpovídají skutečným popiskům. |[Kalkulační](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Žádné|
average_precision_score_macro|Průměrná přesnost shrnuje křivku pro odvolání přesnosti jako vážený průměr přesností dosaženého při každé prahové hodnotě, přičemž se zvýší počet odvolání z předchozí prahové hodnoty použité jako váha. Makro je aritmetický průměr skóre každé třídy přesnosti.|[Kalkulační](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Average = "Macro"|
average_precision_score_micro|Průměrná přesnost shrnuje křivku pro odvolání přesnosti jako vážený průměr přesností dosaženého při každé prahové hodnotě, přičemž se zvýší počet odvolání z předchozí prahové hodnoty použité jako váha. Hodnota mikro je vypočítána globálně kombinací pravdivé kladné hodnoty a falešně pozitivních hodnot při každém přerušení.|[Kalkulační](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Average = "mikro"|
average_precision_score_weighted|Průměrná přesnost shrnuje křivku pro odvolání přesnosti jako vážený průměr přesností dosaženého při každé prahové hodnotě, přičemž se zvýší počet odvolání z předchozí prahové hodnoty použité jako váha. Vážená průměrná hodnota průměrného skóre přesnosti pro každou třídu, vážená o počet true instancí v každé třídě.|[Kalkulační](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Average = vážený|
balanced_accuracy|Vyvážená přesnost je aritmetickým průměrem odvolání jednotlivých tříd.|[Kalkulační](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Average = "Macro"|
f1_score_macro|Známkou F1 je harmonický průměr přesnosti a odvolání. Makro je aritmetický průměr skóre F1 pro každou třídu.|[Kalkulační](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Average = "Macro"|
f1_score_micro|Známkou F1 je harmonický průměr přesnosti a odvolání. Hodnota mikro je vypočítávána globálně vynásobením celkového počtu skutečných kladných hodnot, falešně negativních hodnot a falešně pozitivních hodnot.|[Kalkulační](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Average = "mikro"|
f1_score_weighted|Známkou F1 je harmonický průměr přesnosti a odvolání. Vážený průměr podle četnosti třídy F1 pro každou třídu|[Kalkulační](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Average = vážený|
log_loss|Jedná se o funkci ztráty použitou v rámci (MULTINOMIAL) logistické regrese a rozšíření, jako jsou například sítě neuronové, definované jako negativní protokol – pravděpodobnost hodnot true pro pravděpodobnostní třídění předpovědi. V případě jedné ukázky s true Label YT v {0,1} a odhadované pravděpodobnosti YP, že YT = 1 se ztratí protokol – log P (yt&#124;YP) =-(yt log (YP) + (1-YT) log (1-YP)).|[Kalkulační](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Žádné|
norm_macro_recall|Normalizované odvolání makra představuje normalizované odvolání makra, takže náhodný výkon má skóre 0 a dokonalý výkon má skóre 1. Toho dosáhnete pomocí norm_macro_recall: = (recall_score_macro-R)/(1-R), kde R je očekávaná hodnota recall_score_macro pro náhodný předpovědi (tj. R = 0,5 pro binární klasifikaci a R = (1/C) pro problémy s klasifikací C-Class).|[Kalkulační](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Average = "Macro" |
precision_score_macro|Přesnost je procento prvků, které jsou označeny jako určitá třída, která ve skutečnosti je v této třídě. Makro je aritmetický průměr přesnosti pro každou třídu.|[Kalkulační](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Average = "Macro"|
precision_score_micro|Přesnost je procento prvků, které jsou označeny jako určitá třída, která ve skutečnosti je v této třídě. Hodnota mikro se vypočítává globálně pomocí inventarizace celkových skutečných hodnot a falešně pozitivních hodnot.|[Kalkulační](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Average = "mikro"|
precision_score_weighted|Přesnost je procento prvků, které jsou označeny jako určitá třída, která ve skutečnosti je v této třídě. Váže se aritmetický průměr pro každou třídu, vážený podle počtu skutečných instancí v každé třídě.|[Kalkulační](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Average = vážený|
recall_score_macro|Odvolání je procentuální podíl prvků ve skutečnosti v určité třídě, které jsou označeny správně. Makro je aritmetický průměr odvolání každé třídy.|[Kalkulační](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Average = "Macro"|
recall_score_micro|Odvolání je procentuální podíl prvků ve skutečnosti v určité třídě, které jsou označeny správně. Hodnota mikro se vypočítává globálně pomocí inventarizace celkových skutečných hodnot, falešně negativních hodnot a falešně pozitivních hodnot.|[Kalkulační](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Average = "mikro"|
recall_score_weighted|Odvolání je procentuální podíl prvků ve skutečnosti v určité třídě, které jsou označeny správně. Váže se aritmetický průměr pro každou třídu, vážený podle počtu skutečných instancí v každé třídě.|[Kalkulační](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Average = vážený|
weighted_accuracy|Vážená přesnost je přesnost, při které se váha předaná jednotlivým příkladům rovná poměru skutečných instancí v této skutečné třídě v tomto příkladu.|[Kalkulační](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight je vektor, který se rovná podílu třídy pro každý prvek v cíli.|

### <a name="confusion-matrix"></a>Nejasná matice

K popisu výkonu modelu klasifikace se používá nejasná matice. Každý řádek zobrazí instance třídy true a každý sloupec představuje instance předpovídané třídy. Matice nejasností zobrazuje správně klasifikované popisky a nesprávně klasifikované popisky pro daný model.

Pro problémy s klasifikací Azure Machine Learning automaticky poskytuje záměnu pro každý model, který je sestaven. U každé nejasnosti se v automatizovaném ML zobrazí frekvence každého předpovězeného popisku a průsečík každého true Label. Čím tmavší je barva, tím vyšší je počet v této konkrétní části matice. V ideálním případě by nejtmavší barvy byly podél diagonály matice. 

Příklad 1: klasifikační model s nízkou přesností ![klasifikační model s nízkou přesností](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

Příklad 2: model klasifikace s vysokou přesností (ideální) ![klasifikační model s vysokou přesností](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)


### <a name="precision-recall-chart"></a>Přesnost – graf odvolání

Pomocí tohoto grafu můžete porovnat křivky přesnosti a odvolání pro každý model, abyste zjistili, který model má přijatelný vztah mezi přesností a odvoláním konkrétního obchodního problému. Tento graf znázorňuje průměrnou přesnost v makru – odvolání, průměrnou přesnost při odvolání a vrácení přesnosti přidružené ke všem třídám pro model.

Přesnost termínu představuje, že schopnost klasifikátoru správně popsat všechny instance. Odvolání představuje schopnost klasifikátoru najít všechny instance konkrétního popisku. Křivka s přesností na odvolání zobrazuje vztah mezi těmito dvěma koncepty. V ideálním případě by měl mít model 100% přesnost a 100% přesnost.

Příklad 1: klasifikační model s nízkou přesností a nízkým odvoláním ![klasifikační model s nízkou přesností a nízkým navrácením](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

Příklad 2: klasifikační model s ~ 100% Precision a ~ 100% Recalling (ideální) ![model klasifikace s vysokou přesností a Recall](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)

### <a name="roc"></a>ODDĚLOVAČ

Provozní charakteristika přijímače (nebo ROC) je graf správně klasifikovaných popisků vs. nesprávně klasifikované popisky pro konkrétní model. Křivka ROC může být méně informativní při výuce modelů u datových sad s vysokým posunem, protože se nezobrazují falešně pozitivní popisky.

Příklad 1: klasifikační model s dolními popisky s nízkou hodnotou a horním nepravdivým popiskem ![klasifikační model s dolními a horními popisky s hodnotou false](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

Příklad 2: klasifikační model s horními popisky a dolními nepravdivými popisky ![klasifikační model s horními popisky true a s dolními falešnými popisky](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)

### <a name="lift-curve"></a>Zvednutí křivky

Můžete porovnat výtah modelu sestavený automaticky s Azure Machine Learning do směrného plánu, aby bylo možné zobrazit hodnoty pro konkrétní model.

K vyhodnocení výkonu modelu klasifikace se používají výtahové grafy. Ukazuje, kolik lépe můžete očekávat s modelem v porovnání s modelem bez modelu. 

Příklad 1: model je vykonává horší než model náhodného výběru ![klasifikační model, který je horší než model náhodného výběru](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)

Příklad 2: model je vylepšený, než model náhodného výběru ![model klasifikace, který zajišťuje lepší](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)

### <a name="gains-curve"></a>Křivka zisků

Graf zisků vyhodnocuje výkon klasifikačního modelu podle každé části dat. Zobrazuje se pro každý percentil sady dat, kolik lepších výsledků můžete očekávat v porovnání s modelem náhodného výběru.

Pomocí grafu kumulativních zisků vám pomůžete vybrat přerušení klasifikace pomocí procenta, které odpovídá požadovanému zisku z modelu. Tyto informace poskytují další způsob, jak si prohlédnout výsledky v doprovodném grafu výtahu.

Příklad 1: klasifikační model s minimálním ziskem ![model klasifikace s minimálním ziskem](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

Příklad 2: model klasifikace s významným ziskem ![model klasifikace s významným ziskem](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)

### <a name="calibration-plot"></a>Graf kalibrace

Pro všechny problémy s klasifikací můžete zkontrolovat kalibrační čáru pro střední průměr, průměr makra a každou třídu v daném prediktivním modelu. 

Graf kalibrace se používá k zobrazení spolehlivosti prediktivního modelu. Zobrazuje vztah mezi předpovězenou pravděpodobností a skutečnou pravděpodobností, kde "pravděpodobnost" představuje pravděpodobnost, že konkrétní instance patří do nějakého popisku. Dobře kalibrovaný model zarovnává se čárou y = x, kde je v jeho předpovědi rozumně oprávněně zajistině. Model s vysokou jistotou se zarovnává s čárou y = 0, kde předpokládaná pravděpodobnost je přítomna, ale neexistuje žádná skutečná pravděpodobnost.

Příklad 1: model s větším kalibrovaným ![ s dobře kalibrovaným modelem](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

Příklad 2: model s navětším jistým ![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression"></a>Výsledky regrese

Thee následující metriky a grafy jsou k dispozici pro každý regresní model, který sestavíte pomocí možností automatizovaného strojového učení Azure Machine Learning

+ [Metriky](#reg-metrics)
+ [Předpověď oproti hodnotě true](#pvt)
+ [Histogram zbytků](#histo)


### <a name="reg-metrics"></a>Regresní metriky

Následující metriky jsou uloženy v každé iteraci spuštění pro úlohu regrese nebo předpovědi.

|Metrika|Popis|Výpočet|Další parametry
--|--|--|--|
explained_variance|Vysvětlení odchylky je poměr, ke kterému chcete vytvořit matematické modely pro variaci dané datové sady. Jedná se o procento snížení odchylky původních dat s odchylkou chyb. Pokud je střední hodnota chyb 0, je rovna vysvětlit odchylku.|[Kalkulační](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Žádné|
r2_score|R2 je koeficient určení nebo procento snížení počtu kvadratických chyb v porovnání se základním modelem, který má za následek výstup středníku. |[Kalkulační](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Žádné|
spearman_correlation|Korelace Spearman je neparametrová míra monotonicity vztahu mezi dvěma datovými sadami. Na rozdíl od korelace Pearsonova nepředpokládá korelace Spearman, že obě datové sady jsou normálně distribuovány. Podobně jako u jiných korelačních korelace se tato hodnota liší v rozsahu-1 až + 1 s 0, což neimplikuje žádnou korelaci. Korelace-1 nebo + 1 implikuje přesný vztah monotónní. Kladné korelace znamenají, že při zvýšení hodnoty x, takže ano. Záporná korelace implikuje za následek, že při zvyšování hodnoty x se sníží hodnota y.|[Kalkulační](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Žádné|
mean_absolute_error|Střední absolutní chyba je očekávaná hodnota absolutní hodnoty rozdíl mezi cílem a předpovědí.|[Kalkulační](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Žádné|
normalized_mean_absolute_error|Normalizovaná střední chyba znamená absolutní chybu dělenou rozsahem dat.|[Kalkulační](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Rozdělit podle rozsahu dat|
median_absolute_error|Střední absolutní chyba je medián všech absolutních rozdílů mezi cílem a předpovědi. Tato ztráta je robustní pro odlehlé hodnoty.|[Kalkulační](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Žádné|
normalized_median_absolute_error|Normalizovaná absolutní chyba mediánu je absolutní chyba mediánu dělená rozsahem dat.|[Kalkulační](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Rozdělit podle rozsahu dat|
root_mean_squared_error|Kořenová střední Chyba je druhá odmocnina očekávaného čtvercového rozdílu mezi cílem a předpovědí.|[Kalkulační](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Žádné|
normalized_root_mean_squared_error|Normalizovaný základní Průměrná chyba je kořenová chyba, která je dělená rozsahem dat.|[Kalkulační](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Rozdělit podle rozsahu dat|
root_mean_squared_log_error|Hodnota původní střední Chyba protokolu je druhá odmocnina očekávaného čtvercového logaritmu chyby.|[Kalkulační](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Žádné|
normalized_root_mean_squared_log_error|Normalizovaná chyba na čtvercovém středním významu protokolu je chyba v kořenovém středním významu protokolu dělená rozsahem dat|[Kalkulační](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Rozdělit podle rozsahu dat|

### <a name="pvt"></a>Předpověď oproti hodnotě true

Předpovězené vs. hodnota true zobrazuje vztah mezi předpokládanou hodnotou a korelaci pravdivé hodnoty pro problém regrese. Tento graf lze použít k měření výkonu modelu jako blíže řádku y = x, protože jsou předpovězené hodnoty lepší přesností prediktivního modelu.

Po každém spuštění můžete zobrazit předpokládaný a pravdivý graf pro každý regresní model. Za účelem ochrany ochrany osobních údajů jsou hodnoty rozdělený dohromady a velikost každé přihrádky se v dolní části oblasti grafu zobrazuje jako pruhový graf. Prediktivní model můžete porovnat s plochým barevným nádechem, na kterém se zobrazují okraje chyb, oproti ideální hodnotě, kde by měl model být.

Příklad 1: regresní model s nízkou přesností v předpovědi ![regresní model s nízkou přesností v předpovědi](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

Příklad 2: regresní model s vysokou přesností ve svém předpovědi [![regresní model s vysokou přesností ve své předpovědi](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histo"></a>Histogram zbytků

Reziduální představuje pozorovanou hodnotu y – předpovězené y. Chcete-li zobrazit marži chyby s nízkou špičkou, histogram zbytku by měl být ve tvaru špičky uprostřed na střed 0. 

Příklad 1: regresní model s bias v jeho chybách ![SA regresní model s posunem v jeho chybách](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

Příklad 2: regresní model s větší rovnoměrné distribucí chyb ![regresní model s větším množstvím rovnoměrné distribuce chyb](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="explain-model"></a>Závažnost modelu a důležitost funkcí

Důležitost funkcí vám umožní zjistit, jak cenná je každá funkce v konstrukci modelu. Tento výpočet je ve výchozím nastavení vypnutý, protože může významně zvýšit dobu běhu.   Můžete povolit vysvětlení modelu pro všechny modely nebo vysvětlit pouze model, který nejlépe vyhovuje.

Můžete zkontrolovat skóre důležitosti pro daný model a také pro jednotlivé třídy v prediktivním modelu. Můžete vidět na funkci, jak důležitost porovnává s každou třídou a celkovou.

![Funkce vysvětlující možnosti](./media/how-to-understand-automated-ml/feature-importance.gif)

Další informace o povolení funkcí pro interpretaci najdete v tématu [postup](how-to-machine-learning-interpretability-automl.md) při povolování interpretace v rámci automatizovaných experimentů ml.

## <a name="next-steps"></a>Další kroky

+ Přečtěte si další informace o [automatizovaném ml](concept-automated-ml.md) v Azure Machine Learning.
+ Vyzkoušejte ukázkový Poznámkový blok pro [automatizovaný Machine Learning model vysvětlení](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation) .
