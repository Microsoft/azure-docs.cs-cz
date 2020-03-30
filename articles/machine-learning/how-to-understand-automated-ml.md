---
title: Principy automatických výsledků ML
titleSuffix: Azure Machine Learning
description: Přečtěte si, jak zobrazit grafy a metriky pro každý automatizovaný strojový učení a porozumět mu.
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: c5f12da3606361b504d4581916d9645fa3cd24f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283456"
---
# <a name="understand-automated-machine-learning-results"></a>Vysvětlení výsledků automatizovaného strojového učení
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak zobrazit a pochopit grafy a metriky pro každý z vašich automatizovaných spuštění strojového učení. 

Další informace:
+ [Metriky, grafy a křivky pro klasifikační modely](#classification)
+ [Metriky, grafy a grafy pro regresní modely](#regression)
+ [Interpretovatelnost modelu a důležitost funkce](#explain-model)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušejte [bezplatnou nebo placenou verzi Azure Machine Learning](https://aka.ms/AMLFree) ještě dnes.

* Vytvořte experiment pro automatické spuštění strojového učení, buď pomocí sady SDK, nebo ve studiu Azure Machine Learning.

    * Vytvoření [klasifikačního nebo](how-to-auto-train-remote.md) [regresního modelu](tutorial-auto-train-models.md) pomocí sady SDK
    * Pomocí [sady Azure Machine Learning studio](how-to-use-automated-ml-for-ml-models.md) vytvořit klasifikaci nebo regresní model nahráním příslušných dat.

## <a name="view-the-run"></a>Zobrazit běh

Po spuštění automatizovaného experimentu strojového učení se historie spuštění nachází v pracovním prostoru strojového učení. 

1. Přejděte do svého pracovního prostoru.

1. V levém panelu pracovní plochy vyberte **Experimenty**.

   ![Snímek obrazovky s nabídkou experimentu](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. V seznamu experimentů vyberte ten, který chcete prozkoumat.

   [![Seznam experimentů](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. V dolní tabulce vyberte **spustit**.

   Experiment spustit ) [ ![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png)

1. V modelech vyberte **název algoritmu** pro model, který chcete dále prozkoumat.

   [![Model experimentu](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

Tyto stejné výsledky se zobrazí také při `RunDetails`spuštění při použití [widgetu Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).

## <a name="classification-results"></a><a name="classification"></a>Výsledky klasifikace

Následující metriky a grafy jsou k dispozici pro každý klasifikační model, který vytvoříte pomocí automatizovaných funkcí strojového učení Azure Machine Learning

+ [Metriky](#classification-metrics)
+ [Matice záměny](#confusion-matrix)
+ [Graf přesnosti odvolání](#precision-recall-chart)
+ [Provozní charakteristiky přijímače (nebo ROC)](#roc)
+ [Zvedací křivka](#lift-curve)
+ [Křivka zisků](#gains-curve)
+ [Kalibrační graf](#calibration-plot)

### <a name="classification-metrics"></a>Metriky klasifikace

Následující metriky jsou uloženy v každé iteraci spuštění pro úlohu klasifikace.

Metrika|Popis|Výpočet|Další parametry
--|--|--|--
AUC_Macro| AUC je oblast pod křivkou provozní charakteristiky přijímače. Makro je aritmetický průměr AUC pro každou třídu.  | [Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="makro"|
AUC_Micro| AUC je oblast pod křivkou provozní charakteristiky přijímače. Micro se vypočítá globálně kombinací skutečných pozitivních a falešných poplachů z každé třídy.| [Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | průměr="mikro"|
AUC_Weighted  | AUC je oblast pod křivkou provozní charakteristiky přijímače. Vážený je aritmetický průměr skóre pro každou třídu vážený počtem skutečných instancí v každé třídě.| [Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|průměr="vážený"
accuracy|Přesnost je procento předpovídaných popisků, které přesně odpovídají skutečným popiskům. |[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Žádný|
average_precision_score_macro|Průměrná přesnost shrnuje křivku přesnosti a stažení jako vážený průměr přesnosti dosažený při každé prahové hodnotě, přičemž zvýšení svolávací akce z předchozí prahové hodnoty použité jako hmotnost. Makro je aritmetický průměr průměrného skóre přesnosti každé třídy.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="makro"|
average_precision_score_micro|Průměrná přesnost shrnuje křivku přesnosti a stažení jako vážený průměr přesnosti dosažený při každé prahové hodnotě, přičemž zvýšení svolávací akce z předchozí prahové hodnoty použité jako hmotnost. Micro se vypočítá globálně kombinací skutečných pozitiv a falešných poplachů při každém přerušení.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|průměr="mikro"|
average_precision_score_weighted|Průměrná přesnost shrnuje křivku přesnosti a stažení jako vážený průměr přesnosti dosažený při každé prahové hodnotě, přičemž zvýšení svolávací akce z předchozí prahové hodnoty použité jako hmotnost. Vážený je aritmetický průměr průměrného skóre přesnosti pro každou třídu vážený počtem skutečných instancí v každé třídě.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|průměr="vážený"|
balanced_accuracy|Vyvážená přesnost je aritmetický průměr odvolání pro každou třídu.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="makro"|
f1_score_macro|Skóre F1 je harmonický průměr přesnosti a odvolání. Makro je aritmetický průměr skóre F1 pro každou třídu.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="makro"|
f1_score_micro|Skóre F1 je harmonický průměr přesnosti a odvolání. Micro se vypočítá globálně počítáním celkových skutečných poplachů, falešných negativů a falešných poplachů.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|průměr="mikro"|
f1_score_weighted|Skóre F1 je harmonický průměr přesnosti a odvolání. Vážený průměr podle četnosti tříd y skóre F1 pro každou třídu|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|průměr="vážený"|
log_loss|Toto je funkce ztráty používaná v (multinomické) logistické regresi a rozšíření, jako jsou například neuronové sítě, definované jako negativní pravděpodobnost protokolu true popisky dané pravděpodobnostní třídění předpovědi. U jednoho vzorku s hodnotným {0,1} štítkem yt in a odhadovanou pravděpodobností yp, že yt = 1, je ztráta protokolu -log P(yt&#124;yp) = -(yt log(yp) + (1 - yt) log(1 - yp)).|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Žádný|
norm_macro_recall|Normalizované makro odvolání je makro odvolání normalizováno tak, aby náhodný výkon má skóre 0 a perfektní výkon má skóre 1. Toho je dosaženo norm_macro_recall := (recall_score_macro - R)/(1 - R), kde R je očekávaná hodnota recall_score_macro pro náhodné předpovědi (tj. R = 0,5 pro binární klasifikaci a R = (1 / C) pro problémy klasifikace třídy C).|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|průměr = "makro" |
precision_score_macro|Přesnost je procento pozitivně předpovídaných prvků, které jsou správně označeny. Makro je aritmetický průměr přesnosti pro každou třídu.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="makro"|
precision_score_micro|Přesnost je procento pozitivně předpovídaných prvků, které jsou správně označeny. Micro se vypočítá globálně počítáním celkových skutečných poplachů a falešných poplachů.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|průměr="mikro"|
precision_score_weighted|Přesnost je procento pozitivně předpovídaných prvků, které jsou správně označeny. Vážený je aritmetický průměr přesnosti pro každou třídu vážený počtem skutečných instancí v každé třídě.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|průměr="vážený"|
recall_score_macro|Odvolání je procento správně označených prvků určité třídy. Makro je aritmetický průměr odvolání pro každou třídu.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="makro"|
recall_score_micro|Odvolání je procento správně označených prvků určité třídy. Micro se vypočítá globálně počítáním celkových skutečných poplachů, falešných negativů a falešných poplachů|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|průměr="mikro"|
recall_score_weighted|Odvolání je procento správně označených prvků určité třídy. Vážený je aritmetický průměr odvolání pro každou třídu vážený počtem skutečných instancí v každé třídě.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|průměr="vážený"|
weighted_accuracy|Vážená přesnost je přesnost, kde hmotnost uvedená v každém příkladu se rovná podílu skutečných instancí v pravé třídě daného příkladu.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight je vektor rovnající se podílu této třídy pro každý prvek v cílovém|

<a name="confusion-matrix"></a>

### <a name="confusion-matrix"></a>Matice záměny

#### <a name="what-is-a-confusion-matrix"></a>Co je matice zmatku?
Matice záměny se používá k popisu výkonu klasifikačního modelu. Každý řádek zobrazuje instance true nebo actual class ve vaší datové sadě a každý sloupec představuje instance třídy, která byla předpovězena modelem. 

#### <a name="what-does-automated-ml-do-with-the-confusion-matrix"></a>Co dělá automatizovaná ML s maticí záměny?
Pro problémy s klasifikací Azure Machine Learning automaticky poskytuje matice zmatku pro každý model, který je sestaven. Pro každou matici záměny bude automatizovaná hodnota ML zobrazovat frekvenci každého předpokládaného popisku (sloupce) ve srovnání s popiskem true (řádkem). Čím tmavší je barva, tím vyšší je počet v této konkrétní části matice. 

#### <a name="what-does-a-good-model-look-like"></a>Jak vypadá dobrý model?
Porovnáváme skutečnou hodnotu datové sady s předpovídanými hodnotami, které model dal. Z tohoto důvodu mají modely strojového učení vyšší přesnost, pokud má model většinu svých hodnot podél úhlopříčky, což znamená, že model předpověděl správnou hodnotu. Pokud má model nerovnováhu třídy, matice záměny pomůže odhalit neobjektivní model.

##### <a name="example-1-a-classification-model-with-poor-accuracy"></a>Příklad 1: Klasifikační model se špatnou přesností
![Klasifikační model se špatnou přesností](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

##### <a name="example-2-a-classification-model-with-high-accuracy"></a>Příklad 2: Klasifikační model s vysokou přesností 
![Klasifikační model s vysokou přesností](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)

##### <a name="example-3-a-classification-model-with-high-accuracy-and-high-bias-in-model-predictions"></a>Příklad 3: Klasifikační model s vysokou přesností a vysokým zkreslením v předpovědích modelu
![Klasifikační model s vysokou přesností a vysokým zkreslením v předpovědích modelu](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-biased-model.png)

<a name="precision-recall-chart"></a>
### <a name="precision-recall-chart"></a>Graf přesného odvolání
#### <a name="what-is-a-precision-recall-chart"></a>Co je graf přesnosti odvolání?
Křivka přesnosti odvolání zobrazuje vztah mezi přesností a navrácením z modelu. Termín přesnost představuje tuto schopnost pro model správně označit všechny instance. Odvolání představuje schopnost třídění najít všechny instance určitého popisku.

#### <a name="what-does-automated-ml-do-with-the-precision-recall-chart"></a>Co dělá automatizovaná technologie ML s grafem přesnosti odvolání?

Pomocí tohoto grafu můžete porovnat křivky přesnosti odvolání pro každý model a určit, který model má přijatelný vztah mezi přesností a odvolání pro konkrétní obchodní problém. Tento graf znázorňuje makroprůměr přesnosti-recall, Micro Average Precision-Recall a přesnost odvolání spojené se všemi třídami pro model. 

Makro-průměr vypočítá metriku nezávisle na každé třídě a pak se průměr, zacházet se všemi třídami stejně. Mikroprůměr však agreguje příspěvky všech tříd k výpočtu průměru. Mikroprůměr je vhodnější, pokud je třída nerovnováha přítomna v datové sadě.

#### <a name="what-does-a-good-model-look-like"></a>Jak vypadá dobrý model?
V závislosti na cíli obchodního problému se může ideální křivka přesnosti a odvolání lišit. Některé příklady jsou uvedeny níže

##### <a name="example-1-a-classification-model-with-low-precision-and-low-recall"></a>Příklad 1: Klasifikační model s nízkou přesností a nízkou svolávací akcí
![Klasifikační model s nízkou přesností a nízkou svolávací akcí](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

##### <a name="example-2-a-classification-model-with-100-precision-and-100-recall"></a>Příklad 2: Klasifikační model s přesností ~100 % a ~100 % vyvoláním 
![Klasifikační model s vysokou přesností a stažením](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)
<a name="roc"></a>
### <a name="roc-chart"></a>Graf ROC

#### <a name="what-is-a-roc-chart"></a>Co je graf ROC?
Přijímací provozní charakteristika (nebo ROC) je obrázek správně klasifikovaných štítků vs. nesprávně klasifikované štítky pro konkrétní model. Křivka ROC může být méně informativní při trénování modelů na datových sadách s vysokým zkreslením, protože nezobrazuje falešně pozitivní popisky.

#### <a name="what-does-automated-ml-do-with-the-roc-chart"></a>Co dělá automatizovaná ml s grafem ROC?
Automatizované ML generuje makro průměr přesnosti odvolání, Micro Average Precision-Recall a přesnost odvolání spojené se všemi třídami pro model. 

Makro-průměr vypočítá metriku nezávisle na každé třídě a pak se průměr, zacházet se všemi třídami stejně. Mikroprůměr však agreguje příspěvky všech tříd k výpočtu průměru. Mikroprůměr je vhodnější, pokud je třída nerovnováha přítomna v datové sadě.

#### <a name="what-does-a-good-model-look-like"></a>Jak vypadá dobrý model?
V ideálním případě bude mít model blíže k 100% skutečné pozitivní míře a blíže k 0% falešně pozitivní míře. 

##### <a name="example-1-a-classification-model-with-low-true-labels-and-high-false-labels"></a>Příklad 1: Klasifikační model s nízkými skutečnými popisky a vysokými falešnými popisky
![Klasifikační model s nízkými skutečnými štítky a vysokými falešnými štítky](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

##### <a name="example-2-a-classification-model-with-high-true-labels-and-low-false-labels"></a>Příklad 2: Klasifikační model s vysokými skutečnými popisky a nízkými falešnými popisky
![klasifikační model s vysoce pravdivými štítky a nízkými falešnými štítky](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)
<a name="lift-curve"></a>
### <a name="lift-chart"></a>Graf navýšení
#### <a name="what-is-a-lift-chart"></a>Co je graf výtahu?
Grafy navýšení se používají k vyhodnocení výkonu klasifikačního modelu. Ukazuje, o kolik lépe můžete očekávat, že dělat s generovaným modelem ve srovnání s bez modelu, pokud jde o přesnost.
#### <a name="what-does-automated-ml-do-with-the-lift-chart"></a>Co dělá automatizovaná ml s grafem zdvihu?
Výtah modelu vytvořeného automaticky s Azure Machine Learning můžete porovnat se směrným plánem, abyste mohli zobrazit nárůst hodnoty daného modelu.
#### <a name="what-does-a-good-model-look-like"></a>Jak vypadá dobrý model?

##### <a name="example-1-a-classification-model-that-does-worse-than-a-random-selection-model"></a>Příklad 1: Klasifikační model, který je horší než model náhodného výběru
![Klasifikační model, který je horší než model náhodného výběru](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)
##### <a name="example-2-a-classification-model-that-performs-better-than-a-random-selection-model"></a>Příklad 2: Klasifikační model, který funguje lépe než model náhodného výběru
![Klasifikační model, který funguje lépe](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)
<a name="gains-curve"></a>
### <a name="gains-chart"></a>Graf zisků
#### <a name="what-is-a-gains-chart"></a>Co je graf zisků?

Graf zisky vyhodnocuje výkon klasifikačního modelu podle každé části dat. Pro každý percentil sady dat se zobrazuje, o kolik lépe můžete očekávat, že budete provádět ve srovnání s modelem náhodného výběru.

#### <a name="what-does-automated-ml-do-with-the-gains-chart"></a>Co dělá automatizovaná ml s grafem zisků?
Pomocí grafu kumulativní zisky, které vám pomohou vybrat snížení klasifikace pomocí procenta, které odpovídá požadovanému zisku z modelu. Tyto informace poskytují další způsob pohledu na výsledky v doprovodné graf výtahu.

#### <a name="what-does-a-good-model-look-like"></a>Jak vypadá dobrý model?
##### <a name="example-1-a-classification-model-with-minimal-gain"></a>Příklad 1: Klasifikační model s minimálním ziskem
![klasifikační model s minimálním ziskem](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

##### <a name="example-2-a-classification-model-with-significant-gain"></a>Příklad 2: Klasifikační model s významným ziskem
![Klasifikační model s významným ziskem](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)
<a name="calibration-plot"></a>
### <a name="calibration-chart"></a>Kalibrační graf

#### <a name="what-is-a-calibration-chart"></a>Co je kalibrační graf?
Kalibrační obrázek se používá k zobrazení spolehlivosti prediktivního modelu. Je to tím, že ukazuje vztah mezi předpovídané pravděpodobnosti a skutečné pravděpodobnosti, kde "pravděpodobnost" představuje pravděpodobnost, že konkrétní instance patří pod některé popisek.
#### <a name="what-does-automated-ml-do-with-the-calibration-chart"></a>Co dělá automatizovaná zpráva ML s kalibračním grafem?
U všech problémů s klasifikací můžete zkontrolovat kalibrační čáru pro mikroprůměr, makro-průměr a každou třídu v daném prediktivním modelu.

Makro-průměr vypočítá metriku nezávisle na každé třídě a pak se průměr, zacházet se všemi třídami stejně. Mikroprůměr však agreguje příspěvky všech tříd k výpočtu průměru. 
#### <a name="what-does-a-good-model-look-like"></a>Jak vypadá dobrý model?
 Dobře kalibrovaný model je zarovnán s čárou y=x, kde je přiměřeně jistý ve svých předpovědích. Příliš sebevědomý model zarovná s řádkem y=0, kde je přítomna předpokládaná pravděpodobnost, ale neexistuje žádná skutečná pravděpodobnost. 


##### <a name="example-1-a-well-calibrated-model"></a>Příklad 1: Dobře kalibrovaný model
![ více dobře kalibrovaný model](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

##### <a name="example-2-an-over-confident-model"></a>Příklad 2: Příliš sebevědomý model
![Příliš sebevědomý model](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression-results"></a><a name="regression"></a>Výsledky regrese

Následující metriky a grafy jsou k dispozici pro každý regresní model, který vytvoříte pomocí automatizovaných funkcí strojového učení Azure Machine Learning

+ [Metriky](#reg-metrics)
+ [Předpovídané vs. Pravda](#pvt)
+ [Histogram zbytků](#histo)


### <a name="regression-metrics"></a><a name="reg-metrics"></a>Regresní metriky

Následující metriky jsou uloženy v každé iteraci spuštění pro regresní nebo prognostické úlohy.

|Metrika|Popis|Výpočet|Další parametry
--|--|--|--|
explained_variance|Vysvětlený rozptyl je poměr, k němuž matematický model odpovídá za změnu dané datové sady. Jedná se o procentuální snížení rozptylu původních dat na odchylku chyb. Pokud je průměr chyb 0, rovná se vysvětlené odchylky.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Žádný|
r2_score|R2 je koeficient stanovení nebo procentuální snížení kvadratických chyb ve srovnání s základním modelem, který vyznamená střední hodnotu. |[Výpočet](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Žádný|
spearman_correlation|Spearmanko je neparametrické měřítko monotonicity vztahu mezi dvěma datovými sadami. Na rozdíl od Pearsonkorelace Spearman korelace nepředpokládá, že obě datové sady jsou obvykle distribuovány. Stejně jako ostatní korelační koeficienty se tento koeficienty liší mezi -1 a +1 s 0, což znamená žádnou korelaci. Korelace -1 nebo +1 naznačují přesný monotónní vztah. Pozitivní korelace naznačují, že jak se x zvyšuje, tak y. Negativní korelace naznačují, že jak se x zvyšuje, y klesá.|[Výpočet](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Žádný|
mean_absolute_error|Průměrná absolutní chyba je očekávaná hodnota absolutní hodnoty rozdílu mezi cílem a predikcí|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Žádný|
normalized_mean_absolute_error|Normalizovaná průměrná absolutní chyba je průměrná absolutní chyba dělená rozsahem dat|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Vydělit rozsahem dat|
median_absolute_error|Medián absolutní chyby je medián všech absolutních rozdílů mezi cílem a predikcí. Tato ztráta je robustní k odlehlým hodnotám.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Žádný|
normalized_median_absolute_error|Normalizovaná mediánová absolutní chyba je medián absolutní chyby dělený rozsahem|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Vydělit rozsahem dat|
root_mean_squared_error|Střední kvadratická chyba odmocnina odmocniny je druhá odmocnina očekávaného kvadratového rozdílu mezi cílem a predikcí.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Žádný|
normalized_root_mean_squared_error|Normalizovaná střední kvadratická chyba je střední kvadratická chyba vydělená rozsahem dat|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Vydělit rozsahem dat|
root_mean_squared_log_error|Střední kvadratická chyba protokolu je druhá odmocnina očekávané kvadratické logaritmické chyby|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Žádný|
normalized_root_mean_squared_log_error|Normalizovaná chyba kvadratických protokolů kořenového adresáře je střední kvadratická chyba protokolu vydělená rozsahem dat|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Vydělit rozsahem dat|

### <a name="predicted-vs-true-chart"></a><a name="pvt"></a>Graf Predikované vs. pravda
#### <a name="what-is-a-predicted-vs-true-chart"></a>Co je graf Predikované vs. pravda?
Předpovídané vs True zobrazuje vztah mezi předpovídané hodnoty a jeho korelace true hodnotu pro regresní problém. Tento graf lze použít k měření výkonu modelu jako blíže k y = x řádek předpovídané hodnoty jsou, tím lepší je přesnost prediktivní model.

#### <a name="what-does-automated-ml-do-with-the-predicted-vs-true-chart"></a>Co dělá automatizovaná hodnota ML s grafem Predikované vs. pravda?
Po každém spuštění můžete zobrazit předpovídaný vs. skutečný graf pro každý regresní model. Z důvodu ochrany osobních údajů jsou hodnoty připonovány společně a velikost každé přihrádky je zobrazena jako pruhový graf v dolní části oblasti grafu. Prediktivní model můžete porovnat s světlejší oblastí odstínu zobrazující chybové okraje s ideální hodnotou, kde by měl být model.

#### <a name="what-does-a-good-model-look-like"></a>Jak vypadá dobrý model?
##### <a name="example-1-a-classification-model-with-low-accuracy"></a>Příklad 1: Klasifikační model s nízkou přesností
![Regresní model s nízkou přesností v předpovědích](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

##### <a name="example-2-a-regression-model-with-high-accuracy"></a>Příklad 2: Regresní model s vysokou přesností 
[![Regresní model s vysokou přesností ve svých předpovědích](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histogram-of-residuals-chart"></a><a name="histo"></a>Histogram reziduální hodiagramu
#### <a name="what-is-a-residuals-chart"></a>Co je reziduální graf?
Reziduální představuje pozorované y – předpokládané y. Chcete-li ukázat rozpětí chyby s nízkou zaujatostí, histogram zbytků by měl být tvarován jako křivka zvonu, soustředěný kolem 0. 
#### <a name="what-does-automated-ml-do-with-the-residuals-chart"></a>Co dělá automatizovaná ml s grafem zbytků?
Automatické ML automaticky poskytuje reziduální graf pro zobrazení rozložení chyb v předpovědi.
#### <a name="what-does-a-good-model-look-like"></a>Jak vypadá dobrý model?
Dobrý model bude mít obvykle zvonek křivky nebo chyby kolem nuly.

##### <a name="example-1-a-regression-model-with-bias-in-its-errors"></a>Příklad 1: Regresní model s předpojatostí v chybách
![Regresní model s s předpojatostí v chybách](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

##### <a name="example-2-a-regression-model-with-more-even-distribution-of-errors"></a>Příklad 2: Regresní model s rovnoměrnějším rozložením chyb
![Regresní model s rovnoměrnějším rozložením chyb](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="model-interpretability-and-feature-importance"></a><a name="explain-model"></a>Interpretovatelnost modelu a důležitost funkce
Automatizované ML poskytuje řídicí panel interpretability strojového učení pro vaše běhy.
Další informace o povolení funkcí interpretace naleznete v [návodu k](how-to-machine-learning-interpretability-automl.md) povolení interpretability v automatizovaných experimentech ML.

## <a name="next-steps"></a>Další kroky

+ Další informace o [automatizovanéml](concept-automated-ml.md) v Azure Machine Learning.
+ Vyzkoušejte ukázkové poznámkové bloky [automatického modelu strojového učení.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)
