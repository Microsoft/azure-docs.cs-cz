---
title: Vysvětlení automatizovaných výsledků na ML
titleSuffix: Azure Machine Learning
description: Naučte se zobrazovat a pochopit grafy a metriky pro každé z automatizovaných běhů strojového učení.
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: 81f17de7627658b756edd19438a80fb32add859d
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974083"
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

   ![Snímek obrazovky nabídky experimentu](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. V seznamu experimentů vyberte tu, kterou chcete prozkoumat.

   [seznam experimentů ![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. V dolní tabulce vyberte **běh**.

   [Spuštění experimentu![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png))

1. V části modely vyberte **název algoritmu** pro model, který chcete prozkoumat.

   [model experimentování ![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

Stejné výsledky se zobrazí také při spuštění, když použijete [pomůcku `RunDetails`Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).

## <a name="classification"></a>Výsledky klasifikace

Thee následující metriky a grafy jsou k dispozici pro každý model klasifikace, který sestavíte pomocí možností automatizovaného strojového učení Azure Machine Learning

+ [Metriky](#classification-metrics)
+ [Chybová matice](#confusion-matrix)
+ [Graf přesnosti a úplnosti](#precision-recall-chart)
+ [Příjemce provozní vlastnosti (nebo roc s více TŘÍDAMI)](#roc)
+ [Výtah křivky](#lift-curve)
+ [Zvýšení křivky](#gains-curve)
+ [Diagram kalibrací](#calibration-plot)

### <a name="classification-metrics"></a>Klasifikace metriky

Následující metriky jsou uloženy v každé iteraci spuštění pro úlohu klasifikace.

|Metrika|Popis|Výpočet|Další parametry
--|--|--|--|
AUC_Macro| AUC je oblasti pod křivkou charakteristiku provozní příjemce. Makro je aritmetický průměr AUC pro každou třídu.  | [Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | Průměrná = "makro"|
AUC_Micro| AUC je oblasti pod křivkou charakteristiku provozní příjemce. Hodnota mikro je vypočítána globálně kombinací skutečných kladných hodnot a falešně pozitivních hodnot z každé třídy.| [Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | Průměrná = "micro"|
AUC_Weighted  | AUC je oblasti pod křivkou charakteristiku provozní příjemce. Váže se aritmetický průměr skóre pro každou třídu, vážená o počet hodnot true instance v každé třídě.| [Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|Průměrná = "váha"
accuracy|Přesnost je procento předpokládané popisky, které přesně odpovídají true popisky. |[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Žádné|
average_precision_score_macro|Průměrná přesnost shrnuje křivky přesnosti a úplnosti jako vážený průměr přesnosti dosáhnout při každé prahové hodnotě, zvýšení spojené s vracením z předchozí prahové hodnoty použít jako váhu. Makro je aritmetický průměr skóre každé třídy přesnosti.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Průměrná = "makro"|
average_precision_score_micro|Průměrná přesnost shrnuje křivky přesnosti a úplnosti jako vážený průměr přesnosti dosáhnout při každé prahové hodnotě, zvýšení spojené s vracením z předchozí prahové hodnoty použít jako váhu. Hodnota mikro je vypočítána globálně kombinací pravdivé kladné hodnoty a falešně pozitivních hodnot při každém přerušení.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Průměrná = "micro"|
average_precision_score_weighted|Průměrná přesnost shrnuje křivky přesnosti a úplnosti jako vážený průměr přesnosti dosáhnout při každé prahové hodnotě, zvýšení spojené s vracením z předchozí prahové hodnoty použít jako váhu. Vážená průměrná hodnota průměrného skóre přesnosti pro každou třídu, vážená o počet true instancí v každé třídě.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Průměrná = "váha"|
balanced_accuracy|Vyvážený přesnost je aritmetický průměr o odvolání pro každou třídu.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Průměrná = "makro"|
f1_score_macro|F1 skóre je průměr přesnosti a odvolání. Makro je aritmetický průměr skóre F1 pro každou třídu.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Průměrná = "makro"|
f1_score_micro|F1 skóre je průměr přesnosti a odvolání. Hodnota mikro je vypočítávána globálně vynásobením celkového počtu skutečných kladných hodnot, falešně negativních hodnot a falešně pozitivních hodnot.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Průměrná = "micro"|
f1_score_weighted|F1 skóre je průměr přesnosti a odvolání. Vážený průměr třídy frekvence skóre F1 pro každou třídu|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Průměrná = "váha"|
log_loss|Toto je ztráta funkce použitá v logistické regrese (multinomial) a rozšíření jako jsou neuronové sítě definované jako záporné pravděpodobnost protokolu true popisků zadaný pravděpodobnostní třídění předpovědi. V případě jedné ukázky s true Label YT v {0,1} a odhadované pravděpodobnosti YP, že YT = 1 se ztratí protokol – log P (yt&#124;YP) =-(yt log (YP) + (1-YT) log (1-YP)).|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Žádné|
norm_macro_recall|Normalizovaná si možná Vzpomínáte – makro je makro si možná Vzpomínáte, normalizovaná tak, aby náhodná výkonu má skóre 0 a ideální výkon má skóre 1. Toho je dosaženo norm_macro_recall: = (recall_score_macro-R)/(1-R), kde R je očekávaná hodnota recall_score_macro pro náhodný předpovědi (tj. R = 0,5 pro binární klasifikaci a R = (1/C) pro problémy klasifikace C-Class).|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Average = "Macro" |
precision_score_macro|Přesnost je procentuální podíl přesně předpokládaných prvků, které jsou správně označeny. Makro je aritmetický průměr přesnosti pro každou třídu.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Průměrná = "makro"|
precision_score_micro|Přesnost je procentuální podíl přesně předpokládaných prvků, které jsou správně označeny. Hodnota mikro se vypočítává globálně pomocí inventarizace celkových skutečných hodnot a falešně pozitivních hodnot.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Průměrná = "micro"|
precision_score_weighted|Přesnost je procentuální podíl přesně předpokládaných prvků, které jsou správně označeny. Váže se aritmetický průměr pro každou třídu, vážený podle počtu skutečných instancí v každé třídě.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Průměrná = "váha"|
recall_score_macro|Odvolání je procento správně označených prvků určité třídy. Makro je aritmetický průměr odvolání každé třídy.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Průměrná = "makro"|
recall_score_micro|Odvolání je procento správně označených prvků určité třídy. Hodnota mikro se vypočítává globálně pomocí inventarizace celkových skutečných hodnot, falešně negativních hodnot a falešně pozitivních hodnot.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Průměrná = "micro"|
recall_score_weighted|Odvolání je procento správně označených prvků určité třídy. Váže se aritmetický průměr pro každou třídu, vážený podle počtu skutečných instancí v každé třídě.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Průměrná = "váha"|
weighted_accuracy|Vážená přesnost je přesnost, při které se váha předaná jednotlivým příkladům rovná poměru skutečných instancí v této skutečné třídě v tomto příkladu.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight je rovna poměr této třídy pro každý prvek v cílovém vektor|
<a name="confusion-matrix"></a>
### <a name="confusion-matrix"></a>Chybová matice
#### <a name="what-is-a-confusion-matrix"></a>Co je nejasná matice?
Chybová matice se používá k popisu výkonu model klasifikace. Každý řádek zobrazí v datové sadě instance hodnoty true nebo actual, přičemž každý sloupec představuje instance třídy, která byla předpovězena modelem. 

#### <a name="what-does-automated-ml-do-with-the-confusion-matrix"></a>Co dělá automatizovaná paleta s nejasností?
Azure Machine Learning pro klasifikaci problémy automaticky poskytuje chybová matice pro každý model, který je sestaven. U každé nejasnosti bude v případě automatizované palety zobrazena frekvence všech předpokládaných popisků (sloupců) porovnaných k hodnotě true Label (řádek). Čím tmavší je barva, tím vyšší je počet v této konkrétní části matice. 

#### <a name="what-does-a-good-model-look-like"></a>Co vypadá dobrý model?
Porovnáváme skutečnou hodnotu datové sady s předpovězenými hodnotami, které model poskytl. Z tohoto důvodu mají modely strojového učení vyšší přesnost, pokud model má většinu hodnot po diagonále, což znamená, že model předpovídá správnou hodnotu. Pokud má model nerovnováhu mezi třídou, matice nejasností vám pomůže zjistit, který model byl posunut.

##### <a name="example-1-a-classification-model-with-poor-accuracy"></a>Příklad 1: klasifikační model s nízkou přesností
![Klasifikační model s nízkou přesností](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

##### <a name="example-2-a-classification-model-with-high-accuracy"></a>Příklad 2: model klasifikace s vysokou přesností 
![Klasifikační model s vysokou přesností](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)

##### <a name="example-3-a-classification-model-with-high-accuracy-and-high-bias-in-model-predictions"></a>Příklad 3: klasifikační model s vysokou přesností a vysokým posunem v modelu předpovědi
![Klasifikační model s vysokou přesností a vysokým posunem v modelu předpovědi](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-biased-model.png)

<a name="precision-recall-chart"></a>
### <a name="precision-recall-chart"></a>Graf přesnosti a úplnosti
#### <a name="what-is-a-precision-recall-chart"></a>Co je graf se zavoláním přesnosti?
Křivka s přesností na odvolání zobrazuje vztah mezi přesností a vrácením z modelu. Pojem přesnost představuje, že schopnost modelu správně popsat všechny instance. Odvolání představuje možnost pro třídění a vyhledat všechny instance konkrétní popisek.

#### <a name="what-does-automated-ml-do-with-the-precision-recall-chart"></a>Jak automatizované ML dělá pomocí grafu s přesností na odvolání?

Pomocí tohoto grafu lze porovnávat přesnosti a úplnosti křivky pro každý model k určení, které model má přijatelné vztah mezi přesnosti a odvolání pro vaše konkrétní obchodní problém. Tento graf ukazuje průměrnou přesnosti a úplnosti – makro, Micro průměrné přesnosti a úplnosti a přesnosti a úplnosti přidružené všechny třídy modelu. 

Makro – průměr vypočítává metriku nezávisle na každé třídě a pak vypočítá průměr a současně zpracuje všechny třídy. Střední hodnota však agreguje příspěvky všech tříd pro výpočet průměru. Mikroprůměr je vhodnější, pokud je v datové sadě přítomna nerovnováha tříd.

#### <a name="what-does-a-good-model-look-like"></a>Co vypadá dobrý model?
V závislosti na cíli podnikového problému se může způsobit, že se křivka s ideální přesností a odvolání může lišit. Některé příklady jsou uvedeny níže.

##### <a name="example-1-a-classification-model-with-low-precision-and-low-recall"></a>Příklad 1: klasifikační model s nízkou přesností a nízkým navrácením
![Klasifikační model s nízkou přesností a nízkým navrácením](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

##### <a name="example-2-a-classification-model-with-100-precision-and-100-recall"></a>Příklad 2: klasifikační model s ~ 100% přesností a ~ 100% Recalling 
![vysoké přesnosti modelu klasifikace a odvolání](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)
<a name="roc"></a>
### <a name="roc-chart"></a>Graf ROC

#### <a name="what-is-a-roc-chart"></a>Co je graf ROC?
Příjemce provozních charakteristik (nebo roc s více TŘÍDAMI) je vykreslení správně klasifikované popisků vs. nesprávně klasifikované popisky konkrétním modelu. Při trénování modelů na datové sady s vysokou posun, protože se nezobrazí falešně pozitivní popisky, může být méně informativní křivka roc s více TŘÍDAMI.

#### <a name="what-does-automated-ml-do-with-the-roc-chart"></a>K čemu se v grafu ROC používá automatizovaná ML?
Automatizované ML generuje průměrnou přesnost v makru – odvolání, průměrnou přesnost přesnosti – odvolání a přesnost odvolání přidruženou ke všem třídám pro model. 

Makro – průměr vypočítává metriku nezávisle na každé třídě a pak vypočítá průměr a současně zpracuje všechny třídy. Střední hodnota však agreguje příspěvky všech tříd pro výpočet průměru. Mikroprůměr je vhodnější, pokud je v datové sadě přítomna nerovnováha tříd.

#### <a name="what-does-a-good-model-look-like"></a>Co vypadá dobrý model?
V ideálním případě bude mít model číslo bližší až 100% true kladné míry a nablíže k 0% falešně pozitivní hodnotě. 

##### <a name="example-1-a-classification-model-with-low-true-labels-and-high-false-labels"></a>Příklad 1: klasifikační model s dolními popisky s nízkou hodnotou a horními nepravdivými popisky
![Klasifikační model s dolními popisky s nízkou hodnotou a horními nepravdivými popisky](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

##### <a name="example-2-a-classification-model-with-high-true-labels-and-low-false-labels"></a>Příklad 2: klasifikační model s horními popisky a popisky s nízkou hodnotou false
![klasifikační model s horními popisky s hodnotou true a s dolními falešnými popisky](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)
<a name="lift-curve"></a>
### <a name="lift-chart"></a>Nazvednutí grafu
#### <a name="what-is-a-lift-chart"></a>Co je graf výtahu?
Výtah grafy se používají k vyhodnocení výkonu modelu klasifikace. Ukazuje, kolik lépe můžete očekávat s generovaným modelem v porovnání s bez modelu z hlediska přesnosti.
#### <a name="what-does-automated-ml-do-with-the-lift-chart"></a>Co dělá automatizované ML s grafem výtahu?
Můžete porovnat výtah modelu u automaticky vytvořených službou Azure Machine Learning do směrného plánu chcete-li zobrazit takto získané místo hodnoty tohoto konkrétního modelu.
#### <a name="what-does-a-good-model-look-like"></a>Co vypadá dobrý model?

##### <a name="example-1-a-classification-model-that-does-worse-than-a-random-selection-model"></a>Příklad 1: model klasifikace, který je horší než model náhodného výběru
![Model klasifikace, který je horší než model náhodného výběru](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)
##### <a name="example-2-a-classification-model-that-performs-better-than-a-random-selection-model"></a>Příklad 2: model klasifikace, který provádí lepší, než model náhodného výběru
![model klasifikace, který zajišťuje lepší](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)
<a name="gains-curve"></a>
### <a name="gains-chart"></a>Graf zisků
#### <a name="what-is-a-gains-chart"></a>Co je graf zisků?

Graf zvýšení nastavení vyhodnocuje výkon model klasifikace podle každá část data. Ukazuje pro každý percentilu sady dat, kolik lépe můžete očekávat k provádění porovnání proti náhodný výběr modelu.

#### <a name="what-does-automated-ml-do-with-the-gains-chart"></a>Co dělá automatizované ML s grafem zisků?
Použijte graf kumulativní zisky si můžete vybrat klasifikace odříznutí pomocí procenta, který odpovídá požadované zisk z modelu. Tyto informace obsahuje jiný způsob řešení prohlížení výsledků v související tabulce.

#### <a name="what-does-a-good-model-look-like"></a>Co vypadá dobrý model?
##### <a name="example-1-a-classification-model-with-minimal-gain"></a>Příklad 1: klasifikační model s minimálním ziskem
![klasifikační model s minimálním ziskem](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

##### <a name="example-2-a-classification-model-with-significant-gain"></a>Příklad 2: klasifikační model s významným ziskem
![model klasifikace s významným ziskem](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)
<a name="calibration-plot"></a>
### <a name="calibration-chart"></a>Graf kalibrace

#### <a name="what-is-a-calibration-chart"></a>Co je kalibrační graf?
Vykreslení kalibrací slouží k zobrazení spolehlivosti prediktivního modelu. Dělá to tak, že znázorňující vztah mezi pravděpodobnost předpovězené a skutečný pravděpodobnosti, kde "pravděpodobnost" představuje pravděpodobnost, že konkrétní instanci patří pod některé popiskem.
#### <a name="what-does-automated-ml-do-with-the-calibration-chart"></a>Co dělá automatizovaná ML s kalibračním grafem?
Všechny klasifikace problémy můžete zkontrolovat řádku kalibrací micro průměr, – makro průměr a každá třída v daném prediktivního modelu.

Makro – průměr vypočítává metriku nezávisle na každé třídě a pak vypočítá průměr a současně zpracuje všechny třídy. Střední hodnota však agreguje příspěvky všech tříd pro výpočet průměru. 
#### <a name="what-does-a-good-model-look-like"></a>Co vypadá dobrý model?
 Dobře kalibrovaný model zarovnává se čárou y = x, kde je v jeho předpovědi rozumně oprávněně zajistině. Model typu over-pass-the důvěrné v souladu s y = 0 řádku, kde pravděpodobnost předpovězené existuje, ale neexistuje žádný skutečný pravděpodobnosti. 


##### <a name="example-1-a-well-calibrated-model"></a>Příklad 1: dobře kalibrovaný model
![ dobře kalibrovaný model](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

##### <a name="example-2-an-over-confident-model"></a>Příklad 2: model s nadjistým využitím
![Model s vysokou jistotou](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression"></a>Výsledky regrese

Thee následující metriky a grafy jsou k dispozici pro každý regresní model, který sestavíte pomocí možností automatizovaného strojového učení Azure Machine Learning

+ [Metriky](#reg-metrics)
+ [Předpověď oproti hodnotě true](#pvt)
+ [Histogram zbytků](#histo)


### <a name="reg-metrics"></a>Regresní metriky

Následující metriky jsou uloženy v každé iteraci spuštění pro úlohu regrese nebo předpovědi.

|Metrika|Popis|Výpočet|Další parametry
--|--|--|--|
explained_variance|Bylo vysvětleno odchylky je poměr, ke kterému matematické modelu účty pro variantu dané datové sady. Je, že procento snížení odchylku původní data, která mají odchylku chyby. Pokud chyby je 0, je roven bylo vysvětleno variance.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Žádné|
r2_score|R2 je koeficient stanovení nebo snížení procenta v porovnání s modelem směrný plán, který zobrazí průměr kvadratických chyb. |[Výpočet](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Žádné|
spearman_correlation|Korelace spearman je nonparametric míra monotonicity vztah mezi dvěma datovými sadami. Na rozdíl od korelace Pearson korelace Spearman nepředpokládá, že oba datové sady mají obvykle distribuovat. Stejně jako ostatní koeficienty korelace ten se pohybuje mezi -1 a + 1 hodnotou 0 zdání žádnou souvislost. Korelací mezi -1 nebo + 1 implikují přesné monotónní relace. Pozitivní korelace neznamená, že jako x zvýšení zločinců se stejně y. Negativní korelace znamenají, že jako x zvyšují v pořadí, y snižuje.|[Výpočet](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Žádné|
mean_absolute_error|To znamenat, že absolutní chyba je očekávaná hodnota absolutní hodnota rozdílu mezi cílem a do predikce.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Žádné|
normalized_mean_absolute_error|Normalizovaná střední absolutní chyba je střední absolutní chyba rozdělené podle rozsahu dat|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Rozdělit podle rozsahu dat|
median_absolute_error|Střední absolutní chyba je Medián všech absolutních rozdílů mezi cílem a do predikce. To je robustní odlehlé hodnoty.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Žádné|
normalized_median_absolute_error|Normalizovaná střední absolutní chyba je střední absolutní chyba rozdělené podle rozsahu dat|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Rozdělit podle rozsahu dat|
root_mean_squared_error|Kořenové bude kvadratická chyba odmocninu očekávané kvadratickému rozdílu mezi cílem a do predikce.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Žádné|
normalized_root_mean_squared_error|Normalizovaná kořenové bude kvadratická chyba kořenové střední kvadratické chyby rozdělené podle rozsahu dat|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Rozdělit podle rozsahu dat|
root_mean_squared_log_error|Kořenové znamenat protokolu kvadratických chyb je odmocninu očekávané kvadratická chyba logaritmické|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Žádné|
normalized_root_mean_squared_log_error|Normalizovaná chyba na čtvercovém středním významu protokolu je chyba v kořenovém středním významu protokolu dělená rozsahem dat|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Rozdělit podle rozsahu dat|

### <a name="pvt"></a>Předpokládaný vs. pravdivý graf
#### <a name="what-is-a-predicted-vs-true-chart"></a>Co je předpokládaný graf vs. true?
Předpovězené vs. hodnota true zobrazuje vztah mezi předpokládanou hodnotou a korelaci pravdivé hodnoty pro problém regrese. Tento graf lze použít k měření výkonu modelu jako blíže k y = x řádku predikované hodnoty jsou, tím lepší přesnost prediktivního modelu.

#### <a name="what-does-automated-ml-do-with-the-predicted-vs-true-chart"></a>Co dělá automatizované ML s předpokládaným grafem vs. true?
Po každém spuštění zobrazí se předpokládané vs. true grafu pro každou regresní model. K ochraně dat o ochraně osobních údajů, hodnoty jsou rozdělený na intervaly společně a velikost každé z nich se zobrazuje jako pruhový graf v dolní části oblasti grafu. Prediktivní model, můžete porovnat s označením vystínovat oblast s okraji chyba proti ideální hodnotě, kde by mělo být modelu.

#### <a name="what-does-a-good-model-look-like"></a>Co vypadá dobrý model?
##### <a name="example-1-a-classification-model-with-low-accuracy"></a>Příklad 1: klasifikační model s nízkou přesností
![Regresní model s nízkou přesností v předpovědi](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

##### <a name="example-2-a-regression-model-with-high-accuracy"></a>Příklad 2: regresní model s vysokou přesností 
[![regresní model s vysokou přesností ve svém předpovědi](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histo"></a>Histogram grafu zbytků
#### <a name="what-is-a-residuals-chart"></a>Co je graf zbytků?
Zbývající představuje zjištěnou y – předpokládaná y. Zobrazíte okraj chyby s nízkou posun by měl mít tvar histogram zbytky křivku zvonku zaměřená na 0. 
#### <a name="what-does-automated-ml-do-with-the-residuals-chart"></a>Co dělá automatizované ML s grafem zbytků?
Automatizovaná ML automaticky poskytuje graf reziduálního grafu pro zobrazení distribuce chyb v předpovědi.
#### <a name="what-does-a-good-model-look-like"></a>Co vypadá dobrý model?
Dobrým modelem bude obvykle křivka zvonku nebo chyby s nulovou hodnotou.

##### <a name="example-1-a-regression-model-with-bias-in-its-errors"></a>Příklad 1: regresní model s posunem v jeho chybách
![SA regresní model s posunem v jeho chybách](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

##### <a name="example-2-a-regression-model-with-more-even-distribution-of-errors"></a>Příklad 2: regresní model s větší rovnoměrné distribucí chyb
![Regresní model s větší rovnoměrné distribucí chyb](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="explain-model"></a>Závažnost modelu a důležitost funkcí
Automatizované ML poskytuje řídicí panel pro vyhodnocení pro vaše běhy na strojovém učení.
Další informace o povolení funkcí pro interpretaci najdete v tématu [postup](how-to-machine-learning-interpretability-automl.md) při povolování interpretace v rámci automatizovaných experimentů ml.

## <a name="next-steps"></a>Další kroky

+ Přečtěte si další informace o [automatizovaném ml](concept-automated-ml.md) v Azure Machine Learning.
+ Vyzkoušejte [model automatizovaného Machine Learning vysvětlení](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) ukázkových poznámkových bloků.
