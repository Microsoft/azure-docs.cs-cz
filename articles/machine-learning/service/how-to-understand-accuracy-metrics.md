---
title: Metrika přesnosti školení v automatizovaném ML
titleSuffix: Azure Machine Learning service
description: Přečtěte si o automatizované metriky přesnosti Machine Learning pro každé z vašich běhů.
author: j-martens
ms.author: jmartens
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/16/2019
ms.openlocfilehash: dc147fd0252b2b5ec4ce334d6c1c464d9cde8ef5
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297901"
---
# <a name="evaluate-training-accuracy-in-automated-ml-with-metrics"></a>Vyhodnotit přesnost školení v automatizovaných ML s metrikami

V tomto článku se seznámíte s různými metrikami, které jsou k dispozici pro automatizované modely ml v Azure Machine Learning. 

Existuje několik způsobů, jak zobrazit metriky přesnosti školení pro každou iteraci spuštění.
* Použití [widgetu Jupyter](how-to-track-experiments.md#view-run-details)
* Použití funkce u libovolného `Run` objektu [ `get_metrics()` ](how-to-track-experiments.md#query-run-metrics)
* Zobrazit [metriky experimentu v Azure Portal](how-to-track-experiments.md#view-the-experiment-in-the-azure-portal)

## <a name="prerequisites"></a>Požadavky
 
* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze služby Azure Machine Learning](https://aka.ms/AMLFree) dnes
 
* Pomocí sady SDK nebo Azure Portal můžete vytvořit automatický experiment Machine Learning.
 
    * Použití sady SDK k sestavení [modelu klasifikace](how-to-auto-train-remote.md) nebo [regresního modelu](tutorial-auto-train-models.md)
    * Pomocí [Azure Portal](how-to-create-portal-experiments.md) můžete vytvořit model klasifikace nebo regrese tím, že nahrajete příslušná data.

## <a name="classification-metrics"></a>Klasifikace metriky

Následující metriky jsou uloženy v každé iteraci spuštění pro úlohu klasifikace.

|Metrika|Popis|Výpočet|Další parametry
--|--|--|--|
AUC_Macro| AUC je oblasti pod křivkou charakteristiku provozní příjemce. Makro je aritmetický průměr AUC pro každou třídu.  | [Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | Průměrná = "makro"|
AUC_Micro| AUC je oblasti pod křivkou charakteristiku provozní příjemce. Hodnota mikro je vypočítána globálně kombinací skutečných kladných hodnot a falešně pozitivních hodnot z každé třídy.| [Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | Průměrná = "micro"|
AUC_Weighted  | AUC je oblasti pod křivkou charakteristiku provozní příjemce. Váha je aritmetický průměr skóre pro každou třídu váženo podle počtu true instancí každé třídy| [Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|Průměrná = "váha"
accuracy|Přesnost je procento předpokládané popisky, které přesně odpovídají true popisky. |[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Žádný|
average_precision_score_macro|Průměrná přesnost shrnuje křivky přesnosti a úplnosti jako vážený průměr přesnosti dosáhnout při každé prahové hodnotě, zvýšení spojené s vracením z předchozí prahové hodnoty použít jako váhu. Aritmetický průměr přesnost skóre každé třídy je – makro|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Průměrná = "makro"|
average_precision_score_micro|Průměrná přesnost shrnuje křivky přesnosti a úplnosti jako vážený průměr přesnosti dosáhnout při každé prahové hodnotě, zvýšení spojené s vracením z předchozí prahové hodnoty použít jako váhu. Micro je vypočítán globálně pomocí kombinace pravdivě pozitivní i falešně pozitivních výsledků při každé odříznutí|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Průměrná = "micro"|
average_precision_score_weighted|Průměrná přesnost shrnuje křivky přesnosti a úplnosti jako vážený průměr přesnosti dosáhnout při každé prahové hodnotě, zvýšení spojené s vracením z předchozí prahové hodnoty použít jako váhu. Váha je aritmetický průměr přesnost průměrné skóre pro každou třídu váženo podle počtu true instancí každé třídy|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Průměrná = "váha"|
balanced_accuracy|Vyvážený přesnost je aritmetický průměr o odvolání pro každou třídu.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Průměrná = "makro"|
f1_score_macro|F1 skóre je průměr přesnosti a odvolání. Aritmetický průměr skóre F1 pro každou třídu je – makro|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Průměrná = "makro"|
f1_score_micro|F1 skóre je průměr přesnosti a odvolání. Micro je vypočítán globálně výpočtem celkového pravdivě pozitivní, falešně negativní a počet falešně pozitivních výsledků|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Průměrná = "micro"|
f1_score_weighted|F1 skóre je průměr přesnosti a odvolání. Vážený průměr třídy frekvence skóre F1 pro každou třídu|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Průměrná = "váha"|
log_loss|Toto je ztráta funkce použitá v logistické regrese (multinomial) a rozšíření jako jsou neuronové sítě definované jako záporné pravděpodobnost protokolu true popisků zadaný pravděpodobnostní třídění předpovědi. Jeden vzorek hodnotou true, pokud popisek yt v {0,1} a odhad pravděpodobnosti yp této yt = 1, ztráta protokolu je – protokolování P (yt&#124;yp) =-(yt log(yp) + (1 - yt) protokolu (1 - yp))|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Žádný|
norm_macro_recall|Normalizovaná si možná Vzpomínáte – makro je makro si možná Vzpomínáte, normalizovaná tak, aby náhodná výkonu má skóre 0 a ideální výkon má skóre 1. Toho dosáhnete pomocí norm_macro_recall: = (recall_score_macro - R) /(1-R), kde R je očekávané hodnotě recall_score_macro pro náhodné predikcí (tj, R = 0,5 pro binární klasifikaci) a R=(1/C) problémů klasifikace C – třída|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Průměrná = "makro" a potom (recall_score_macro - R) /(1-R), kde R je očekávané hodnotě recall_score_macro pro náhodné predikcí (tj, R = 0,5 pro binární klasifikaci) a R=(1/C) problémů klasifikace C – třída|
precision_score_macro|Přesnost je procento prvky označeny jako určité třídy, které jsou ve skutečnosti v dané třídě. Aritmetický průměr přesnosti pro každou třídu je – makro|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Průměrná = "makro"|
precision_score_micro|Přesnost je procento prvky označeny jako určité třídy, které jsou ve skutečnosti v dané třídě. Micro je vypočítán globálně výpočtem celkového pravdivě pozitivní a počet falešně pozitivních výsledků|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Průměrná = "micro"|
precision_score_weighted|Přesnost je procento prvky označeny jako určité třídy, které jsou ve skutečnosti v dané třídě. Váha je aritmetický průměr přesnosti pro každou třídu váženo podle počtu true instancí každé třídy|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Průměrná = "váha"|
recall_score_macro|Odvolání je procento prvky ve skutečnosti určité třídy, které jsou správně označené. Aritmetický průměr o odvolání pro každou třídu je – makro|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Průměrná = "makro"|
recall_score_micro|Odvolání je procento prvky ve skutečnosti určité třídy, které jsou správně označené. Micro je vypočítán globálně výpočtem celkového pravdivě pozitivní, falešně negativní|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Průměrná = "micro"|
recall_score_weighted|Odvolání je procento prvky ve skutečnosti určité třídy, které jsou správně označené. Váha je aritmetický průměr o odvolání pro každou třídu váženo podle počtu true instancí každé třídy|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Průměrná = "váha"|
weighted_accuracy|Vážený přesnost je přesnost, kde je rovna podíl true instancí v tomto příkladu true třídě váhou na každý příklad|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight je rovna poměr této třídy pro každý prvek v cílovém vektor|

## <a name="regression-and-forecasting-metrics"></a>Regrese a Prognózování metriky

Následující metriky jsou uloženy v každé iteraci spuštění pro úlohu regrese nebo předpovědi.

|Metrika|Popis|Výpočet|Další parametry
--|--|--|--|
explained_variance|Bylo vysvětleno odchylky je poměr, ke kterému matematické modelu účty pro variantu dané datové sady. Je, že procento snížení odchylku původní data, která mají odchylku chyby. Pokud chyby je 0, je roven bylo vysvětleno variance.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Žádný|
r2_score|R2 je koeficient stanovení nebo snížení procenta v porovnání s modelem směrný plán, který zobrazí průměr kvadratických chyb. Pokud chyby je 0, je roven bylo vysvětleno variance.|[Výpočet](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Žádný|
spearman_correlation|Korelace spearman je nonparametric míra monotonicity vztah mezi dvěma datovými sadami. Na rozdíl od korelace Pearson korelace Spearman nepředpokládá, že oba datové sady mají obvykle distribuovat. Stejně jako ostatní koeficienty korelace ten se pohybuje mezi -1 a + 1 hodnotou 0 zdání žádnou souvislost. Korelací mezi -1 nebo + 1 implikují přesné monotónní relace. Pozitivní korelace neznamená, že jako x zvýšení zločinců se stejně y. Negativní korelace znamenají, že jako x zvyšují v pořadí, y snižuje.|[Výpočet](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Žádný|
mean_absolute_error|To znamenat, že absolutní chyba je očekávaná hodnota absolutní hodnota rozdílu mezi cílem a do predikce.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Žádný|
normalized_mean_absolute_error|Normalizovaná střední absolutní chyba je střední absolutní chyba rozdělené podle rozsahu dat|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Rozdělit podle rozsahu dat|
median_absolute_error|Střední absolutní chyba je Medián všech absolutních rozdílů mezi cílem a do predikce. To je robustní odlehlé hodnoty.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Žádný|
normalized_median_absolute_error|Normalizovaná střední absolutní chyba je střední absolutní chyba rozdělené podle rozsahu dat|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Rozdělit podle rozsahu dat|
root_mean_squared_error|Kořenové bude kvadratická chyba odmocninu očekávané kvadratickému rozdílu mezi cílem a do predikce.|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Žádný|
normalized_root_mean_squared_error|Normalizovaná kořenové bude kvadratická chyba kořenové střední kvadratické chyby rozdělené podle rozsahu dat|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Rozdělit podle rozsahu dat|
root_mean_squared_log_error|Kořenové znamenat protokolu kvadratických chyb je odmocninu očekávané kvadratická chyba logaritmické|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Žádný|
normalized_root_mean_squared_log_error|Normalizovaná chyba na čtvercovém středním významu protokolu je chyba v kořenovém středním významu protokolu dělená rozsahem dat|[Výpočet](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Rozdělit podle rozsahu dat|

## <a name="next-steps"></a>Další postup

Přečtěte si další informace o [automatizovaném ml](concept-automated-ml.md) v Azure Machine Learning.