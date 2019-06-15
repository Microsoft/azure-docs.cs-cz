---
title: 'Vytvoření modelu Pythonu: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak vytvořit vlastní modul modelování nebo zpracování dat pomocí modelu vytvořit Model Python ve službě Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ea9b50cede3e2d264ca0476b6a987ca6896c3c79
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029742"
---
# <a name="create-python-model"></a>Vytvoření modelu Pythonu

Tento článek popisuje způsob použití **vytvořit Model Python** modul k vytváření Nezkušený modelu ze skriptu Pythonu. 

Model můžete založit na jakékoli student, který je součástí balíčku Pythonu v prostředí Azure Machine Learning. 

Po vytvoření modelu, můžete použít [trénování modelu](train-model.md) pro trénování modelu pro datovou sadu, jako jsou jiné learner ve službě Azure Machine Learning. Může trénovaný model předán [Score Model](score-model.md) použít model k predikci. Pak lze uložit trénovaného modelu a vyhodnocení pracovního postupu lze publikovat jako webovou službu.

> [!WARNING]
> Aktuálně není možné předat Vyhodnocená výsledky model Python k [Evaluate Model](evaluate-model.md). Pokud potřebujete k vyhodnocení modelu, můžete napsat vlastní Python skript a spusťte ho pomocí [Execute Python Script](execute-python-script.md) modulu.  


## <a name="how-to-configure-create-python-model"></a>Jak nakonfigurovat vytvořit Model Python

Použijte tento modul vyžaduje zprostředkující nebo odborné znalosti Pythonu. Modul podporuje použití všech student, který je součástí balíčků Python, který je již nainstalován ve službě Azure Machine Learning. Zobrazit předinstalované seznam balíčků Python v [Execute Python Script](execute-python-script.md).
  

Tento článek vám ukáže způsob použití **vytvořit Model Python** s jednoduchý experiment. Níže je graf experimentu.

![create-python-model](./media/module/aml-create-python-model.png)

1.  Klikněte na tlačítko **vytvořit Model Python**, upravte skript, který chcete implementovat vaše modelování nebo zpracování dat správy. Model můžete založit na jakékoli student, který je součástí balíček Pythonu v prostředí Azure Machine Learning.


    Tady je ukázkový kód dvěma třídami Naive Bayes třídění s využitím Oblíbené *skriptu sklearn* balíčku.

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


2. Připojení **vytvořit Model Python** modulu, které jste právě vytvořili, **Train Model** a **určení skóre modelu**

3. Pokud potřebujete k vyhodnocení modelu, přidejte [Execute Python Script](execute-python-script.md) a upravte skript Pythonu pro implementaci hodnocení.

Tady je ukázkový kód hodnocení.

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
