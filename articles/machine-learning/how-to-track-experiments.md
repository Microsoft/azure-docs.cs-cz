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
ms.date: 03/12/2020
ms.custom: seodec18
ms.openlocfilehash: 9613b74b727d27bd47a05fadc1398bf898f667a5
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835715"
---
# <a name="monitor-azure-ml-experiment-runs-and-metrics"></a>Monitorování běhů a metriky Azure ML
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Vylepšete proces vytváření modelů sledováním experimentů a monitorováním metrik spuštění. V tomto článku se dozvíte, jak přidat kód protokolování do školicího skriptu, jak odeslat experiment, monitorovat tento běh a zkontrolovat výsledky v Azure Machine Learning.

> [!NOTE]
> Azure Machine Learning může také protokolovat informace z jiných zdrojů během školení, jako je automatické spuštění strojového učení nebo kontejner Docker, který spouští školicí úlohu. Tyto protokoly nejsou dokumentovány. Pokud narazíte na problémy a kontaktujte podporu Microsoftu, můžou při řešení potíží používat tyto protokoly.

> [!TIP]
> Informace v tomto dokumentu jsou primárně určené pro odborníky přes data a vývojáře, kteří chtějí monitorovat proces školení modelu. Pokud jste správcem a chcete monitorovat využití prostředků a události z Azure Machine Learningu, jako jsou kvóty, dokončené školicí běhy nebo dokončená nasazení modelu, přečtěte si téma [monitorování Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="available-metrics-to-track"></a>Dostupné metriky ke sledování

Následující metriky je možné přidat ke spuštění během školení experimentu. Podrobné informace o tom, co lze sledovat při spuštění, naleznete v [dokumentaci třídy Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

|Typ| Funkce Pythonu | Poznámky|
|----|:----|:----|
|Skalární hodnoty |Slouží<br>`run.log(name, value, description='')`<br><br>Příklad:<br>Run. log ("přesnost", 0,95) |Zaprotokoluje do běhu číselnou hodnotu nebo řetězec s daným názvem. Protokolování metriky do běhu způsobí, že se metrika uloží do záznamu spuštění v experimentu.  Stejnou metriku můžete v rámci spuštění zaprotokolovat několikrát, výsledek je považován za vektor této metriky.|
|Seznamy|Slouží<br>`run.log_list(name, value, description='')`<br><br>Příklad:<br>Run. log_list ("přesností"; [0,6; 0,7; 0,87]) | Protokoluje seznam hodnot pro běh se zadaným názvem.|
|Řádek|Slouží<br>`run.log_row(name, description=None, **kwargs)`<br>Příklad:<br>Run. log_row ("Y over X", X = 1, Y = 0.4) | Pomocí *log_row* se vytvoří metrika s více sloupci, jak je popsáno v tématu kwargs. Každý pojmenovaný parametr vygeneruje sloupec se zadanou hodnotou.  *log_row* může být jednou volána k zaznamenání libovolné řazené kolekce členů nebo vícekrát ve smyčce, aby vygenerovala úplnou tabulku.|
|Tabulka|Slouží<br>`run.log_table(name, value, description='')`<br><br>Příklad:<br>Run. log_table ("Y over X"; {"X": [1; 2; 3]; "Y": [0,6, 0,7, 0,89]}) | Zaprotokoluje objekt Dictionary do běhu s daným názvem. |
|Image|Slouží<br>`run.log_image(name, path=None, plot=None)`<br><br>Příklad:<br>`run.log_image("ROC", plot=plt)` | Zaprotokoluje obrázek do záznamu spuštění. K přihlášení použijte log_image. Soubor obrázku PNG nebo matplotlib vykreslení pro běh.  Tyto obrázky budou viditelné a srovnatelné v záznamu spuštění.|
|Označení běhu|Slouží<br>`run.tag(key, value=None)`<br><br>Příklad:<br>Run. Tag ("Selected"; "Yes") | Označte běh pomocí klíče řetězce a volitelné řetězcové hodnoty.|
|Odeslat soubor nebo adresář|Slouží<br>`run.upload_file(name, path_or_stream)`<br> <br> Příklad:<br>Run. upload_file ("best_model. pkl", "./model.pkl") | Nahrajte soubor na záznam spuštění. Spustí automaticky zachytávání souboru v zadaném výstupním adresáři, který pro většinu typů spuštění nastaví jako výchozí hodnotu "./Outputs".  Použijte upload_file jenom v případě, že je potřeba nahrát další soubory, nebo není zadaný výstupní adresář. Doporučujeme přidat `outputs` k názvu, aby se nahrál do adresáře výstupy. Můžete zobrazit seznam všech souborů, které jsou přidruženy k tomuto záznamu spuštění voláním`run.get_file_names()`|

> [!NOTE]
> Metriky pro skalární hodnoty, seznamy, řádky a tabulky mohou mít typ: float, Integer nebo String.

## <a name="choose-a-logging-option"></a>Zvolit možnost protokolování

Pokud chcete sledovat nebo monitorovat experiment, je nutné přidat kód pro spuštění protokolování při odeslání běhu. Níže jsou uvedené způsoby, jak spustit odeslání spuštění:
* __Spusťte. start_logging__ – do školicího skriptu přidejte funkce protokolování a v zadaném experimentu spusťte interaktivní relaci protokolování. **start_logging** vytvoří interaktivní běh pro použití ve scénářích, jako jsou třeba poznámkové bloky. Všechny metriky, které jsou protokolovány během relace, jsou přidány do záznamu spuštění v experimentu.
* __ScriptRunConfig__ – přidejte funkce protokolování do školicího skriptu a načtěte celou složku skriptu pomocí běhu.  **ScriptRunConfig** je třída pro nastavení konfigurací pro spuštění skriptu. Pomocí této možnosti můžete přidat kód monitorování, který bude upozorněn na dokončení nebo získat vizuální pomůcku k monitorování.
* __Protokolování návrháře__ – přidejte funkce protokolování do kanálu návrháře & přetažením pomocí modulu __Spustit skript Pythonu__ . Přidejte kód Pythonu k experimentům log designeru. 

## <a name="set-up-the-workspace"></a>Nastavení pracovního prostoru
Před přidáním protokolování a odeslání experimentu musíte nastavit pracovní prostor.

1. Načtěte pracovní prostor. Další informace o nastavení konfigurace pracovního prostoru najdete v tématu [konfigurační soubor pracovního prostoru](how-to-configure-environment.md#workspace).

[! notebook-Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb? název = load_ws)]


## <a name="option-1-use-start_logging"></a>Možnost 1: použití start_logging

**start_logging** vytvoří interaktivní běh pro použití ve scénářích, jako jsou třeba poznámkové bloky. Všechny metriky, které jsou protokolovány během relace, jsou přidány do záznamu spuštění v experimentu.

Následující příklad vytvoří jednoduchý Ridge model skriptu sklearn místně v místním poznámkovém bloku Jupyter. Další informace o odesílání experimentů do různých prostředí najdete v tématu [Nastavení výpočetních cílů pro školení modelů s Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-set-up-training-targets).

### <a name="load-the-data"></a>Načtení dat

Tento příklad používá datovou sadu diabetes, známou malou datovou sadu, která je dodávána s scikit-učí. Tato buňka načte datovou sadu a rozdělí ji do náhodných školicích a testovacích sad.

[! notebook-Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb? název = load_data)]

### <a name="add-tracking"></a>Přidat sledování
Přidejte sledování experimentů pomocí Azure Machine Learning SDK a nahrajte trvalý model do záznamu experimentálního spuštění. Následující kód přidá značky, protokoly a nahraje soubor modelu do běhu experimentu.

[! notebook-Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb? název = create_experiment)]

