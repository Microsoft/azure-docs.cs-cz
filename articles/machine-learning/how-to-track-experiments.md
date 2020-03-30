---
title: Protokolovat experimenty ML & metriky
titleSuffix: Azure Machine Learning
description: Sledujte experimenty Azure ML a monitorujte metriky spuštění, abyste vylepšili proces vytváření modelu. Přidejte protokolování do školicího skriptu a zobrazte protokolované výsledky spuštění.  Použijte soubor run.log, soubor Run.start_logging nebo SkriptRunConfig.
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
ms.openlocfilehash: 0c77e9d0aa4f44f33b1345a6021fc0378459ee85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79296961"
---
# <a name="monitor-azure-ml-experiment-runs-and-metrics"></a>Monitorování spuštění experimentu Azure ML a metrik
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Vylepšete proces vytváření modelu sledováním experimentů a sledováním metrik spuštění. V tomto článku se dozvíte, jak přidat kód protokolování do školicího skriptu, odeslat spuštění experimentu, sledovat, že běží a zkontrolujte výsledky v Azure Machine Learning.

> [!NOTE]
> Azure Machine Learning může také protokolovat informace z jiných zdrojů během školení, jako je například automatizované spuštění strojového učení nebo kontejner Dockeru, který spouští úlohu školení. Tyto protokoly nejsou dokumentovány. Pokud narazíte na problémy a obraťte se na podporu společnosti Microsoft, mohou být schopni používat tyto protokoly při řešení potíží.

