---
title: Experimenty protokolu ML & metriky
titleSuffix: Azure Machine Learning
description: Sledujte experimenty Azure ML a sledujte metriky spuštění, abyste vylepšili proces vytváření modelů. Přidání protokolování do školicího skriptu a zobrazení protokolovaných výsledků spuštění.  Použijte run. log, spusťte. start_logging nebo ScriptRunConfig.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/05/2019
ms.custom: seodec18
ms.openlocfilehash: e6b2f73540a0af7ed9c12469406a77d1bed8a2b4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270001"
---
# <a name="monitor-azure-ml-experiment-runs-and-metrics"></a>Monitorování běhů a metriky Azure ML
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Vylepšete proces vytváření modelů sledováním experimentů a monitorováním metrik spuštění. V tomto článku se dozvíte, jak přidat kód protokolování do školicího skriptu, jak odeslat experiment, monitorovat tento běh a zkontrolovat výsledky v Azure Machine Learning.

> [!NOTE]
> Azure Machine Learning může také protokolovat informace z jiných zdrojů během školení, jako je automatické spuštění strojového učení nebo kontejner Docker, který spouští školicí úlohu. Tyto protokoly nejsou dokumentovány. Pokud narazíte na problémy a kontaktujte podporu Microsoftu, můžou při řešení potíží používat tyto protokoly.

