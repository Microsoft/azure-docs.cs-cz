---
title: Experimenty protokolu ML & metriky
titleSuffix: Azure Machine Learning service
description: Sledujte experimenty Azure ML a sledujte metriky spuštění, abyste vylepšili proces vytváření modelů. Přidání protokolování do školicího skriptu a zobrazení protokolovaných výsledků spuštění.  Použijte run. log, spusťte. start_logging nebo ScriptRunConfig.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/11/2019
ms.custom: seodec18
ms.openlocfilehash: 1c2747c8eaac0323ac32f67afb27ac854a3a5ce9
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2019
ms.locfileid: "70959947"
---
# <a name="monitor-azure-ml-experiment-runs-and-metrics"></a>Monitorování běhů a metriky Azure ML

Vylepšete proces vytváření modelů sledováním experimentů a monitorováním metrik spuštění. V tomto článku se dozvíte, jak přidat kód protokolování do školicího skriptu, odeslat experiment, monitorovat a zkontrolovat výsledky ve službě Azure Machine Learning.

> [!NOTE]
> Služba Azure Machine Learning může také protokolovat informace z jiných zdrojů během školení, jako je automatické spuštění strojového učení nebo kontejner Docker, který spouští školicí úlohu. Tyto protokoly nejsou dokumentovány. Pokud narazíte na problémy a kontaktujte podporu Microsoftu, můžou při řešení potíží používat tyto protokoly.

## <a name="available-metrics-to-track"></a>Dostupné metriky ke sledování

