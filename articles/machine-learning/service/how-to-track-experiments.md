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
ms.date: 09/11/2019
ms.custom: seodec18
ms.openlocfilehash: 2d8bf44f5e5e7a3f8c328a47480599f9dd18b845
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489520"
---
# <a name="monitor-azure-ml-experiment-runs-and-metrics"></a>Monitorování běhů a metriky Azure ML
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Vylepšete proces vytváření modelů sledováním experimentů a monitorováním metrik spuštění. V tomto článku se dozvíte, jak přidat kód protokolování do školicího skriptu, jak odeslat experiment, monitorovat tento běh a zkontrolovat výsledky v Azure Machine Learning.

> [!NOTE]
> Azure Machine Learning může také protokolovat informace z jiných zdrojů během školení, jako je automatické spuštění strojového učení nebo kontejner Docker, který spouští školicí úlohu. Tyto protokoly nejsou dokumentovány. Pokud narazíte na problémy a kontaktujte podporu Microsoftu, můžou při řešení potíží používat tyto protokoly.

## <a name="available-metrics-to-track"></a>Dostupné metriky ke sledování

Následující metriky je možné přidat ke spuštění během školení experimentu. Podrobné informace o tom, co lze sledovat při spuštění, naleznete v [dokumentaci třídy Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

|Typ| Funkce Pythonu | Poznámky|
|----|:----|:----|
|Skalární hodnoty |Slouží<br>`run.log(name, value, description='')`<br><br>Příklad:<br>Run. log ("přesnost", 0,95) |Zaprotokoluje do běhu číselnou hodnotu nebo řetězec s daným názvem. Protokolování metriky do běhu způsobí, že se metrika uloží do záznamu spuštění v experimentu.  Stejnou metriku můžete v rámci spuštění zaprotokolovat několikrát, výsledek je považován za vektor této metriky.|
|Vytvoří|Slouží<br>`run.log_list(name, value, description='')`<br><br>Příklad:<br>Run. log _List ("přesností"; [0,6, 0,7, 0,87]) | Protokoluje seznam hodnot pro běh se zadaným názvem.|
|řadě|Slouží<br>`run.log_row(name, description=None, **kwargs)`<br>Příklad:<br>Run. log _row ("Y over X", X = 1, Y = 0.4) | Použití *log_row* vytvoří metriku s více sloupci, jak je popsáno v kwargs. Každý pojmenovaný parametr vygeneruje sloupec se zadanou hodnotou.  *log_row* se dá volat jednou pro zaznamenání libovolné řazené kolekce členů nebo víckrát ve smyčce, aby se vygenerovala úplná tabulka.|
|Tabulka|Slouží<br>`run.log_table(name, value, description='')`<br><br>Příklad:<br>Run. log _table ("Y over X"; {"X": [1; 2; 3]; "Y": [0,6, 0,7, 0,89]}) | Zaprotokoluje objekt Dictionary do běhu s daným názvem. |
|Image|Slouží<br>`run.log_image(name, path=None, plot=None)`<br><br>Příklad:<br>`run.log_image("ROC", plt)` | Zaprotokoluje obrázek do záznamu spuštění. Pomocí log_image Zaprotokolujte soubor obrázku nebo matplotlib vykreslení do běhu.  Tyto obrázky budou viditelné a srovnatelné v záznamu spuštění.|
|Označení běhu|Slouží<br>`run.tag(key, value=None)`<br><br>Příklad:<br>Run. Tag ("Selected"; "Yes") | Označte běh pomocí klíče řetězce a volitelné řetězcové hodnoty.|
|Odeslat soubor nebo adresář|Slouží<br>`run.upload_file(name, path_or_stream)`<br> <br> Příklad:<br>Run. upload_file ("best_model. pkl"; "./model.pkl") | Nahrajte soubor na záznam spuštění. Spustí automaticky zachytávání souboru v zadaném výstupním adresáři, který pro většinu typů spuštění nastaví jako výchozí hodnotu "./Outputs".  Upload_file použijte jenom v případě, že je potřeba nahrát další soubory nebo není zadaný výstupní adresář. Doporučujeme přidat `outputs` k názvu, aby se nahrál do adresáře výstupy. Můžete zobrazit seznam všech souborů, které jsou přidruženy k tomuto záznamu spuštění pomocí metody `run.get_file_names()`|

> [!NOTE]
> Metriky pro skalární hodnoty, seznamy, řádky a tabulky mohou mít typ: float, Integer nebo String.

## <a name="choose-a-logging-option"></a>Zvolit možnost protokolování

Pokud chcete sledovat nebo monitorovat experiment, je nutné přidat kód pro spuštění protokolování při odeslání běhu. Níže jsou uvedené způsoby, jak spustit odeslání spuštění:
* __Spusťte. start_logging__ – přidejte funkce protokolování do školicího skriptu a spusťte interaktivní protokolovací relaci v zadaném experimentu. **start_logging** vytvoří interaktivní běh pro použití ve scénářích, jako jsou třeba poznámkové bloky. Všechny metriky, které jsou protokolovány během relace, jsou přidány do záznamu spuštění v experimentu.
* __ScriptRunConfig__ – přidejte funkce protokolování do školicího skriptu a načtěte celou složku skriptu pomocí běhu.  **ScriptRunConfig** je třída pro nastavení konfigurací pro spuštění skriptu. Pomocí této možnosti můžete přidat kód monitorování, který bude upozorněn na dokončení nebo získat vizuální pomůcku k monitorování.

## <a name="set-up-the-workspace"></a>Nastavení pracovního prostoru
Před přidáním protokolování a odeslání experimentu musíte nastavit pracovní prostor.

1. Načtěte pracovní prostor. Další informace o nastavení konfigurace pracovního prostoru najdete v tématu [konfigurační soubor pracovního prostoru](how-to-configure-environment.md#workspace).

   ```python
   from azureml.core import Experiment, Run, Workspace
   import azureml.core
  
   ws = Workspace.from_config()
   ```
  
## <a name="option-1-use-start_logging"></a>Možnost 1: použití start_logging

**start_logging** vytvoří interaktivní běh pro použití ve scénářích, jako jsou třeba poznámkové bloky. Všechny metriky, které jsou protokolovány během relace, jsou přidány do záznamu spuštění v experimentu.

Následující příklad vytvoří jednoduchý Ridge model skriptu sklearn místně v místním poznámkovém bloku Jupyter. Další informace o odesílání experimentů do různých prostředí najdete v tématu [Nastavení výpočetních cílů pro školení modelů s Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-set-up-training-targets).

1. Vytvořte školicí skript v místním Jupyter poznámkovém bloku. 

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

2. Přidejte sledování experimentů pomocí Azure Machine Learning SDK a nahrajte trvalý model do záznamu experimentálního spuštění. Následující kód přidá značky, protokoly a nahraje soubor modelu do běhu experimentu.

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

    Skript končí na ```run.complete()```, který označí běh jako dokončený.  Tato funkce se obvykle používá v interaktivních scénářích poznámkových bloků.

## <a name="option-2-use-scriptrunconfig"></a>Možnost 2: použití ScriptRunConfig

[**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) je třída pro nastavení konfigurací pro spuštění skriptu. Pomocí této možnosti můžete přidat kód monitorování, který bude upozorněn na dokončení nebo získat vizuální pomůcku k monitorování.

Tento příklad rozšiřuje základní model skriptu sklearn Ridge z výše uvedeného. Pro zachycení metrik a školených modelů v rámci experimentu používá jednoduché rozmazání parametrů pro setrvání hodnot alfa modelu. Příklad se spouští místně na uživatelsky spravovaném prostředí. 

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

3. Konfigurace místního prostředí spravovaného uživatelem

   ```python
   from azureml.core import Environment
    
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

## <a name="view-run-details"></a>Zobrazit podrobnosti o spuštění

### <a name="monitor-run-with-jupyter-notebook-widget"></a>Monitorovat běh pomocí widgetu pro Poznámkový blok Jupyter
Když použijete metodu **ScriptRunConfig** k odeslání spuštění, můžete sledovat průběh běhu s [pomůckou Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py). Podobně jako odeslání spuštění je tento widget asynchronní a poskytuje průběžné aktualizace každých 10 až 15 sekund, dokud se úloha nedokončí.

1. Zobrazit widget Jupyter při čekání na dokončení běhu.

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Snímek obrazovky s pomůckou pro Poznámkový blok Jupyter](./media/how-to-track-experiments/run-details-widget.png)

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

   ![Widget pro Jupyter Poznámkový blok pro automatizované Machine Learning](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


Pokud chcete zobrazit další podrobnosti o kanálu, klikněte na kanál, který chcete prozkoumat v tabulce, a grafy se vykreslí v automaticky otevíraném okně Azure Machine Learning Studiu.

### <a name="get-log-results-upon-completion"></a>Získání protokolu výsledků při dokončení

Školení a monitorování modelů probíhá na pozadí, takže můžete spouštět další úlohy při čekání. Před spuštěním více kódů můžete také počkat na dokončení školení modelu. Když použijete **ScriptRunConfig**, můžete použít ```run.wait_for_completion(show_output = True)``` k zobrazení po dokončení školení modelu. Příznak ```show_output``` poskytuje podrobný výstup. 


### <a name="query-run-metrics"></a>Metriky spuštění dotazu

Metriky proučeného modelu můžete zobrazit pomocí ```run.get_metrics()```. Nyní můžete získat všechny metriky, které byly zaprotokolovány výše v předchozím příkladu, a určit tak nejlepší model.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-your-workspace-in-azure-machine-learning-studiohttpsmlazurecom"></a>Zobrazení experimentu v pracovním prostoru v [Azure Machine Learning Studiu](https://ml.azure.com)

Po dokončení experimentu můžete přejít na zaznamenaný záznam spuštění experimentu. K historii můžete přistupovat z [Azure Machine Learning studia](https://ml.azure.com).

Přejděte na kartu experimenty a vyberte svůj experiment. Přejdete do řídicího panelu experiment spustit, kde vidíte sledované metriky a grafy, které jsou protokolovány pro každé spuštění. V tomto případě jsme nahlásili hodnoty MSE a alfa.

  ![Podrobnosti o spuštění v Azure Machine Learning Studiu](./media/how-to-track-experiments/experiment-dashboard.png)

Můžete přejít k podrobnostem konkrétního spuštění a zobrazit jeho výstupy nebo protokoly nebo stáhnout snímek experimentu, který jste odeslali, abyste mohli sdílet složku experimentů s ostatními.

### <a name="viewing-charts-in-run-details"></a>Zobrazení grafů v podrobnostech o běhu

Existují různé způsoby, jak použít rozhraní API protokolování k nahrávání různých typů metrik během běhu a jejich zobrazení jako grafů v Azure Machine Learning Studiu.

|Hodnota protokolu|Příklad kódu| Zobrazit na portálu|
|----|----|----|
|Protokolování pole číselných hodnot| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|Spojnicový graf s jednou proměnnou|
|Zaprotokoluje jednu číselnou hodnotu se stejným názvem metriky, který se opakovaně používá (například v rámci smyčky for).| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Spojnicový graf s jednou proměnnou|
|Opakované zaznamenání řádku se dvěma číselnými sloupci|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Spojnicový graf se dvěma proměnnými|
|Tabulka protokolu se dvěma číselnými sloupci|`run.log_table(name='Sine Wave', value=sines)`|Spojnicový graf se dvěma proměnnými|


## <a name="example-notebooks"></a>Ukázkové poznámkové bloky
Následující poznámkové bloky ukazují koncepty v tomto článku:
* [Postupy: použití-AzureML/školení/výuka v rámci poznámkového bloku](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [Postupy: použití-AzureML/školení/výuka v místním prostředí](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [How-to-use-AzureML/Training/Logging-API/Logging-API. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak používat sadu SDK Azure Machine Learning pro Python, zkuste použít následující postup:

* Podívejte se na příklad, jak registrovat nejlepší model a nasadit ho v kurzu, [Naučte si model klasifikace imagí pomocí Azure Machine Learning](tutorial-train-models-with-aml.md).

* Naučte se, jak [pomocí Azure Machine Learning naučit modely PyTorch](how-to-train-pytorch.md).
