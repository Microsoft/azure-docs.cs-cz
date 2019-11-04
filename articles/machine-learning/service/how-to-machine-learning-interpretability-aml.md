---
title: Interpretace modelu pro místní a vzdálené spuštění
titleSuffix: Azure Machine Learning
description: Naučte se vysvětlit, proč model předpovědi pomocí sady SDK Azure Machine Learning. Dá se použít během školení a odvození, abyste porozuměli tomu, jak model určuje důležitost funkcí a zpřístupňuje předpovědi.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: a2b71a10606b7cd20f06b2497515b758426833a9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515301"
---
# <a name="model-interpretability-for-local-and-remote-runs"></a>Interpretace modelu pro místní a vzdálené spuštění

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, proč váš model provedl předpovědi pomocí balíčku interpretace Azure Machine Learning Python SDK. Naučíte se následující úlohy:

* Interpretace modelů strojového učení, které jsou vyškolené místně i na vzdálených výpočetních prostředcích
* Ukládání místních a globálních vysvětlení v historii spuštění Azure
* Zobrazit vizualizace interpretace v [Azure Machine Learning Studiu](https://ml.azure.com)
* Nasazení vysvětlujícího bodování pomocí modelu

Další informace o interpretaci modelu naleznete v [článku koncept](how-to-machine-learning-interpretability.md).

## <a name="local-interpretability"></a>Místní výklad

Následující příklad ukazuje, jak použít balíček interpretovat místně bez kontaktování služeb Azure. Pro získání balíčku pro interpretaci spusťte `pip install azureml-interpret`.

1. Výuku ukázkového modelu v místním poznámkovém bloku Jupyter

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

2. Zavolat vysvětlit místně: Chcete-li inicializovat objekt vysvětlujícího objektu, je nutné předat svůj model a určitou školicí data konstruktoru vysvětlujícího. Volitelně můžete také předávat názvy funkcí a názvy tříd výstupu (Pokud se provádí klasifikace), které budou sloužit k tomu, aby vaše vysvětlení a vizualizace byly více informativní. Tady je postup vytvoření instance objektu vysvětlení pomocí `TabularExplainer`, `MimicExplainer`a `PFIExplainer` lokálně. `TabularExplainer` volá jedno ze tří SHAP vysvětlení pod (`TreeExplainer`, `DeepExplainer`nebo `KernelExplainer`) a automaticky vybere nejvhodnější z nich pro váš případ použití. Můžete ale zavolat každé ze svých tří základních vysvětlení přímo.

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

### <a name="overall-global-feature-importance-values"></a>Celkové (globální) hodnoty důležitosti funkcí

Získá hodnoty důležitosti pro globální funkce.
    
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

### <a name="instance-level-local-feature-importance-values"></a>Hodnoty důležitosti funkcí na úrovni instance (místní)

Získejte hodnoty důležitosti místních funkcí: pomocí následujících volání funkcí Vysvětlete jednotlivou instanci nebo skupinu instancí. Upozorňujeme, že PFIExplainer nepodporuje místní vysvětlení.

```python
# explain the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

## <a name="interpretability-for-remote-runs"></a>Interpretace pro vzdálené běhy

Tento příklad ukazuje, jak použít třídu `ExplanationClient` pro povolení interpretace modelu pro vzdálené běhy. Koncept je podobný předchozímu oddílu, ale použijete `ExplanationClient` ve vzdáleném běhu pro nahrání kontextu interpretace a pak můžete tento kontext stáhnout později v místním prostředí. Potřebný balíček získáte pomocí `pip install azureml-contrib-interpret`.

1. Vytvořte školicí skript v místním Jupyter poznámkovém bloku (například train_explain. py).

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

1. Postupujte podle pokynů pro [nastavení cílových výpočetních prostředků pro školení modelů](how-to-set-up-training-targets.md#amlcompute) a Naučte se, jak nastavit Azure Machine Learning COMPUTE jako výpočetní cíl a odeslat školicí běh. Můžete si také prohlédnout [ukázkové poznámkové bloky](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation).

1. Stáhněte si vysvětlení do svého místního poznámkového bloku Jupyter.

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

## <a name="raw-feature-transformations"></a>Nezpracované transformace funkcí

Volitelně můžete předat kanál transformace vaší funkce do vysvětlujícího (v train_explain. py) a získat vysvětlení z podmínek nezpracovaných funkcí před transformací (spíše než pomocí navržených funkcí). Pokud přeskočíte tuto funkci, vysvětluje vysvětlující funkce v souvislosti s funkcemi pro inženýry.

Formát podporovaných transformací je stejný jako ten, který je popsaný v [skriptu sklearn-PANDAS](https://github.com/scikit-learn-contrib/sklearn-pandas). Obecně platí, že všechny transformace jsou podporovány, pokud pracují v jednom sloupci a jsou proto jasně jedna až mnoho. 

Vysvětlete nezpracované funkce buď pomocí `sklearn.compose.ColumnTransformer`, nebo seznamu namontovaných řazených kolekcí členů transformátoru. Následující kód používá `sklearn.compose.ColumnTransformer`. 


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

V případě, že chcete spustit příklad se seznamem namontovaných řazených kolekcí členů Transformer, použijte následující kód.

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

## <a name="visualizations"></a>Vizualizace

Po stažení vysvětlení v místním poznámkovém bloku Jupyter můžete model pochopit a interpretovat pomocí řídicího panelu vizualizace.

### <a name="global-visualizations"></a>Globální vizualizace

Následující vykreslení poskytují globální pohled na trained model spolu s jeho předpovědi a vysvětleními.

|Znázorněte|Popis|
|----|-----------|
|Zkoumání dat| Přehled datové sady spolu s hodnotami předpovědi.|
|Globální důležitost|Zobrazuje globálně K (konfigurovatelné K) důležitým funkcím globálně. Tento graf je užitečný pro porozumění globálnímu chování základního modelu.|
|Zkoumání vysvětlení|Ukazuje, jak je funkce zodpovědná za provedení změny v hodnotách předpovědi modelu (nebo pravděpodobnosti hodnot předpovědi). Také ukazuje, jak dvě funkce pracují s ovlivněním předpovědi.|
|Souhrnná důležitost| Používá hodnoty důležitosti místních funkcí v rámci všech datových bodů k zobrazení distribuce vlivu každé funkce na hodnotu předpovědi.|

[Globální řídicí panel vizualizace ![](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>Místní vizualizace

Po kliknutí na libovolný jednotlivý datový bod v předchozí zkusné nabídce můžete načíst vykreslení důležitosti místní funkce pro daný datový bod.

|Znázorněte|Popis|
|----|-----------|
|Místní důležitost|Zobrazuje globálně K (konfigurovatelné K) důležitým funkcím globálně. Tento graf je užitečný pro porozumění místnímu chování základního modelu v konkrétním datovém bodě.|
|Průzkum Perturbation|Umožňuje změnit hodnoty funkcí vybraného datového bodu a sledovat, jaký vliv mají tyto změny na hodnotu předpovědi.|
|Očekávání individuálního podmíněného (ICE)| Umožňuje změnit hodnotu funkce z minimální hodnoty na maximální hodnotu, abyste viděli, jak se předpověď datových bodů mění při změně funkce.|

[Důležitost místní funkce řídicího panelu vizualizace ![](./media/machine-learning-interpretability-explainability/local-charts.png)](./media/machine-learning-interpretability-explainability/local-charts.png#lightbox)


[Perturbation funkce řídicího panelu vizualizace ![](./media/machine-learning-interpretability-explainability/perturbation.gif)](./media/machine-learning-interpretability-explainability/perturbation.gif#lightbox)


[Řídicí panel vizualizace ![ICE](./media/machine-learning-interpretability-explainability/ice-plot.png)](./media/machine-learning-interpretability-explainability/ice-plot.png#lightbox)

Všimněte si, že budete muset mít rozšíření widgetů řídicího panelu vizualizace povolené před spuštěním jádra Jupyter.

* Poznámkové bloky Jupyter

    ```shell
    jupyter nbextension install --py --sys-prefix azureml.contrib.interpret.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.interpret.visualize
    ```



* Jupyter Labs

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```

Chcete-li načíst řídicí panel vizualizace, použijte následující kód.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

### <a name="visualization-in-azure-machine-learning-studio"></a>Vizualizace v Azure Machine Learning Studiu

Provedením kroků v části věnované [vzdálenému výkladu](how-to-machine-learning-interpretability-aml.md#interpretability-for-remote-runs) můžete na řídicím panelu vizualizace v [Azure Machine Learning Studiu](https://ml.azure.com)kontrolovat. Řídicí panel zobrazený v Azure Machine Learning Studiu je jednodušší verze řídicího panelu vizualizace, která je vysvětlena výše a podporuje pouze následující dvě karty.

|Znázorněte|Popis|
|----|-----------|
|Globální důležitost|Zobrazuje globálně K (konfigurovatelné K) důležitým funkcím globálně. Tento graf je užitečný pro porozumění globálnímu chování základního modelu.|
|Souhrnná důležitost| Používá hodnoty důležitosti místních funkcí v rámci všech datových bodů k zobrazení distribuce vlivu každé funkce na hodnotu předpovědi.|

Pokud jsou k dispozici jak globální, tak i místní vysvětlení, budou obě karty naplněny daty. Pokud je k dispozici pouze globální vysvětlení, druhá karta bude zakázána.

Pro přístup k řídicímu panelu vizualizace v aplikaci Azure Machine Learning Studio můžete projít některou z následujících cest:

1. Karta experimenty (Preview): kliknutím na kartu experimenty se zobrazí seznam experimentů, které jste spustili ve službě Azure Machine Learning Service. Z tohoto seznamu můžete vybrat konkrétní experiment, který bude přesměrován na stránku se všemi spuštěními pod vybraným názvem experimentu. Kliknutím na každé spuštění a na jeho kartě "vysvětlení" zobrazíte řídicí panel vizualizace.


[Důležitost místní funkce řídicího panelu vizualizace ![](./media/machine-learning-interpretability-explainability/amlstudio-experiments.png)](./media/machine-learning-interpretability-explainability/amlstudio-experiments.png#lightbox)


2. Karta modely: v případě, že jste svůj původní model zaregistrovali pomocí postupu v tématu [nasazení modelů pomocí Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where), váš model se zobrazí v seznamu na kartě modely. Kliknutím na každý model a na jeho kartě "vysvětlení" zobrazíte řídicí panel vizualizace.

## <a name="interpretability-at-inference-time"></a>Výklad v době odvození

Tento vysvětlující může být nasazen společně s původním modelem a lze jej použít v čase odvození a poskytnout tak místní informace o vysvětlení. Nabízíme také zapalovače s důrazem na zjednodušené hodnocení, které zlepšují výkon interpretace v době odvození. Proces nasazování vysvětlujícího bodování je podobný jako nasazení modelu a obsahuje následující kroky:


1. Vytvoření objektu vysvětlení (například pomocí TabularExplainer):

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Vytvoření vysvětlujícího bodování pomocí objektu vysvětlení:

   ```python
   from azureml.contrib.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

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

1. Volitelné Načte vysvětlující bodování z cloudu a otestuje vysvětlení.

   ```python
   from azureml.contrib.interpret.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. Nasaďte bitovou kopii na cíl výpočtů:

   1. Vytvořte soubor bodování (před tímto krokem, postupujte podle kroků v části [nasazení modelů s Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where) k zaregistrování původního modelu předpovědi).

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

   1. Definujte konfiguraci nasazení (Tato konfigurace závisí na požadavcích vašeho modelu. Následující příklad definuje konfiguraci, která používá jeden procesor jádra a 1 GB paměti.

        ```python
        from azureml.core.webservice import AciWebservice

        aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                       memory_gb=1,
                                                       tags={"data": "NAME_OF_THE_DATASET",
                                                             "method" : "local_explanation"},
                                                       description='Get local explanations for NAME_OF_THE_PROBLEM')
        ```

   1. Vytvoření souboru se závislostmi prostředí

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

   1. Vytvoření vlastního souboru dockerfileu s nainstalovaným g + +

        ```python
        %%writefile dockerfile
        RUN apt-get update && apt-get install -y g++
        ```

   1. Nasadit vytvořenou bitovou kopii (odhad času: 5 minut)

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

1. Otestování nasazení

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

1. Vyčištění: Chcete-li odstranit nasazenou webovou službu, použijte `service.delete()`.

## <a name="next-steps"></a>Další kroky

Další informace o interpretaci modelu naleznete v [koncepčním článku](how-to-machine-learning-interpretability.md).
