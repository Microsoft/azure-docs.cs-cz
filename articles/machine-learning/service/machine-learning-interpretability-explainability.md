---
title: Interpretovatelnost modelů
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak vysvětlit, proč váš model vytváří předpovědi pomocí sady SDK služby Azure Machine Learning Interpretability. Můžete použít při trénování a odvozování pochopit, jak váš model vytváří předpovědi.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: larryfr
ms.date: 04/09/2019
ms.openlocfilehash: 668551603dfa2a9c42f4538fd9a66ee646e1feb7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60817241"
---
# <a name="model-interpretability-with-azure-machine-learning-service"></a>Model interpretability službou Azure Machine Learning

V tomto článku se dozvíte postupy vysvětlují, proč váš model provedené předpovědi ho pomocí sady Azure Machine Learning Interpretability SDK. Schopnost popisují váš model je důležitá z následujících důvodů:

* Zákazníci a zúčastněnými stranami chtít vědět, **v případě, že důvěřujete předpovědi modelu díky**.
* Jako odborník přes data, potřebujete pochopit **jak provádět dotazy modelu, který má získat přehledy**. Musíte také nástroje a informovaně se rozhodovat na **jak vylepšit svůj model**.
* Jako společnost, je třeba porozumět **chování modelu s použitím různých vstup distribuce** a **bude model chování při analýze specifický vstup**.

Je důležité ve dvou fázích strojového učení vývojový cyklus Machine learning interpretability: 

* Během **školení**: Návrháři model a nástroje pro vyhodnocení vyžadují interpretability nástroje, které popisují výstup z modelu zúčastněným stranám, k vytvoření vztahu důvěryhodnosti. Potřebují také přehled o modelu tak, aby můžete ladit modelu a rozhodování na, jestli odpovídá chování svých cílů. Nakonec je třeba Ujistěte se, že není tendenční modelu.

* Během **odvozování**: Předpovědi musí být explainable uživatelé, kteří používají model. Například, proč modelu odepřít úvěru hypoteční nebo předpovídat, že investice portfolia s sebou nese vyšší riziko?

Azure Machine Learning Interpretability SDK zahrnuje technologie vyvinutá společností Microsoft a ověřené knihovny třetích stran (například okno a LIMETKOVĚ). Sada SDK vytvoří společné rozhraní API přes integrované knihovny a integraci služby Azure Machine Learning. Pomocí této sady SDK, mohou vysvětlovat modelů strojového učení **globálně na všechny data**, nebo **místně na konkrétní datový bod** pomocí technologií stavu techniky snadným ovládáním a škálovatelným způsobem.

## <a name="how-does-it-work"></a>Jak to funguje?

Azure Machine Learning Interpretability lze použít k pochopení chování globální nebo konkrétní předpovědi modelu. Předchozí se nazývá globální vysvětlení a ten je místní vysvětlení.

Azure Machine Learning Interpretability metody může být také zařazených do kategorií podle toho, jestli metoda bez ohledu na model nebo model konkrétní. Některé metody cílit na určité typy modelů. Například pro okno stromu vysvětlení platí jenom pro modely založený na stromové architektuře. Některé metody považovat černé pole, jako je například mimic vysvětlení nebo vysvětlení jádra na okno modelu. Azure Machine Learning Interpretability SDK využívá tyto různé přístupy, na základě datové sady, model typy a případy použití.

Azure Machine Learning Interpretability vrátí sadu informace jak modelu díky jeho předpovědi. Informace zahrnují položky jako například:

* Globální/místní funkce relativní důležitost
* Globální/místní funkce a predikcí vztah

## <a name="architecture"></a>Architektura

Azure Machine Learning Interpretability SDK je rozdělen do dvou balíčcích Pythonu:

* [azureml.EXPLAIN.model](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py) -hlavního balíčku, který obsahuje funkce, které jsou podporovány společností Microsoft.
* `azureml.contrib.explain.model` – Ve verzi preview a experimentální funkce, které můžete vyzkoušet.

    > [!IMPORTANT]
    > Věci v contrib nejsou plně podporované. Jakmile budou až po zralé experimentální funkce, jsou postupně se přesune do hlavního balíčku.

