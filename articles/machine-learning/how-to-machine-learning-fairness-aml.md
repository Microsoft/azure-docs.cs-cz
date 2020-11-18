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
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 3fbd4990fd330960bb8dbce2e2a8d1bcb578cf2a
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701180"
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



## <a name="upload-fairness-insights-for-a-single-model"></a>Nahrávání přehledů o nerovnosti pro jeden model

Následující příklad ukazuje, jak použít balíček s přístejnou jistotou k nahrání nerovnosti modelu do Azure Machine Learning a na řídicím panelu pro posouzení spravedlivého zobrazení v Azure Machine Learning Studiu.

1. Vytvořte Vzorový model na Jupyter poznámkovém bloku. 

    Pro datovou sadu používáme známou datovou sadu pro seznámení s načtením `shap` (pro usnadnění). Pro účely tohoto příkladu budeme tuto datovou sadu zakládat jako problém s rozhodnutím o výpůjčce a předstírat, že popisek označuje, jestli se v minulosti má za každou jednotlivou půjčku zaplatit. Tato data použijeme k tomu, abychom předvedli předpověď, jestli nezobrazená jednotliví uživatelé uhradí půjčku nebo ne. Předpokladem je, že předpovědi modelu slouží k rozhodnutí, jestli by měl jednotlivec být nabízený půjčkou.

    ```python
    from sklearn.model_selection import train_test_split
    from fairlearn.widget import FairlearnDashboard
    from sklearn.linear_model import LogisticRegression
    from sklearn.preprocessing import LabelEncoder, StandardScaler
    import pandas as pd
    import shap

    # Load the census dataset
    X_raw, Y = shap.datasets.adult()
    X_raw["Race"].value_counts().to_dict()
    

    # (Optional) Separate the "sex" and "race" sensitive features out and drop them from the main data prior to training your model
    A = X_raw[['Sex','Race']]
    X = X_raw.drop(labels=['Sex', 'Race'],axis = 1)
    X = pd.get_dummies(X)
    
    sc = StandardScaler()
    X_scaled = sc.fit_transform(X)
    X_scaled = pd.DataFrame(X_scaled, columns=X.columns)

    # Perform some standard data preprocessing steps to convert the data into a format suitable for the ML algorithms
    le = LabelEncoder()
    Y = le.fit_transform(Y)

    # Split data into train and test
    from sklearn.model_selection import train_test_split
    from sklearn.model_selection import train_test_split
    X_train, X_test, Y_train, Y_test, A_train, A_test = train_test_split(X_scaled, 
                                                        Y, 
                                                        A,
                                                        test_size = 0.2,
                                                        random_state=0,
                                                        stratify=Y)

    # Work around indexing issue
    X_train = X_train.reset_index(drop=True)
    A_train = A_train.reset_index(drop=True)
    X_test = X_test.reset_index(drop=True)
    A_test = A_test.reset_index(drop=True)

    # Improve labels
    A_test.Sex.loc[(A_test['Sex'] == 0)] = 'female'
    A_test.Sex.loc[(A_test['Sex'] == 1)] = 'male'


    A_test.Race.loc[(A_test['Race'] == 0)] = 'Amer-Indian-Eskimo'
    A_test.Race.loc[(A_test['Race'] == 1)] = 'Asian-Pac-Islander'
    A_test.Race.loc[(A_test['Race'] == 2)] = 'Black'
    A_test.Race.loc[(A_test['Race'] == 3)] = 'Other'
    A_test.Race.loc[(A_test['Race'] == 4)] = 'White'


    # Train a classification model
    lr_predictor = LogisticRegression(solver='liblinear', fit_intercept=True)
    lr_predictor.fit(X_train, Y_train)

    # (Optional) View this model in Fairlearn's fairness dashboard, and see the disparities which appear:
    from fairlearn.widget import FairlearnDashboard
    FairlearnDashboard(sensitive_features=A_test, 
                       sensitive_feature_names=['Sex', 'Race'],
                       y_true=Y_test,
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
    sf = { 'Race': A_test.Race, 'Sex': A_test.Sex}
    ys_pred = { lr_reg_id:lr_predictor.predict(X_test) }
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

Pokud vás zajímá porovnání více modelů a zjistíte, jak se jejich posouzení neprojeví, můžete do řídicího panelu vizualizace předat více než jeden model a přejít na jeho kompromis v souvislosti s výkonem.

1. Školení vašich modelů:
    
    Kromě předchozího modelu logistické regrese teď vytvoříme druhý klasifikátor založený na Estimator Vector support Machine a nahrajte slovník řídicích panelů s použitím `metrics` balíčku Fairlearn. Všimněte si, že přeskočíme kroky pro načtení a předzpracování dat a přechod přímo do fáze školení modelů.


    ```python
    # Train your first classification model
    from sklearn.linear_model import LogisticRegression
    lr_predictor = LogisticRegression(solver='liblinear', fit_intercept=True)
    lr_predictor.fit(X_train, Y_train)

    # Train your second classification model
    from sklearn import svm
    svm_predictor = svm.SVC()
    svm_predictor.fit(X_train, Y_train)
    ```

2. Registrace modelů

    Další Registrujte oba modely v rámci Azure Machine Learning. Pro usnadnění v následných voláních metod uložte výsledky do slovníku, který mapuje `id` registrovaný model (řetězec ve `name:version` formátu) na prediktivní samotný:

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
                    sensitive_feature_names=['Sex', 'Race'],
                    y_true=Y_test.tolist(),
                    y_pred=ys_pred)
    ```

3. Předvýpočetní metriky spravedlnosti.

    Vytvořte slovník řídicích panelů pomocí `metrics` balíčku Fairlearn.

    ```python
    sf = { 'Race': A_test.Race, 'Sex': A_test.Sex }

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

V tomto [ukázkovém poznámkovém bloku](https://github.com/Azure/MachineLearningNotebooks/blob/master/contrib/fairness/fairlearn-azureml-mitigation.ipynb)si můžete prohlédnout příklad, který demonstruje použití algoritmu zmírňování při [hledání mřížky](https://fairlearn.github.io/master/user_guide/mitigation.html#grid-search) (který vytváří kolekci rizikových modelů s různými kompromisy a kompromisy týkající se obchodování) 

Nahrávání přehledů o neprospěchu více modelů v rámci jednoho spuštění umožní porovnat modely s ohledem na spravedlivé a výkon. Pokud chcete zobrazit podrobné přehledy o konkrétním modelu, můžete si dále kliknout na libovolný model zobrazený v grafu porovnání modelů.


[![Řídicí panel Fairlearn porovnání modelů](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png)](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png#lightbox)
    

## <a name="next-steps"></a>Další kroky

[Další informace o spravedlivosti modelu](concept-fairness-ml.md)

[Podívejte se na ukázkové poznámkové bloky Azure Machine Learning spravedlivosti](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness)
