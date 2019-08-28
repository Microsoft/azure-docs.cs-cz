---
title: Vysvětlení automatizovaných výsledků na ML
titleSuffix: Azure Machine Learning service
description: Naučte se zobrazovat a pochopit grafy a metriky pro každé z automatizovaných běhů strojového učení.
services: machine-learning
author: nilesha
ms.author: nilesha
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/22/2019
ms.openlocfilehash: 387865ac229acc5d16f595b96c76e7c9a014e991
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2019
ms.locfileid: "70062129"
---
# <a name="understand-automated-machine-learning-results"></a>Vysvětlení automatizovaných výsledků strojového učení

V tomto článku se dozvíte, jak zobrazit a pochopit grafy a metriky pro jednotlivé běhy automatizovaného strojového učení. 

Další informace pro:
+ [Metriky, grafy a křivky pro modely klasifikace](#classification)
+ [Metriky, grafy a grafy pro regresní modely](#regression)
+ [Závažnost modelu a důležitost funkcí](#explain-model)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze služby Azure Machine Learning](https://aka.ms/AMLFree) dnes

* Pomocí sady SDK nebo Azure Portal můžete vytvořit automatický experiment Machine Learning.

    * Použití sady SDK k sestavení [modelu klasifikace](how-to-auto-train-remote.md) nebo [regresního modelu](tutorial-auto-train-models.md)
    * Pomocí [Azure Portal](how-to-create-portal-experiments.md) můžete vytvořit model klasifikace nebo regrese tím, že nahrajete příslušná data.

## <a name="view-the-run"></a>Zobrazit běh

Po spuštění automatizovaného experimentu strojového učení můžete v pracovním prostoru služby Machine Learning najít historii běhů. 

1. Přejděte do svého pracovního prostoru.

1. V levém panelu pracovního prostoru vyberte experimenty.

   ![Snímek obrazovky nabídky experimentu](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. V seznamu experimentů vyberte tu, kterou chcete prozkoumat.

   [![Seznam experimentů](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. V dolní tabulce vyberte **číslo spuštění**.

   Experimentovat za běhu) [ ![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png)

1. V tabulce iterace vyberte **číslo iterace** pro model, který chcete prozkoumat.

   [![Experiment model](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

Stejné výsledky se zobrazí také při spuštění při použití `RunDetails` [widgetu Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).

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
accuracy|Přesnost je procento předpokládané popisky, které přesně odpovídají true popisky. |[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Žádný|
average_precision_score_macro|Průměrná přesnost shrnuje křivky přesnosti a úplnosti jako vážený průměr přesnosti dosáhnout při každé prahové hodnotě, zvýšení spojené s vracením z předchozí prahové hodnoty použít jako váhu. Makro je aritmetický průměr skóre každé třídy přesnosti.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Průměrná = "makro"|
average_precision_score_micro|Průměrná přesnost shrnuje křivky přesnosti a úplnosti jako vážený průměr přesnosti dosáhnout při každé prahové hodnotě, zvýšení spojené s vracením z předchozí prahové hodnoty použít jako váhu. Hodnota mikro je vypočítána globálně kombinací pravdivé kladné hodnoty a falešně pozitivních hodnot při každém přerušení.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Průměrná = "micro"|
average_precision_score_weighted|Průměrná přesnost shrnuje křivky přesnosti a úplnosti jako vážený průměr přesnosti dosáhnout při každé prahové hodnotě, zvýšení spojené s vracením z předchozí prahové hodnoty použít jako váhu. Vážená průměrná hodnota průměrného skóre přesnosti pro každou třídu, vážená o počet true instancí v každé třídě.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Průměrná = "váha"|
balanced_accuracy|Vyvážený přesnost je aritmetický průměr o odvolání pro každou třídu.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Průměrná = "makro"|
f1_score_macro|F1 skóre je průměr přesnosti a odvolání. Makro je aritmetický průměr skóre F1 pro každou třídu.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Průměrná = "makro"|
f1_score_micro|F1 skóre je průměr přesnosti a odvolání. Hodnota mikro je vypočítávána globálně vynásobením celkového počtu skutečných kladných hodnot, falešně negativních hodnot a falešně pozitivních hodnot.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Průměrná = "micro"|
f1_score_weighted|F1 skóre je průměr přesnosti a odvolání. Vážený průměr třídy frekvence skóre F1 pro každou třídu|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Průměrná = "váha"|
log_loss|Toto je ztráta funkce použitá v logistické regrese (multinomial) a rozšíření jako jsou neuronové sítě definované jako záporné pravděpodobnost protokolu true popisků zadaný pravděpodobnostní třídění předpovědi. V případě jedné ukázky s true Label YT v {0,1} a odhadované pravděpodobnosti YP, že YT = 1 se ztratí protokol – log P (yt&#124;YP) =-(yt log (YP) + (1-YT) log (1-YP)).|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Žádný|
norm_macro_recall|Normalizovaná si možná Vzpomínáte – makro je makro si možná Vzpomínáte, normalizovaná tak, aby náhodná výkonu má skóre 0 a ideální výkon má skóre 1. Toho dosáhnete pomocí norm_macro_recall: = (recall_score_macro-R)/(1-R), kde R je očekávaná hodnota recall_score_macro pro náhodný předpovědi (tj. R = 0,5 pro binární klasifikaci a R = (1/C) pro problémy s klasifikací C-Class).|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Average = "Macro" |
precision_score_macro|Přesnost je procento prvky označeny jako určité třídy, které jsou ve skutečnosti v dané třídě. Makro je aritmetický průměr přesnosti pro každou třídu.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Průměrná = "makro"|
precision_score_micro|Přesnost je procento prvky označeny jako určité třídy, které jsou ve skutečnosti v dané třídě. Hodnota mikro se vypočítává globálně pomocí inventarizace celkových skutečných hodnot a falešně pozitivních hodnot.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Průměrná = "micro"|
precision_score_weighted|Přesnost je procento prvky označeny jako určité třídy, které jsou ve skutečnosti v dané třídě. Váže se aritmetický průměr pro každou třídu, vážený podle počtu skutečných instancí v každé třídě.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Průměrná = "váha"|
recall_score_macro|Odvolání je procento prvky ve skutečnosti určité třídy, které jsou správně označené. Makro je aritmetický průměr odvolání každé třídy.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Průměrná = "makro"|
recall_score_micro|Odvolání je procento prvky ve skutečnosti určité třídy, které jsou správně označené. Hodnota mikro se vypočítává globálně pomocí inventarizace celkových skutečných hodnot, falešně negativních hodnot a falešně pozitivních hodnot.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Průměrná = "micro"|
recall_score_weighted|Odvolání je procento prvky ve skutečnosti určité třídy, které jsou správně označené. Váže se aritmetický průměr pro každou třídu, vážený podle počtu skutečných instancí v každé třídě.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Průměrná = "váha"|
weighted_accuracy|Vážená přesnost je přesnost, při které se váha předaná jednotlivým příkladům rovná poměru skutečných instancí v této skutečné třídě v tomto příkladu.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight je rovna poměr této třídy pro každý prvek v cílovém vektor|

### <a name="confusion-matrix"></a>Chybová matice

Chybová matice se používá k popisu výkonu model klasifikace. Každý řádek zobrazí instancí třídy true a každý sloupec představuje instance předpokládaná třída. Chybová matice zobrazí správně klasifikované popisky a nesprávně klasifikované popisky pro daný model.

Azure Machine Learning pro klasifikaci problémy automaticky poskytuje chybová matice pro každý model, který je sestaven. Pro každý chybovou matici automatizované ML zobrazí správně klasifikované popisky jako popisky zelené a nesprávně klasifikovaný jako červený. Velikost kruhu představuje počet vzorků v této přihrádky. Kromě toho frekvence počet každý předpokládané popisek a každému popisku true je součástí sousední pruhové grafy. 

Příklad 1: Klasifikační model s nízkou přesností ![model klasifikace s nízkou přesností](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

Příklad 2: Klasifikační model s vysokou přesností (ideální) ![model klasifikace s vysokou přesností](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)


### <a name="precision-recall-chart"></a>Graf přesnosti a úplnosti

Pomocí tohoto grafu lze porovnávat přesnosti a úplnosti křivky pro každý model k určení, které model má přijatelné vztah mezi přesnosti a odvolání pro vaše konkrétní obchodní problém. Tento graf ukazuje průměrnou přesnosti a úplnosti – makro, Micro průměrné přesnosti a úplnosti a přesnosti a úplnosti přidružené všechny třídy modelu.

Termín přesnosti představuje tuto možnost pro třídění k označení všech instancí správně. Odvolání představuje možnost pro třídění a vyhledat všechny instance konkrétní popisek. Přesnosti a úplnosti křivky znázorňuje vztah mezi tyto dva pojmy. V ideálním případě by modelu mají 100 % přesností a 100 % přesností.

Příklad 1: Klasifikační model s nízkou přesností a nízkým ![odvoláním modelu klasifikace s nízkou přesností a nízkým navrácením](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

Příklad 2: Klasifikační model s ~ 100% Precision a ~ 100% Recall (ideální) ![klasifikační model s vysokou přesností a vrácením](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)

### <a name="roc"></a>ROC S VÍCE TŘÍDAMI

Příjemce provozních charakteristik (nebo roc s více TŘÍDAMI) je vykreslení správně klasifikované popisků vs. nesprávně klasifikované popisky konkrétním modelu. Při trénování modelů na datové sady s vysokou posun, protože se nezobrazí falešně pozitivní popisky, může být méně informativní křivka roc s více TŘÍDAMI.

Příklad 1: Klasifikační model s dolními popisky s nízkou hodnotou a s ![vysokým falešnou jmenovkou, s nízkými a horními popisky](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

Příklad 2: Klasifikační model s horními popisky s hodnotou true a s ![nízkou hodnotou false označí klasifikační model s horními a nepravdivými popisky.](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)

### <a name="lift-curve"></a>Výtah křivky

Můžete porovnat výtah modelu u automaticky vytvořených službou Azure Machine Learning do směrného plánu chcete-li zobrazit takto získané místo hodnoty tohoto konkrétního modelu.

Výtah grafy se používají k vyhodnocení výkonu modelu klasifikace. Ukazuje, jak mnohem lépe můžete očekávat s modelem porovnání bez modelu. 

Příklad 1: Model je vykonává horší než model ![náhodného výběru modelu klasifikace, který je horší než model náhodného výběru.](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)

Příklad 2: Model je lepší než model ![náhodného výběru, který provádí model klasifikace, který je lepší.](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)

### <a name="gains-curve"></a>Zvýšení křivky

Graf zvýšení nastavení vyhodnocuje výkon model klasifikace podle každá část data. Ukazuje pro každý percentilu sady dat, kolik lépe můžete očekávat k provádění porovnání proti náhodný výběr modelu.

Použijte graf kumulativní zisky si můžete vybrat klasifikace odříznutí pomocí procenta, který odpovídá požadované zisk z modelu. Tyto informace obsahuje jiný způsob řešení prohlížení výsledků v související tabulce.

Příklad 1: Klasifikační model s minimálním ziskem ![a modelem klasifikace s minimálním ziskem](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

Příklad 2: Klasifikační model s významným ziskem získáte ![model klasifikace s významným ziskem.](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)

### <a name="calibration-plot"></a>Diagram kalibrací

Všechny klasifikace problémy můžete zkontrolovat řádku kalibrací micro průměr, – makro průměr a každá třída v daném prediktivního modelu. 

Vykreslení kalibrací slouží k zobrazení spolehlivosti prediktivního modelu. Dělá to tak, že znázorňující vztah mezi pravděpodobnost předpovězené a skutečný pravděpodobnosti, kde "pravděpodobnost" představuje pravděpodobnost, že konkrétní instanci patří pod některé popiskem. Dobře kalibrované modelu v souladu s y = x řádku, kde je přiměřeně jisti jeho předpovědi. Model typu over-pass-the důvěrné v souladu s y = 0 řádku, kde pravděpodobnost předpovězené existuje, ale neexistuje žádný skutečný pravděpodobnosti.

Příklad 1: Dobře kalibrovaný model ![ s větším kalibrovaným modelem](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

Příklad 2: Model ![s vysokou jistotou modelu nadlimitního modelu](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression"></a>Výsledky regrese

Thee následující metriky a grafy jsou k dispozici pro každý regresní model, který sestavíte pomocí možností automatizovaného strojového učení Azure Machine Learning

+ [Metriky](#reg-metrics)
+ [Předpokládaná vs. Hodnota TRUE](#pvt)
+ [Histogram zbytků](#histo)


### <a name="reg-metrics"></a>Regresní metriky

Následující metriky jsou uloženy v každé iteraci spuštění pro úlohu regrese nebo předpovědi.

|Metrika|Popis|Výpočet|Další parametry
--|--|--|--|
explained_variance|Bylo vysvětleno odchylky je poměr, ke kterému matematické modelu účty pro variantu dané datové sady. Je, že procento snížení odchylku původní data, která mají odchylku chyby. Pokud chyby je 0, je roven bylo vysvětleno variance.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Žádný|
r2_score|R2 je koeficient stanovení nebo snížení procenta v porovnání s modelem směrný plán, který zobrazí průměr kvadratických chyb. |[Výpočet](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Žádný|
spearman_correlation|Korelace spearman je nonparametric míra monotonicity vztah mezi dvěma datovými sadami. Na rozdíl od korelace Pearson korelace Spearman nepředpokládá, že oba datové sady mají obvykle distribuovat. Stejně jako ostatní koeficienty korelace ten se pohybuje mezi -1 a + 1 hodnotou 0 zdání žádnou souvislost. Korelací mezi -1 nebo + 1 implikují přesné monotónní relace. Pozitivní korelace neznamená, že jako x zvýšení zločinců se stejně y. Negativní korelace znamenají, že jako x zvyšují v pořadí, y snižuje.|[Výpočet](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Žádný|
mean_absolute_error|To znamenat, že absolutní chyba je očekávaná hodnota absolutní hodnota rozdílu mezi cílem a do predikce.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Žádný|
normalized_mean_absolute_error|Normalizovaná střední absolutní chyba je střední absolutní chyba rozdělené podle rozsahu dat|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Rozdělit podle rozsahu dat|
median_absolute_error|Střední absolutní chyba je Medián všech absolutních rozdílů mezi cílem a do predikce. To je robustní odlehlé hodnoty.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Žádný|
normalized_median_absolute_error|Normalizovaná střední absolutní chyba je střední absolutní chyba rozdělené podle rozsahu dat|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Rozdělit podle rozsahu dat|
root_mean_squared_error|Kořenové bude kvadratická chyba odmocninu očekávané kvadratickému rozdílu mezi cílem a do predikce.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Žádný|
normalized_root_mean_squared_error|Normalizovaná kořenové bude kvadratická chyba kořenové střední kvadratické chyby rozdělené podle rozsahu dat|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Rozdělit podle rozsahu dat|
root_mean_squared_log_error|Kořenové znamenat protokolu kvadratických chyb je odmocninu očekávané kvadratická chyba logaritmické|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Žádný|
normalized_root_mean_squared_log_error|Normalizovaná chyba na čtvercovém středním významu protokolu je chyba v kořenovém středním významu protokolu dělená rozsahem dat|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Rozdělit podle rozsahu dat|

### <a name="pvt"></a>Předpovězeno vs. Pravda

Předpokládaná vs. Hodnota TRUE označuje vztah mezi předpovězené hodnoty a její usnadňuje korelování hodnotu true pro regresní problém. Tento graf lze použít k měření výkonu modelu jako blíže k y = x řádku predikované hodnoty jsou, tím lepší přesnost prediktivního modelu.

Po každém spuštění zobrazí se předpokládané vs. true grafu pro každou regresní model. K ochraně dat o ochraně osobních údajů, hodnoty jsou rozdělený na intervaly společně a velikost každé z nich se zobrazuje jako pruhový graf v dolní části oblasti grafu. Prediktivní model, můžete porovnat s označením vystínovat oblast s okraji chyba proti ideální hodnotě, kde by mělo být modelu.

Příklad 1: Regresní model s nízkou přesností v předpovědi ![regresní model s nízkou přesností v předpovědi](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

Příklad 2: Regresní model s vysokou přesností v jeho předpovědi [ ![regresní model s vysokou přesností ve své předpovědi](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histo"></a>Histogram zbytků

Zbývající představuje zjištěnou y – předpokládaná y. Zobrazíte okraj chyby s nízkou posun by měl mít tvar histogram zbytky křivku zvonku zaměřená na 0. 

Příklad 1: Regresní model s posunem chyb ![SA regresní model s posunem v jeho chybách](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

Příklad 2: Regresní model s větší rovnoměrné distribucí chyb ![A regresní model s větším množstvím distribucí chyb](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="explain-model"></a>Závažnost modelu a důležitost funkcí

Důležitost funkcí vám umožní zjistit, jak cenná je každá funkce v konstrukci modelu. Tento výpočet je ve výchozím nastavení vypnutý, protože může významně zvýšit dobu běhu.   Můžete povolit vysvětlení modelu pro všechny modely nebo vysvětlit pouze model, který nejlépe vyhovuje.

Můžete zkontrolovat skóre význam funkce pro model celkové stejně jako na třídu v prediktivním modelem. Zobrazí se na funkci jak si vede význam pro každou třídu a celková.

![Možnost vysvětlit funkce](./media/how-to-understand-automated-ml/feature-importance.gif)

Další informace o povolení funkcí pro interpretaci najdete [v tématu Konfigurace automatizovaných experimentů ml v Pythonu](how-to-configure-auto-train.md#explain-the-model-interpretability).  Příklad, který vysvětluje nejlepší model, naleznete v tématu [nejlepší vysvětlení modelu](how-to-auto-train-remote.md#explain).

## <a name="next-steps"></a>Další postup

+ Přečtěte si další informace o [automatizovaném ml](concept-automated-ml.md) v Azure Machine Learning.
+ Vyzkoušejte ukázkový Poznámkový blok pro [automatizovaný Machine Learning model vysvětlení](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation) .