Pro spuštění při školení experiment lze přidat následující metriky. Chcete-li zobrazit podrobnější seznam co lze sledovat na běh, najdete v článku [spuštění třídy referenční dokumentaci](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

|Typ| Funkce jazyka Python | Poznámky|
|----|:----|:----|
|Skalární hodnoty |Funkce:<br>`run.log(name, value, description='')`<br><br>Příklad:<br>Run.log ("přesnost", 0,95) |Protokol a číselné nebo řetězcová hodnota pro spouštění s daným názvem. Protokolování metriky o spuštění způsobí, že tuto metriku, který bude uložen do běhu záznam v experimentu.  Stejné metriky můžete přihlásit více než jednou v rámci spuštěný proces, výsledek se považuje za vektor tuto metriku.|
|Seznamy|Funkce:<br>`run.log_list(name, value, description='')`<br><br>Příklad:<br>Run.log_list ("přesností" [0.6, 0,7, 0.87]) | Přihlaste se seznam hodnot pro spouštění s daným názvem.|
|Řádek|Funkce:<br>`run.log_row(name, description=None, **kwargs)`<br>Příklad:<br>Run.log_row ("Y nad X", x = 1, y = 0.4) | Pomocí *log_row* vytvoří metriku s více sloupců, jak je popsáno v kwargs. Každý pojmenovaný parametr generuje sloupec s hodnotou.  *log_row* může být volána jednou pro přihlášení libovolné řazené kolekce členů nebo více než jednou ve smyčce pro generování celou tabulku.|
|Table|Funkce:<br>`run.log_table(name, value, description='')`<br><br>Příklad:<br>Run.log_table ("Y nad X", {"x": [1, 2, 3], "y": [0.6, 0,7, 0.89]}) | Přihlaste se na objekt slovníku běh s daným názvem. |
|Image|Funkce:<br>`run.log_image(name, path=None, plot=None)`<br><br>Příklad:<br>`run.log_image("ROC", plt)` | Přihlaste se image spusťte záznam. Použití k protokolování soubor obrázku nebo matplotlib log_image vykreslení spustit.  Tyto Image budou viditelné a srovnatelné v běhu záznamu.|
|Označení spuštění|Funkce:<br>`run.tag(key, value=None)`<br><br>Příklad:<br>Run.tag ("vybrat", "Ano") | Označte běh s klíčem řetězce a volitelný řetězec.|
|Nahrát soubor nebo adresář|Funkce:<br>`run.upload_file(name, path_or_stream)`<br> <br> Příklad:<br>Run.upload_file ("best_model.pkl", ". / model.pkl") | Nahrání souboru do běhu záznamu. Spuštění automaticky zachytávací soubor v zadané výstupní adresář, kde je použit výchozí ". / výstupy" pro většinu spuštění typy.  Není zadána upload_file použijte jenom v případě, že budete muset nahrát další soubory nebo výstupní adresář. Doporučujeme přidat `outputs` název tak, že nahrán do adresáře výstupy. Můžete vytvořit seznam všech souborů, které jsou spojeny s tímto spustit záznam podle volá `run.get_file_names()`|

> [!NOTE]
> Metriky pro skaláry, seznamy, řádků a tabulky může mít typ: float, celé číslo nebo řetězec.

## <a name="choose-a-logging-option"></a>Zvolit možnost protokolování

Pokud chcete sledovat nebo sledovat experimentu, musíte přidat kód pro spuštění protokolování při odesílání příkazu run. Následují způsoby, jak aktivovat spuštění odeslání:
* __Run.start_logging__ – přidání funkce protokolování do trénovací skript a spustit relaci interactive protokolování v zadané experimentu. **start_logging** vytvoří interaktivní spuštění pro použití ve scénářích, jako je například poznámkových bloků. Všechny metriky, které jsou zaznamenány během relace jsou přidány do běhu záznam v experimentu.
* __ScriptRunConfig__ – přidání funkce protokolování do trénovací skript a načíst složku celý skript s spuštění.  **ScriptRunConfig** je třída pro nastavení konfigurace pro skript spustí. Pomocí této možnosti přidáte kód monitorování, abyste dostávali oznámení o dokončení nebo chcete-li získat vizuální pomůcky pro monitorování.

## <a name="set-up-the-workspace"></a>Nastavit pracovní prostor
Před přidáním protokolování a odeslání experimentu, musíte nastavit pracovní prostor.

1. Načtení pracovního prostoru. Další informace o nastavení konfigurace pracovního prostoru najdete v tématu [konfigurační soubor pracovního prostoru](how-to-configure-environment.md#workspace).

   ```python
   from azureml.core import Experiment, Run, Workspace
   import azureml.core
  
   ws = Workspace.from_config()
   ```
  
## <a name="option-1-use-start_logging"></a>Možnost 1: Použití start_logging

**start_logging** vytvoří interaktivní spuštění pro použití ve scénářích, jako je například poznámkových bloků. Všechny metriky, které jsou zaznamenány během relace jsou přidány do běhu záznam v experimentu.

Následující příklad trénovat jednoduchý model skriptu sklearn Ridge místně v místní aplikace Jupyter notebook. Další informace o odesílání experimentů do různých prostředí najdete v tématu [nastavení cílových výpočetních prostředí pro trénování modelu s využitím služby Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-set-up-training-targets).

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

2. Přidat sledování experimentu pomocí sady SDK služby Azure Machine Learning a nahrajte trvalý modelu do experimentu, spusťte záznam. Následující kód přidá značky, protokoly a nahraje soubor modelu pro běh experimentu.

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

    Skript končí ```run.complete()```, spustit označí jako dokončenou.  Tato funkce se obvykle používá ve scénářích interaktivní poznámkového bloku.

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

2. `train.py` Odkaz`mylib.py` na skript, který umožňuje získat seznam hodnot alfa pro použití v modelu Ridge.

   ```python
   # mylib.py
  
   import numpy as np

   def get_alphas():
      # list of numbers from 0.0 to 1.0 with a 0.05 interval
      return np.arange(0.0, 1.0, 0.05)
   ```

3. Nakonfigurujte uživatele spravovat místní prostředí.

   ```python
   from azureml.core import Environment
    
   # Editing a run configuration property on-fly.
   user_managed_env = Environment("user-managed-env")
    
   user_managed_env.python.user_managed_dependencies = True
    
   # You can choose a specific Python environment by pointing to a Python path 
   #user_managed_env.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
   ```

4. Odeslání ```train.py``` skript ke spuštění v prostředí spravované uživatele. Tato složka celý skript se odešle ke školení, včetně ```mylib.py``` souboru.

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

2. **[Pro automatizované strojového učení spuštění]**  Pro přístup k grafy z předchozích spuštění. Nahraďte `<<experiment_name>>` názvem vhodného experimentu:

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![Widget poznámkového bloku Jupyter pro automatizované Machine Learning](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


Chcete-li zobrazit další podrobnosti klikněte na kanál ke kanálu chcete data prozkoumat v tabulce a grafy se vykreslují v místní nabídce na webu Azure Portal.

### <a name="get-log-results-upon-completion"></a>Získání protokolu výsledků při dokončení

Model školení a monitorování probíhá na pozadí tak, aby můžete spouštět další úlohy, zatímco čekáte. Můžete také počkat, dokud modelu byla dokončena školení před spuštěním další kód. Při použití **ScriptRunConfig**, můžete použít ```run.wait_for_completion(show_output = True)``` zobrazíte po dokončení cvičení modelu. ```show_output``` Příznak vám poskytne podrobný výstup. 


### <a name="query-run-metrics"></a>Dotaz spustit metriky

Metriky trénovaný model použití lze zobrazit ```run.get_metrics()```. Teď můžete získat všechny metriky, které byly zaznamenány v příkladu výše k určení nejvhodnějšího modelu.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-the-azure-portal-or-your-workspace-landing-page-previewhttpsmlazurecom"></a>Zobrazit experiment na Azure Portal nebo na [cílové stránce pracovního prostoru (Preview)](https://ml.azure.com)

Po dokončení spuštění experimentu můžete přejít na zaznamenané experiment spustit záznam. K historii můžete přistupovat dvěma způsoby:

* Získat adresu URL pro spuštění přímo ```print(run.get_portal_url())```
* Zobrazit podrobnosti o spuštění, odešlete název běhu (v tomto případě ```run```). Tímto způsobem můžete odkazuje na název experimentu, ID, typ, stav, stránka s podrobnostmi o, odkaz na webu Azure portal a odkaz na dokumentaci.

Odkaz pro spuštění disponuje přímo na stránku Podrobnosti o spuštění na webu Azure Portal. Zde můžete zobrazit vlastnosti, sledované metriky, obrázky a grafy, které se protokolují v experimentu. V tomto případě jsme do protokolu zapíše MSE a hodnoty alfa.

  ![Podrobnosti o spuštění na webu Azure Portal](./media/how-to-track-experiments/run-details-page.png)

Můžete také zobrazit všechny výstupy a protokoly pro spuštění nebo stáhnout snímek experiment, který jste odeslali, složce experimentu můžete sdílet s ostatními.

### <a name="viewing-charts-in-run-details"></a>Zobrazení grafů v podrobnosti o spuštění

Existují různé způsoby, jak používat protokolování rozhraní API pro různé typy záznamů metrik během spuštění a zobrazit jako grafy na webu Azure Portal. 

|Zaznamenané hodnoty|Příklad kódu| Zobrazit v portálu|
|----|----|----|
|Protokol pole číselných hodnot| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|Single-variable spojnicový graf|
|Přihlásit se stejným názvem metriky opakovaně použít jednu numerickou hodnotu (jako v rámci smyčky for)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Single-variable spojnicový graf|
|Opakovaným odesláním řádek 2 číselné sloupce|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Dvě proměnné spojnicový graf|
|Tabulku protokolu s 2 číselné sloupce|`run.log_table(name='Sine Wave', value=sines)`|Dvě proměnné spojnicový graf|


## <a name="example-notebooks"></a>Příklad poznámkové bloky
Tyto poznámkové bloky předvedení konceptů v tomto článku:
* [How-to-use-azureml/Training/Train-within-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [How-to-use-azureml/Training/Train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [How-to-use-azureml/Training/Logging-API/Logging-API.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další postup

Vyzkoušejte tyto další kroky pro další informace o použití sady SDK Azure Machine Learning pro Python:

* Příklad toho, jak zaregistrovat tento nejlepší model a nasadit ho v tomto kurzu [trénování modelu klasifikace obrázků s Azure Machine Learning](tutorial-train-models-with-aml.md).

* Zjistěte, jak [trénování modelů PyTorch službou Azure Machine Learning](how-to-train-pytorch.md).