### <a name="explainers"></a>Explainers

Azure Machine Learning Interpretability SDK zavádí dvě sady explainers: Přímé Explainers a Meta Explainers.

__Přímé explainers__ pocházejí z integrované knihovny. Sada SDK zabalí všechny explainers tak, aby zveřejňovaly společné rozhraní API a formát výstupu. Následuje seznam s přímým přístupem explainers k dispozici v sadě SDK:

> [!TIP]
> Pokud jsou vám bližší přímo pomocí těchto explainers, můžete je vyvolat přímo namísto použití společné rozhraní API a formát výstupu.

* **Strom vysvětlení**: Vysvětlení stromu na okno, které se zaměřuje na polynomické rychlé okno hodnotu odhad algoritmus time specifické pro stromy a umožňující stromů.
* **Podrobné vysvětlení**: Podle vysvětlení z okno, podrobné vysvětlení "je vysokorychlostní aproximační algoritmus pro okno hodnoty v modely obsáhlého learningu, která staví na připojení s DeepLIFT popsané v dokumentu NIPS okno. Jsou podporovány TensorFlow modely a Keras modelů pomocí back-endu TensorFlow (je taky předběžnou podporu pro PyTorch) ".
* **Vysvětlení jádra**: Vysvětlení jádra na okno používá k odhadu okno hodnoty pro všechny modely speciálně vážený místní lineární regrese.
* **Napodobení vysvětlení**: Vysvětlení mimic je založená na nápad globální náhradní modely. Globální náhradní model je vnitřně interpretovatelném model, který se trénuje aproximace předpovědi modelu černé skříňky co nejpřesněji. Mezi odborníky přes data interpretovat model náhradní závěrům o černé pole modelu.
* **Vysvětlení LIMETKOVĚ**: Na základě LIMETKOVĚ, LIMETKOVĚ vysvětlení pomocí algoritmu stavu nejmodernější místní interpretovatelném modelu bez ohledu na vysvětlení (LIMETKOVĚ) vytvářet modely místní náhradní. Na rozdíl od modelů globální náhradní nastavení LIMETKOVĚ se zaměřuje na školení místní náhradní modely, které popisují jednotlivé předpovědi.
* **Text vysvětlení HAN**: HANU Text vysvětlení používá síť hierarchické pozornost pro získávání vysvětlení modelu z textových dat pro danou černé skříňky textový model. Model náhradní HAN na předpokládané výstupy učitelů daný model trénujeme. Po školení globálně napříč text souhrnu, jsme přidali fine-tune krok pro určitého dokumentu za účelem zlepšení přesnosti vysvětlení. HANU používá obousměrný RNN s dvě vrstvy pozornost věty a word pozornost. Jakmile DNN natrénovali model učitele a doladíte na konkrétním dokumentu, jsme importances slovo extrahovat z vrstvy pozornost. Našli jsme HAN jako přesnější než LIMETKOVĚ nebo okno textových dat, ale dražší z hlediska přípravy a čas. Ale provedli jsme vylepšení na školení čas tím, že uživatel možnost inicializace síť s rukavice vkládání slov, i když je stále pomalý. Čas školení může značně zlepšit spuštěním HAN na vzdáleném virtuálním počítači Azure GPU. Implementace HAN je popsána v "hierarchické pozornost sítě pro klasifikace dokumentů (Yang et al., 2016). ([https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf](https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf)).

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

Modely, které jsou trénované na datové sady v Pythonu `numpy.array`, `pandas.DataFrame`, `iml.datatypes.DenseData`, nebo `scipy.sparse.csr_matrix` formátu jsou podporovány Machine Learning Interpretability SDK.

Vysvětlení funkce přijímají jako vstup modely a kanály. Pokud model je k dispozici, musí implementovat model funkci předpovědi `predict` nebo `predict_proba` , který odpovídá Scikit konvence. Pokud kanál (název souboru, který kanálu) vysvětlení funkce předpokládá, že spouštění skriptu kanálu vrací předpověď na.

### <a name="local-and-remote-compute-target"></a>Místní a vzdálené cílové výpočetní prostředí

