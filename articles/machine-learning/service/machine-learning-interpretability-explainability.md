---
title: Interpretovatelnost modelů
titleSuffix: Azure Machine Learning service
description: Naučte se vysvětlit, proč model předpovědi pomocí sady SDK Azure Machine Learning. Dá se použít během školení a odvození, abyste porozuměli tomu, jak model dělá předpovědi.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: larryfr
ms.date: 06/21/2019
ms.openlocfilehash: 1e742c278b9356c7501964541802e0c96dc74b09
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358657"
---
# <a name="model-interpretability-with-azure-machine-learning-service"></a>Interpretace modelu pomocí služby Azure Machine Learning

V tomto článku se dozvíte, proč váš model provedl předpovědi s různými balíčky pro interpretaci Azure Machine Learning Python SDK.

Pomocí tříd a metod v sadě SDK můžete získat:
+ Hodnoty důležitosti funkcí pro nezpracované i inženýrské funkce
+ Možnost interpretace datových sad reálného světa ve velkém měřítku, během školení a odvození.
+ Interaktivní vizualizace, které pomáhají při zjišťování vzorů v datech a vysvětlení v době školení

V rámci školicí fáze cyklu vývoje mohou návrháři modelů a vyhodnocovací filtry použít výstup interpretace modelu k ověření hypotéz a vytváření vztahů důvěryhodnosti se zúčastněnými stranami.  Využívají také přehledy modelu pro ladění, ověřování chování modelu odpovídá jejich cílům a ke kontrole bias.

Ve strojovém učení jsou **funkcemi** datová pole, která slouží k předpovědi cílového datového bodu. Například pro předpověď úvěrového rizika je možné použít datová pole pro stáří, velikost účtu a stáří účtu. V tomto případě jsou **funkce**stáří, velikost účtu a stáří účtu. Důležitost funkcí vysvětluje, jak každé datové pole ovlivnilo předpovědi modelu. Například věk může být v předpovědi silně využíván, zatímco velikost účtu a stáří neovlivňují přesnost předpovědi významně. Tento proces umožňuje datovým vědcům vysvětlit výsledné předpovědi, aby zúčastněné strany měly přehled o tom, jaké datové body jsou v modelu nejdůležitější.

Pomocí těchto nástrojů můžete vysvětlovat modely strojového učení **globálně na všech datech**nebo **lokálně na konkrétním datovém bodu** pomocí snadno použitelného a škálovatelného datového bodu s využitím nejmodernějších technologií.

