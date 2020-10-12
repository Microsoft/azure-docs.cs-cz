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
ms.date: 06/18/2020
ms.openlocfilehash: 03f80ebeb7b30d8c0fe14060335541d77b7523b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90898624"
---
# <a name="create-python-model-module"></a>Vytvořit modul Python model

Tento článek popisuje modul v Návrháři Azure Machine Learning.

Naučte se používat modul vytvořit model Python k vytvoření nevýukového modelu ze skriptu Pythonu. Model můžete založit na jakémkoli učení, které je součástí balíčku Pythonu v prostředí návrháře Azure Machine Learning. 

Po vytvoření modelu můžete použít [model výuky](train-model.md) pro výuku modelu pro datovou sadu, jako je jakékoli jiné informace v Azure Machine Learning. Školený model lze předat [modelu skóre](score-model.md) , aby bylo možné provést předpovědi. Potom můžete uložit trained model a publikovat pracovní postup bodování jako webovou službu.

> [!WARNING]
> V současné době není možné tento modul připojit k **ladění modelu modulu parametrů** nebo předávat výsledky skóre modelu Pythonu k [vyhodnocení modelu](evaluate-model.md). Pokud potřebujete ladit moje parametry nebo vyhodnotit model, můžete napsat vlastní skript Pythonu pomocí modulu [Spustit skript Pythonu](execute-python-script.md) .


## <a name="configure-the-module"></a>Konfigurace modulu

Použití tohoto modulu vyžaduje mezilehlé nebo odborné znalosti Pythonu. Modul podporuje použití všech informací, které jsou součástí balíčků Pythonu, které jsou už nainstalované v Azure Machine Learning. Podívejte se na seznam předinstalovaných balíčků Pythonu v tématu [spuštění skriptu Pythonu](execute-python-script.md).

> [!NOTE]
> Při psaní skriptu Buďte velmi opatrní a zajistěte, aby nedošlo k žádné chybě syntaxe, jako je například použití nedeklarovaného objektu nebo neimportovaného modulu.

> [!NOTE]
> Navíc věnujte další pozornost seznamu předinstalovaných modulů v příkazu [Spustit skript Pythonu](execute-python-script.md). Importujte jenom předem nainstalované moduly. V tomto skriptu neinstalujte dodatečné balíčky, jako je například "PIP Install xgboost", jinak se při čtení modelů v modulech s nižšími proudy vyvolá chyby.
  
V tomto článku se dozvíte, jak pomocí jednoduchého kanálu **vytvořit model Pythonu** . Zde je diagram kanálu:

![Diagram vytvoření modelu Pythonu](./media/module/create-python-model.png)

1. Vyberte **vytvořit model Python**a upravte skript pro implementaci modelování nebo procesu správy dat. Model můžete založit na jakémkoli učení, které je součástí balíčku Pythonu v prostředí Azure Machine Learning.

> [!NOTE]
> Věnujte zvláštní pozornost komentářům v ukázkovém kódu skriptu a ujistěte se, že skript přesně dodržuje požadavky, včetně názvu třídy, metod a signatury metody. Porušení bude mít za následek výjimky. 

   Následující vzorový kód Naive Bayes klasifikátoru používá oblíbený balíček *skriptu sklearn* :

   ```Python

   # The script MUST define a class named AzureMLModel.
   # This class MUST at least define the following three methods:
       # __init__: in which self.model must be assigned,
       # train: which trains self.model, the two input arguments must be pandas DataFrame,
       # predict: which generates prediction result, the input argument and the prediction result MUST be pandas DataFrame.
   # The signatures (method names and argument names) of all these methods MUST be exactly the same as the following example.

   # Please do not install extra packages such as "pip install xgboost" in this script,
   # otherwise errors will be raised when reading models in down-stream modules.
   
   import pandas as pd
   from sklearn.naive_bayes import GaussianNB


   class AzureMLModel:
       def __init__(self):
           self.model = GaussianNB()
           self.feature_column_names = list()

       def train(self, df_train, df_label):
           # self.feature_column_names records the column names used for training.
           # It is recommended to set this attribute before training so that the
           # feature columns used in predict and train methods have the same names.
           self.feature_column_names = df_train.columns.tolist()
           self.model.fit(df_train, df_label)

       def predict(self, df):
           # The feature columns used for prediction MUST have the same names as the ones for training.
           # The name of score column ("Scored Labels" in this case) MUST be different from any other columns in input data.
           return pd.DataFrame(
               {'Scored Labels': self.model.predict(df[self.feature_column_names]), 
                'probabilities': self.model.predict_proba(df[self.feature_column_names])[:, 1]}
           )


   ```

2. Připojte modul pro **Vytvoření modelu Pythonu** , který jste právě vytvořili, ke **výukovém** modelu a **modelu hodnocení**.

3. Pokud potřebujete tento model vyhodnotit, přidejte modul [spuštění skriptu Pythonu](execute-python-script.md) a upravte skript Pythonu.

   Následující skript je ukázkový zkušební kód:

   ```Python


   # The script MUST contain a function named azureml_main
   # which is the entry point for this module.

   # imports up here can be used to 
   import pandas as pd

   # The entry point function MUST have two input arguments:
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