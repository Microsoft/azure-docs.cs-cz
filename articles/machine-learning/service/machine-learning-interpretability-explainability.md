---
title: Interpretovatelnost modelů
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak vysvětlit, proč váš model vytváří předpovědi pomocí sady SDK služby Azure Machine Learning. Můžete použít při trénování a odvozování pochopit, jak váš model vytváří předpovědi.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: larryfr
ms.date: 05/30/2019
ms.openlocfilehash: 4f1dd0bfc0637c6681b513860a63df2b8c35fe5e
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/31/2019
ms.locfileid: "66430856"
---
# <a name="model-interpretability-with-azure-machine-learning-service"></a>Model interpretability službou Azure Machine Learning

V tomto článku se dozvíte, jak vysvětlit, proč váš model provedené předpovědi se u různých interpretability balíčky Azure Machine Learning Python SDK.

Použití tříd a metod v sadě SDK, můžete získat:
+ Funkce význam hodnoty funkcí nezpracovaná a analyzován
+ Interpretability na skutečné datové sady ve velkém měřítku, během trénování a odvozování.
+ Interaktivní vizualizace pro pomoc při zjišťování vzory v datech a vysvětlení chvíli školení

Během fáze školení vývojového cyklu můžete návrháře modelů a nástroje pro vyhodnocení použít interpretability výstup z modelu k ověření hypotézy a vytvoření vztahu důvěryhodnosti se zúčastněnými stranami.  Používá taky informace o modelu pro ladění, ověřování chování modelu odpovídá své cíle a ke kontrole posun.

Ve službě machine learning, **funkce** jsou datová pole použít k předvídání cílový datový bod. Například předpovědět úvěrové riziko, datová pole pro stáří, velikost účet a účet stáří dají zneužít. V tomto případě jsou věk, účet velikost a stáří účet **funkce**. Důležité funkce zjistíte, jaký vliv na každé datové pole modelu předpovědi. Například věk silně lze do predikce. když účet velikost a stáří nemají vliv na přesnost předpovědi výrazně. Tento proces umožňuje datovým vědcům vysvětlují výsledný predikcí, tak, aby zúčastněné strany mají přehled o jaké datové body jsou nejdůležitější v modelu.

Pomocí těchto nástrojů můžete vysvětlit modelů strojového učení **globálně na všechny data**, nebo **místně na konkrétní datové body** pomocí technologií stavu techniky snadným ovládáním a škálovatelným způsobem.