> [!TIP]
> Informace v tomto dokumentu jsou určeny především pro datové vědce a vývojáře, kteří chtějí sledovat proces školení modelu. Pokud jste správce zájem o monitorování využití prostředků a události z Azure Machine learning, jako jsou kvóty, dokončené školení běží nebo dokončena nasazení modelu, najdete [v tématu monitorování Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="available-metrics-to-track"></a>Dostupné metriky ke sledování

Následující metriky lze přidat do běhu při trénování experimentu. Chcete-li zobrazit podrobnější seznam toho, co lze sledovat při běhu, naleznete [v referenční dokumentaci ke třídě Spustit](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

|Typ| Pythonu | Poznámky|
|----|:----|:----|
|Skalární hodnoty |Funkce:<br>`run.log(name, value, description='')`<br><br>Příklad:<br>run.log("přesnost", 0,95) |Protokolovat číselnou nebo řetězcovou hodnotu ke spuštění s daným názvem. Protokolování metriky ke spuštění způsobí, že tato metrika bude uložena v záznamu spuštění v experimentu.  Můžete protokolovat stejnou metriku vícekrát v rámci běhu, výsledek je považován za vektor této metriky.|
|Seznamy|Funkce:<br>`run.log_list(name, value, description='')`<br><br>Příklad:<br>run.log_list("přesnost", [0.6, 0.7, 0.87]) | Protokolovat seznam hodnot spustit s daným názvem.|
|Řádek|Funkce:<br>`run.log_row(name, description=None, **kwargs)`<br>Příklad:<br>run.log_row("Y přes X", x=1, y=0.4) | Pomocí *log_row* vytvoří metriku s více sloupci, jak je popsáno v kwargs. Každý pojmenovaný parametr generuje sloupec se zadanou hodnotou.  *log_row* lze volat jednou protokolovat libovolné řazené kolekce členů nebo vícekrát ve smyčce generovat úplnou tabulku.|
|Table|Funkce:<br>`run.log_table(name, value, description='')`<br><br>Příklad:<br>run.log_table("Y nad X", {"x":[1, 2, 3], "y":[0,6, 0,7, 0,89]}) | Protokolovat objekt slovníku spustit s daným názvem. |
|Obrázky|Funkce:<br>`run.log_image(name, path=None, plot=None)`<br><br>Příklad:<br>`run.log_image("ROC", plot=plt)` | Protokolovat bitovou kopii ke spuštění záznamu. Pomocí log_image protokolovat soubor obrázku nebo matplotlib plot ke spuštění.  Tyto obrázky budou viditelné a srovnatelné v záznamu běhu.|
|Označení běhu|Funkce:<br>`run.tag(key, value=None)`<br><br>Příklad:<br>run.tag("selected", "yes") | Označte spuštění pomocí řetězcového klíče a volitelné hodnoty řetězce.|
|Nahrání souboru nebo adresáře|Funkce:<br>`run.upload_file(name, path_or_stream)`<br> <br> Příklad:<br>run.upload_file("best_model.pkl", "./model.pkl") | Nahrajte soubor do záznamu spuštění. Spustí automaticky zachytit soubor v zadaném výstupním adresáři, který výchozí "./výstupy" pro většinu typů spuštění.  Upload_file použijte pouze v případě, že je třeba odeslat další soubory nebo není zadán výstupní adresář. Doporučujeme `outputs` přidat k názvu tak, aby se nahrál do adresáře výstupů. Můžete vypsat všechny soubory, které jsou přidruženy k tomuto záznamu spuštění,`run.get_file_names()`|

> [!NOTE]
> Metriky pro skaláry, seznamy, řádky a tabulky mohou mít typ: float, celé číslo nebo řetězec.

## <a name="choose-a-logging-option"></a>Volba možnosti protokolování

Pokud chcete experiment sledovat nebo sledovat, musíte přidat kód, abyste při odesílání spuštění začali protokolovat. Níže jsou uvedeny způsoby, jak spustit podání:
* __Run.start_logging__ - Přidejte funkce protokolování do školicího skriptu a spusťte interaktivní relaci protokolování v zadaném experimentu. **start_logging** vytvoří interaktivní spuštění pro použití ve scénářích, jako jsou poznámkové bloky. Všechny metriky, které jsou zaznamenány během relace jsou přidány do záznamu spuštění v experimentu.
* __ScriptRunConfig__ - Přidejte funkce protokolování do školicího skriptu a načtěte celou složku skriptu s runem.  **ScriptRunConfig** je třída pro nastavení konfigurací pro spuštění skriptů. Pomocí této možnosti můžete přidat monitorovací kód, který bude upozorňován na dokončení nebo získat vizuální widget ke sledování.

## <a name="set-up-the-workspace"></a>Nastavení pracovního prostoru
Před přidáním protokolování a odeslání experimentu je nutné nastavit pracovní prostor.

1. Načtěte pracovní prostor. Další informace o nastavení konfigurace pracovního prostoru naleznete v tématu [Configuration file pracovního prostoru](how-to-configure-environment.md#workspace).

[!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb?name=load_ws)]


## <a name="option-1-use-start_logging"></a>Možnost 1: Použití start_logging

**start_logging** vytvoří interaktivní spuštění pro použití ve scénářích, jako jsou poznámkové bloky. Všechny metriky, které jsou zaznamenány během relace jsou přidány do záznamu spuštění v experimentu.

Následující příklad trénuje jednoduchý model sklearn Ridge místně v místním notebooku Jupyter. Další informace o odesílání experimentů do různých prostředí najdete v tématu [Nastavení výpočetních cílů pro školení modelu pomocí Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-set-up-training-targets).

### <a name="load-the-data"></a>Načtení dat

Tento příklad používá datovou sadu diabetu, známou malou datovou sadu, která je dodávána s scikit-learn. Tato buňka načte datovou sadu a rozdělí ji na náhodné trénování a testování sad.

[!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb?name=load_data)]

### <a name="add-tracking"></a>Přidat sledování
Přidejte sledování experimentu pomocí sady Azure Machine Learning SDK a nahrajte trvalý model do záznamu spuštění experimentu. Následující kód přidá značky, protokoly a nahraje soubor modelu do experimentu spustit.

[!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb?name=create_experiment)]

Skript končí ```run.complete()```, který označí spuštění jako dokončené.  Tato funkce se obvykle používá ve scénářích interaktivního poznámkového bloku.

