---
title: 'Vytvoření modelu Pythonu: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se, jak pomocí modulu vytvořit model Pythonu v Azure Machine Learning vytvořit vlastní modul pro modelování nebo zpracování dat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: 929938bba9c9512ecfd663a540cf4a7ebbf68e2b
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371813"
---
# <a name="create-python-model-module"></a>Vytvořit modul Python model

Tento článek popisuje modul v Návrháři Azure Machine Learning (Preview).

Naučte se používat modul vytvořit model Python k vytvoření nevýukového modelu ze skriptu Pythonu. Model můžete založit na jakémkoli učení, které je součástí balíčku Pythonu v prostředí návrháře Azure Machine Learning. 

Po vytvoření modelu můžete použít [model výuky](train-model.md) pro výuku modelu pro datovou sadu, jako je jakékoli jiné informace v Azure Machine Learning. Školený model lze předat [modelu skóre](score-model.md) , aby bylo možné provést předpovědi. Potom můžete uložit trained model a publikovat pracovní postup bodování jako webovou službu.

> [!WARNING]
> V současné době není možné předat výsledky skóre modelu Pythonu k [vyhodnocení modelu](evaluate-model.md). Pokud potřebujete vyhodnotit model, můžete napsat vlastní skript Pythonu a spustit ho pomocí modulu [spuštění skriptu Pythonu](execute-python-script.md) .  


## <a name="configure-the-module"></a>Konfigurace modulu

Použití tohoto modulu vyžaduje mezilehlé nebo odborné znalosti Pythonu. Modul podporuje použití všech informací, které jsou součástí balíčků Pythonu, které jsou už nainstalované v Azure Machine Learning. Podívejte se na seznam předinstalovaných balíčků Pythonu v tématu [spuštění skriptu Pythonu](execute-python-script.md).
  

V tomto článku se dozvíte, jak pomocí jednoduchého kanálu **vytvořit model Pythonu** . Zde je diagram kanálu:

![Diagram vytvoření modelu Pythonu](./media/module/create-python-model.png)

1. Vyberte **vytvořit model Python**a upravte skript pro implementaci modelování nebo procesu správy dat. Model můžete založit na jakémkoli učení, které je součástí balíčku Pythonu v prostředí Azure Machine Learning.

   Následující vzorový kód Naive Bayes klasifikátoru používá oblíbený balíček *skriptu sklearn* :

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

1. Připojte modul pro **Vytvoření modelu Pythonu** , který jste právě vytvořili, ke **výukovém** modelu a **modelu hodnocení**.

1. Pokud potřebujete tento model vyhodnotit, přidejte modul [spuštění skriptu Pythonu](execute-python-script.md) a upravte skript Pythonu.

   Následující skript je ukázkový zkušební kód:

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

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 