Třídy interpretability jsou k dispozici prostřednictvím více balíčků sady SDK. Zjistěte, jak [instalaci balíčků sady SDK pro Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* [`azureml.explain.model`](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py), hlavního balíčku, který obsahuje funkce, které podporuje Microsoft.

* `azureml.contrib.explain.model`, ve verzi preview a experimentální funkce, které můžete vyzkoušet.

* `azureml.train.automl.automlexplainer` balíček pro interpretaci automatizované modelů strojového učení.

> [!IMPORTANT]
> Obsah `contrib` oboru názvů není podporováno. Experimentální funkce jsou až po zralé, jsou postupně se přesune do hlavní oboru názvů.

## <a name="how-to-interpret-your-model"></a>Jak interpretovat modelu

Můžete použít interpretability třídy a metody k pochopení chování modelu globální nebo konkrétní předpovědi. Předchozí se nazývá globální vysvětlení a ten je místní vysvětlení.

Metody mohou být seznamu zařazena také podle toho, jestli metoda bez ohledu na model nebo model konkrétní. Některé metody cílit na určité typy modelů. Například pro okno stromu vysvětlení platí jenom pro modely založený na stromové architektuře. Některé metody považovat černé pole, jako je například mimic vysvětlení nebo vysvětlení jádra na okno modelu. `explain` Balíček využívá tyto různé přístupy, na základě datové sady, model typy a případy použití.

Výstup je sada informací o jak daný model díky jeho predikcí, jako například:
* Globální/místní funkce relativní důležitost

* Globální/místní funkce a predikcí vztah

### <a name="explainers"></a>Explainers

Existují dvě sady explainers: Přímé Explainers a Meta Explainers v sadě SDK.

__Přímé explainers__ pocházejí z integrované knihovny. Sada SDK zabalí všechny explainers tak, aby zveřejňovaly společné rozhraní API a formát výstupu. Pokud jsou vám bližší přímo pomocí těchto explainers, můžete je vyvolat přímo namísto použití společné rozhraní API a formát výstupu. Následuje seznam s přímým přístupem explainers k dispozici v sadě SDK:

* **Strom vysvětlení**: Vysvětlení stromu na okno, které se zaměřuje na polynomické rychlé okno hodnotu odhad algoritmus time specifické pro stromy a umožňující stromů.
* **Podrobné vysvětlení**: Podle vysvětlení z okno, podrobné vysvětlení "je vysokorychlostní aproximační algoritmus pro okno hodnoty v modely obsáhlého learningu, která staví na připojení s DeepLIFT popsané v dokumentu NIPS okno. Jsou podporovány TensorFlow modely a Keras modelů pomocí back-endu TensorFlow (je taky předběžnou podporu pro PyTorch) ".
* **Vysvětlení jádra**: Vysvětlení jádra na okno používá k odhadu okno hodnoty pro všechny modely speciálně vážený místní lineární regrese.
* **Napodobení vysvětlení**: Vysvětlení mimic je založená na nápad globální náhradní modely. Globální náhradní model je vnitřně interpretovatelném model, který se trénuje aproximace předpovědi modelu černé skříňky co nejpřesněji. Mezi odborníky přes data interpretovat model náhradní závěrům o černé pole modelu.
* **LIMETKOVĚ vysvětlení** (`contrib`): Na základě LIMETKOVĚ, LIMETKOVĚ vysvětlení pomocí algoritmu stavu nejmodernější místní interpretovatelném modelu bez ohledu na vysvětlení (LIMETKOVĚ) vytvářet modely místní náhradní. Na rozdíl od modelů globální náhradní nastavení LIMETKOVĚ se zaměřuje na školení místní náhradní modely, které popisují jednotlivé předpovědi.
* **Text vysvětlení HAN** (`contrib`): HANU Text vysvětlení používá síť hierarchické pozornost pro získávání vysvětlení modelu z textových dat pro danou černé skříňky textový model. Model náhradní HAN na předpokládané výstupy učitelů daný model trénujeme. Po školení globálně napříč text souhrnu, jsme přidali fine-tune krok pro určitého dokumentu za účelem zlepšení přesnosti vysvětlení. HANU používá obousměrný RNN s dvě vrstvy pozornost věty a word pozornost. Jakmile DNN natrénovali model učitele a doladíte na konkrétním dokumentu, jsme importances slovo extrahovat z vrstvy pozornost. Našli jsme HAN jako přesnější než LIMETKOVĚ nebo okno textových dat, ale dražší z hlediska přípravy a čas. Ale provedli jsme vylepšení na školení čas tím, že uživatel možnost inicializace síť s rukavice vkládání slov, i když je stále pomalý. Čas školení může značně zlepšit spuštěním HAN na vzdáleném virtuálním počítači Azure GPU. Implementace HAN je popsána v "hierarchické pozornost sítě pro klasifikace dokumentů (Yang et al., 2016). ([https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf](https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf)).

__Meta explainers__ automaticky vybrat vhodný vysvětlení s přímým přístupem a generovat nejlepší vysvětlení informací na základě daného modelu a datových sad. Meta explainers využívat všechny knihovny (okno, LIMETKOVĚ, znázorněna atd.), které budeme integrovat nebo vyvinutý. Tady jsou k dispozici v sadě SDK meta explainers:

* **Tabulkové vysvětlení**: Použít s tabulkové datové sady.
* **Text vysvětlení**: Použít s datovými sadami text.

Kromě toho na meta výběr z přímé explainers, meta explainers vyvinout další funkce nad rámec základní knihovny a zlepšit rychlost a škálovatelnost na přímé explainers.

Aktuálně `TabularExplainer` využívá k vyvolání přímé Explainers podle následujícího postupu:

1. Pokud je model založený na stromové architektuře, použít `TreeExplainer`, else
2. Pokud je DNN model, použít `DeepExplainer`, else
3. Považovat černé skříňky modelu a použít `KernelExplainer`

Funkce intelligence integrované do `TabularExplainer` bude složitější, další knihovny jsou integrované do sady SDK a další výhody a nevýhody jednotlivých vysvětlení.

`TabularExplainer` také udělal významná vylepšení funkcí a výkonu prostřednictvím přímé Explainers:

* **Shrnutí datové sady, inicializace**. V případech, kdy je nejdůležitější rychlost vysvětlení můžeme shrnout inicializace datové sady a generovat malou sadu ukázky, které urychluje vysvětlení globální a místní.
* **Vzorkování sady dat hodnocení**. Pokud uživatel předává ve velké sady vzorků hodnocení, ale ve skutečnosti nemusí, všechny z nich má být vyhodnocen, vzorkování parametr lze nastavit na hodnotu true pro urychlení globálního vysvětlení.

Následující diagram znázorňuje vztah mezi dvěma sadami přímo a meta explainers.

[![Ve službě Machine Learning Interpretability architektury](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)

### <a name="models-supported"></a>Podporované modely

Modely, které jsou trénované na datové sady v Pythonu `numpy.array`, `pandas.DataFrame`, `iml.datatypes.DenseData`, nebo `scipy.sparse.csr_matrix` formátu jsou podporovány interpretability `explain` balíčku sady SDK.

Vysvětlení funkce přijímají jako vstup modely a kanály. Pokud model je k dispozici, musí implementovat model funkci předpovědi `predict` nebo `predict_proba` , který odpovídá Scikit konvence. Pokud kanál (název souboru, který kanálu) vysvětlení funkce předpokládá, že spouštění skriptu kanálu vrací předpověď na.

### <a name="local-and-remote-compute-target"></a>Místní a vzdálené cílové výpočetní prostředí

`explain` Balíčku je navržen pro práci s oběma cílových výpočetních prostředí místní a vzdálené. Je-li spustit místně, sada SDK funkce nebude obraťte se na služby Azure. Můžete spustit vysvětlení vzdáleně v Azure Machine Learning Compute a informace o vysvětlení přihlásit spuštění služby historie Azure Machine Learning. Jakmile tyto informace se zaznamená, sestavy a vizualizace z vysvětlení jsou snadno dostupné na portálu pracovní prostor Azure Machine Learning pro analýzu uživatele.

## <a name="interpretability-in-training"></a>Interpretability do školení.

### <a name="train-and-explain-locally"></a>Trénování a popisují místně

1. Vyzkoušejte svůj model v místní aplikace Jupyter notebook.

    ```python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    # Split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data, breast_cancer_data.target, test_size=0.2, random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

2. Volání vysvětlení: K inicializaci objektu vysvětlení, je potřeba předat konstruktoru vysvětlení modelu a některé trénovací data. Také v případě potřeby můžete předat funkci názvy a názvy tříd výstup (Pokud je to klasifikace), které se budou používat k označení informativnější vysvětlení a vizualizace. Tady je postup pro vytvoření instance objektu vysvětlení pomocí [TabularExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.tabularexplainer?view=azure-ml-py) a [MimicExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic.mimicexplainer?view=azure-ml-py) místně. `TabularExplainer` volání jedné z tři explainers pod (`TreeExplainer`, `DeepExplainer`, nebo `KernelExplainer`) a je automaticky vybere nejvhodnější z nich pro případy použití. Můžete však všechny její tři základní explainers volat přímo.

    ```python
    from azureml.explain.model.tabular_explainer import TabularExplainer
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    ```

    nebo

    ```python
    from azureml.explain.model.mimic.mimic_explainer import MimicExplainer
    from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel

    # "features" and "classes" fields are optional
    explainer = MimicExplainer(model, x_train, LGBMExplainableModel, features=breast_cancer_data.feature_names, classes=classes)
    ```

3. Získá globální funkce význam hodnoty.

    ```python
    # You can use the training data or the test data here
    global_explanation = explainer.explain_global(x_train)
    # Sorted feature importance values and feature names
    sorted_global_importance_values = global_explanation.get_ranked_global_values()
    sorted_global_importance_names = global_explanation.get_ranked_global_names()
    dict(zip(sorted_global_importance_names, sorted_global_importance_values))
    ```

4. Místní funkce význam hodnoty: popisují jednotlivé instance nebo na skupinu instancí systému pomocí následující volání funkce.

    ```python
    # explain the first data point in the test set
    local_explanation = explainer.explain_local(x_test[0])

    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

    nebo

    ```python
    # explain the first five data points in the test set
    local_explanation = explainer.explain_local(x_test[0:4])

    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

### <a name="train-and-explain-remotely"></a>Trénování a popisují vzdáleně

Zatímco trénovat na různých cílových výpočetních prostředí podporované službou Azure Machine Learning, příklad v této části ukazuje, jak to udělat cíli Azure Machine Learning Compute.

1. Vytvoření trénovací skript v místní aplikace Jupyter notebook (například run_explainer.py).

    ```python
    run = Run.get_context()
    client = ExplanationClient.from_run(run)

    # Train your model here

    # explain predictions on your local machine
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    # explain local data points (individual instances)
    local_explanation = explainer.explain_local(x_test[0])
    # upload global and local explanation objects to Run History
    client.upload_model_explanation(run, local_explanation, top_k=2, comment='local explanation: top 2 features')
    # Uploading global model explanation data for storage or visualization in webUX
    # The explanation can then be downloaded on any compute
    # Multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # Or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

2. Postupujte podle pokynů [nastavení cílových výpočetních prostředí k tréninku modelu](how-to-set-up-training-targets.md#amlcompute) Další informace o nastavení Azure Machine Learning Compute jako vaše cílové výpočetní prostředí a odeslat spuštění školení.

3. Stáhněte si vysvětlení ve vaší místní aplikace Jupyter notebook.

    ```python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    # Get model explanation data
    client = ExplanationClient.from_run(run)
    explanation = client.download_model_explanation()
    local_importance_values = explanation.local_importance_values
    expected_values = explanation.expected_values
    # Or you can use the saved run.id to retrive the feature importance values
    client = ExplanationClient.from_run_id(ws, experiment_name, run.id)
    explanation = client.download_model_explanation()
    local_importance_values = explanation.local_importance_values
    expected_values = explanation.expected_values
    # Get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```

## <a name="visualizations"></a>Vizualizace

Pomocí řídicího panelu vizualizace k pochopení a interpretovat modelu:

### <a name="global-visualizations"></a>Globální vizualizace

Následující grafy poskytuje globální přehled o trénovaného modelu spolu s jeho předpovědi a vysvětlení.

|Plot|Popis|
|----|-----------|
|Zkoumání dat| Přehled datové sady společně s hodnotami předpovědi.|
|Globální význam|Zobrazuje globálně hlavní důležité funkce K (Konfigurovat K). V této tabulce jsou užitečné pro pochopení globální chování základní model.|
|Vysvětlení průzkum|Ukazuje, jak funkce je odpovědný za provedení změny v modelu předpovědi hodnot (nebo pravděpodobnost předpovědi hodnot). |
|Souhrn| Používá hodnoty význam podepsaný místní funkci napříč všechny datové body rozdělení dopad, který každá funkce má na základě hodnoty předpovědi.|

[![Řídicí panel vizualizace globální](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>Místní vizualizace

Kliknutím na libovolný jednotlivých datových bodů v okamžiku předchozí vykreslení se načíst diagram význam místní funkce pro daný datový bod.

|Plot|Popis|
|----|-----------|
|Místní význam|Zobrazuje globálně hlavní důležité funkce K (Konfigurovat K). V této tabulce jsou užitečné pro pochopení místní chování základní model na konkrétní datový bod.|

[![Místní vizualizace řídicího panelu](./media/machine-learning-interpretability-explainability/local-charts.png)](./media/machine-learning-interpretability-explainability/local-charts.png#lightbox)

Uložení řídicího panelu vizualizace, použijte následující kód:

```python
from azureml.contrib.explain.model.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

## <a name="raw-feature-transformations"></a>Nezpracovaná funkce transformace

Můžete volitelně předat vysvětlení přijímat vysvětlení, co se týče nezpracovaná funkce před transformaci (spíše než inženýrství funkce) kanál transformace vaší funkce. Pokud přeskočíte tím, vysvětlení poskytuje vysvětlení, co se týče inženýrství funkce.

Formát podporované transformace je stejný, jak je popsáno v [skriptu sklearn pandas](https://github.com/scikit-learn-contrib/sklearn-pandas). Všechny transformace jsou podporovány obecně, za předpokladu, pracovat s jedním sloupcem a proto jsou jasně jednoho k několika.

```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# Assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(handle_unknown='ignore', sparse=False)) for f in categorical]

transformations = numeric_transformations + categorical_transformations

# Append model to preprocessing pipeline.
# Now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', DataFrameMapper(transformations)),
                    ('classifier', LogisticRegression(solver='lbfgs'))])

# clf.steps[-1][1] returns the trained classification model
# Pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1], initialization_examples=x_train, features=dataset_feature_names, classes=dataset_classes, transformations=transformations)
```

## <a name="interpretability-in-inference"></a>Interpretability v odvození

Vysvětlení, se dají nasadit spolu s původním modelu a je možné při vyhodnocování čas zadání informací o místní vysvětlení. Proces nasazení bodování vysvětlení je podobný modelu a zahrnuje následující kroky:

1. Vytvoření objektu vysvětlení:

   ```python
   from azureml.contrib.explain.model.tabular_explainer import TabularExplainer

   explainer = TabularExplainer(model, x_test)
   ```

1. Vytváření hodnoticí vysvětlení, používání objektu vysvětlení:

   ```python
   scoring_explainer = explainer.create_scoring_explainer(x_test)

   # Pickle scoring explainer
   scoring_explainer_path = scoring_explainer.save('scoring_explainer_deploy')
   ```

1. Konfigurace a registrace bitovou kopii, která používá model bodování vysvětlení.

   ```python
   # Register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   run.upload_file('breast_cancer_scoring_explainer.pkl', scoring_explainer_path)
   model = run.register_model(model_name='breast_cancer_scoring_explainer', model_path='breast_cancer_scoring_explainer.pkl')
   print(model.name, model.id, model.version, sep = '\t')
   ```

1. [Volitelné] Získání hodnoticího vysvětlení z cloudu a otestovat vysvětlení

   ```python
   from azureml.contrib.explain.model.scoring.scoring_explainer import ScoringExplainer

   # Retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'breast_cancer_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # Load scoring explainer from disk
   scoring_explainer = ScoringExplainer.load(scoring_explainer_model_path)

   # Test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. Nasazení bitové kopie do cílového výpočetního prostředí:

   1. Vytvořit soubor vyhodnocení (před tímto krokem, postupujte podle kroků v [nasazujte modely pomocí služby Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where) zaregistrovat váš původním prediktivního modelu)

        ```python
        %%writefile score.py
        import json
        import numpy as np
        import os
        import pickle
        from sklearn.externals import joblib
        from sklearn.linear_model import LogisticRegression
        from azureml.core.model import Model

        def init():

            global original_model
            global scoring_model

            # Retrieve the path to the model file using the model name
            # Assume original model is named original_prediction_model
            original_model_path = Model.get_model_path('original_prediction_model')
            scoring_explainer_path = Model.get_model_path('breast_cancer_scoring_explainer')

            original_model = joblib.load(original_model_path)
            scoring_explainer = joblib.load(scoring_explainer_path)

        def run(raw_data):
            # Get predictions and explanations for each data point
            data = np.array(json.loads(raw_data)['data'])
            # Make prediction
            predictions = original_model.predict(data)
            # Retrieve model explanations
            local_importance_values = scoring_explainer.explain(data)
            # You can return any data type as long as it is JSON-serializable
            return {'predictions': predictions.tolist(), 'local_importance_values': local_importance_values}
        ```

   1. Definování konfigurace nasazení (Tato konfigurace závisí na požadavcích modelu. Následující příklad definuje konfiguraci, která používá jedno Procesorové jádro a 1 GB paměti)

        ```python
        from azureml.core.webservice import AciWebservice

        aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                       memory_gb=1,
                                                       tags={"data": "breastcancer",
                                                             "method" : "local_explanation"},
                                                       description='Get local explanations for breast cancer data')
        ```

   1. Vytvořte soubor s prostředí závislosti

        ```python
        from azureml.core.conda_dependencies import CondaDependencies

        # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)


        myenv = CondaDependencies.create(pip_packages=["azureml-defaults", "azureml-explain-model", "azureml-contrib-explain-model"],
                                        conda_packages=["scikit-learn"])

        with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

        with open("myenv.yml","r") as f:
            print(f.read())
        ```

   1. Vytvoření vlastního souboru dockerfile pomocí nainstalované g ++

        ```python
        %%writefile dockerfile
        RUN apt-get update && apt-get install -y g++
        ```

   1. Nasadit vytvořené image (časový odhad: 5 minut).

        ```python
        from azureml.core.webservice import Webservice
        from azureml.core.image import ContainerImage

        # Use the custom scoring, docker, and conda files we created above
        image_config = ContainerImage.image_configuration(execution_script="score.py",
                                                        docker_file="dockerfile",
                                                        runtime="python",
                                                        conda_file="myenv.yml")

        # Use configs and models generated above
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

    # Create data to test service with
    x_list = x_test.tolist()
    examples = x_list[:4]
    input_data = "{\"data\": " + str(examples) + "}"

    headers = {'Content-Type':'application/json'}

    # send request to service
    resp = requests.post(service.scoring_uri, input_data, headers=headers)

    print("POST to url", service.scoring_uri)
    # can covert back to Python objects from json string if desired
    print("prediction:", resp.text)
    ```

1. Vyčistěte: Chcete-li odstranit nasazenou webovou službu, použijte `service.delete()`.

## <a name="interpretability-in-automated-ml"></a>Interpretability automatizované ml

Automatizované machine learning obsahuje balíčky pro interpretaci funkce význam v modelů trénovaných automaticky. Kromě toho scénáře klasifikace umožňují načíst význam funkce na úrovni třídy. Chcete-li povolit toto chování v rámci automatizovaného strojového učení dvěma způsoby:

* Chcete-li povolit funkci důležitosti pro komplet trénovaného modelu, použijte [ `explain_model()` ](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlexplainer?view=azure-ml-py) funkce.

    ```python
    from azureml.train.automl.automlexplainer import explain_model

    shap_values, expected_values, overall_summary, overall_imp, \
        per_class_summary, per_class_imp = explain_model(fitted_model, X_train, X_test)
    ```

* Chcete-li povolit funkci důležitosti pro každé jedno spuštění před školení, nastavte `model_explainability` parametr `True` v `AutoMLConfig` objektu, spolu s poskytuje data pro ověření. Potom použijte [ `retrieve_model_explanation()` ](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlexplainer?view=azure-ml-py) funkce.

    ```python
    from azureml.train.automl.automlexplainer import retrieve_model_explanation

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, \
        per_class_imp = retrieve_model_explanation(best_run)
    ```

Další informace najdete v tématu [postupy](how-to-configure-auto-train.md#explain-the-model-interpretability) o povolení funkce interpretability automatizované machine Learning.

## <a name="next-steps"></a>Další postup

Kolekce poznámkové bloky Jupyter, které ukazují výše uvedené pokyny najdete v tématu [Azure Machine Learning Interpretability ukázkové poznámkové bloky](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).
