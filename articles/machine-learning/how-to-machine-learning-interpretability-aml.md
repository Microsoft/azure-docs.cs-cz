---
title: Interpretovatelnost modelu pro místní a vzdálené spuštění
titleSuffix: Azure Machine Learning
description: Zjistěte, jak získat vysvětlení, jak váš model strojového učení určuje důležitost funkce a dělá předpovědi při použití sady Azure Machine Learning SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: Luis.Quintanilla
ms.date: 04/02/2020
ms.openlocfilehash: 1ff42149ccb629a0a7094e6dfede422d4dd7f61f
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632027"
---
# <a name="model-interpretability-for-local-and-remote-runs"></a>Interpretovatelnost modelu pro místní a vzdálené spuštění

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto návodu se naučíte používat balíček interpretability sady Python SDK azure machine learningu k provádění následujících úloh:


* Vysvětlete celé chování modelu nebo jednotlivé předpovědi na vašem osobním počítači místně.

* Povolte techniky interpretace pro inženýrské funkce.

* Vysvětlete chování pro celý model a jednotlivé předpovědi v Azure.

 
* Pomocí vizualizačního řídicího panelu můžete pracovat s vysvětlením modelu.

* Nasaďte vysvětlující vysvětlivku hodnocení vedle modelu sledovat vysvětlení během odvození.



Další informace o podporovaných technikách interpretace a modelech strojového učení najdete [v tématu Model interpretability v Azure Machine Learning](how-to-machine-learning-interpretability.md) a [ukázkové poznámkové bloky](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).

## <a name="generate-feature-importance-value-on-your-personal-machine"></a>Generovat hodnotu důležitosti funkce na vašem osobním počítači 
Následující příklad ukazuje, jak používat balíček interpretability ve vašem osobním počítači bez kontaktování služeb Azure.

1. Instalace `azureml-interpret` `azureml-interpret-contrib` a balíčky.
    ```bash
    pip install azureml-interpret
    pip install azureml-interpret-contrib
    ```

2. Trénování ukázkového modelu v místním notebooku Jupyter.

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

3. Zavolejte vysvětlivku místně.
   * Chcete-li inicializovat vysvětlující objekt, předejte model a některá trénovací data konstruktoru vysvětlujícího.
   * Chcete-li, aby vaše vysvětlení a vizualizace byly informativnější, můžete při klasifikaci předat názvy funkcí a výstupní třídy.

   Následující bloky kódu ukazují, jak vytvořit konkretizovat vysvětlující objekt pomocí `TabularExplainer`aplikace , `MimicExplainer`a `PFIExplainer` místně.
   * `TabularExplainer`volá jeden ze tří shap`TreeExplainer`explainers pod ( , `DeepExplainer`, , nebo `KernelExplainer`).
   * `TabularExplainer`automaticky vybere nejvhodnější pro váš případ použití, ale můžete volat každý z jeho tří podkladových explainers přímo.

    ```python
    from interpret.ext.blackbox import TabularExplainer

    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    – nebo –

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

    – nebo –

    ```python
    from interpret.ext.blackbox import PFIExplainer

    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model,
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

### <a name="explain-the-entire-model-behavior-global-explanation"></a>Vysvětlení chování celého modelu (globální vysvětlení) 

Naleznete v následujícím příkladu, který vám pomůže získat hodnoty důležitosti agregačních (globálních) funkcí.

```python

# you can use the training data or the test data here
global_explanation = explainer.explain_global(x_train)

# if you used the PFIExplainer in the previous step, use the next line of code instead
# global_explanation = explainer.explain_global(x_train, true_labels=y_test)

# sorted feature importance values and feature names
sorted_global_importance_values = global_explanation.get_ranked_global_values()
sorted_global_importance_names = global_explanation.get_ranked_global_names()
dict(zip(sorted_global_importance_names, sorted_global_importance_values))

# alternatively, you can print out a dictionary that holds the top K feature names and values
global_explanation.get_feature_importance_dict()
```

### <a name="explain-an-individual-prediction-local-explanation"></a>Vysvětlete individuální předpověď (místní vysvětlení)
Získejte hodnoty důležitosti jednotlivých funkcí různých datových bodů voláním vysvětlení pro jednotlivé instance nebo skupinu instancí.
> [!NOTE]
> `PFIExplainer`nepodporuje místní vysvětlení.