Machine Learning Interpretability SDK je navržená pro práci s oběma cílových výpočetních prostředí místní a vzdálené. Je-li spustit místně, sada SDK funkce nebude obraťte se na služby Azure. Můžete spustit vysvětlení vzdáleně v Azure Machine Learning Compute a informace o vysvětlení přihlásit spuštění služby historie Azure Machine Learning. Jakmile tyto informace se zaznamená, sestavy a vizualizace z vysvětlení se snadno k dispozici na portálu Azure Machine Learning Workspace analýza uživatelů.

## <a name="train-and-explain-locally"></a>Trénování a popisují místně

1. Vyzkoušejte svůj model v místní aplikace Jupyter notebook. 

    ``` python
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

2. Volání vysvětlení: K zahájení vysvětlení objektu, je nutné předat modelu, trénovací data, funkce zájmu (volitelné) a názvy tříd výstup (Pokud klasifikaci) k vysvětlení. Tady je postup pro vytvoření instance objektu vysvětlení pomocí [TabularExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.tabularexplainer?view=azure-ml-py), [MimicExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic.mimicexplainer?view=azure-ml-py), a `LimeExplainer` místně. `TabularExplainer` volání jedné z tři explainers pod (`TreeExplainer`, `DeepExplainer`, nebo `KernelExplainer`) a je automaticky vybere nejvhodnější z nich pro případy použití. Můžete však všechny její tři základní explainers volat přímo.

    ```python
    from azureml.explain.model.tabular_explainer import TabularExplainer
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    ```
    nebo
    ```python
    from azureml.explain.model.mimic.mimic_explainer import MimicExplainer
    from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel
    explainer = MimicExplainer(model, x_train, LGBMExplainableModel, features=breast_cancer_data.feature_names, classes=classes)
    ```

3. Získá globální funkce význam hodnoty.

    ```python
    # You can use the training data or the test data here. 
    global_explanation = explainer.explain_global(x_train)
    # Sorted feature importance values and feature names
    sorted_global_importance_values = global_explanation.get_ranked_global_values()
    sorted_global_importance_names = global_explanation.get_ranked_global_names()
    dict(zip(sorted_global_importance_names, sorted_global_importance_values))
    ```

4. Místní funkce význam hodnoty: popisují jednotlivé instance nebo na skupinu instancí systému pomocí následující volání funkce.

    ```python
    # explain the first data point in the test set
    local_explanation = explainer.explain_local(x_test[0,:])
    
    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```
    nebo
    ```python
    # explain the first five data points in the test set
    local_explanation = explainer.explain_local(x_test[0:4,:])
    
    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

## <a name="train-and-explain-remotely"></a>Trénování a popisují vzdáleně

Zatímco trénovat na různých cílových výpočetních prostředí podporované službou Azure Machine Learning, příklad v této části ukazuje, jak to udělat pomocí AMLCompute.

1. Vytvoření trénovací skript v místní aplikace Jupyter notebook (například run_explainer.py).

    ``` python  
    run = Run.get_context()
    client = ExplanationClient.from_run(run)
    
    # Train your model here

    # explain predictions on your local machine    
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    # explain local data points (individual instances)
    local_explanation = explainer.explain_local(x_test[0,:])
    # upload global and local explanation objects to Run History
    upload_model_explanation(run, local_explanation, top_k=2, comment='local explanation: top 2 features')
    # Uploading global model explanation data for storage or visualization in webUX
    # The explanation can then be downloaded on any compute
    # Multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # Or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

2. Postupujte podle pokynů [nastavení cílových výpočetních prostředí k tréninku modelu](how-to-set-up-training-targets.md#amlcompute) Další informace o nastavení Azure Machine Learning Compute jako vaše cílové výpočetní prostředí a odeslat spuštění školení.

3. Stáhněte si vysvětlení ve vaší místní aplikace Jupyter notebook. 
    > [!IMPORTANT]
    > Věci v contrib nejsou plně podporované. Jakmile budou až po zralé experimentální funkce, jsou postupně se přesune do hlavního balíčku.

    ``` python
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

## <a name="next-steps"></a>Další kroky

Kolekce poznámkové bloky Jupyter, které ukazují výše uvedené pokyny najdete v tématu [Azure Machine Learning Interpretability ukázkové poznámkové bloky](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).
