---
title: Metriky protokolu během školicích běhů
titleSuffix: Azure Machine Learning service
description: Můžete sledovat experimenty a monitorovat metriky a zlepšit tak proces vytváření modelů. Naučte se, jak přidat protokolování do školicího skriptu, jak odeslat experiment, jak kontrolovat průběh spuštěné úlohy a jak zobrazit zaznamenané výsledky spuštění.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/11/2019
ms.custom: seodec18
ms.openlocfilehash: 269568c172ff6c65c9877f9ad22067a11125b339
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67847415"
---
# <a name="log-metrics-during-training-runs-in-azure-machine-learning"></a>Metriky protokolu během školicích běhů v Azure Machine Learning

Vylepšete proces vytváření modelů sledováním experimentů a metrik monitorování. V tomto článku se dozvíte, jak přidat protokolování do školicího skriptu, jak odeslat experiment, monitorovat běh a zobrazit výsledky spuštění ve službě Azure Machine Learning.

## <a name="list-of-training-metrics"></a>Seznam metrik školení 

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

## <a name="start-logging-metrics"></a>Spustit protokolování metrik

Pokud chcete sledovat nebo sledovat experimentu, musíte přidat kód pro spuštění protokolování při odesílání příkazu run. Následují způsoby, jak aktivovat spuštění odeslání:
* __Run.start_logging__ – přidání funkce protokolování do trénovací skript a spustit relaci interactive protokolování v zadané experimentu. **start_logging** vytvoří interaktivní spuštění pro použití ve scénářích, jako je například poznámkových bloků. Všechny metriky, které jsou zaznamenány během relace jsou přidány do běhu záznam v experimentu.
* __ScriptRunConfig__ – přidání funkce protokolování do trénovací skript a načíst složku celý skript s spuštění.  **ScriptRunConfig** je třída pro nastavení konfigurace pro skript spustí. Pomocí této možnosti přidáte kód monitorování, abyste dostávali oznámení o dokončení nebo chcete-li získat vizuální pomůcky pro monitorování.

## <a name="set-up-the-workspace"></a>Nastavit pracovní prostor
Před přidáním protokolování a odeslání experimentu, musíte nastavit pracovní prostor.

1. Načtení pracovního prostoru. Pokud chcete získat další informace o nastavení konfigurace pracovního prostoru, postupujte podle kroků v části [Vytvoření pracovního prostoru služby Azure Machine Learning](setup-create-workspace.md#sdk).

   ```python
   from azureml.core import Experiment, Run, Workspace
   import azureml.core
  
   ws = Workspace.from_config()
   ```
  
## <a name="option-1-use-startlogging"></a>Možnost 1: Použití start_logging

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

Články týkající se [spuštění, sledování a zrušení školení](how-to-manage-runs.md) popisují konkrétní Azure Machine Learning pracovní postupy pro správu experimentů.

## <a name="view-run-details"></a>Zobrazení podrobností o spuštění

### <a name="monitor-run-with-jupyter-notebook-widgets"></a>Monitorování spuštění pomocí widgetů poznámkového bloku Jupyter
Při použití **ScriptRunConfig** spuštěním metody k odeslání, můžete sledovat průběh spuštění s pomůckou Poznámkový blok Jupyter. Podobně jako odeslání spuštění je tento widget asynchronní a poskytuje průběžné aktualizace každých 10 až 15 sekund, dokud se úloha nedokončí.

1. Při čekání na dokončení běhu zobrazte widgetu Jupyter.

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Snímek obrazovky aplikace Jupyter notebook widgetu](./media/how-to-track-experiments/run-details-widget.png)

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
## <a name="view-the-experiment-in-the-azure-portal"></a>Zobrazení experimentu na webu Azure Portal

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

<a name="auto"></a>
## <a name="understanding-automated-ml-charts"></a>Principy automatického ML grafy

Po odeslání automatizované úlohy ML v poznámkovém bloku, historie tyto spouštění najdete v vaší služby pracovního prostoru machine learning. 