```python
# get explanation for the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

### <a name="raw-feature-transformations"></a>Transformace nezpracovaných prvků

Můžete se rozhodnout získat vysvětlení, pokud jde o nezpracované, netransformované funkce, spíše než konstrukční funkce. Pro tuto možnost předáte kanál transformace funkce `train_explain.py`vysvětlivakvaplikaci v aplikaci . V opačném případě vysvětluje vysvětlení, pokud jde o inženýrské funkce.

Formát podporovaných transformací je stejný, jak je popsáno v [sklearn-pandas](https://github.com/scikit-learn-contrib/sklearn-pandas). Obecně platí, že všechny transformace jsou podporovány tak dlouho, dokud pracují na jeden sloupec tak, aby bylo jasné, že jsou 1:N.

Získejte vysvětlení pro nezpracované `sklearn.compose.ColumnTransformer` funkce pomocí nebo se seznamem namontované transformer řazené kolekce členů. Následující příklad `sklearn.compose.ColumnTransformer`používá .

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

V případě, že chcete spustit příklad se seznamem namontovaných transformer řazené kolekce členů, použijte následující kód:

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

## <a name="generate-feature-importance-values-via-remote-runs"></a>Generovat hodnoty důležitosti funkcí pomocí vzdálených spuštění

Následující příklad ukazuje, jak `ExplanationClient` můžete použít třídu k povolení interpretability modelu pro vzdálené spuštění. Je koncepčně podobný místnímu procesu, s výjimkou vás:

* Použijte `ExplanationClient` ve vzdáleném spuštění k nahrání kontextu interpretability.
* Stáhněte si kontext později v místním prostředí.

1. Instalace `azureml-interpret` `azureml-interpret-contrib` a balíčky.
    ```bash
    pip install azureml-interpret
    pip install azureml-interpret-contrib
    ```
1. Vytvořte školicí skript v místním poznámkovém bloku Jupyter. Například, `train_explain.py`.

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
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

1. Nastavte azure machine learning výpočetní jako váš výpočetní cíl a odešlete tréninkovou run. Pokyny najdete [v tématu nastavení výpočetních cílů pro trénování modelu.](how-to-set-up-training-targets.md#amlcompute) Ukázkové [poznámkové bloky](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation) vám také mohou být užitečné.

1. Stáhněte si vysvětlení v místním notebooku Jupyter.

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
    
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

Po stažení vysvětlení v místním poznámkovém bloku Jupyter, můžete použít řídicí panel vizualizace pochopit a interpretovat váš model.

### <a name="understand-entire-model-behavior-global-explanation"></a>Pochopit chování celého modelu (globální vysvětlení) 

Následující obrázky poskytují celkový pohled na trénovaný model spolu s jeho předpovědi a vysvětlení.

|Plot|Popis|
|----|-----------|
|Průzkum dat| Zobrazí přehled datové sady spolu s hodnotami předpověď.|
|Globální význam|Agregace funkce důležitost hodnoty jednotlivých datových bodů zobrazit model je celkově top K (konfigurovatelné K) důležité funkce. Pomáhá pochopit celkové chování základního modelu.|
|Vysvětlení průzkumu|Ukazuje, jak funkce ovlivňuje změnu hodnot předpovědi modelu nebo pravděpodobnost hodnot předpověď. Zobrazuje dopad interakce funkcí.|
|Souhrnná důležitost|Používá hodnoty důležitosti jednotlivých funkcí ve všech datových bodech k zobrazení rozložení dopadu jednotlivých funkcí na hodnotu předpověď. Pomocí tohoto diagramu můžete prozkoumat, v jakém směru hodnoty prvku ovlivňuje hodnoty předpovědi.
|

[![Globální vizualizační řídicí panel](./media/how-to-machine-learning-interpretability-aml/global-charts.png)](./media/how-to-machine-learning-interpretability-aml/global-charts.png#lightbox)

### <a name="understand-individual-predictions-local-explanation"></a>Porozumět jednotlivým předpovědím (místní vysvětlení) 

Jednotlivé vykreslování důležitosti prvku můžete načíst pro libovolný datový bod kliknutím na libovolný jednotlivý datový bod v libovolném z celkových vykreslování.

|Plot|Popis|
|----|-----------|
|Místní význam|Zobrazuje nejdůležitější funkce top K (konfigurovatelné K) pro individuální předpověď. Pomáhá ilustrovat místní chování podkladového modelu v určitém datovém bodu.|
|Průzkum perturbation (co když analýza)|Umožňuje změny hodnot prvku vybraného datového bodu a sledování výsledných změn hodnoty předpovědi.|
|Individuální podmíněné očekávání (ICE)| Umožňuje změny hodnoty prvku z minimální hodnoty na maximální hodnotu. Pomáhá ilustrovat, jak se mění předpověď datového bodu při změně funkce.|

[![Důležitost místního prvku vizualizačního řídicího panelu](./media/how-to-machine-learning-interpretability-aml/local-charts.png)](./media/how-to-machine-learning-interpretability-aml/local-charts.png#lightbox)


[![Optimalizace funkce ovládacího panelu vizualizace](./media/how-to-machine-learning-interpretability-aml/perturbation.gif)](./media/how-to-machine-learning-interpretability-aml/perturbation.gif#lightbox)


[![Vizualizace Dashboard ICE Obrázky](./media/how-to-machine-learning-interpretability-aml/ice-plot.png)](./media/how-to-machine-learning-interpretability-aml/ice-plot.png#lightbox)

> [!NOTE]
> Před spuštěním jádra Jupyter se ujistěte, že jste povolili rozšíření widgetů pro vizualizační řídicí panel.

* Poznámkové bloky Jupyter

    ```shell
    jupyter nbextension install --py --sys-prefix azureml.contrib.interpret.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.interpret.visualize
    ```

* JupyterLab

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```

