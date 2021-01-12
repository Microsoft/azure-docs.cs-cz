---
title: Interpretace & vysvětlit ML v Pythonu (Preview)
titleSuffix: Azure Machine Learning
description: Naučte se, jak získat vysvětlení způsobu, jakým váš model strojového učení určuje důležitost funkcí a který je předpovědi při použití sady SDK Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: mithigpe
author: minthigpen
ms.reviewer: Luis.Quintanilla
ms.date: 07/09/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 74ddaaf7a2d279439c0cd27ba0840f02f297877b
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98119556"
---
# <a name="use-the-interpretability-package-to-explain-ml-models--predictions-in-python-preview"></a>Vysvětlení modelů ML & předpovědi v Pythonu (Preview) pomocí balíčku pro interpretaci



V tomto průvodci se naučíte používat balíček pro interpretaci sady Azure Machine Learning Python SDK k provádění následujících úloh:


* Vysvětlete kompletní chování modelu nebo individuální předpovědi na osobním počítači v místním počítači.

* Povolte techniky interpretace pro inženýrské funkce.

* Vysvětlete chování celého modelu a jednotlivých předpovědi v Azure.

* Pomocí řídicího panelu vizualizace můžete pracovat s vysvětlením modelu.

* Nasaďte vysvětlující bodování spolu s modelem a sledujte vysvětlení během Inferencing.



Další informace o podporovaných technikách interpretace a modelech strojového učení najdete v tématu věnovaném [interpretaci modelu v Azure Machine Learning](how-to-machine-learning-interpretability.md) a [ukázkových poznámkových blocích](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).

## <a name="generate-feature-importance-value-on-your-personal-machine"></a>Vygenerovat hodnotu důležitosti funkce na vašem osobním počítači 
Následující příklad ukazuje, jak použít balíček pro interpretaci na osobním počítači bez kontaktování služeb Azure.

1. Nainstalujte balíček `azureml-interpret`.
    ```bash
    pip install azureml-interpret
    ```

2. Výuka ukázkového modelu v místní Jupyter Notebook.

    ```python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    
    # split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data,            
                                                        breast_cancer_data.target,  
                                                        test_size=0.2,
                                                        random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

3. Zavolejte na místní vysvětlení.
   * Chcete-li inicializovat objekt vysvětlujícího objektu, předejte svůj model a určitou výukovou data konstruktoru vysvětlujícího.
   * Chcete-li, aby vaše vysvětlení a vizualizace byly více informativní, můžete při provádění klasifikace předávat názvy funkcí a názvy výstupních tříd.

   Následující bloky kódu ukazují, jak vytvořit instanci objektu vysvětlit pomocí `TabularExplainer` , `MimicExplainer` a `PFIExplainer` místně.
   * `TabularExplainer` volá jedno ze tří SHAP vysvětlení pod ( `TreeExplainer` , `DeepExplainer` nebo `KernelExplainer` ).
   * `TabularExplainer` automaticky vybere nejvhodnější z nich pro váš případ použití, ale můžete zavolat každé ze svých tří základních vysvětlujícíů přímo.

    ```python
    from interpret.ext.blackbox import TabularExplainer

    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    nebo

    ```python

    from interpret.ext.blackbox import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    
    from interpret.ext.glassbox import LGBMExplainableModel
    from interpret.ext.glassbox import LinearExplainableModel
    from interpret.ext.glassbox import SGDExplainableModel
    from interpret.ext.glassbox import DecisionTreeExplainableModel

    # "features" and "classes" fields are optional
    # augment_data is optional and if true, oversamples the initialization examples to improve surrogate model accuracy to fit original model.  Useful for high-dimensional data where the number of rows is less than the number of columns.
    # max_num_of_augmentations is optional and defines max number of times we can increase the input data size.
    # LGBMExplainableModel can be replaced with LinearExplainableModel, SGDExplainableModel, or DecisionTreeExplainableModel
    explainer = MimicExplainer(model, 
                               x_train, 
                               LGBMExplainableModel, 
                               augment_data=True, 
                               max_num_of_augmentations=10, 
                               features=breast_cancer_data.feature_names, 
                               classes=classes)
    ```

    nebo

    ```python
    from interpret.ext.blackbox import PFIExplainer

    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model,
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

### <a name="explain-the-entire-model-behavior-global-explanation"></a>Vysvětlit chování celého modelu (globální vysvětlení) 

V následujícím příkladu se dozvíte, jak získat agregované hodnoty důležitosti funkcí (Global).

```python

