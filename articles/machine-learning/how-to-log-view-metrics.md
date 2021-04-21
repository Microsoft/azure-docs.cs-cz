---
title: '& protokolu zobrazit metriky a soubory protokolu'
titleSuffix: Azure Machine Learning
description: Povolte protokolování na vaše běhy školení na ML, abyste mohli monitorovat metriky spuštění v reálném čase a pomáhat diagnostikovat chyby a upozornění.
services: machine-learning
author: swinner95
ms.author: shwinne
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 04/19/2021
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 5bc7564a11f570833bd6cb10c14267e08c3b5753
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107820376"
---
# <a name="log--view-metrics-and-log-files"></a>& protokolu zobrazit metriky a soubory protokolu

Protokoluje informace v reálném čase pomocí výchozího balíčku protokolování Pythonu a Azure Machine Learning specifických funkcí sady Python SDK. Můžete protokolovat místně a odesílat protokoly do svého pracovního prostoru na portálu.

Protokoly vám pomohou při diagnostice chyb a upozornění a také při sledování metriky výkonu, jako jsou parametry a výkon modelu. V tomto článku se dozvíte, jak povolit protokolování v následujících scénářích:

> [!div class="checklist"]
> * Metriky spuštění protokolu
> * Interaktivní trénovací relace
> * Odesílání trénovacích úloh pomocí ScriptRunConfig
> * Nastavení `logging` nativní pro Python
> * Protokolování z dalších zdrojů