Skript končí znakem ```run.complete()``` , který označí běh jako dokončený.  Tato funkce se obvykle používá v interaktivních scénářích poznámkových bloků.

## <a name="option-2-use-scriptrunconfig"></a>Možnost 2: použití ScriptRunConfig

[**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) je třída pro nastavení konfigurací pro spuštění skriptu. Pomocí této možnosti můžete přidat kód monitorování, který bude upozorněn na dokončení nebo získat vizuální pomůcku k monitorování.

Tento příklad rozšiřuje základní model skriptu sklearn Ridge z výše uvedeného. Pro zachycení metrik a školených modelů v rámci experimentu používá jednoduché rozmazání parametrů pro setrvání hodnot alfa modelu. Příklad se spouští místně na uživatelsky spravovaném prostředí. 

1. Vytvořte školicí skript `train.py` .

   [! Code-Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]

2. `train.py`Odkaz na skript, `mylib.py` který umožňuje získat seznam hodnot alfa pro použití v modelu Ridge.

   [! Code-Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/mylib.py)] 

3. Konfigurace místního prostředí spravovaného uživatelem

   [! notebook-Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? název = user_managed_env)]


4. Odešlete ```train.py``` skript, který se spustí v prostředí spravovaném uživatelem. Tato složka pro všechny skripty je odeslána pro školení, včetně ```mylib.py``` souboru.

   [! notebook-Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? název = src)] [! notebook-Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? název = run)]