# you can use the training data or the test data here, but test data would allow you to use Explanation Exploration
global_explanation = explainer.explain_global(x_test)

# if you used the PFIExplainer in the previous step, use the next line of code instead
# global_explanation = explainer.explain_global(x_train, true_labels=y_train)

# sorted feature importance values and feature names
sorted_global_importance_values = global_explanation.get_ranked_global_values()
sorted_global_importance_names = global_explanation.get_ranked_global_names()
dict(zip(sorted_global_importance_names, sorted_global_importance_values))

# alternatively, you can print out a dictionary that holds the top K feature names and values
global_explanation.get_feature_importance_dict()
```

### <a name="explain-an-individual-prediction-local-explanation"></a>Vysvětlit jednotlivou předpověď (místní vysvětlení)
Získejte hodnoty důležitosti jednotlivých funkcí různých datapoints voláním vysvětlení pro jednotlivé instance nebo skupiny instancí.
> [!NOTE]
> `PFIExplainer` nepodporuje místní vysvětlení.

```python
# get explanation for the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

### <a name="raw-feature-transformations"></a>Nezpracované transformace funkcí

Místo navržených funkcí se můžete rozhodnout, že získáte vysvětlení z nezpracovaných a netransformovaných funkcí. Pro tuto možnost předáte do vysvětlujícího kanálu kanál transformace vaší funkce `train_explain.py` . V opačném případě vysvětluje vysvětlení v souvislosti s funkcemi pro inženýry.