> [!TIP]
> Informace v tomto dokumentu jsou primárně určené pro odborníky přes data a vývojáře, kteří chtějí monitorovat proces školení modelu. Pokud jste správcem a chcete monitorovat využití prostředků a události z Azure Machine Learningu, jako jsou kvóty, dokončené školicí běhy nebo dokončená nasazení modelu, přečtěte si téma [monitorování Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="available-metrics-to-track"></a>Dostupné metriky ke sledování

Pro spuštění při školení experiment lze přidat následující metriky. Podrobné informace o tom, co lze sledovat při spuštění, naleznete v [dokumentaci třídy Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

|Typ| Funkce jazyka Python | Poznámky|
|----|:----|:----|
|Skalární hodnoty |Funkce:<br>`run.log(name, value, description='')`<br><br>Příklad:<br>Run.log ("přesnost", 0,95) |Protokol a číselné nebo řetězcová hodnota pro spouštění s daným názvem. Protokolování metriky o spuštění způsobí, že tuto metriku, který bude uložen do běhu záznam v experimentu.  Stejné metriky můžete přihlásit více než jednou v rámci spuštěný proces, výsledek se považuje za vektor tuto metriku.|
|Seznamy|Funkce:<br>`run.log_list(name, value, description='')`<br><br>Příklad:<br>Run.log_list ("přesností" [0.6, 0,7, 0.87]) | Přihlaste se seznam hodnot pro spouštění s daným názvem.|
|Řádek|Funkce:<br>`run.log_row(name, description=None, **kwargs)`<br>Příklad:<br>Run.log_row ("Y nad X", x = 1, y = 0.4) | Pomocí *log_row* se vytvoří metrika s více sloupci, jak je popsáno v tématu kwargs. Každý pojmenovaný parametr generuje sloupec s hodnotou.  *log_row* může být jednou volána k zaznamenání libovolné řazené kolekce členů nebo vícekrát ve smyčce, aby vygenerovala úplnou tabulku.|
|Tabulka|Funkce:<br>`run.log_table(name, value, description='')`<br><br>Příklad:<br>Run.log_table ("Y nad X", {"x": [1, 2, 3], "y": [0.6, 0,7, 0.89]}) | Přihlaste se na objekt slovníku běh s daným názvem. |
|Image|Funkce:<br>`run.log_image(name, path=None, plot=None)`<br><br>Příklad:<br>`run.log_image("ROC", plot=plt)` | Přihlaste se image spusťte záznam. Použití k protokolování soubor obrázku nebo matplotlib log_image vykreslení spustit.  Tyto Image budou viditelné a srovnatelné v běhu záznamu.|
|Označení spuštění|Funkce:<br>`run.tag(key, value=None)`<br><br>Příklad:<br>Run.tag ("vybrat", "Ano") | Označte běh s klíčem řetězce a volitelný řetězec.|
|Nahrát soubor nebo adresář|Funkce:<br>`run.upload_file(name, path_or_stream)`<br> <br> Příklad:<br>Run.upload_file ("best_model.pkl", ". / model.pkl") | Nahrání souboru do běhu záznamu. Spuštění automaticky zachytávací soubor v zadané výstupní adresář, kde je použit výchozí ". / výstupy" pro většinu spuštění typy.  Není zadána upload_file použijte jenom v případě, že budete muset nahrát další soubory nebo výstupní adresář. Doporučujeme přidat `outputs` k názvu, aby se nahrál do adresáře výstupy. Můžete zobrazit seznam všech souborů, které jsou přidruženy k tomuto záznamu spuštění pomocí metody `run.get_file_names()`|

> [!NOTE]
> Metriky pro skaláry, seznamy, řádků a tabulky může mít typ: float, celé číslo nebo řetězec.

## <a name="choose-a-logging-option"></a>Zvolit možnost protokolování

Pokud chcete sledovat nebo sledovat experimentu, musíte přidat kód pro spuštění protokolování při odesílání příkazu run. Následují způsoby, jak aktivovat spuštění odeslání:
* __Spusťte. start_logging__ – do školicího skriptu přidejte funkce protokolování a v zadaném experimentu spusťte interaktivní relaci protokolování. **start_logging** vytvoří interaktivní běh pro použití ve scénářích, jako jsou třeba poznámkové bloky. Všechny metriky, které jsou zaznamenány během relace jsou přidány do běhu záznam v experimentu.
* __ScriptRunConfig__ – přidejte funkce protokolování do školicího skriptu a načtěte celou složku skriptu pomocí běhu.  **ScriptRunConfig** je třída pro nastavení konfigurací pro spuštění skriptu. Pomocí této možnosti přidáte kód monitorování, abyste dostávali oznámení o dokončení nebo chcete-li získat vizuální pomůcky pro monitorování.

## <a name="set-up-the-workspace"></a>Nastavit pracovní prostor
Před přidáním protokolování a odeslání experimentu, musíte nastavit pracovní prostor.

1. Načtení pracovního prostoru. Další informace o nastavení konfigurace pracovního prostoru najdete v tématu [konfigurační soubor pracovního prostoru](how-to-configure-environment.md#workspace).

   ```python
   from azureml.core import Experiment, Run, Workspace
   import azureml.core
  
   ws = Workspace.from_config()
   ```
  
## <a name="option-1-use-start_logging"></a>Možnost 1: Použití start_logging

**start_logging** vytvoří interaktivní běh pro použití ve scénářích, jako jsou třeba poznámkové bloky. Všechny metriky, které jsou zaznamenány během relace jsou přidány do běhu záznam v experimentu.

Následující příklad trénovat jednoduchý model skriptu sklearn Ridge místně v místní aplikace Jupyter notebook. Další informace o odesílání experimentů do různých prostředí najdete v tématu [Nastavení výpočetních cílů pro školení modelů s Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-set-up-training-targets).

1. Vytvoření trénovací skript v místní aplikace Jupyter notebook. 

   ```python
   # load diabetes dataset, a well-known small dataset that comes with scikit-learn
   from sklearn.datasets import load_diabetes
   from sklearn.linear_model import Ridge
   from sklearn.metrics import mean_squared_error
   from sklearn.model_selection import train_test_split
   from sklearn.externals import joblib

   X, y = load_diabetes(return_X_y = True)
   columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
   data = {
      "train":{"X": X_train, "y": y_train},        
      "test":{"X": X_test, "y": y_test}
   }
   reg = Ridge(alpha = 0.03)
   reg.fit(data['train']['X'], data['train']['y'])
   preds = reg.predict(data['test']['X'])
   print('Mean Squared Error is', mean_squared_error(preds, data['test']['y']))
   joblib.dump(value = reg, filename = 'model.pkl');
   ```

2. Přidejte sledování experimentů pomocí Azure Machine Learning SDK a nahrajte trvalý model do záznamu experimentálního spuštění. Následující kód přidá značky, protokoly a nahraje soubor modelu pro běh experimentu.

   ```python
    # Get an experiment object from Azure Machine Learning
    experiment = Experiment(workspace=ws, name="train-within-notebook")
    
    # Create a run object in the experiment
    run =  experiment.start_logging()
    # Log the algorithm parameter alpha to the run
    run.log('alpha', 0.03)
    
    # Create, fit, and test the scikit-learn Ridge regression model
    regression_model = Ridge(alpha=0.03)
    regression_model.fit(data['train']['X'], data['train']['y'])
    preds = regression_model.predict(data['test']['X'])
    
    # Output the Mean Squared Error to the notebook and to the run
    print('Mean Squared Error is', mean_squared_error(data['test']['y'], preds))
    run.log('mse', mean_squared_error(data['test']['y'], preds))
    
    # Save the model to the outputs directory for capture
    model_file_name = 'outputs/model.pkl'
    
    joblib.dump(value = regression_model, filename = model_file_name)
    
    # upload the model file explicitly into artifacts 
    run.upload_file(name = model_file_name, path_or_stream = model_file_name)
    
    # Complete the run
    run.complete()
   ```

    Skript končí na ```run.complete()```, který označí běh jako dokončený.  Tato funkce se obvykle používá ve scénářích interaktivní poznámkového bloku.

## <a name="option-2-use-scriptrunconfig"></a>Možnost 2: Použití ScriptRunConfig

[**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) je třída pro nastavení konfigurací pro spuštění skriptu. Pomocí této možnosti přidáte kód monitorování, abyste dostávali oznámení o dokončení nebo chcete-li získat vizuální pomůcky pro monitorování.

Tento příklad rozšiřuje základní model skriptu sklearn Ridge výše. Provádí jednoduché parametr parametrů k vyčištění přes alfa hodnoty modelu, který má zachytávat metriky a trénované modely ve spuštění v rámci testu. V příkladu spustí místně prostředí spravované uživatele. 

1. Vytvořte školicí skript `train.py`.

   ```python
   # train.py

   import os
   from sklearn.datasets import load_diabetes
   from sklearn.linear_model import Ridge
   from sklearn.metrics import mean_squared_error
   from sklearn.model_selection import train_test_split
   from azureml.core.run import Run
   from sklearn.externals import joblib

   import numpy as np

   #os.makedirs('./outputs', exist_ok = True)

   X, y = load_diabetes(return_X_y = True)

   run = Run.get_context()

   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
   data = {"train": {"X": X_train, "y": y_train},
          "test": {"X": X_test, "y": y_test}}

   # list of numbers from 0.0 to 1.0 with a 0.05 interval
   alphas = mylib.get_alphas()

   for alpha in alphas:
      # Use Ridge algorithm to create a regression model
      reg = Ridge(alpha = alpha)
      reg.fit(data["train"]["X"], data["train"]["y"])

      preds = reg.predict(data["test"]["X"])
      mse = mean_squared_error(preds, data["test"]["y"])
      # log the alpha and mse values
      run.log('alpha', alpha)
      run.log('mse', mse)

      model_file_name = 'ridge_{0:.2f}.pkl'.format(alpha)
      # save model in the outputs folder so it automatically get uploaded
      with open(model_file_name, "wb") as file:
          joblib.dump(value = reg, filename = model_file_name)

      # upload the model file explicitly into artifacts 
      run.upload_file(name = model_file_name, path_or_stream = model_file_name)

      # register the model
      #run.register_model(file_name = model_file_name)

      print('alpha is {0:.2f}, and mse is {1:0.2f}'.format(alpha, mse))
  
   ```

2. Skript `train.py` odkazuje na `mylib.py`, které vám umožní získat seznam hodnot Alpha pro použití v modelu Ridge.

   ```python
   # mylib.py
  
   import numpy as np

   def get_alphas():
      # list of numbers from 0.0 to 1.0 with a 0.05 interval
      return np.arange(0.0, 1.0, 0.05)
   ```

3. Nakonfigurujte uživatele spravovat místní prostředí.

   ```python
   from azureml.core.environment import Environment
    
   # Editing a run configuration property on-fly.
   user_managed_env = Environment("user-managed-env")
    
   user_managed_env.python.user_managed_dependencies = True
    
   # You can choose a specific Python environment by pointing to a Python path 
   #user_managed_env.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
   ```

4. Odešlete skript ```train.py```, který se spustí v prostředí spravovaném uživatelem. Tato složka pro všechny skripty je odeslána pro školení, včetně souboru ```mylib.py```.

   ```python
   from azureml.core import ScriptRunConfig
    
   exp = Experiment(workspace=ws, name="train-on-local")
   src = ScriptRunConfig(source_directory='./', script='train.py')
   src.run_config.environment = user_managed_env
   run = exp.submit(src)
   ```

## <a name="manage-a-run"></a>Správa běhu

Články týkající se [spuštění, sledování a zrušení školicích programů](how-to-manage-runs.md) zvýrazňují konkrétní Azure Machine Learning pracovní postupy pro správu experimentů.

## <a name="view-run-details"></a>Zobrazení podrobností o spuštění

### <a name="view-activequeued-runs-from-the-browser"></a>Zobrazit aktivní/zařazené běhy z prohlížeče

Výpočetní cíle použité pro výukové modely jsou sdíleným prostředkem. V takovém případě mohou mít několik spuštění ve frontě nebo v daném okamžiku aktivní. Pokud chcete zobrazit spuštění pro konkrétní výpočetní cíl z prohlížeče, použijte následující postup:

1. V [Azure Machine Learning Studiu](https://ml.azure.com/)vyberte svůj pracovní prostor a pak na levé straně stránky vyberte __COMPUTE__ .

1. Vyberte __školicí clustery__ , abyste zobrazili seznam cílových výpočetních prostředků, které se používají pro školení. Pak vyberte cluster.

    ![Vyberte školicí cluster.](./media/how-to-track-experiments/select-training-compute.png)

1. Vyberte __Spustit__. Zobrazí se seznam spuštění, která používají tento cluster. Chcete-li zobrazit podrobnosti o konkrétním spuštění, použijte odkaz ve sloupci __Spustit__ . Chcete-li zobrazit podrobnosti experimentu, použijte odkaz ve sloupci __experiment__ .

    ![Vybrat běhy pro cluster školení](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > Běh může obsahovat podřízené spuštění, takže jedna školicí úloha může mít za následek několik položek.

Po dokončení běhu se již na této stránce nebude zobrazovat. Chcete-li zobrazit informace o dokončených spuštěních, přejděte k části __experimenty__ v nástroji Studio a vyberte experiment a spusťte. Další informace najdete v části [metriky spuštění dotazu](#queryrunmetrics) .

### <a name="monitor-run-with-jupyter-notebook-widget"></a>Monitorovat běh pomocí widgetu pro Poznámkový blok Jupyter
Když použijete metodu **ScriptRunConfig** k odeslání spuštění, můžete sledovat průběh běhu s [pomůckou Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py). Podobně jako odeslání spuštění je tento widget asynchronní a poskytuje průběžné aktualizace každých 10 až 15 sekund, dokud se úloha nedokončí.

1. Při čekání na dokončení běhu zobrazte widgetu Jupyter.

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Snímek obrazovky aplikace Jupyter notebook widgetu](./media/how-to-track-experiments/run-details-widget.png)

   Můžete také získat odkaz na stejný displej v pracovním prostoru.

   ```python
   print(run.get_portal_url())
   ```

2. **[Pro automatizované běhy strojového učení]** Pro přístup k grafům z předchozího běhu. Nahraďte `<<experiment_name>>` vhodným názvem experimentu:

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![Widget poznámkového bloku Jupyter pro automatizované Machine Learning](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


Pokud chcete zobrazit další podrobnosti o kanálu, klikněte na kanál, který chcete prozkoumat v tabulce, a grafy se vykreslí v automaticky otevíraném okně Azure Machine Learning Studiu.

### <a name="get-log-results-upon-completion"></a>Získání protokolu výsledků při dokončení

Model školení a monitorování probíhá na pozadí tak, aby můžete spouštět další úlohy, zatímco čekáte. Můžete také počkat, dokud modelu byla dokončena školení před spuštěním další kód. Když použijete **ScriptRunConfig**, můžete použít ```run.wait_for_completion(show_output = True)``` k zobrazení po dokončení školení modelu. Příznak ```show_output``` poskytuje podrobný výstup. 

<a id="queryrunmetrics"></a>

### <a name="query-run-metrics"></a>Dotaz spustit metriky

Metriky proučeného modelu můžete zobrazit pomocí ```run.get_metrics()```. Teď můžete získat všechny metriky, které byly zaznamenány v příkladu výše k určení nejvhodnějšího modelu.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-your-workspace-in-azure-machine-learning-studio"></a>Zobrazení experimentu v pracovním prostoru v [Azure Machine Learning Studiu](https://ml.azure.com)

Po dokončení spuštění experimentu můžete přejít na zaznamenané experiment spustit záznam. K historii můžete přistupovat z [Azure Machine Learning studia](https://ml.azure.com).

Přejděte na kartu experimenty a vyberte svůj experiment. Přejdete do řídicího panelu experiment spustit, kde vidíte sledované metriky a grafy, které jsou protokolovány pro každé spuštění. V tomto případě jsme do protokolu zapíše MSE a hodnoty alfa.

  ![Podrobnosti o spuštění v Azure Machine Learning Studiu](./media/how-to-track-experiments/experiment-dashboard.png)

Můžete přejít k podrobnostem konkrétního spuštění a zobrazit jeho výstupy nebo protokoly nebo stáhnout snímek experimentu, který jste odeslali, abyste mohli sdílet složku experimentů s ostatními.

### <a name="viewing-charts-in-run-details"></a>Zobrazení grafů v podrobnosti o spuštění

Existují různé způsoby, jak použít rozhraní API protokolování k nahrávání různých typů metrik během běhu a jejich zobrazení jako grafů v Azure Machine Learning Studiu.

|Zaznamenané hodnoty|Příklad kódu| Zobrazit v portálu|
|----|----|----|
|Protokol pole číselných hodnot| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|Single-variable spojnicový graf|
|Přihlásit se stejným názvem metriky opakovaně použít jednu numerickou hodnotu (jako v rámci smyčky for)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Single-variable spojnicový graf|
|Opakovaným odesláním řádek 2 číselné sloupce|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Dvě proměnné spojnicový graf|
|Tabulku protokolu s 2 číselné sloupce|`run.log_table(name='Sine Wave', value=sines)`|Dvě proměnné spojnicový graf|


## <a name="example-notebooks"></a>Příklad poznámkové bloky
Tyto poznámkové bloky předvedení konceptů v tomto článku:
* [Postupy: použití-AzureML/školení/výuka v rámci poznámkového bloku](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [Postupy: použití-AzureML/školení/výuka v místním prostředí](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [Postupy: použití-AzureML/Track-and-monitor-experimenty/protokolování – rozhraní API](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další kroky

Vyzkoušejte tyto další kroky pro další informace o použití sady SDK Azure Machine Learning pro Python:

* Podívejte se na příklad, jak registrovat nejlepší model a nasadit ho v kurzu, [Naučte si model klasifikace imagí pomocí Azure Machine Learning](tutorial-train-models-with-aml.md).

* Naučte se, jak [pomocí Azure Machine Learning naučit modely PyTorch](how-to-train-pytorch.md).