## <a name="option-2-use-scriptrunconfig"></a>Možnost 2: Použití funkce ScriptRunConfig

[**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) je třída pro nastavení konfigurací pro spuštění skriptů. Pomocí této možnosti můžete přidat monitorovací kód, který bude upozorňován na dokončení nebo získat vizuální widget ke sledování.

Tento příklad rozšiřuje základní model sklearn Ridge shora. Provádí jednoduché zametání parametrů, aby se zametaly alfa hodnoty modelu, aby bylo zachyceno metriky a trénované modely v spuštěních v rámci experimentu. Příklad běží místně proti prostředí spravovaného uživatelem. 

1. Vytvořte školicí skript `train.py`.

   [!code-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]

2. Skript `train.py` odkazy, `mylib.py` které vám umožní získat seznam hodnot alfa použít v modelu hřebene.

   [!code-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/mylib.py)] 

3. Nakonfigurujte místní prostředí spravované uživatelem.

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=user_managed_env)]


4. Odešlete ```train.py``` skript ke spuštění v prostředí spravovaném uživatelem. Celá tato složka skriptu je ```mylib.py``` odeslána k školení, včetně souboru.

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=src)] [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=run)]




## <a name="manage-a-run"></a>Správa běhu

[Spuštění, monitorování a zrušení školení spustí](how-to-manage-runs.md) článek upozorňuje konkrétní Pracovní postupy Azure Machine Learning pro správu experimentů.

## <a name="view-run-details"></a>Zobrazit podrobnosti o spuštění

### <a name="view-activequeued-runs-from-the-browser"></a>Zobrazení aktivních nebo zařazených běhů z prohlížeče

Výpočetní cíle používané k trénování modelů jsou sdílený prostředek. Jako takové mohou mít více spuštění ve frontě nebo aktivní v daném okamžiku. Chcete-li zobrazit spuštění konkrétního výpočetního cíle z prohlížeče, použijte následující kroky:

