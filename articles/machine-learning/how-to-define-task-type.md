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
ms.openlocfilehash: fef3f0347d963b8a7d5fe2f1ca771293e9c3d2a9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80475499"
---
# <a name="how-to-define-a-machine-learning-task"></a>Jak definovat úlohu strojového učení 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se seznámíte s podporovanými úlohami strojového učení a postupem jejich definování pro automatizované experimenty strojového učení (automatizovaného ML).


## <a name="what-is-a-machine-learning-task"></a><a name="What is a machine learning task?"></a>Co je úloha strojového učení?

Úloha strojového učení představuje typ problému, který se řeší, vytvořením prediktivního modelu. Automatizované ML podporuje tři různé typy úloh, včetně klasifikace, regrese a prognózy časových řad.

Typ úlohy| Popis| Příklad
----|----|----
Classification | Úkol pro předpověď kategorie určitého řádku v datové sadě. | Odhalování podvodů na platební kartě. U cílového sloupce by byl **zjištěn podvod** s kategoriemi *true* nebo *false*. V tomto případě budeme klasifikovat každý řádek v datech buď jako true, nebo false.
Regrese | Úkol pro předpověď výstupu průběžného množství. | Náklady na automobil na základě jeho funkcí, cílový sloupec by byl **Price**.
Prognózování |Úkol pro zajištění informovanéch odhadů při určování směru budoucích trendů.| Předpověď poptávky energie za další 48 hodin. Cílový sloupec by byl **Demand** a předpokládané hodnoty by se měly použít k zobrazení vzorů v poptávce na energii.

Automatizované ML během procesu automatizace a optimalizace podporuje následující algoritmy. Jako uživatel není nutné zadávat algoritmus.

Classification | Regrese | Prognózování časové řady
-- |-- |--
[Logistická regrese](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Elastická síť](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Elastická síť](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Lehký GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Lehký GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Lehký GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Zvýšení barevného přechodu](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Zvýšení barevného přechodu](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Zvýšení barevného přechodu](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Rozhodovací strom](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Rozhodovací strom](https://scikit-learn.org/stable/modules/tree.html#regression)|[Rozhodovací strom](https://scikit-learn.org/stable/modules/tree.html#regression)
[K nejbližším sousedním sousedům](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K nejbližším sousedním sousedům](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K nejbližším sousedním sousedům](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Lineární SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[C++ – podpora klasifikace vektoru (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Stochastickéhoový přechod klesá (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Stochastickéhoový přechod klesá (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Náhodný les](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Náhodný les](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Náhodný les](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Extrémně náhodné stromy](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extrémně náhodné stromy](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extrémně náhodné stromy](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Třídění DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN regresor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN regresor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN lineární třídění](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Lineární regresor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Lineární regresor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naivní Bayesovy klasifikátory](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)||
[Stochastickéhoový přechod klesá (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)||


### <a name="set-the-task-type"></a>Nastavení typu úlohy
Můžete nastavit typ úkolu pro automatizované experimenty ML pomocí sady SDK nebo Azure Machine Learning studia.

Použijte `task` parametr v `AutoMLConfig` konstruktoru k určení typu experimentu.

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
+ Vyzkoušejte kurz [automatizovaného Machine Learningho vyhodnocení](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb) .
+ Vyzkoušejte ukázkový Poznámkový blok [automatizované Machine Learning regrese](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb) .