Třídy interpretace jsou zpřístupněny prostřednictvím více balíčků sady SDK. Naučte se [instalovat balíčky SDK pro Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* [`azureml.explain.model`](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py), hlavní balíček obsahující funkce podporované Microsoftem.

* `azureml.contrib.explain.model`, ve verzi Preview a experimentálních funkcích, které můžete vyzkoušet.

* `azureml.train.automl.automlexplainer`balíček pro interpretaci automatizovaných modelů strojového učení.

> [!IMPORTANT]
> Obsah v `contrib` oboru názvů není plně podporován. Vzhledem k tomu, že experimentální funkce začnou být v zralosti, budou postupně přesunuty do hlavního oboru názvů.

## <a name="how-to-interpret-your-model"></a>Jak interpretovat model

Třídy a metody interpretace můžete použít k pochopení globálního chování modelu nebo konkrétního předpovědiu. Předchozí se nazývá globální vysvětlení a druhá se nazývá místní vysvětlení.

Metody mohou být také zařazeny do kategorií podle toho, zda je metoda model nezávislá nebo specifická pro model. Některé metody cílí na určitý typ modelů. Například vysvětlující strom SHAP se vztahuje pouze na modely založené na stromové struktuře. Některé metody nacházejí s modelem jako s černým polem, jako je například napodobení nebo vysvětlení SHAPi jádra. Tento `explain` balíček využívá různé přístupy založené na datových sadách, typech modelů a případech použití.

Výstupem je sada informací o tom, jak daný model provádí předpovědi, například:
* Důležitost globálních/místních relativních funkcí

* Globální/místní funkce a vztah předpovědi

### <a name="explainers"></a>Vysvětlení

K dispozici jsou dvě sady vysvětlení: V sadě SDK přímo vysvětlené a meta vysvětlené.

__Přímí vysvětlujícíi__ přicházejí z integrovaných knihoven. Sada SDK zabalí všechny vysvětlení, aby vystavoval společné rozhraní API a výstupní formát. Pokud je lépe pohodlnější použití těchto popisů, můžete je přímo vyvolat namísto použití společného rozhraní API a formátu výstupu. Níže najdete seznam přímých vysvětlujících, které jsou k dispozici v sadě SDK:

* **SHAP stromové struktury**: SHAP na stromové struktuře, který se zaměřuje na polynomická rychlá hodnota SHAP algoritmu odhadu, která je specifická pro stromy a komplety stromů.
* **SHAP hluboký vysvětlující**: Na základě vysvětlení z SHAP je hluboko vysvětlující algoritmus pro hodnoty SHAP v modelech hloubkového učení s vysokou rychlostí, který se vytváří na základě připojení s DeepLIFT popsané v dokumentu SHAP NIPS. TensorFlow modely a modely Keras používající back-end TensorFlow jsou podporované (k dispozici je také předběžná podpora pro PyTorch).
* **SHAP jádra pro jádro**: SHAP vysvětlení jádra používá speciálně váženou místní lineární regresi k odhadování hodnot SHAP pro libovolný model.
* Nepodobující se **vysvětlení**: Nepodobná vysvětlení jsou založená na nápadu z globálních náhradních modelů. Globální náhradní model je vnitřně interpretně interpretováný model, který je vyškolen na to, aby co nejpřesněji předpovědi model černého pole. Odborník na data může interpretovat náhradní model, aby vykreslil závěry o modelu černého pole. Jako svůj náhradní model můžete použít jeden z následujících typů, které lze interpretovat: LightGBM (LinearExplainableModel), lineární regrese (LinearExplainableModel), stochastického Gradientový model klesání na vysvětlené (SGDExplainableModel) a rozhodovací strom (DecisionTreeExplainableModel).


* **Vysvětlení důležitosti funkce permutace**: Funkce permutace je důležitou metodou pro vysvětlení modelů klasifikace a regrese, které jsou nechte inspirovat pomocí [náhodných strukturních struktur Breiman](https://www.stat.berkeley.edu/%7Ebreiman/randomforest2001.pdf) (viz oddíl 10). Na nejvyšší úrovni je způsob, jakým funguje, náhodným pohybem dat pro celou datovou sadu a výpočtem množství metriky výkonu, které se v zájmu sníží. Čím větší je tato změna, tím důležitější je funkce.

* **Vysvětlujícíka vápna** (`contrib`): V závislosti na VÁPNě vysvětlená VÁPNa používá pro vytvoření místních náhradních modelů špičkový algoritmus nezávislá (VÁPNo), který je založený na modelu. Na rozdíl od globálních náhradních modelů se VÁPNo zaměřuje na školení místních náhradních modelů pro vysvětlení jednotlivých předpovědi.
* **Vysvětlující text Han** (`contrib`): Vysvětlující text HAN používá hierarchickou síť k získání vysvětlení modelu z textových dat pro daný textový model černého pole. V rámci předpokládaných výstupů pro daný model učitelů se naučíme mít k disvede náhradní model HAN. Po globálním školení na corpus textu jsme přidali krok s doladěním pro konkrétní dokument, aby se zlepšila přesnost vysvětlení. HAN používá obousměrný RNN se dvěma úrovněmi pozornosti, pro větu a wordovou pozornost. Jakmile je DNN vyškolený pro model učitelů a vyladěný podle konkrétního dokumentu, můžeme z vrstev pozornosti extrahovat slova důležitost. Zjistili jsme, že HAN bude přesnější než VÁPNo nebo SHAP pro textová data, ale také dražší z hlediska času školení. Vylepšili jsme ale dobu školení tím, že uživateli umožníme inicializovat síť s vkládáním slov šetrnější, i když je pořád pomalu. Dobu školení můžete významně zlepšit spuštěním HAN na vzdáleném virtuálním počítači s grafickým procesorem Azure. Implementace HAN je popsána v části hierarchické pozornosti sítě pro klasifikaci dokumentu (Yang et al., 2016) ([https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf](https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf)).


__Meta__ selektory automaticky vyberou vhodný přímý vysvětlující a generují nejlepší informace, které se budou vysvětlovat na základě daného modelu a datových sad. Metaznačky využije všechny knihovny (SHAP, VÁPNo, napodobá atd.), které jsme spojili nebo vyvinuli. Následující jsou metaznačky, které jsou k dispozici v sadě SDK:

* **Tabulkový**Popis: Používá se s tabulkovými datovými sadami.
* **Vysvětlující text**: Používá se s textovou datovou sadou.
* **Vysvětlující obrázek**: Používá se u obrazových datových sad.

Kromě jasného výběru přímých popisů můžou metaznačky vysvětlovat další funkce nad základními knihovnami a zlepšit rychlost a škálovatelnost přes přímé vysvětlení.

V `TabularExplainer` současné době používá následující logiku k vyvolání přímých SHAPch vysvětlení:

1. Pokud se jedná o model založený na stromové struktuře, `TreeExplainer`použijte SHAP, jinak.
2. Pokud se jedná o DNN model, použijte SHAP `DeepExplainer`, jinak
3. Nahlížet jako na model černého pole a použít SHAP`KernelExplainer`

Zahrnuté inteligentní `TabularExplainer` informace se budou považovat za sofistikované, protože do sady SDK jsou integrovány další knihovny a dozvíme se o specialistech a nevýhody jednotlivých vysvětlujících.

`TabularExplainer`má také významná vylepšení funkcí a výkonu nad přímými sesvětlemi:

* **Sumarizace inicializační sady dat**. V případech, kdy je rychlost vysvětlení nejdůležitější, shrnujeme inicializační datovou sadu a vygenerujeme malou sadu reprezentativních vzorků, které urychlují globální i místní vysvětlení.
* **Vzorkování sady dat pro vyhodnocení**. Pokud uživatel projde velkou sadou zkušebních vzorků, ale nepotřebuje vyhodnotit všechny z nich, je možné nastavit parametr vzorkování na hodnotu true, aby se globální vysvětlení urychlilo.

Následující diagram znázorňuje aktuální strukturu přímých a meta vysvětlujících.

[![Machine Learning architektura pro interpretaci](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>Podporované modely

Všechny modely, které jsou výukou v datových sadách `numpy.array`v `pandas.DataFrame`Pythonu, `scipy.sparse.csr_matrix` , `explain` nebo ve formátu, `iml.datatypes.DenseData`jsou podporovány balíčkem interpretace sady SDK.

Funkce vysvětlení přijímají jako vstup oba modely i kanály. Pokud je k dispozici model, model musí implementovat funkci `predict` předpovědi nebo `predict_proba` , která odpovídá Scikit konvenci. Pokud je k dispozici kanál (název skriptu kanálu), funkce vysvětlení předpokládá, že spuštěný skript kanálu vrátí předpověď. Podporujeme modely vyškolené prostřednictvím PyTorch, TensorFlow a Keras architektury hloubkového učení.

### <a name="local-and-remote-compute-target"></a>Cíl pro místní a vzdálené výpočty

`explain` Balíček je navržený tak, aby fungoval s místními i vzdálenými výpočetními cíli. Pokud spustíte místně, funkce SDK nebudou kontaktovat žádné služby Azure. Vysvětlení můžete spustit vzdáleně na Azure Machine Learning COMPUTE a protokolovat informace o vysvětlení do Azure Machine Learning spuštění služby historie. Po zaznamenání těchto informací jsou sestavy a vizualizace z vysvětlení snadno dostupné na portálu pracovní prostor Azure Machine Learning pro analýzu uživatelů.

## <a name="interpretability-in-training"></a>Výklad při školení

### <a name="train-and-explain-locally"></a>Výuka a vysvětlete místně

1. Svůj model si Vyškolte v místním Jupyter poznámkovém bloku.

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

2. Zavolejte na vysvětlujícího: Chcete-li inicializovat objekt vysvětlujícího objektu, je nutné předat svůj model a určitá školicí data konstruktoru vysvětlujícího. Volitelně můžete také předávat názvy funkcí a názvy tříd výstupu (Pokud se provádí klasifikace), které budou sloužit k tomu, aby vaše vysvětlení a vizualizace byly více informativní. Tady je postup vytvoření instance vysvětlujícího objektu pomocí [TabularExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.tabularexplainer?view=azure-ml-py), [MimicExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic.mimicexplainer?view=azure-ml-py)a [PFIExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.permutation.permutation_importance.pfiexplainer?view=azure-ml-py) lokálně. `TabularExplainer`volá jedno ze tří SHAP vysvětlení pod (`TreeExplainer`, `DeepExplainer`nebo `KernelExplainer`) a automaticky vybere nejvhodnější z nich pro váš případ použití. Můžete ale zavolat každé ze svých tří základních vysvětlení přímo.

    ```python
    from azureml.explain.model.tabular_explainer import TabularExplainer
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    or

    ```python
    from azureml.explain.model.mimic.mimic_explainer import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel
    from azureml.explain.model.mimic.models.linear_model import LinearExplainableModel
    from azureml.explain.model.mimic.models.linear_model import SGDExplainableModel
    from azureml.explain.model.mimic.models.tree_model import DecisionTreeExplainableModel

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
   or

    ```python
    from azureml.explain.model.permutation.permutation_importance import PFIExplainer 
    
    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model, 
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

3. Získá hodnoty důležitosti pro globální funkce.

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

4. Získejte hodnoty důležitosti místních funkcí: pomocí následujících volání funkcí Vysvětlete jednotlivou instanci nebo skupinu instancí. Upozorňujeme, že PFIExplainer nepodporuje místní vysvětlení.

    ```python
    # explain the first data point in the test set
    local_explanation = explainer.explain_local(x_test[0])

    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

    or

    ```python
    # explain the first five data points in the test set
    local_explanation = explainer.explain_local(x_test[0:4])

    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

### <a name="train-and-explain-remotely"></a>Dálková analýza a vysvětlení

I když můžete vyškolit různé výpočetní cíle podporované službou Azure Machine Learning, v příkladu v této části se dozvíte, jak to udělat pomocí Azure Machine Learningho cíle služby Compute.

1. Vytvořte školicí skript v místním Jupyter poznámkovém bloku (například run_explainer. py).

    ```python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    from azureml.core.run import Run

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

2. Postupujte podle pokynů v tématu [nastavení cílových výpočetních prostředků pro školení modelů](how-to-set-up-training-targets.md#amlcompute) a Naučte se, jak nastavit výpočetní Azure Machine Learning jako výpočetní cíl a odeslat školicí běh.

3. Stáhněte si vysvětlení do svého místního poznámkového bloku Jupyter.

    ```python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    
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

Pro pochopení a interpretaci modelu použijte řídicí panel vizualizace:

### <a name="global-visualizations"></a>Globální vizualizace

Následující vykreslení poskytují globální pohled na trained model spolu s jeho předpovědi a vysvětleními.

|Graf|Popis|
|----|-----------|
|Zkoumání dat| Přehled datové sady spolu s hodnotami předpovědi.|
|Globální důležitost|Zobrazuje globálně K (konfigurovatelné K) důležitým funkcím globálně. Tento graf je užitečný pro porozumění globálnímu chování základního modelu.|
|Zkoumání vysvětlení|Ukazuje, jak je funkce zodpovědná za provedení změny v hodnotách předpovědi modelu (nebo pravděpodobnosti hodnot předpovědi). |
|Souhrn| Používá hodnoty důležitosti místních funkcí v rámci všech datových bodů k zobrazení distribuce vlivu každé funkce na hodnotu předpovědi.|

[![Globální řídicí panel vizualizace](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>Místní vizualizace

Můžete kdykoli kliknout na libovolný jednotlivý datový bod a načíst tak pro daný datový bod vykreslení důležitosti místní funkce.

|Graf|Popis|
|----|-----------|
|Místní důležitost|Zobrazuje globálně K (konfigurovatelné K) důležitým funkcím globálně. Tento graf je užitečný pro porozumění místnímu chování základního modelu v konkrétním datovém bodě.|
|Průzkum Perturbation|Umožňuje změnit hodnoty funkcí vybraného datového bodu a sledovat, jaký vliv mají tyto změny na hodnotu předpovědi.|
|Očekávání individuálního podmíněného (ICE)| Umožňuje změnit hodnotu funkce z minimální hodnoty na maximální hodnotu, abyste viděli, jak se předpověď datových bodů mění při změně funkce.|

[![Důležitost místní funkce řídicího panelu vizualizace](./media/machine-learning-interpretability-explainability/local-charts.png)](./media/machine-learning-interpretability-explainability/local-charts.png#lightbox)


[![Perturbation funkce řídicího panelu vizualizace](./media/machine-learning-interpretability-explainability/perturbation.gif)](./media/machine-learning-interpretability-explainability/perturbation.gif#lightbox)


[![Řídicí panel vizualizace ICE](./media/machine-learning-interpretability-explainability/ice-plot.png)](./media/machine-learning-interpretability-explainability/ice-plot.png#lightbox)

Všimněte si, že budete muset mít rozšíření widgetů řídicího panelu vizualizace povolené před spuštěním jádra Jupyter.

* Poznámkové bloky Jupyter

    ```shell
    jupyter nbextension install --py --sys-prefix azureml.contrib.explain.model.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.explain.model.visualize
    ```



* Jupyter Labs

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```
Řídicí panel vizualizace načtete pomocí následujícího kódu:

```python
from azureml.contrib.explain.model.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

## <a name="raw-feature-transformations"></a>Nezpracované transformace funkcí

Volitelně můžete předat kanál transformace vaší funkce vysvětlujícímu, aby bylo možné získat vysvětlení z pohledu nezpracovaných funkcí před transformací (spíše než inženýrské funkce). Pokud přeskočíte tuto funkci, vysvětluje vysvětlující funkce v souvislosti s funkcemi pro inženýry.

Formát podporovaných transformací je stejný jako ten, který je popsaný v [skriptu sklearn-PANDAS](https://github.com/scikit-learn-contrib/sklearn-pandas). Obecně platí, že všechny transformace jsou podporovány, pokud pracují v jednom sloupci a jsou proto jasně jedna až mnoho. 

Nezpracované funkce můžeme vysvětlovat buď pomocí `sklearn.compose.ColumnTransformer` , nebo pomocí seznamu namontovaných řazených kolekcí členů Transformer. Následující buňka používá `sklearn.compose.ColumnTransformer`. 

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

## <a name="interpretability-at-inferencing-time"></a>Výklad v Inferencing době

Tento vysvětlující se dá nasadit společně s původním modelem a dá se použít v době bodování k poskytnutí místních informací o vysvětlení. Nabízíme také jednodušší vyhodnocování bodování, aby bylo možné provádět interpretaci při inferencingém čase. Proces nasazování vysvětlujícího bodování je podobný jako nasazení modelu a obsahuje následující kroky:




1. Vytvoření objektu vysvětlení (například pomocí TabularExplainer):

   ```python
   from azureml.contrib.explain.model.tabular_explainer import TabularExplainer

   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Vytvoření vysvětlujícího bodování pomocí objektu vysvětlení:

   ```python
   from azureml.contrib.explain.model.scoring.scoring_explainer import KernelScoringExplainer, save

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
   from azureml.contrib.explain.model.scoring.scoring_explainer import load

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

   1. Vytvořte soubor hodnocení (před tímto krokem použijte postup v části [nasazení modelů pomocí služby Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where) k registraci původního modelu předpovědi).

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

        azureml_pip_packages = ['azureml-defaults', 'azureml-contrib-explain-model', 'azureml-core', 'azureml-telemetry', 'azureml-explain-model']
 

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

   1. Nasadit vytvořenou bitovou kopii (časový odhad: 5 minut)

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

Chcete-li zobrazit kolekci poznámkových bloků Jupyter, které ukazují výše uvedené pokyny, přečtěte si [ukázkové poznámkové bloky s ukázkou interpretace Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).