Chcete-li načíst řídicí panel vizualizace, použijte následující kód.

```python
from interpret_community.widget import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

### <a name="visualization-in-azure-machine-learning-studio"></a>Vizualizace ve studiu Azure Machine Learning

Pokud dokončíte kroky [vzdálené interpretability](how-to-machine-learning-interpretability-aml.md#generate-feature-importance-values-via-remote-runs) (nahrávání generované vysvětlení do historie spuštění Azure Machine Learning), můžete zobrazit řídicí panel vizualizace ve [studiu Azure Machine Learning](https://ml.azure.com). Tento řídicí panel je jednodušší verze vizualizačního řídicího panelu vysvětleného výše (vysvětlení průzkumu a obrázky ICE jsou zakázány, protože ve studiu není žádný aktivní výpočetní výkon, který by mohl provádět výpočty v reálném čase).

Pokud jsou k dispozici dataset, globální a místní vysvětlení, data naplní všechny karty (s výjimkou průzkumu perturbation a ICE). Pokud je k dispozici pouze globální vysvětlení, karta Důležitost souhrnu a všechny místní karty vysvětlení jsou zakázány.

Postupujte podle jedné z těchto cest pro přístup k řídicímu panelu vizualizace ve studiu Azure Machine Learning:

* **Podokno Experimenty** (náhled)
  1. Výběrem **možnosti Experimenty** v levém podokně zobrazíte seznam experimentů, které jste spouštěli v Azure Machine Learning.
  1. Vyberte konkrétní experiment, chcete-li zobrazit všechny spuštění v tomto experimentu.
  1. Vyberte spustit a potom na kartu **Vysvětlení** na řídicí panel vizualizace vysvětlení.

   [![Důležitost místního prvku vizualizačního řídicího panelu](./media/how-to-machine-learning-interpretability-aml/amlstudio-experiments.png)](./media/how-to-machine-learning-interpretability-aml/amlstudio-experiments.png#lightbox)

* **Podokno Modely**
  1. Pokud jste zaregistrovali původní model podle kroků v [nasazení modelů pomocí Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where), můžete vybrat **modely** v levém podokně a zobrazit ho.
  1. Vyberte model a potom na kartě **Vysvětlení** zobrazte řídicí panel vizualizace vysvětlení.

## <a name="interpretability-at-inference-time"></a>Interpretovatelnost v době odvození

Můžete nasadit vysvětlující spolu s původním modelem a použít jej v době odvození poskytnout jednotlivé hodnoty důležitosti funkce (místní vysvětlení) pro nové nové datového bodu. Nabízíme také vysvětlení s nižší hmotností pro zlepšení interpretability výkonu v době odvození. Proces nasazení vysvětlení s nižší hmotností je podobný nasazení modelu a zahrnuje následující kroky:

1. Vytvořte objekt vysvětlení. Můžete například použít `TabularExplainer`:

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Vytvořte vysvětlivku hodnocení s objektem vysvětlení.

   ```python
   from azureml.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. Konfigurace a registrace bitové kopie, která používá model vysvětlivky bodování.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. Jako volitelný krok můžete načíst vysvětlivku vysvětlivky z cloudu a otestovat vysvětlení.

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

1. Nasazení image na výpočetní cíl, podle následujících kroků:

   1. V případě potřeby zaregistrujte původní model předpovědi podle kroků v [části Nasazení modelů pomocí Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where).

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

         Tato konfigurace závisí na požadavcích modelu. Následující příklad definuje konfiguraci, která používá jedno jádro procesoru a jeden GB paměti.

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

         azureml_pip_packages = ['azureml-defaults', 'azureml-contrib-interpret', 'azureml-core', 'azureml-telemetry', 'azureml-interpret']
 

         # specify CondaDependencies obj
         myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas'],
                                          pip_packages=['sklearn-pandas'] + azureml_pip_packages,
                                          pin_sdk_version=False)


         with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

         with open("myenv.yml","r") as f:
            print(f.read())
         ```

   1. Vytvořte vlastní dockerfile s g++ nainstalován.

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

1. Ukliďte to.

   Chcete-li odstranit nasazenou `service.delete()`webovou službu, použijte .

## <a name="next-steps"></a>Další kroky

[Další informace o interpretace modelu](how-to-machine-learning-interpretability.md)

[Podívejte se na ukázkové poznámkové bloky interpretace interpretace Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)