> [!TIP]
> V tomto článku se dozvíte, jak monitorovat proces trénování modelu. Pokud vás zajímá monitorování využití prostředků a událostí z Azure Machine Learningu, jako jsou kvóty, dokončená trénovací spuštění nebo dokončená nasazení modelů, přečtěte si téma věnované [monitorování služby Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="data-types"></a>Typy dat

Můžete protokolovat různé datové typy, včetně skalárních hodnot, seznamů, tabulek, obrázků, adresářů a dalších prvků. Další informace a ukázky kódu v Pythonu pro různé datové typy najdete na [referenční stránce k třídě Run](/python/api/azureml-core/azureml.core.run%28class%29).

### <a name="logging-run-metrics"></a>Protokolování spuštění metrik 

Pomocí následujících metod v rozhraních API protokolování můžete ovlivnit vizualizace metrik. Poznamenejte si [omezení služby](./resource-limits-quotas-capacity.md#metrics) pro tyto protokolované metriky. 

|Hodnota protokolu|Příklad kódu| Formátování na portálu|
|----|----|----|
|Protokolování pole číselných hodnot| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|Spojnicový graf s jednou proměnnou|
|Zaprotokoluje jednu číselnou hodnotu se stejným názvem metriky, který se opakovaně používá (například v rámci smyčky for).| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Spojnicový graf s jednou proměnnou|
|Opakované zaznamenání řádku se dvěma číselnými sloupci|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Spojnicový graf se dvěma proměnnými|
|Tabulka protokolu se dvěma číselnými sloupci|`run.log_table(name='Sine Wave', value=sines)`|Spojnicový graf se dvěma proměnnými|
|Obrázek protokolu|`run.log_image(name='food', path='./breadpudding.jpg', plot=None, description='desert')`|Tuto metodu použijte k zaznamenání souboru obrázku nebo matplotlib vykreslení do běhu. Tyto obrázky budou viditelné a srovnatelné v záznamu spuštění.|

### <a name="logging-with-mlflow"></a>Protokolování pomocí MLflow
Pomocí MLFlowLogger můžete protokolovat metriky.

```python
from azureml.core import Run
# connect to the workspace from within your running code
run = Run.get_context()
ws = run.experiment.workspace

# workspace has associated ml-flow-tracking-uri
mlflow_url = ws.get_mlflow_tracking_uri()

#Example: PyTorch Lightning
from pytorch_lightning.loggers import MLFlowLogger

mlf_logger = MLFlowLogger(experiment_name=run.experiment.name, tracking_uri=mlflow_url)
mlf_logger._run_id = run.id
```

## <a name="view-run-metrics"></a>Zobrazit metriky spuštění

## <a name="via-the-sdk"></a>Přes sadu SDK
Metriky proučeného modelu můžete zobrazit pomocí ```run.get_metrics()``` . Viz následující příklad. 

```python
from azureml.core import Run
run = Run.get_context()
run.log('metric-name', metric_value)

metrics = run.get_metrics()
# metrics is of type Dict[str, List[float]] mapping mertic names
# to a list of the values for that metric in the given run.

metrics.get('metric-name')
# list of metrics in the order they were recorded
```

<a name="view-the-experiment-in-the-web-portal"></a>

## <a name="view-run-metrics-in-aml-studio-ui"></a>Zobrazit metriky spuštění v uživatelském rozhraní AML studia

V [Azure Machine Learning Studiu](https://ml.azure.com)můžete procházet dokončené záznamy spuštění, včetně protokolovaných metrik.

Přejděte na kartu **experimenty** . Pokud chcete zobrazit všechny vaše běhy v pracovním prostoru mezi experimenty, vyberte kartu **všechna spuštění** . Můžete přejít k podrobnostem o spuštění pro konkrétní experimenty pomocí filtru experimentů v horním řádku nabídek.

Pro jednotlivé zobrazení experimentů vyberte kartu **všechny experimenty** . Na řídicím panelu experimentu můžete vidět sledované metriky a protokoly pro každé spuštění. 

Můžete také upravit tabulku spustit seznam a vybrat vícenásobná spuštění a zobrazit buď hodnotu v poli Poslední, minimální nebo maximální hodnota protokolu. Přizpůsobte si grafy pro porovnání hodnot protokolovaných metrik a agregací napříč několika spuštěními. Můžete vykreslit více metrik na ose y grafu a přizpůsobit osu x pro vykreslení protokolovaných metrik. 


### <a name="view-and-download-log-files-for-a-run"></a>Zobrazení a stažení souborů protokolu pro spuštění 

Soubory protokolu jsou zásadním prostředkem pro ladění úloh Azure ML. Po odeslání školicí úlohy přejdete k podrobnostem konkrétního spuštění a zobrazíte jeho protokoly a výstupy:  

1. Přejděte na kartu **experimenty** .
1. Vyberte runID pro konkrétní spuštění.
1. V horní části stránky vyberte **výstupy a protokoly** .
2. Vyberte **Stáhnout vše** a Stáhněte si všechny protokoly do složky zip.
3. Jednotlivé soubory protokolů si můžete stáhnout i tak, že vyberete soubor protokolu a vyberete **Stáhnout** .

:::image type="content" source="media/how-to-log-view-metrics/download-logs.png" alt-text="Snímek obrazovky s částí výstupu a protokolů běhu":::

Níže uvedené tabulky zobrazují obsah souborů protokolu ve složkách, které vidíte v této části.

> [!NOTE]
> Každý soubor se pro každé spuštění nemusí zobrazovat. Například 20_image_build_log *. txt se zobrazí pouze v případě, že je vytvořen nový obrázek (například při změně prostředí).

#### <a name="azureml-logs-folder"></a>`azureml-logs` složky

|Soubor  |Description  |
|---------|---------|
|20_image_build_log.txt     | Protokol pro vytváření imagí Docker pro školicí prostředí, volitelně, jeden za běhu. Platí pouze při aktualizaci vašeho prostředí. V opačném případě AML znovu použije obrázek uložený v mezipaměti. V případě úspěchu obsahuje podrobnosti o bitové kopii registru pro odpovídající obrázek.         |
|55_azureml-Execution-<node_id # C1.txt     | stdout/stderr protokol hostitelského nástroje, jeden pro každý uzel. Vyžádá si image do cílového výpočetního prostředí. Poznámka: Tento protokol se zobrazí jenom tehdy, když máte zabezpečené výpočetní prostředky.         |
|65_job_prep-<node_id # C1.txt     |   záznam stdout/stderr skriptu přípravy úlohy, jeden na uzel. Stáhněte si kód do výpočetního cíle a úložiště dat (Pokud se požaduje).       |
|70_driver_log (_x). txt      |  protokol stdout/stderr z skriptu ovládacího prvku AML a školicí skript zákazníka, jeden pro každý proces. **Standardní výstup z vašeho skriptu. Tento soubor je místo, kde se zobrazují protokoly kódu (například příkazy Print).** Ve většině případů zde budete monitorovat protokoly.       |
|70_mpi_log.txt     |   Protokol MPI Framework, volitelné, jeden za běhu. Pouze ke spuštění MPI.   |
|75_job_post-<node_id # C1.txt     |  stdout/stderr protokol skriptu pro vydání úlohy, jeden pro každý uzel. Odešlete protokoly a vyuvolněte výpočetní prostředky zpátky do Azure.        |
|process_info.jsna      |   zobrazí, který proces je spuštěný na kterém uzlu.  |
|process_status.jsna      | Zobrazit stav procesu, například pokud proces není spuštěn, spuštěn nebo dokončen.         |

#### <a name="logs--azureml-folder"></a>`logs > azureml` složky

|Soubor  |Description  |
|---------|---------|
|110_azureml. log      |         |
|job_prep_azureml. log     |   systémový protokol pro přípravu úlohy        |
|job_release_azureml. log     | systémový protokol pro uvolnění úlohy        |

#### <a name="logs--azureml--sidecar--node_id-folder"></a>`logs > azureml > sidecar > node_id` složky

Když je povolený postranní vozík, skripty pro přípravu úlohy a uvolnění úlohy se spustí v rámci kontejneru na vozíku.  Pro každý uzel existuje jedna složka. 

|Soubor  |Description  |
|---------|---------|
|start_cms.txt     |  Protokol procesu, který se spustí při spuštění kontejneru postranního vozíku       |
|prep_cmd.txt      |   Protokol pro ContextManagers zadaný při `job_prep.py` spuštění (u některých z těchto obsahů se streamuje `azureml-logs/65-job_prep` )       |
|release_cmd.txt     |  Protokol pro ComtextManagers se ukončil při `job_release.py` spuštění.        |

#### <a name="other-folders"></a>Další složky

Pro úlohy školení ve více výpočetních clusterech jsou k dispozici protokoly pro každou IP adresu uzlu. Struktura pro každý uzel je stejná jako úloha s jedním uzlem. K dispozici je ještě jedna složka protokolů pro celkové spuštění, stderr a protokoly STDOUT.

Azure Machine Learning protokolovat informace z různých zdrojů během školení, jako je AutoML nebo kontejner Docker, který spouští školicí úlohu. Mnohé z těchto protokolů nejsou dokumentovány. Pokud narazíte na problémy a kontaktujte podporu Microsoftu, můžou při řešení potíží používat tyto protokoly.


## <a name="interactive-logging-session"></a>Interaktivní relace protokolování

Interaktivní relace protokolování se obvykle používají v prostředích poznámkových bloků. Interaktivní relace protokolování se pouštějí pomocí metody [Experiment.start_logging()](/python/api/azureml-core/azureml.core.experiment%28class%29#start-logging--args----kwargs-). Všechny metriky zaznamenávané během relace se přidají do záznamu spuštění v experimentu. Metoda [run.complete()](/python/api/azureml-core/azureml.core.run%28class%29#complete--set-status-true-) ukončí relace a označí běh jako dokončený.

## <a name="scriptrun-logs"></a>Protokoly ScriptRun

V této části se dozvíte, jak přidat kód protokolování do běhů vytvořených při využití konfigurace pomocí třídy ScriptRunConfig. K zapouzdření skriptů a prostředí pro opakovatelná spouštění můžete využít třídu [**ScriptRunConfig**](/python/api/azureml-core/azureml.core.scriptrunconfig). Tuto možnost můžete také využít pro zobrazení vizuálního widgetu pro Jupyter Notebook pro monitorování.

V tomto příkladu se provede uklizení parametrů přes hodnoty alfa a k zaznamenání výsledků se použije metoda [run. log ()](/python/api/azureml-core/azureml.core.run%28class%29#log-name--value--description----).

1. Vytvořte trénovací skript, který obsahuje logiku protokolování: `train.py`.

   [!code-python[](~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. Odešlete skript ```train.py``` pro spuštění v prostředí spravovaném uživatelem. K trénování se odešle celá složka skriptu.

   [! notebook-Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? název = src)]


   [! notebook-Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? název = run)]

    Parametr `show_output` zapne podrobné protokolování, které vám umožní zobrazit podrobnosti z procesu trénování a také informace o všech vzdálených prostředcích nebo cílových výpočetních objektech. Pomocí následujícího kódu zapněte podrobné protokolování při odeslání experimentu.

```python
run = exp.submit(src, show_output=True)
```

Ve výsledném běhu můžete použít také stejný parametr ve funkci `wait_for_completion`.

```python
run.wait_for_completion(show_output=True)
```

## <a name="native-python-logging"></a>Nativní protokolování v Pythonu

Některé protokoly v sadě SDK mohou obsahovat chybu, která dává pokyn k nastavení úrovně protokolování na ladění. Pokud chcete nastavit úroveň protokolování, přidejte do skriptu následující kód.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="other-logging-sources"></a>Další zdroje protokolování

Azure Machine Learning může během trénování protokolovat také informace z jiných zdrojů, jako jsou například spuštění automatizovaného strojové učení nebo kontejnery Docker, které spouští úlohy. Tyto protokoly nejsou zdokumentované, ale pokud narazíte na problémy a kontaktujete podporu Microsoftu, dají se tyto protokoly využít při řešení potíží.

Informace o protokolování metrik v designeru služby Azure Machine Learning najdete v tématu věnovaném [postupu při protokolování metrik v designeru](how-to-track-designer-experiments.md).

## <a name="example-notebooks"></a>Příklady poznámkových bloků

Následující poznámkové bloky ukazují koncepty popsané v tomto článku:
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak používat Azure Machine Learning, najdete v těchto článcích:

* Příklad postupu při registraci nejvhodnějšího modelu a jeho nasazení najdete v kurzu [Trénování modelu klasifikace obrázků s Azure Machine Learning](tutorial-train-models-with-aml.md).