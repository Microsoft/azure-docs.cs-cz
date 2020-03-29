---
title: Definování úlohy strojového učení pro automatizovaný běh strojového učení
titleSuffix: Azure Machine Learning
description: Zjistěte, jak definovat úlohu strojového učení pro automatizovaný běh strojového učení
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 11/04/2019
ms.openlocfilehash: a58959904559cd3b2cec27762f2df93cfea72abd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75540526"
---
# <a name="how-to-define-a-machine-learning-task"></a>Jak definovat úlohu strojového učení 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte podporované úlohy strojového učení a jak je definovat pro spuštění experimentu automatizovaného strojového učení (automatizované ml).


## <a name="what-is-a-machine-learning-task"></a><a name="What is a machine learning task?"></a>Co je úkol strojového učení?

Úloha strojového učení představuje typ problému, který se řeší vytvořením prediktivního modelu. Automatizované ML podporuje tři různé typy úkolů, včetně klasifikace, regrese a prognózy časových řad.

Typ úkolu| Popis| Příklad
----|----|----
Classification | Úloha pro předvídání kategorie určitého řádku v datové sadě. | Odhalování podvodů na kreditní kartě. Cílový sloupec by **byl podvod zjištěn** s kategoriemi *Pravda* nebo *Nepravda*. V tomto případě klasifikujeme každý řádek v datech jako true nebo false.
Regrese | Úloha pro předvídání výstupu souvislého množství. | Náklady na automobil na základě jeho vlastností, cílový sloupec by **cena**.
Prognózování |Úkol pro vytváření informovaných odhadů při určování směru budoucích trendů.| Předpovídání poptávky po energii pro ně příštích 48 hodin. Cílovýsloupec by **byla poptávka** a předpokládané hodnoty by se použily k zobrazení vzorců v poptávce po energii.

Automatizované ML podporuje následující algoritmy během automatizace a ladění procesu. Jako uživatel není nutné zadat algoritmus.

Classification | Regrese | Prognózování časové řady
-- |-- |--
[Logistická regrese](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Elastická síť](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Elastická síť](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Lehký GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Lehký GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Lehký GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Zesílení přechodu](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Zesílení přechodu](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Zesílení přechodu](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Rozhodovací strom](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Rozhodovací strom](https://scikit-learn.org/stable/modules/tree.html#regression)|[Rozhodovací strom](https://scikit-learn.org/stable/modules/tree.html#regression)
[K Nejbližší sousedé](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Nejbližší sousedé](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Nejbližší sousedé](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Lineární SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Klasifikace vektorů podpory C (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Stochastický gradient nísestup (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Stochastický gradient nísestup (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Náhodný les](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Náhodný les](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Náhodný les](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Extrémně randomizované stromy](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extrémně randomizované stromy](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extrémně randomizované stromy](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[DNN třídění](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN regresor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN regresor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Lineární klasifikátor DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Lineární regresor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Lineární regresor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naivní Bayesovy klasifikátory](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)||
[Stochastický gradient nísestup (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)||


### <a name="set-the-task-type"></a>Nastavení typu úkolu
Můžete nastavit typ úlohy pro automatizované experimenty ML s Sadou SDK nebo Azure Machine Learning studio.

Pomocí `task` parametru `AutoMLConfig` v konstruktoru určete typ experimentu.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

Úlohu můžete nastavit jako součást automatického spuštění experimentu ML ve studiu Azure Machine Learning. 

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

![Výběr typu úkolu](./media/how-to-define-task-type/task-type.png)


## <a name="next-steps"></a>Další kroky

+ Další informace o [automatizovanéml](concept-automated-ml.md) v Azure Machine Learning.
+ Další informace o [automatickém trénování modelu prognózy časových řad](how-to-auto-train-forecast.md) v Azure Machine Learning
+ Vyzkoušejte kurz [klasifikace automatizovaného strojového učení.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation)
+ Vyzkoušejte ukázkový poznámkový blok [automatického učení.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation)