1. Ve [studiu Azure Machine Learning vyberte](https://ml.azure.com/)pracovní prostor a pak vyberte __Výpočetní výkon__ z levé strany stránky.

1. Vyberte __trénovací clustery,__ chcete-li zobrazit seznam výpočetních cílů používaných pro trénování. Pak vyberte cluster.

    ![Výběr tréninkového clusteru](./media/how-to-track-experiments/select-training-compute.png)

1. Vyberte __možnost Spuštění__. Zobrazí se seznam spuštění, která používají tento cluster. Chcete-li zobrazit podrobnosti pro konkrétní spuštění, použijte odkaz ve sloupci __Spustit.__ Chcete-li zobrazit podrobnosti o experimentu, použijte odkaz ve sloupci __Experiment.__

    ![Vybrat spuštění pro trénovací cluster](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > Spuštění může obsahovat podřízené spuštění, takže jedna úloha školení může mít za následek více položek.

Po dokončení spuštění se již na této stránce nezobrazí. Chcete-li zobrazit informace o dokončených spuštěních, navštivte část __Experimenty__ ve studiu a vyberte experiment a spusťte. Další informace naleznete v části [Metriky spuštění dotazu.](#queryrunmetrics)

### <a name="monitor-run-with-jupyter-notebook-widget"></a>Spuštění monitoru s widgetem jupyterového notebooku
Při použití **ScriptRunConfig** metoda odeslat spustí, můžete sledovat průběh běhu s [Widget Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py). Podobně jako odeslání spuštění je tento widget asynchronní a poskytuje průběžné aktualizace každých 10 až 15 sekund, dokud se úloha nedokončí.

1. Zobrazit widget Jupyter při čekání na dokončení spuštění.

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Snímek obrazovky widgetu poznámkového bloku Jupyter](./media/how-to-track-experiments/run-details-widget.png)

   Můžete také získat odkaz na stejný displej v pracovním prostoru.

   ```python
   print(run.get_portal_url())
   ```

2. **[Pro automatizované spuštění strojového učení]** Přístup k grafům z předchozího spuštění. Nahraďte `<<experiment_name>>` příslušným názvem experimentu:

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![Jupyter notebook widget pro automatizované strojové učení](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


Chcete-li zobrazit další podrobnosti o kanálu klikněte na kanálu, který chcete prozkoumat v tabulce a grafy se vykreslí v automaticky otevírané z Azure Machine Learning studio.

### <a name="get-log-results-upon-completion"></a>Získání protokolu výsledků při dokončení

Model školení a monitorování dojít na pozadí, takže můžete spustit další úkoly během čekání. Můžete také počkat, dokud model dokončil školení před spuštěním další kód. Při použití **ScriptRunConfig**, ```run.wait_for_completion(show_output = True)``` můžete použít k zobrazení, když je dokončena školení modelu. Příznak ```show_output``` poskytuje podrobný výstup. 

<a id="queryrunmetrics"></a>

### <a name="query-run-metrics"></a>Metriky spuštění dotazu

Metriky trénovaného modelu můžete ```run.get_metrics()```zobrazit pomocí aplikace . Nyní můžete získat všechny metriky, které byly zaznamenány ve výše uvedeném příkladu k určení nejlepší model.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-your-workspace-in-azure-machine-learning-studio"></a>Zobrazení experimentu ve vašem pracovním prostoru ve [studiu Azure Machine Learning](https://ml.azure.com)

Po dokončení experimentu můžete přejít na zaznamenaný záznam spuštění experimentu. K historii se dostanete ze [studia Azure Machine Learning studio](https://ml.azure.com).

Přejděte na kartu Experimenty a vyberte experiment. Budete převedeni na řídicí panel spuštění experimentu, kde můžete vidět sledované metriky a grafy, které jsou protokolovány pro každé spuštění. V tomto případě jsme zaznamenali MSE a hodnoty alfa.

  ![Spouštění podrobností ve studiu Azure Machine Learning](./media/how-to-track-experiments/experiment-dashboard.png)

Můžete přejít k určitému spuštění a zobrazit jeho výstupy nebo protokoly nebo stáhnout snímek odeslaného experimentu, abyste mohli sdílet složku experimentu s ostatními.

### <a name="viewing-charts-in-run-details"></a>Zobrazení grafů v podrobnostech spuštění

Existují různé způsoby, jak použít protokolování API pro záznam různých typů metrik během spuštění a jejich zobrazení jako grafy v Azure Machine Learning studio.

|Zaznamenaná hodnota|Příklad kódu| Zobrazit na portálu|
|----|----|----|
|Protokolovat pole číselných hodnot| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|spojnicový graf s jednou proměnnou|
|Protokolovat jednu číselnou hodnotu se stejným názvem metriky opakovaně používané (jako v rámci for smyčky)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Spojnicový graf s jednou proměnnou|
|Opakované protokolování řádku se 2 číselnými sloupci|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Spojnicový graf se dvěma proměnnými|
|Tabulka protokolů se 2 číselnými sloupci|`run.log_table(name='Sine Wave', value=sines)`|Spojnicový graf se dvěma proměnnými|


## <a name="example-notebooks"></a>Ukázkové poznámkové bloky
Následující poznámkové bloky ukazují koncepty v tomto článku:
* [použití azureml/školení/školení v notebooku](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [jak používat azureml/školení/vlak-na-místní](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experimenty/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další kroky

Pokud se chcete naučit používat sadu Azure Machine Learning SDK pro Python, vyzkoušejte tyto další kroky:

* Podívejte se na příklad, jak zaregistrovat nejlepší model a nasadit ho v [kurzu, Trénování modelu klasifikace obrázků pomocí Azure Machine Learning](tutorial-train-models-with-aml.md).

* Naučte se [trénovat modely PyTorch pomocí Azure Machine Learning](how-to-train-pytorch.md).
