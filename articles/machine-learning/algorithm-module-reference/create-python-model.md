---
title: 'Vytvořit model Pythonu: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu Vytvořit model Pythonu v Azure Machine Learning vytvořit vlastní modelování nebo zpracování dat modulu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: 929938bba9c9512ecfd663a540cf4a7ebbf68e2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371813"
---
# <a name="create-python-model-module"></a>Vytvořit modul Modelu Pythonu

Tento článek popisuje modul v návrháři Azure Machine Learning (preview).

Přečtěte si, jak pomocí modulu Vytvořit model Pythonu vytvořit netrénovaný model ze skriptu Pythonu. Model můžete založit na jakémkoli studentovi, který je součástí balíčku Pythonu v prostředí návrháře Azure Machine Learning. 

Po vytvoření modelu můžete použít [Model Train](train-model.md) k trénování modelu v datové sadě, jako každý jiný student v Azure Machine Learning. Trénovaný model může být předán [skóre modelu,](score-model.md) aby předpovědi. Potom můžete uložit trénovaný model a publikovat pracovní postup hodnocení jako webovou službu.

> [!WARNING]
> V současné době není možné předat výsledky skóre modelu [Pythonu k vyhodnocení modelu](evaluate-model.md). Pokud potřebujete vyhodnotit model, můžete napsat vlastní skript Pythonu a spustit jej pomocí modulu [Spustit skript Pythonu.](execute-python-script.md)  


## <a name="configure-the-module"></a>Konfigurace modulu

Použití tohoto modulu vyžaduje středně pokročilé nebo odborné znalosti Pythonu. Modul podporuje použití všech studentů, který je součástí balíčků Pythonu, které jsou už nainstalované v Azure Machine Learning. Podívejte se na předinstalovaný seznam balíčků Pythonu v [aplikaci Execute Python Script](execute-python-script.md).
  

Tento článek ukazuje, jak používat **vytvořit model Pythonu** s jednoduchým kanálem. Zde je diagram potrubí:

![Diagram vytvoření modelu Pythonu](./media/module/create-python-model.png)

1. Vyberte **Vytvořit model Pythonu**a upravte skript, abyste implementovali proces modelování nebo správy dat. Model můžete založit na jakémkoli studentovi, který je součástí balíčku Pythonu v prostředí Azure Machine Learning.

   Následující ukázkový kód klasifikátoru Dvoutřídy Naive Bayes používá populární balíček *sklearn:*

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

1. Připojte modul **Vytvořit model Pythonu,** který jste právě vytvořili pro **trénování modelu** a modelu **skóre**.

1. Pokud potřebujete model vyhodnotit, přidejte modul [Spustit skript Pythonu](execute-python-script.md) a upravte skript Pythonu.

   Následující skript je ukázkový kód hodnocení:

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