## <a name="option-3-log-designer-experiments"></a>Možnost 3: experimenty návrháře protokolů

K přidání logiky protokolování do experimentů návrháře použijte modul __spouštěného skriptu Pythonu__ . Pomocí tohoto pracovního postupu můžete protokolovat libovolnou hodnotu, ale je zvláště užitečné protokolovat metriky z modulu __vyhodnocení modelu__ ke sledování výkonu modelu v různých spuštěních.

1. Připojte modul __spuštění skriptu Pythonu__ k výstupu modulu __vyhodnocení modelu__ .

    ![Připojit modul spuštění skriptu Pythonu k vyhodnocení modulu modelu](./media/how-to-track-experiments/designer-logging-pipeline.png)

1. Vložte následující kód do editoru kódu __spouštěného v Pythonu__ , abyste protokoloval střední absolutní chybu pro vyškolený model:

    ```python
    # dataframe1 contains the values from Evaluate Model
    def azureml_main(dataframe1 = None, dataframe2 = None):
        print(f'Input pandas.DataFrame #1: {dataframe1}')

        from azureml.core import Run

        run = Run.get_context()

        # Log the mean absolute error to the current run to see the metric in the module detail pane.
        run.log(name='Mean_Absolute_Error', value=dataframe1['Mean_Absolute_Error'])

        # Log the mean absolute error to the parent run to see the metric in the run details page.
        run.parent.log(name='Mean_Absolute_Error', value=dataframe1['Mean_Absolute_Error'])
    
        return dataframe1,
    ```

## <a name="manage-a-run"></a>Správa běhu

Články týkající se [spuštění, sledování a zrušení školicích programů](how-to-manage-runs.md) zvýrazňují konkrétní Azure Machine Learning pracovní postupy pro správu experimentů.

## <a name="view-run-details"></a>Zobrazit podrobnosti o spuštění

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

2. **[Pro automatizované běhy strojového učení]** Pro přístup k grafům z předchozího běhu. Nahraďte `<<experiment_name>>` názvem vhodného experimentu:

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

Školení a monitorování modelů probíhá na pozadí, takže můžete spouštět další úlohy při čekání. Před spuštěním více kódů můžete také počkat na dokončení školení modelu. Když použijete **ScriptRunConfig**, můžete použít ```run.wait_for_completion(show_output = True)``` k zobrazení po dokončení školení modelu. ```show_output```Příznak vám poskytne podrobný výstup. 

<a id="queryrunmetrics"></a>

### <a name="query-run-metrics"></a>Metriky spuštění dotazu

Metriky proučeného modelu můžete zobrazit pomocí ```run.get_metrics()``` . Nyní můžete získat všechny metriky, které byly zaprotokolovány výše v předchozím příkladu, a určit tak nejlepší model.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-your-workspace-in-azure-machine-learning-studio"></a>Zobrazení experimentu v pracovním prostoru v [Azure Machine Learning Studiu](https://ml.azure.com)

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
* [Postupy: použití-AzureML/Track-and-monitor-experimenty/protokolování – rozhraní API](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další kroky

Pokud se chcete naučit používat sadu Azure Machine Learning SDK pro Python, vyzkoušejte tyto další kroky:

* Podívejte se na příklad, jak registrovat nejlepší model a nasadit ho v kurzu, [Naučte si model klasifikace imagí pomocí Azure Machine Learning](tutorial-train-models-with-aml.md).

* Naučte se, jak [pomocí Azure Machine Learning naučit modely PyTorch](how-to-train-pytorch.md).