Další informace:
+ [Grafy a křivky pro modelů klasifikace](#classification)
+ [Tabulky a grafy pro regresní modely](#regression)
+ [Model popisují možnosti](#model-explain-ability-and-feature-importance)


### <a name="view-the-run-charts"></a>Zobrazení spuštění grafů

1. Přejděte do svého pracovního prostoru. 

1. Vyberte **experimenty** úplně vlevo panelu pracovního prostoru.

   ![Snímek obrazovky nabídky experimentu](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment-menu.png)

1. Vyberte experiment, které vás zajímají.

   ![Seznam experimentu](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment-list.png)

1. V tabulce vyberte číslo spustit.

   ![Běh experimentu](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment-run.png)

1. V tabulce vyberte počet opakování pro model, který byste chtěli dále zkoumat.

   ![Model experimentu](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment-model.png)



### <a name="classification"></a>Klasifikace

Pro každý model klasifikace, který vytvoříte pomocí automatizovaných strojového učení služby Azure Machine Learning zobrazí se následující grafy: 
+ [Chybová matice](#confusion-matrix)
+ [Graf přesnosti a úplnosti](#precision-recall-chart)
+ [Příjemce provozní vlastnosti (nebo roc s více TŘÍDAMI)](#roc)
+ [Výtah křivky](#lift-curve)
+ [Zvýšení křivky](#gains-curve)
+ [Diagram kalibrací](#calibration-plot)

#### <a name="confusion-matrix"></a>Chybová matice

Chybová matice se používá k popisu výkonu model klasifikace. Každý řádek zobrazí instancí třídy true a každý sloupec představuje instance předpokládaná třída. Chybová matice zobrazí správně klasifikované popisky a nesprávně klasifikované popisky pro daný model.

Azure Machine Learning pro klasifikaci problémy automaticky poskytuje chybová matice pro každý model, který je sestaven. Pro každý chybovou matici automatizované ML zobrazí správně klasifikované popisky jako popisky zelené a nesprávně klasifikovaný jako červený. Velikost kruhu představuje počet vzorků v této přihrádky. Kromě toho frekvence počet každý předpokládané popisek a každému popisku true je součástí sousední pruhové grafy. 

Příklad 1: Klasifikační model s nízkou přesností ![model klasifikace s nízkou přesností](./media/how-to-track-experiments/azure-machine-learning-auto-ml-confusion-matrix1.png)

Příklad 2: Klasifikační model s vysokou přesností (ideální) ![model klasifikace s vysokou přesností](./media/how-to-track-experiments/azure-machine-learning-auto-ml-confusion-matrix2.png)


#### <a name="precision-recall-chart"></a>Graf přesnosti a úplnosti

Pomocí tohoto grafu lze porovnávat přesnosti a úplnosti křivky pro každý model k určení, které model má přijatelné vztah mezi přesnosti a odvolání pro vaše konkrétní obchodní problém. Tento graf ukazuje průměrnou přesnosti a úplnosti – makro, Micro průměrné přesnosti a úplnosti a přesnosti a úplnosti přidružené všechny třídy modelu.

Termín přesnosti představuje tuto možnost pro třídění k označení všech instancí správně. Odvolání představuje možnost pro třídění a vyhledat všechny instance konkrétní popisek. Přesnosti a úplnosti křivky znázorňuje vztah mezi tyto dva pojmy. V ideálním případě by modelu mají 100 % přesností a 100 % přesností.

Příklad 1: Klasifikační model s nízkou přesností a nízkým ![odvoláním modelu klasifikace s nízkou přesností a nízkým navrácením](./media/how-to-track-experiments/azure-machine-learning-auto-ml-precision-recall1.png)

Příklad 2: Klasifikační model s ~ 100% Precision a ~ 100% Recall (ideální) ![klasifikační model s vysokou přesností a vrácením](./media/how-to-track-experiments/azure-machine-learning-auto-ml-precision-recall2.png)

#### <a name="roc"></a>ROC S VÍCE TŘÍDAMI

Příjemce provozních charakteristik (nebo roc s více TŘÍDAMI) je vykreslení správně klasifikované popisků vs. nesprávně klasifikované popisky konkrétním modelu. Při trénování modelů na datové sady s vysokou posun, protože se nezobrazí falešně pozitivní popisky, může být méně informativní křivka roc s více TŘÍDAMI.

Příklad 1: Klasifikační model s dolními popisky s nízkou hodnotou a s ![vysokým falešnou jmenovkou, s nízkými a horními popisky](./media/how-to-track-experiments/azure-machine-learning-auto-ml-roc-1.png)

Příklad 2: Klasifikační model s horními popisky s hodnotou true a s ![nízkou hodnotou false označí klasifikační model s horními a nepravdivými popisky.](./media/how-to-track-experiments/azure-machine-learning-auto-ml-roc-2.png)

#### <a name="lift-curve"></a>Výtah křivky

Můžete porovnat výtah modelu u automaticky vytvořených službou Azure Machine Learning do směrného plánu chcete-li zobrazit takto získané místo hodnoty tohoto konkrétního modelu.

Výtah grafy se používají k vyhodnocení výkonu modelu klasifikace. Ukazuje, jak mnohem lépe můžete očekávat s modelem porovnání bez modelu. 

Příklad 1: Model je vykonává horší než model ![náhodného výběru modelu klasifikace, který je horší než model náhodného výběru.](./media/how-to-track-experiments/azure-machine-learning-auto-ml-lift-curve1.png)

Příklad 2: Model je lepší než model ![náhodného výběru, který provádí model klasifikace, který je lepší.](./media/how-to-track-experiments/azure-machine-learning-auto-ml-lift-curve2.png)

#### <a name="gains-curve"></a>Zvýšení křivky

Graf zvýšení nastavení vyhodnocuje výkon model klasifikace podle každá část data. Ukazuje pro každý percentilu sady dat, kolik lépe můžete očekávat k provádění porovnání proti náhodný výběr modelu.

Použijte graf kumulativní zisky si můžete vybrat klasifikace odříznutí pomocí procenta, který odpovídá požadované zisk z modelu. Tyto informace obsahuje jiný způsob řešení prohlížení výsledků v související tabulce.

Příklad 1: Klasifikační model s minimálním ziskem ![a modelem klasifikace s minimálním ziskem](./media/how-to-track-experiments/azure-machine-learning-auto-ml-gains-curve1.png)

Příklad 2: Klasifikační model s významným ziskem získáte ![model klasifikace s významným ziskem.](./media/how-to-track-experiments/azure-machine-learning-auto-ml-gains-curve2.png)

#### <a name="calibration-plot"></a>Diagram kalibrací

Všechny klasifikace problémy můžete zkontrolovat řádku kalibrací micro průměr, – makro průměr a každá třída v daném prediktivního modelu. 

Vykreslení kalibrací slouží k zobrazení spolehlivosti prediktivního modelu. Dělá to tak, že znázorňující vztah mezi pravděpodobnost předpovězené a skutečný pravděpodobnosti, kde "pravděpodobnost" představuje pravděpodobnost, že konkrétní instanci patří pod některé popiskem. Dobře kalibrované modelu v souladu s y = x řádku, kde je přiměřeně jisti jeho předpovědi. Model typu over-pass-the důvěrné v souladu s y = 0 řádku, kde pravděpodobnost předpovězené existuje, ale neexistuje žádný skutečný pravděpodobnosti.

Příklad 1: Dobře kalibrovaný model ![ s větším kalibrovaným modelem](./media/how-to-track-experiments/azure-machine-learning-auto-ml-calib-curve1.png)

Příklad 2: Model ![s vysokou jistotou modelu nadlimitního modelu](./media/how-to-track-experiments/azure-machine-learning-auto-ml-calib-curve2.png)

### <a name="regression"></a>Regrese
Pro každý regresní model programujte automatizované strojového učení služby Azure Machine Learning, zobrazí se následující grafy: 
+ [Předpokládaná vs. Hodnota TRUE](#pvt)
+ [Histogram zbytků](#histo)

<a name="pvt"></a>

#### <a name="predicted-vs-true"></a>Předpokládaná vs. True

Předpokládaná vs. Hodnota TRUE označuje vztah mezi předpovězené hodnoty a její usnadňuje korelování hodnotu true pro regresní problém. Tento graf lze použít k měření výkonu modelu jako blíže k y = x řádku predikované hodnoty jsou, tím lepší přesnost prediktivního modelu.

Po každém spuštění zobrazí se předpokládané vs. true grafu pro každou regresní model. K ochraně dat o ochraně osobních údajů, hodnoty jsou rozdělený na intervaly společně a velikost každé z nich se zobrazuje jako pruhový graf v dolní části oblasti grafu. Prediktivní model, můžete porovnat s označením vystínovat oblast s okraji chyba proti ideální hodnotě, kde by mělo být modelu.

Příklad 1: Regresní model s nízkou přesností v předpovědi ![regresní model s nízkou přesností v předpovědi](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression1.png)

Příklad 2: Regresní model s vysokou přesností v jeho předpovědi ![regresní model s vysokou přesností ve své předpovědi](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression2.png)

<a name="histo"></a>

#### <a name="histogram-of-residuals"></a>Histogram zbytků

Zbývající představuje zjištěnou y – předpokládaná y. Zobrazíte okraj chyby s nízkou posun by měl mít tvar histogram zbytky křivku zvonku zaměřená na 0. 

Příklad 1: Regresní model s posunem chyb ![SA regresní model s posunem v jeho chybách](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression3.png)

Příklad 2: Regresní model s větší rovnoměrné distribucí chyb ![A regresní model s větším množstvím distribucí chyb](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression4.png)

### <a name="model-explain-ability-and-feature-importance"></a>Model význam popisují možnosti a funkce

Funkce význam poskytuje skóre, která určuje, jak se jednotlivé funkce v procesu vytváření modelu. Můžete zkontrolovat skóre význam funkce pro model celkové stejně jako na třídu v prediktivním modelem. Zobrazí se na funkci jak si vede význam pro každou třídu a celková.

![Možnost vysvětlit funkce](./media/how-to-track-experiments/azure-machine-learning-auto-ml-feature-explain1.png)

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
