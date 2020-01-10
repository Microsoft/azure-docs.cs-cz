---
title: Definování úlohy strojového učení pro automatizované spuštění strojového učení
titleSuffix: Azure Machine Learning
description: Naučte se definovat úlohu strojového učení pro automatizované spuštění strojového učení.
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 11/04/2019
ms.openlocfilehash: a58959904559cd3b2cec27762f2df93cfea72abd
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2019
ms.locfileid: "75540526"
---
# <a name="how-to-define-a-machine-learning-task"></a>Jak definovat úlohu strojového učení 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se seznámíte s podporovanými úlohami strojového učení a postupem jejich definování pro automatizované experimenty strojového učení (automatizovaného ML).


## <a name="What is a machine learning task?"></a>Co je úloha strojového učení?

Úloha strojového učení představuje typ problému, který se řeší, vytvořením prediktivního modelu. Automatizované ML podporuje tři různé typy úloh, včetně klasifikace, regrese a prognózy časových řad.

Typ úlohy| Popis| Příklad:
----|----|----
Classification | Úkol pro předpověď kategorie určitého řádku v datové sadě. | Odhalování podvodů na platební kartě. U cílového sloupce by byl **zjištěn podvod** s kategoriemi *true* nebo *false*. V tomto případě budeme klasifikovat každý řádek v datech buď jako true, nebo false.
Regrese | Úkol pro předpověď výstupu průběžného množství. | Náklady na automobil na základě jeho funkcí, cílový sloupec by byl **Price**.
Prognózování |Úkol pro zajištění informovanéch odhadů při určování směru budoucích trendů.| Předpověď poptávky energie za další 48 hodin. Cílový sloupec by byl **Demand** a předpokládané hodnoty by se měly použít k zobrazení vzorů v poptávce na energii.

Automatizované ML během procesu automatizace a optimalizace podporuje následující algoritmy. Jako uživatel není nutné lze určit algoritmus.

Classification | Regrese | Prognózování časových řad
-- |-- |--
[Logistické regrese](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Elastické Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Elastické Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Světlý GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Světlý GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Světlý GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Přechodu zvýšení skóre](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Přechodu zvýšení skóre](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Přechodu zvýšení skóre](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Rozhodovací strom](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Rozhodovací strom](https://scikit-learn.org/stable/modules/tree.html#regression)|[Rozhodovací strom](https://scikit-learn.org/stable/modules/tree.html#regression)
[K nejbližší okolí](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K nejbližší okolí](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K nejbližší okolí](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Lineární SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS laso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS laso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Vektorové podpory C klasifikace (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Pomocí stochastického sestupu (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Pomocí stochastického sestupu (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Náhodné doménové struktury](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Náhodné doménové struktury](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Náhodné doménové struktury](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Velmi náhodnou stromů](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Velmi náhodnou stromů](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Velmi náhodnou stromů](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Třídění DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN regresor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN regresor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN lineární třídění](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Lineární regresor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Lineární regresor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)||
[Pomocí stochastického sestupu (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)||


### <a name="set-the-task-type"></a>Nastavení typu úlohy
Můžete nastavit typ úkolu pro automatizované experimenty ML pomocí sady SDK nebo Azure Machine Learning studia.

K určení typu experimentu použijte parametr `task` v konstruktoru `AutoMLConfig`.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

Úkol můžete nastavit jako součást tvorby experimentu v rámci automatizovaného testu ML v Azure Machine Learning Studiu. 

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

![Výběr typu úlohy](./media/how-to-define-task-type/task-type.png)


## <a name="next-steps"></a>Další kroky

+ Přečtěte si další informace o [automatizovaném ml](concept-automated-ml.md) v Azure Machine Learning.
+ Přečtěte si další informace o [automatickém školení modelu prognózy časových řad](how-to-auto-train-forecast.md) v Azure Machine Learning
+ Vyzkoušejte kurz [automatizovaného Machine Learningho vyhodnocení](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation) .
+ Vyzkoušejte ukázkový Poznámkový blok [automatizované Machine Learning regrese](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation) .

