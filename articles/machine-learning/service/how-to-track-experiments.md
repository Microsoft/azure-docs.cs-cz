---
title: Sledování experimenty a vzdělávání metrik - Azure Machine Learning | Dokumentace Microsoftu
description: Ve službě Azure Machine Learning můžete sledovat své experimenty a monitorovat metriky vylepšit proces vytváření modelu. Zjistěte, jak přidat protokolování do trénovací skript, jak odeslání experimentu, jak zkontrolovat průběh spuštěné úlohy a postup zobrazení výsledků spuštění.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: 4d8b913c9e77254eb4d232e6bf5add136204b842
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49427752"
---
# <a name="track-experiments-and-training-metrics-in-azure-machine-learning"></a>Sledujte experimenty a vzdělávání metriky ve službě Azure Machine Learning

Ve službě Azure Machine Learning může sledovat vaše experimenty a monitorovat metriky vylepšit proces vytváření modelu. V tomto článku se dozvíte o různých způsobech přidání protokolování do skriptu školení, jak odeslání experimentu s **start_logging** a **ScriptRunConfig**, jak zkontrolovat průběh spuštěné úlohy a postup zobrazení výsledků spuštění. 

>[!NOTE]
> V tomto článku kódu byl testován s Azure Machine Learning SDK verze 0.168 

## <a name="list-of-training-metrics"></a>Seznam metrik školení 