Formát podporovaných transformací je stejný, jak je popsáno v [skriptu sklearn-PANDAS](https://github.com/scikit-learn-contrib/sklearn-pandas). Obecně platí, že všechny transformace jsou podporovány, pokud pracují v jednom sloupci, aby byly jasné, že jsou 1: n.

Získejte vysvětlení pro nezpracované funkce pomocí sady `sklearn.compose.ColumnTransformer` nebo se seznamem namontovaných řazených kolekcí členů transformátoru. Následující příklad používá `sklearn.compose.ColumnTransformer` .

```python
from sklearn.compose import ColumnTransformer

numeric_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='median')),
    ('scaler', StandardScaler())])

categorical_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='constant', fill_value='missing')),
    ('onehot', OneHotEncoder(handle_unknown='ignore'))])

preprocessor = ColumnTransformer(
    transformers=[
        ('num', numeric_transformer, numeric_features),
        ('cat', categorical_transformer, categorical_features)])

# append classifier to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', preprocessor),
                      ('classifier', LogisticRegression(solver='lbfgs'))])


# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=preprocessor)
```

V případě, že chcete spustit příklad se seznamem namontovaných řazených kolekcí členů Transformer, použijte následující kód:

```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(
    strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(
    handle_unknown='ignore', sparse=False)) for f in categorical]

transformations = numeric_transformations + categorical_transformations

# append model to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', DataFrameMapper(transformations)),
                      ('classifier', LogisticRegression(solver='lbfgs'))])

# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=transformations)
```

## <a name="generate-feature-importance-values-via-remote-runs"></a>Generování hodnot důležitosti funkcí prostřednictvím vzdálených běhů

Následující příklad ukazuje, jak lze použít `ExplanationClient` třídu pro povolení interpretace modelu pro vzdálené běhy. Je koncepčně podobný místnímu procesu, s výjimkou:

* Pomocí `ExplanationClient` ve vzdáleném spuštění nahrajte kontext interpretace.
* Stáhněte kontext později v místním prostředí.

1. Nainstalujte balíček `azureml-interpret`.
    ```bash
    pip install azureml-interpret
    ```
1. Vytvořte školicí skript v místním Jupyter Notebook. Například `train_explain.py`.

    ```python
    from azureml.interpret import ExplanationClient
    from azureml.core.run import Run
    from interpret.ext.blackbox import TabularExplainer

    run = Run.get_context()
    client = ExplanationClient.from_run(run)

    # write code to get and split your data into train and test sets here
    # write code to train your model here 

    # explain predictions on your local machine
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=feature_names, 
                                 classes=classes)

    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    
    # uploading global model explanation data for storage or visualization in webUX
    # the explanation can then be downloaded on any compute
    # multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

1. Nastavte Azure Machine Learning COMPUTE jako cíl výpočtů a odešlete školicí běh. Pokyny najdete v tématu [Vytvoření a Správa výpočetních clusterů Azure Machine Learning](how-to-create-attach-compute-cluster.md) . Můžete také najít užitečné [ukázkové poznámkové bloky](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation) .

1. Stáhněte si vysvětlení v místní Jupyter Notebook.

    ```python
    from azureml.interpret import ExplanationClient
    
    client = ExplanationClient.from_run(run)
    
    # get model explanation data
    explanation = client.download_model_explanation()
    # or only get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```


## <a name="visualizations"></a>Vizualizace

Po stažení vysvětlení v místní Jupyter Notebook můžete k pochopení a interpretaci modelu použít řídicí panel vizualizace. Pokud chcete do Jupyter Notebook načíst widget řídicího panelu vizualizace, použijte následující kód:

```python
from interpret_community.widget import ExplanationDashboard

ExplanationDashboard(global_explanation, model, datasetX=x_test)
```

Tato vizualizace podporuje vysvětlení na základě inženýrů a nezpracovaných funkcí. Nehrubá vysvětlení jsou založená na funkcích z původní datové sady a popsaných vysvětlení jsou založená na funkcích z datové sady s použitou technikou funkcí.

Při pokusu o interpretaci modelu s ohledem na původní datovou sadu se doporučuje použít nezpracované vysvětlení, protože každá důležitost funkcí bude odpovídat sloupci z původní datové sady. Jedním z situací, kdy může být užitečné vysvětlivek, je zkoumání vlivu jednotlivých kategorií na funkci kategorií. Pokud se pro funkci kategorií použije kódování s jedním horkou, budou výsledná navržená vysvětlení zahrnovat jinou hodnotu důležitosti na kategorii, jednu na jednu, inženýrskou funkci. To může být užitečné při zúžení, které části datové sady jsou pro model nejvíc informativní.

> [!NOTE]
> Propracovaná a nehrubá vysvětlení jsou vypočítána sekvenčně. V závislosti na modelu a kanálu featurization se vytvoří první technický výklad. Nezpracované vysvětlení se vytvoří na základě tohoto podrobného vysvětlení, které agreguje důležitost navržených funkcí, které byly získány ze stejné nezpracované funkce.

### <a name="create-edit-and-view-dataset-cohorts"></a>Vytváření, úpravy a zobrazování kohorty datových sad

Horní pás karet zobrazuje celkovou statistiku pro model a data. Data můžete rozřezat a indexovat do kohorty datových sad nebo podskupin a prozkoumat nebo porovnávat výkon a vysvětlení modelu v rámci těchto definovaných podskupin. Porovnáním statistik a vysvětlení datových sad v rámci těchto podskupin můžete získat představu o tom, proč dochází k chybám v jedné skupině versus jiné.

[![Vytváření, úpravy a zobrazování kohorty datových sad](./media/how-to-machine-learning-interpretability-aml/dataset-cohorts.gif)](./media/how-to-machine-learning-interpretability-aml/dataset-cohorts.gif#lightbox)

### <a name="understand-entire-model-behavior-global-explanation"></a>Pochopení chování celého modelu (globální vysvětlení) 

První tři karty řídicího panelu vysvětlení poskytují celkovou analýzu proučeného modelu spolu s jeho předpovědi a vysvětlení.

#### <a name="model-performance"></a>Výkon modelu
Vyhodnoťte výkon modelu tím, že prozkoumáte distribuci hodnot předpovědi a hodnoty metrik výkonu vašeho modelu. Model můžete dále prozkoumat tak, že si vyhledáte srovnávací analýzu jeho výkonu napříč různými kohorty nebo podskupinami vaší datové sady. Vyberte Filtry podél y-hodnota a x-Value k vyjmutí napříč různými dimenzemi. Zobrazit metriky, jako je přesnost, přesnost, odvolání, falešná kladná sazba (ZAREGISTROVANÝCH zařízeních) a falešná negativní sazba (FNR).

[![Karta výkon modelu ve vizualizaci vysvětlení](./media/how-to-machine-learning-interpretability-aml/model-performance.gif)](./media/how-to-machine-learning-interpretability-aml/model-performance.gif#lightbox)

#### <a name="dataset-explorer"></a>Průzkumník datových sad
Prozkoumejte statistiku datové sady tak, že vyberete různé filtry podél osy X, Y a barev, abyste mohli rozřezat data mezi různými dimenzemi. Vytvořte datovou sadu kohorty výše, abyste mohli analyzovat statistiky datových sad pomocí filtrů, jako je předpokládaný výsledek, funkce datové sady a skupiny chyb. Pomocí ikony ozubeného kolečka v pravém horním rohu grafu můžete změnit typy grafů.

[![Karta Průzkumník DataSet ve vizualizaci vysvětlení](./media/how-to-machine-learning-interpretability-aml/dataset-explorer.gif)](./media/how-to-machine-learning-interpretability-aml/dataset-explorer.gif#lightbox)

#### <a name="aggregate-feature-importance"></a>Důležitost agregační funkce
Prozkoumejte důležité funkce, které mají vliv na celkový předpovědi modelu (označované také jako globální vysvětlení). Pomocí posuvníku zobrazíte hodnoty důležitosti funkcí. Pokud chcete zobrazit hodnoty důležitosti funkcí vedle sebe, vyberte až tři kohorty. Kliknutím na kterýkoli z panelů funkcí v grafu zjistíte, jak hodnoty vybrané funkce předpověď modelu ovlivňují v grafu závislosti níže.

[![Karta důležitost agregované funkce ve vizualizaci vysvětlení](./media/how-to-machine-learning-interpretability-aml/aggregate-feature-importance.gif)](./media/how-to-machine-learning-interpretability-aml/aggregate-feature-importance.gif#lightbox)

### <a name="understand-individual-predictions-local-explanation"></a>Pochopení individuálních předpovědi (místní vysvětlení) 

Čtvrtá karta na kartě vysvětlení vám umožní přejít na jednotlivé DataPoint a jejich jednotlivé důležité funkce. Můžete načíst vykreslení důležitosti jednotlivých funkcí pro libovolný datový bod tak, že kliknete na kterýkoli z jednotlivých datových bodů v hlavním bodovém grafu nebo v průvodci panelem kliknete na konkrétní DataPoint.

|Znázorněte|Description|
|----|-----------|
|Důležitost jednotlivých funkcí|Zobrazuje důležité funkce, které jsou k dishlavnímu okraji pro jednotlivé předpovědi. Pomáhá ilustrovat místní chování základního modelu v konkrétním datovém bodě.|
|Analýza What-If|Umožňuje změnit hodnoty funkcí vybraného reálného datového bodu a sledovat výsledné změny hodnoty předpovědi tím, že generuje hypotetické DataPoint s novými hodnotami funkcí.|
|Očekávání individuálního podmíněného (ICE)|Povolí změnu hodnoty funkcí z minimální hodnoty na maximální hodnotu. Pomáhá ilustrovat způsob, jakým se předpověď datových bodů mění při změně funkce.|

[![Karta důležitost a citlivost jednotlivých funkcí na řídicím panelu vysvětlení](./media/how-to-machine-learning-interpretability-aml/individual-tab.gif)](./media/how-to-machine-learning-interpretability-aml/individual-tab.gif#lightbox)

> [!NOTE]
> Jedná se o vysvětlení v závislosti na mnoha sbližováních a nejedná se o "příčinu" předpovědi. Bez striktní matematické odolnosti při příčině odvození nedoporučujeme uživatelům, aby na základě funkcí perturbations nástroje pro What-If zajistili v reálném čase. Tento nástroj je primárně určen pro porozumění modelu a ladění.

### <a name="visualization-in-azure-machine-learning-studio"></a>Vizualizace v Azure Machine Learning Studiu

Pokud dokončíte kroky [vzdáleného výkladu](how-to-machine-learning-interpretability-aml.md#generate-feature-importance-values-via-remote-runs) (nahrajete vygenerované vysvětlení pro Azure Machine Learning historii spuštění), můžete zobrazit řídicí panel vizualizace v [Azure Machine Learning Studiu](https://ml.azure.com). Tento řídicí panel je jednodušší verze řídicího panelu vizualizace, která je vysvětlena výše. Generace What-If DataPoint a operace ICE jsou zakázané, protože v Azure Machine Learning Studiu nejsou žádné aktivní výpočetní prostředky, které můžou provádět výpočty v reálném čase.

Pokud jsou k dispozici datové sady, globální a místní vysvětlení, naplní data všechny karty. Pokud je k dispozici pouze globální vysvětlení, karta důležitost jednotlivých funkcí bude zakázána.

Použijte jednu z těchto cest pro přístup k řídicímu panelu vizualizace v Azure Machine Learning Studiu:

* Podokno **experimentů** (Preview)
  1. V levém podokně vyberte **experimenty** a zobrazí se seznam experimentů, které jste spustili v Azure Machine Learning.
  1. Vyberte konkrétní experiment pro zobrazení všech běhů v tomto experimentu.
  1. Vyberte běh a pak kartu **vysvětlení** na řídicím panelu vizualizace.

   [![Řídicí panel vizualizace s důležitou funkcí v nástroji AzureML Studio v experimentech](./media/how-to-machine-learning-interpretability-aml/model-explanation-dashboard-aml-studio.png)](./media/how-to-machine-learning-interpretability-aml/model-explanation-dashboard-aml-studio.png#lightbox)

* Podokno **modely**
  1. Pokud jste svůj původní model zaregistrovali pomocí postupu v části [nasazení modelů pomocí Azure Machine Learning](./how-to-deploy-and-where.md), můžete v levém podokně vybrat **modely** a zobrazit je.
  1. Vyberte model a pak kartu **vysvětlení** pro zobrazení řídicího panelu vizualizace.

## <a name="interpretability-at-inference-time"></a>Výklad v době odvození

Můžete nasadit vysvětlení spolu s původním modelem a použít ho v době odvození k poskytnutí individuálních hodnot důležitosti funkcí (místní vysvětlení) pro jakékoli nové DataPoint. Nabízíme také zapalovače s vyhodnocováním pro zlepšení výkonu při odvozování, které se v současné době podporují jenom v sadě Azure Machine Learning SDK. Proces nasazování vysvětlujícího bodování je podobný jako nasazení modelu a obsahuje následující kroky:

1. Vytvořte objekt vysvětlení. Můžete například použít `TabularExplainer` :

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Vytvořte vysvětlující bodování pomocí objektu vysvětlení.

   ```python
   from azureml.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. Nakonfigurujte a zaregistrujte image, která používá model vysvětlujícího bodování.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. Jako volitelný krok můžete načíst vysvětlující bodování z cloudu a otestovat vysvětlení.

   ```python
   from azureml.interpret.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. Nasaďte bitovou kopii na cíl výpočtů pomocí následujících kroků:

   1. V případě potřeby Zaregistrujte původní model předpovědi pomocí postupu v části [nasazení modelů pomocí Azure Machine Learning](./how-to-deploy-and-where.md).

   1. Vytvořte soubor bodování.

         ```python
         %%writefile score.py
         import json
         import numpy as np
         import pandas as pd
         import os
         import pickle
         from sklearn.externals import joblib
         from sklearn.linear_model import LogisticRegression
         from azureml.core.model import Model
          
         def init():
         
            global original_model
            global scoring_model
             
            # retrieve the path to the model file using the model name
            # assume original model is named original_prediction_model
            original_model_path = Model.get_model_path('original_prediction_model')
            scoring_explainer_path = Model.get_model_path('my_scoring_explainer')

            original_model = joblib.load(original_model_path)
            scoring_explainer = joblib.load(scoring_explainer_path)

         def run(raw_data):
            # get predictions and explanations for each data point
            data = pd.read_json(raw_data)
            # make prediction
            predictions = original_model.predict(data)
            # retrieve model explanations
            local_importance_values = scoring_explainer.explain(data)
            # you can return any data type as long as it is JSON-serializable
            return {'predictions': predictions.tolist(), 'local_importance_values': local_importance_values}
         ```
   1. Definujte konfiguraci nasazení.

         Tato konfigurace závisí na požadavcích vašeho modelu. Následující příklad definuje konfiguraci, která používá jeden procesor jádro a jednu GB paměti.

         ```python
         from azureml.core.webservice import AciWebservice

          aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                    memory_gb=1,
                                                    tags={"data": "NAME_OF_THE_DATASET",
                                                          "method" : "local_explanation"},
                                                    description='Get local explanations for NAME_OF_THE_PROBLEM')
         ```

   1. Vytvořte soubor se závislostmi prostředí.

         ```python
         from azureml.core.conda_dependencies import CondaDependencies

         # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)

         azureml_pip_packages = ['azureml-defaults', 'azureml-core', 'azureml-telemetry', 'azureml-interpret']
 

         # specify CondaDependencies obj
         myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas'],
                                          pip_packages=['sklearn-pandas'] + azureml_pip_packages,
                                          pin_sdk_version=False)


         with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

         with open("myenv.yml","r") as f:
            print(f.read())
         ```

   1. Vytvořte vlastní souboru Dockerfile s nainstalovaným g + +.

         ```python
         %%writefile dockerfile
         RUN apt-get update && apt-get install -y g++
         ```

   1. Nasaďte vytvořenou bitovou kopii.
   
         Tento proces trvá přibližně pět minut.

         ```python
         from azureml.core.webservice import Webservice
         from azureml.core.image import ContainerImage

         # use the custom scoring, docker, and conda files we created above
         image_config = ContainerImage.image_configuration(execution_script="score.py",
                                                         docker_file="dockerfile",
                                                         runtime="python",
                                                         conda_file="myenv.yml")

         # use configs and models generated above
         service = Webservice.deploy_from_model(workspace=ws,
                                             name='model-scoring-service',
                                             deployment_config=aciconfig,
                                             models=[scoring_explainer_model, original_model],
                                             image_config=image_config)

         service.wait_for_deployment(show_output=True)
         ```

1. Otestujte nasazení.

    ```python
    import requests

    # create data to test service with
    examples = x_list[:4]
    input_data = examples.to_json()

    headers = {'Content-Type':'application/json'}

    # send request to service
    resp = requests.post(service.scoring_uri, input_data, headers=headers)

    print("POST to url", service.scoring_uri)
    # can covert back to Python objects from json string if desired
    print("prediction:", resp.text)
    ```

1. Vyčištění.

   Chcete-li odstranit nasazenou webovou službu, použijte `service.delete()` .

## <a name="troubleshooting"></a>Řešení potíží

* **Zhuštěná data nejsou podporovaná**: model, který porušuje řídicí panely nebo se v podstatě zpomaluje s velkým počtem funkcí, proto Momentálně nepodporujeme formát zhuštěných dat. Kromě toho vznikají Obecné problémy s pamětí s velkými datovými sadami a velkým počtem funkcí. 

* **Prognózy modelů nepodporované s vysvětlením modelu**: interpretace, nejlepšího vysvětlení modelu není k dispozici pro AutoML prognózy, které doporučují následující algoritmy jako nejlepší model: TCNForecaster, AutoArima, Prophet, ExponentialSmoothing, Average, Naive, sezónní průměr a sezónní Naive. AutoML prognózování má regresní modely, které podporují vysvětlení. Nicméně na řídicím panelu vysvětlení není karta důležitost jednotlivých funkcí podporována pro prognózování z důvodu složitosti jejich datových kanálů.

* **Místní vysvětlení indexu dat**: řídicí panel vysvětlení nepodporuje související hodnoty místní důležitost k identifikátoru řádku z původní datové sady ověření, pokud je tato datová sada větší než 5000 datových bodů jako řídicí panel náhodně downsamples data. Řídicí panel ale v rámci karty důležitost jednotlivých funkcí zobrazuje nezpracované hodnoty funkcí datové sady pro každý element DataPoint předaný na řídicí panel. Uživatelé mohou mapovat místní důležitost zpátky na původní datovou sadu pomocí porovnání hodnot funkcí nezpracovaných datových sad. Pokud je velikost datové sady ověření menší než 5000 vzorků, `index` funkce v nástroji AzureML Studio bude odpovídat indexu v datové sadě ověřování.

* Funkce **inif/Ice není v studiu podporovaná**: What-If a jednotlivé podmíněná očekávání (ICE) se v Azure Machine Learning studiu na kartě vysvětlení nepodporují, protože nahrané vysvětlení potřebují aktivní výpočetní prostředky k přepočítání předpovědi a pravděpodobnosti funkcí perturbed. V současné době je podporována v poznámkových blocích Jupyter při spuštění jako pomůcka pomocí sady SDK.


## <a name="next-steps"></a>Další kroky

[Další informace o interpretaci modelu](how-to-machine-learning-interpretability.md)

[Podívejte se na ukázkové poznámkové bloky Azure Machine Learning interpretace](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)
