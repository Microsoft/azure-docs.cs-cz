---
title: 'Vytvoření modelu Pythonu: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat model vytvoření modelu Pythonu v Azure Machine Learning k vytvoření vlastního modulu modelování nebo zpracování dat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ms.openlocfilehash: 33e15055958ac99f2aa9eb160f9e5cf3c5b0cd41
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493797"
---
# <a name="create-python-model"></a>Vytvoření modelu Pythonu

Tento článek popisuje, jak pomocí modulu **vytvořit model Pythonu** vytvořit z skriptu Pythonu nevlakový model. 

Model můžete založit na jakémkoli učení, které je součástí balíčku Pythonu v prostředí návrháře Azure Machine Learning. 

Po vytvoření modelu můžete použít [model výuky](train-model.md) pro výuku modelu pro datovou sadu, jako je jakékoli jiné informace v Azure Machine Learning. Vyškolený model může být předán [modelu skóre](score-model.md) pro použití modelu k vytvoření předpovědi. Vyškolený model se pak dá uložit a pracovní postup bodování je možné publikovat jako webovou službu.

> [!WARNING]
> V současné době není možné předat výsledky skóre modelu Pythonu k [vyhodnocení modelu](evaluate-model.md). Pokud potřebujete vyhodnotit model, můžete napsat vlastní skript Pythonu a spustit ho pomocí modulu [spuštění skriptu Pythonu](execute-python-script.md) .  


## <a name="how-to-configure-create-python-model"></a>Jak nakonfigurovat vytváření modelů Pythonu

Použití tohoto modulu vyžaduje mezilehlé nebo odborné znalosti Pythonu. Modul podporuje použití všech informací, které jsou součástí balíčků Pythonu, které jsou už nainstalované v Azure Machine Learning. Podívejte se na seznam předem nainstalovaných balíčků Pythonu ve [skriptu spustit](execute-python-script.md)v jazyce Python.
  

V tomto článku se dozvíte, jak pomocí jednoduchého kanálu **vytvořit model Pythonu** . Níže je uveden graf kanálu.

![Vytvoření – Python-model](./media/module/aml-create-python-model.png)

1.  Klikněte na **vytvořit model Pythonu**, upravte skript pro implementaci modelování nebo procesu správy dat. Model můžete založit na jakémkoli učení, které je součástí balíčku Pythonu v prostředí Azure Machine Learning.


    Níže je ukázkový kód pro třídění dvou tříd Naive Bayes pomocí oblíbeného balíčku *skriptu sklearn* .

```Python

# The script MUST define a class named AzureMLModel.
# This class MUST at least define the following three methods:
    # __init__: in which self.model must be assigned,
    # train: which trains self.model, the two input arguments must be pandas DataFrame,
    # predict: which generates prediction result, the input argument and the prediction result MUST be pandas DataFrame.
# The signatures (method names and argument names) of all these methods MUST be exactly the same as the following example.


import pandas as pd
from sklearn.naive_bayes import GaussianNB


class AzureMLModel:
    def __init__(self):
        self.model = GaussianNB()
        self.feature_column_names = list()

    def train(self, df_train, df_label):
        self.feature_column_names = df_train.columns.tolist()
        self.model.fit(df_train, df_label)

    def predict(self, df):
        return pd.DataFrame(
            {'Scored Labels': self.model.predict(df[self.feature_column_names]), 
             'probabilities': self.model.predict_proba(df[self.feature_column_names])[:, 1]}
        )


```


2. Připojte modul pro **Vytvoření modelu Pythonu** , který jste právě vytvořili, pro model **výuky** a **Model stanovení skóre**

3. Pokud potřebujete tento model vyhodnotit, přidejte skript, který [spustí Python](execute-python-script.md) , a upravte skript Pythonu pro implementaci vyhodnocení.

Níže je ukázkový zkušební kód.

```Python


# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# imports up here can be used to 
import pandas as pd

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a pandas.DataFrame
#   Param<dataframe2>: a pandas.DataFrame
def azureml_main(dataframe1 = None, dataframe2 = None):
    
    from sklearn.metrics import accuracy_score, precision_score, recall_score, roc_auc_score, roc_curve
    import pandas as pd
    import numpy as np
    
    scores = dataframe1.ix[:, ("income", "Scored Labels", "probabilities")]
    ytrue = np.array([0 if val == '<=50K' else 1 for val in scores["income"]])
    ypred = np.array([0 if val == '<=50K' else 1 for val in scores["Scored Labels"]])    
    probabilities = scores["probabilities"]
    
    accuracy, precision, recall, auc = \
    accuracy_score(ytrue, ypred),\
    precision_score(ytrue, ypred),\
    recall_score(ytrue, ypred),\
    roc_auc_score(ytrue, probabilities)
    
    metrics = pd.DataFrame();
    metrics["Metric"] = ["Accuracy", "Precision", "Recall", "AUC"];
    metrics["Value"] = [accuracy, precision, recall, auc]
    
    return metrics,

```