Pro spuštění při školení experiment lze přidat následující metriky. Chcete-li zobrazit podrobnější seznam co lze sledovat na běh, najdete v článku [referenční dokumentaci k sadě SDK](https://docs.microsoft.com/python/api/overview/azure/azure-ml-sdk-overview?view=azure-ml-py).

|Typ| Funkce jazyka Python | Příklad: | Poznámky|
|----|:----|:----|:----|
|Skalární hodnoty | `run.log(name, value, description='')`| `run.log("accuracy", 0.95) ` |Protokol a číselné nebo řetězcová hodnota pro spouštění s daným názvem. Protokolování metriky o spuštění způsobí, že tuto metriku, který bude uložen do běhu záznam v experimentu.  Stejné metriky můžete přihlásit více než jednou v rámci spuštěný proces, výsledek se považuje za vektor tuto metriku.|
|Seznamy| `run.log_list(name, value, description='')`| `run.log_list("accuracies", [0.6, 0.7, 0.87])` | Přihlaste se seznam hodnot pro spouštění s daným názvem.|
|Řádek| `run.log_row(name, description=None, **kwargs)`| `run.log_row("Y over X", x=1, y=0.4)` | Pomocí *log_row* vytvoří metriku s více sloupců, jak je popsáno v kwargs. Každý pojmenovaný parametr generuje sloupec s hodnotou.  *log_row* může být volána jednou pro přihlášení libovolné řazené kolekce členů nebo více než jednou ve smyčce pro generování celou tabulku.|
|Table| `run.log_table(name, value, description='')`| `run.log_table("Y over X", {"x":[1, 2, 3], "y":[0.6, 0.7, 0.89]})` | Přihlaste se na objekt slovníku běh s daným názvem. |
|Image| `run.log_image(name, path=None, plot=None)`| `run.log_image("ROC", plt)` | Přihlaste se image spusťte záznam. Použití k protokolování soubor obrázku nebo matplotlib log_image vykreslení spustit.  Tyto Image budou viditelné a srovnatelné v běhu záznamu.|
|Označení spuštění| `run.tag(key, value=None)`| `run.tag("selected", "yes")` | Označte běh s klíčem řetězce a volitelný řetězec.|
|Nahrát soubor nebo adresář|`run.upload_file(name, path_or_stream)`| Run.upload_file ("best_model.pkl", ". / model.pkl") | Nahrání souboru do běhu záznamu. Spuštění automaticky zachytávací soubor v zadané výstupní adresář, kde je použit výchozí ". / výstupy" pro většinu spuštění typy.  Není zadána upload_file použijte jenom v případě, že budete muset nahrát další soubory nebo výstupní adresář. Doporučujeme přidat `outputs` název tak, že nahrán do adresáře výstupy. Můžete vytvořit seznam všech souborů, které jsou spojeny s tímto spustit záznam podle volá `run.get_file_names()`|

> [!NOTE]
> Metriky pro skaláry, seznamy, řádků a tabulky může mít typ: float, celé číslo nebo řetězec.

## <a name="log-metrics-for-experiments"></a>Protokolujte metriky pro experimentů

Pokud chcete sledovat nebo sledovat experimentu, musíte přidat kód pro spuštění protokolování při odesílání příkazu run. Následují způsoby, jak aktivovat spuštění odeslání:
* __Run.start_logging__ – přidání funkce protokolování do trénovací skript a spustit relaci interactive protokolování v zadané experimentu. **start_logging** vytvoří interaktivní spuštění pro použití ve scénářích, jako je například poznámkových bloků. Všechny metriky, které jsou zaznamenány během relace jsou přidány do běhu záznam v experimentu.
* __ScriptRunConfig__ – přidání funkce protokolování do trénovací skript a načíst složku celý skript s spuštění.  **ScriptRunConfig** je třída pro nastavení konfigurace pro skript spustí. Pomocí této možnosti přidáte kód monitorování, abyste dostávali oznámení o dokončení nebo chcete-li získat vizuální pomůcky pro monitorování.

## <a name="set-up-the-workspace-and-experiment"></a>Nastavení pracovního prostoru a experimentu
Před přidáním protokolování a odeslání experimentu, musíte vytvořit pracovní prostor a experimentu.

1. Načtení pracovního prostoru. Další informace o nastavení konfigurace pracovního prostoru, postupujte [rychlý Start](https://docs.microsoft.com/azure/machine-learning/service/quickstart-get-started).

  ```python
  from azureml.core import Workspace, Run
  import azureml.core
  
  ws = Workspace(workspace_name = <<workspace_name>>,
               subscription_id = <<subscription_id>>,
               resource_group = <<resource_group>>)
   ```

2. Vytvoření experimentu.

  ```python
  from azureml.core import Experiment

  # make up an arbitrary name
  experiment_name = 'train-in-notebook'
  exp = Experiment(workspace_object = ws, name = experiment_name)
  ```
  
## <a name="option-1-use-startlogging"></a>Možnost 1: Použití start_logging

**start_logging** vytvoří interaktivní spuštění pro použití ve scénářích, jako je například poznámkových bloků. Všechny metriky, které jsou zaznamenány během relace jsou přidány do běhu záznam v experimentu.

Následující příklad trénovat jednoduchý model skriptu sklearn Ridge místně v místní aplikace Jupyter notebook. Další informace o odesílání experimentů do různých prostředí najdete v tématu [nastavení cílových výpočetních prostředí pro trénování modelu s využitím služby Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-set-up-training-targets).

1. Vytvoření trénovací skript v místní aplikace Jupyter notebook. 

  ``` python
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
  run = Run.start_logging(experiment = exp)
  run.tag("Description","My first run!")
  run.log('alpha', 0.03)
  reg = Ridge(alpha = 0.03)
  reg.fit(data['train']['X'], data['train']['y'])
  preds = reg.predict(data['test']['X'])
  run.log('mse', mean_squared_error(preds, data['test']['y']))
  joblib.dump(value = reg, filename = 'model.pkl')
  # Upload file directly to the outputs folder
  run.upload_file(name = 'outputs/model.pkl', path_or_stream = './model.pkl')

  run.complete()
  ```

Skript končí ```run.complete()```, spustit označí jako dokončenou.  To se obvykle používá ve scénářích interaktivní poznámkového bloku.

## <a name="option-2-use-scriptrunconfig"></a>Možnost 2: Použití ScriptRunConfig

**ScriptRunConfig** je třída pro nastavení konfigurace pro skript spustí. Pomocí této možnosti přidáte kód monitorování, abyste dostávali oznámení o dokončení nebo chcete-li získat vizuální pomůcky pro monitorování.

Tento příklad rozšiřuje základní model skriptu sklearn Ridge výše. Provádí jednoduché parametr parametrů k vyčištění přes alfa hodnoty modelu, který má zachytávat metriky a trénované modely ve spuštění v rámci testu. V příkladu spustí místně prostředí spravované uživatele. 

1. Vytvoření trénovací skript. Tento mechanismus využívá ```%%writefile%%``` vypsat školení kódu do složky skriptu jako ```train.py```.

  ```python
  %%writefile $project_folder/train.py

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

2. ```train.py``` Odkazy na skripty ```mylib.py```. Tento soubor umožňuje získat seznam hodnot alfa pro použití v modelu ridge.

  ```python
  %%writefile $script_folder/mylib.py
  import numpy as np

  def get_alphas():
      # list of numbers from 0.0 to 1.0 with a 0.05 interval
      return np.arange(0.0, 1.0, 0.05)
  ```

3. Nakonfigurujte uživatele spravovat místní prostředí.

  ```python
  from azureml.core.runconfig import RunConfiguration

  # Editing a run configuration property on-fly.
  run_config_user_managed = RunConfiguration()

  run_config_user_managed.environment.python.user_managed_dependencies = True

  # You can choose a specific Python environment by pointing to a Python path 
  #run_config.environment.python.interpreter_path = '/home/user/miniconda3/envs/sdk2/bin/python'
  ```

4. Odeslání ```train.py``` skript ke spuštění v prostředí spravované uživatele. Tato složka celý skript se odešle ke školení, včetně ```mylib.py``` souboru.

  ```python
  from azureml.core import ScriptRunConfig

  src = ScriptRunConfig(source_directory = script_folder, script = 'train.py', run_config = run_config_user_managed)
  run = exp.submit(src)
  ```
  
## <a name="view-run-details"></a>Zobrazení podrobností o spuštění

### <a name="monitor-run-with-jupyter-notebook-widgets"></a>Monitorování spuštění pomocí widgetů poznámkového bloku Jupyter
Při použití **ScriptRunConfig** spuštěním metody k odeslání, můžete sledovat průběh spuštění s pomůckou Poznámkový blok Jupyter. Podobně jako odeslání spuštění je tento widget asynchronní a poskytuje průběžné aktualizace každých 10 až 15 sekund, dokud se úloha nedokončí.

1. Při čekání na dokončení běhu zobrazte widgetu Jupyter.

  ```python
  from azureml.train.widgets import RunDetails
  RunDetails(run).show()
  ```

  ![Snímek obrazovky aplikace Jupyter notebook widgetu](./media/how-to-track-experiments/widgets.PNG)

### <a name="get-log-results-upon-completion"></a>Získání protokolu výsledků při dokončení

Model školení a monitorování probíhá na pozadí tak, aby můžete spouštět další úlohy, zatímco čekáte. Můžete také počkat, dokud modelu byla dokončena školení před spuštěním další kód. Při použití **ScriptRunConfig**, můžete použít ```run.wait_for_completion(show_output = True)``` zobrazíte po dokončení cvičení modelu. ```show_output``` Příznak vám poskytne podrobný výstup. 
  
### <a name="query-run-metrics"></a>Dotaz spustit metriky

Metriky trénovaný model použití lze zobrazit ```run.get_metrics()```. Teď můžete získat všechny metriky, které byly zaznamenány v příkladu výše k určení nejvhodnějšího modelu.

<a name='view-the-experiment-in-the-web-portal'/>
## <a name="view-the-experiment-in-the-azure-portal"></a>Zobrazení experimentu na webu Azure Portal

Po dokončení spuštění experimentu můžete přejít na zaznamenané experiment spustit záznam. Můžete to provést dvěma způsoby:

* Získat adresu URL pro spuštění přímo ```print(run.get_portal_url())```
* Zobrazit podrobnosti o spuštění, odešlete název běhu (v tomto případě ```run```). Toto odkazuje na název experimentu, ID, typ, stav, stránka s podrobnostmi o, odkaz na webu Azure portal a odkaz na dokumentaci.

Odkaz pro spuštění disponuje přímo na stránku Podrobnosti o spuštění na webu Azure Portal. Zde můžete zobrazit vlastnosti, sledované metriky, obrázky a grafy, které se protokolují v experimentu. V tomto případě jsme do protokolu zapíše MSE a hodnoty alfa.

  ![Snímek obrazovky se podrobnosti o spuštění na webu Azure Portal](./media/how-to-track-experiments/run-details-page-web.PNG)

Můžete také zobrazit všechny výstupy a protokoly pro spuštění nebo stáhnout snímek experiment, který jste odeslali, složce experimentu můžete sdílet s ostatními.

## <a name="example-notebooks"></a>Příklad poznámkové bloky
Tyto poznámkové bloky předvedení konceptů v tomto článku:
* [01.Getting-Started/01.Train-within-notebook/01.Train-within-notebook.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/01.train-within-notebook)
* [01.Getting-Started/02.Train-on-local/02.Train-on-local.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local)

Získejte tyto poznámkové bloky: [!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další postup

Vyzkoušejte tyto další kroky pro další informace o použití sady SDK Azure Machine Learning pro Python:

* Příklad toho, jak zaregistrovat tento nejlepší model a nasadit ho v tomto kurzu [trénování modelu klasifikace obrázků s Azure Machine Learning](tutorial-train-models-with-aml.md).

* Zjistěte, jak [trénování modelů PyTorch službou Azure Machine Learning](how-to-train-pytorch.md).
