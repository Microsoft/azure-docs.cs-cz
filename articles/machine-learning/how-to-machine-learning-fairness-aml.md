---
title: Posouzení spravedlivosti modelů ML v Pythonu (Preview)
titleSuffix: Azure Machine Learning
description: Naučte se hodnotit a zmírnit rizika vašich modelů strojového učení pomocí Fairlearn a sady Azure Machine Learning Python SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: mesameki
author: mesameki
ms.reviewer: luquinta
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, responsible-ml
ms.openlocfilehash: ee589aed2b8f3c53d21c1f762a5d2027f3762649
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222764"
---
# <a name="use-azure-machine-learning-with-the-fairlearn-open-source-package-to-assess-the-fairness-of-ml-models-preview"></a>Použití Azure Machine Learning s open source balíčkem Fairlearn k vyhodnocení spravedlivosti modelů ML (Preview)

V této příručce se dozvíte, jak pomocí [Fairlearn](https://fairlearn.github.io/) Open Source balíčku Pythonu s Azure Machine Learning provádět následující úlohy:

* Vyhodnoťte spravedlivost modelu předpovědi. Další informace o tom, co je v strojovém učení, najdete v [článku o nerovnosti v rámci strojového učení](concept-fairness-ml.md).
* Nahrajte, vypište a Stáhněte si přehledy pro posouzení spravedlnosti v Azure Machine Learning Studiu.
* Podívejte se na řídicí panel posouzení spravedlivosti v Azure Machine Learning studiu, abyste mohli pracovat s přehledy o přístatosti vašich modelů.

>[!NOTE]
> Posouzení spravedlivosti není čistě technické cvičení. **Tento balíček vám může přispět k vyhodnocení spravedlivého modelu strojového učení, ale stačí nakonfigurovat a rozhodnout, jak model vykonává.**  I když tento balíček pomáhá identifikovat kvantitativní metriky k posouzení spravedlivosti, musí vývojáři modelů strojového učení také provést kvalitativní analýzu, aby vyhodnotila spravedlivé jejich vlastních modelů.

## <a name="azure-machine-learning-fairness-sdk"></a>Sada SDK pro Azure Machine Learning spravedlivosti 

Sada SDK pro Azure Machine Learning spravedlivě `azureml-contrib-fairness` integruje Open Source balíček Pythonu [Fairlearn](http://fairlearn.github.io)v rámci Azure Machine Learning. Další informace o integraci Fairlearn v rámci Azure Machine Learning najdete v těchto [ukázkových poznámkových blocích](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness). Další informace o Fairlearn najdete v ukázkové [příručce](https://fairlearn.github.io/master/auto_examples/) a [ukázkových poznámkových blocích](https://github.com/fairlearn/fairlearn/tree/master/notebooks). 

Balíčky a nainstalujte pomocí následujících příkazů `azureml-contrib-fairness` `fairlearn` :
```bash
pip install azureml-contrib-fairness
pip install fairlearn==0.4.6
```
Novější verze Fairlearn by také měly fungovat v následujícím ukázkovém kódu.



## <a name="upload-fairness-insights-for-a-single-model"></a>Nahrávání přehledů o nerovnosti pro jeden model

Následující příklad ukazuje, jak použít balíček s příspravedlivosti. Do Azure Machine Learning nahrajeme přehled o nerovnosti modelu a na Azure Machine Learning Studiu si prohlédněte řídicí panel posouzení spravedlivosti.

1. Výuku ukázkového modelu v Jupyter Notebook. 

    Pro datovou sadu používáme známou datovou sadu pro seznámení dospělé, kterou načítáme z OpenML. Jsme předstírati problémy s rozhodnutím o půjčkě s popiskem, který označuje, jestli jednotlivec zaplatil předchozí půjčku. Vytvoříme model, který bude předpovídat, pokud dříve nepřesné jednotlivce uhradí půjčku. Takový model se může použít při rozhodování o výpůjčce.

    ```python
    import copy
    import numpy as np
    import pandas as pd

    from sklearn.compose import ColumnTransformer
    from sklearn.datasets import fetch_openml
    from sklearn.impute import SimpleImputer
    from sklearn.linear_model import LogisticRegression
    from sklearn.model_selection import train_test_split
    from sklearn.preprocessing import StandardScaler, OneHotEncoder
    from sklearn.compose import make_column_selector as selector
    from sklearn.pipeline import Pipeline
    
    from fairlearn.widget import FairlearnDashboard

    # Load the census dataset
    data = fetch_openml(data_id=1590, as_frame=True)
    X_raw = data.data
    y = (data.target == ">50K") * 1
    
    # (Optional) Separate the "sex" and "race" sensitive features out and drop them from the main data prior to training your model
    X_raw = data.data
    y = (data.target == ">50K") * 1
    A = X_raw[["race", "sex"]]
    X = X_raw.drop(labels=['sex', 'race'],axis = 1)
    
    # Split the data in "train" and "test" sets
    (X_train, X_test, y_train, y_test, A_train, A_test) = train_test_split(
        X_raw, y, A, test_size=0.3, random_state=12345, stratify=y
    )

    # Ensure indices are aligned between X, y and A,
    # after all the slicing and splitting of DataFrames
    # and Series
    X_train = X_train.reset_index(drop=True)
    X_test = X_test.reset_index(drop=True)
    y_train = y_train.reset_index(drop=True)
    y_test = y_test.reset_index(drop=True)
    A_train = A_train.reset_index(drop=True)
    A_test = A_test.reset_index(drop=True)

    # Define a processing pipeline. This happens after the split to avoid data leakage
    numeric_transformer = Pipeline(
        steps=[
            ("impute", SimpleImputer()),
            ("scaler", StandardScaler()),
        ]
    )
    categorical_transformer = Pipeline(
        [
            ("impute", SimpleImputer(strategy="most_frequent")),
            ("ohe", OneHotEncoder(handle_unknown="ignore")),
        ]
    )
    preprocessor = ColumnTransformer(
        transformers=[
            ("num", numeric_transformer, selector(dtype_exclude="category")),
            ("cat", categorical_transformer, selector(dtype_include="category")),
        ]
    )

    # Put an estimator onto the end of the pipeline
    lr_predictor = Pipeline(
        steps=[
            ("preprocessor", copy.deepcopy(preprocessor)),
            (
                "classifier",
                LogisticRegression(solver="liblinear", fit_intercept=True),
            ),
        ]
    )

    # Train the model on the test data
    lr_predictor.fit(X_train, y_train)

    # (Optional) View this model in Fairlearn's fairness dashboard, and see the disparities which appear:
    from fairlearn.widget import FairlearnDashboard
    FairlearnDashboard(sensitive_features=A_test, 
                       sensitive_feature_names=['Race', 'Sex'],
                       y_true=y_test,
                       y_pred={"lr_model": lr_predictor.predict(X_test)})
    ```

2. Přihlaste se Azure Machine Learning a zaregistrujte svůj model.
   
    Řídicí panel spravedlivosti se dá integrovat s registrovanými nebo neregistrovanými modely. Zaregistrujte svůj model v Azure Machine Learning pomocí následujících kroků:
    ```python
    from azureml.core import Workspace, Experiment, Model
    import joblib
    import os
    
    ws = Workspace.from_config()
    ws.get_details()

    os.makedirs('models', exist_ok=True)
    
    # Function to register models into Azure Machine Learning
    def register_model(name, model):
        print("Registering ", name)
        model_path = "models/{0}.pkl".format(name)
        joblib.dump(value=model, filename=model_path)
        registered_model = Model.register(model_path=model_path,
                                        model_name=name,
                                        workspace=ws)
        print("Registered ", registered_model.id)
        return registered_model.id

    # Call the register_model function 
    lr_reg_id = register_model("fairness_logistic_regression", lr_predictor)
    ```

3. Předvýpočetní metriky spravedlnosti.

    Vytvořte slovník řídicích panelů pomocí `metrics` balíčku Fairlearn. `_create_group_metric_set`Metoda má podobné argumenty jako konstruktor řídicího panelu s tím rozdílem, že citlivé funkce jsou předány jako slovník (aby se zajistilo, že jsou k dispozici názvy). Při volání této metody je také nutné zadat typ předpovědi (binární klasifikace v tomto případě).

    ```python
    #  Create a dictionary of model(s) you want to assess for fairness 
    sf = { 'Race': A_test.race, 'Sex': A_test.sex}
    ys_pred = { lr_reg_id:lr_predictor.predict(X_test) }
    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. Nahrajte předpočítané metriky spravedlivosti.
    
    Nyní importujte `azureml.contrib.fairness` balíček pro provedení nahrání:

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    Vytvořte experiment, potom ho spusťte a nahrajte na něj řídicí panel:
    ```python
    exp = Experiment(ws, "Test_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness insights of Logistic Regression Classifier"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```
5. Podívejte se na řídicí panel spravedlnosti z Azure Machine Learning studia.

    Pokud jste dokončili předchozí kroky (Nahrávám vygenerované přehledy o nerovnosti do Azure Machine Learning), můžete zobrazit řídicí panel pro spravedlivé v [Azure Machine Learning Studiu](https://ml.azure.com). Tento řídicí panel je stejný řídicí panel vizualizace, který je k dispozici v Fairlearn, což vám umožní analyzovat rozdíly mezi podskupinami citlivých funkcí (např. samci vs. žena).
    Použijte jednu z těchto cest pro přístup k řídicímu panelu vizualizace v Azure Machine Learning Studiu:

    * **Podokno experimentů (Preview)**
    1. V levém podokně vyberte **experimenty** a zobrazí se seznam experimentů, které jste spustili v Azure Machine Learning.
    1. Vyberte konkrétní experiment pro zobrazení všech běhů v tomto experimentu.
    1. Vyberte běh a pak kartu **spravedlivé** na řídicí panel vizualizace vysvětlení.


    [![Řídicí panel spravedlivého](./media/how-to-machine-learning-fairness-aml/dashboard.png)](./media/how-to-machine-learning-fairness-aml/dashboard.png#lightbox)
    
    * **Podokno modely**
    1. Pokud jste svůj původní model zaregistrovali pomocí předchozích kroků, můžete v levém podokně vybrat **modely** a zobrazit je.
    1. Vyberte model a pak kartu **spravedlivé** pro zobrazení řídicího panelu vizualizace vysvětlení.

    Další informace o řídicím panelu vizualizace a o tom, co obsahuje, najdete v [uživatelské příručce](https://fairlearn.github.io/master/user_guide/assessment.html#fairlearn-dashboard)pro Fairlearn.

## <a name="upload-fairness-insights-for-multiple-models"></a>Nahrávání přehledů o nerovnosti pro více modelů

Chcete-li porovnat více modelů a zjistit, jak se jejich hodnocení neliší, můžete do řídicího panelu vizualizace předat více než jeden model a porovnat jejich kompromisy v rovnosti výkonu.

1. Školení vašich modelů:
    
    Nyní vytvoříme druhý klasifikátor založený na Estimator Vector support Machine a nahrajte slovník řídicích panelů s použitím `metrics` balíčku Fairlearn. Předpokládáme, že dříve vyškolený model je stále k dispozici.


    ```python
    # Put an SVM predictor onto the preprocessing pipeline
    from sklearn import svm
    svm_predictor = Pipeline(
        steps=[
            ("preprocessor", copy.deepcopy(preprocessor)),
            (
                "classifier",
                svm.SVC(),
            ),
        ]
    )

    # Train your second classification model
    svm_predictor.fit(X_train, y_train)
    ```

2. Registrace modelů

    Další Registrujte oba modely v rámci Azure Machine Learning. Pro usnadnění uložte výsledky do slovníku, který mapuje `id` registrovaný model (řetězec ve `name:version` formátu) na prediktivní samotný:

    ```python
    model_dict = {}

    lr_reg_id = register_model("fairness_logistic_regression", lr_predictor)
    model_dict[lr_reg_id] = lr_predictor

    svm_reg_id = register_model("fairness_svm", svm_predictor)
    model_dict[svm_reg_id] = svm_predictor
    ```

3. Místní načtení řídicího panelu Fairlearn

    Před nahráním informací o spravedlnosti do Azure Machine Learning můžete tyto předpovědi zkoumat v místně vyvolaném řídicím panelu Fairlearn. 



    ```python
    #  Generate models' predictions and load the fairness dashboard locally 
    ys_pred = {}
    for n, p in model_dict.items():
        ys_pred[n] = p.predict(X_test)

    from fairlearn.widget import FairlearnDashboard

    FairlearnDashboard(sensitive_features=A_test, 
                    sensitive_feature_names=['Race', 'Sex'],
                    y_true=y_test.tolist(),
                    y_pred=ys_pred)
    ```

3. Předvýpočetní metriky spravedlnosti.

    Vytvořte slovník řídicích panelů pomocí `metrics` balíčku Fairlearn.

    ```python
    sf = { 'Race': A_test.race, 'Sex': A_test.sex }

    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=Y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. Nahrajte předpočítané metriky spravedlivosti.
    
    Nyní importujte `azureml.contrib.fairness` balíček pro provedení nahrání:

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    Vytvořte experiment, potom ho spusťte a nahrajte na něj řídicí panel:
    ```python
    exp = Experiment(ws, "Compare_Two_Models_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness Assessment of Logistic Regression and SVM Classifiers"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```


    Podobně jako v předchozí části můžete sledovat jednu z výše popsaných cest (prostřednictvím **experimentů** nebo **modelů**) v nástroji Azure Machine Learning Studio pro přístup k řídicímu panelu vizualizace a porovnat tyto dva modely z podmínek rovnosti a výkonu.


## <a name="upload-unmitigated-and-mitigated-fairness-insights"></a>Nahrajte si nezmírňované a zmírňované poznatky o rovnosti.

Můžete použít [algoritmy pro zmírnění rizik](https://fairlearn.github.io/master/user_guide/mitigation.html)v Fairlearn, porovnat jejich vygenerované zmírňované modely s původním nehodnoceným modelem a přejít na výkon a kompromisy mezi porovnanými modely.

Příklad, který demonstruje použití algoritmu pro zmírnění [hledání v mřížce](https://fairlearn.github.io/master/user_guide/mitigation.html#grid-search) (což znamená, že vytvoří kolekci rizikových modelů s různými kompromisy a výkonem), najdete v tomto [ukázkovém poznámkovém bloku](https://github.com/Azure/MachineLearningNotebooks/blob/master/contrib/fairness/fairlearn-azureml-mitigation.ipynb). 

Nahrávání přehledů spravedlnosti v různých modelech v jednom spuštění umožňuje porovnat modely s ohledem na spravedlivé a výkon. Můžete kliknout na libovolný model zobrazený v grafu porovnání modelů a zobrazit podrobné informace o spravedlivém konkrétního modelu.


[![Řídicí panel Fairlearn porovnání modelů](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png)](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png#lightbox)
    

## <a name="next-steps"></a>Další kroky

[Další informace o spravedlivosti modelu](concept-fairness-ml.md)

[Podívejte se na ukázkové poznámkové bloky Azure Machine Learning spravedlivosti](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness)
