---
title: Řešení potíží s třídou ParallelRunStep
titleSuffix: Azure Machine Learning
description: Tipy pro řešení potíží při získání chyb pomocí ParallelRunStep v kanálech Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: troubleshooting
ms.custom: troubleshooting
ms.reviewer: larryfr, vaidyas, laobri, tracych
ms.author: trmccorm
author: tmccrmck
ms.date: 09/23/2020
ms.openlocfilehash: ee41ae2a705ceaa0e9742c91552d6bdae26820ce
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101690273"
---
# <a name="troubleshooting-the-parallelrunstep"></a>Řešení potíží s třídou ParallelRunStep

V tomto článku se dozvíte, jak řešit chyby pomocí třídy [ParallelRunStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep?preserve-view=true&view=azure-ml-py) ze sady [SDK Azure Machine Learning](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py).

Obecné tipy pro řešení potíží s kanálem najdete v tématu [řešení potíží s kanály strojového učení](how-to-debug-pipelines.md).

## <a name="testing-scripts-locally"></a>Místní testování skriptů

 Vaše ParallelRunStep se spouští jako krok v kanálech ML. Skripty můžete chtít [místně testovat](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines) jako první krok.

##  <a name="script-requirements"></a>Požadavky na skripty

Skript pro `ParallelRunStep` *musí obsahovat* dvě funkce:
- `init()`: Tuto funkci použijte pro veškerou nákladný nebo běžnou přípravu pro pozdější odvození. Můžete ji například použít k načtení modelu do globálního objektu. Tato funkce bude volána pouze jednou na začátku procesu.
-  `run(mini_batch)`: Funkce se spustí pro každou `mini_batch` instanci.
    -  `mini_batch`: `ParallelRunStep` vyvolá metodu Run a předá `DataFrame` jako argument metody seznam nebo PANDAS. Každá položka v mini_batch bude cestou k souboru, pokud je vstupem `FileDataset` nebo PANDAS, `DataFrame` Pokud je vstupem `TabularDataset` .
    -  `response`: metoda Run () by měla vracet PANDAS `DataFrame` nebo Array. Pro append_row output_action jsou tyto vrácené prvky připojeny do společného výstupního souboru. V případě summary_only se obsah prvků ignoruje. U všech výstupních akcí každý vrácený element Output označuje jedno úspěšné spuštění vstupního prvku ve vstupní Mini-Batch. Ujistěte se, že je ve výsledku spuštění k dispozici dostatek dat pro mapování vstupu na výsledek výstupu. Výstup spuštění se zapíše do výstupního souboru a nebude zaručit, že bude v pořádku, abyste ho namapovali na vstup, měli byste použít nějaký klíč ve výstupu.

```python
%%writefile digit_identification.py
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines/parallel-run)
# for the implementation script.

import os
import numpy as np
import tensorflow as tf
from PIL import Image
from azureml.core import Model


def init():
    global g_tf_sess

    # Pull down the model from the workspace
    model_path = Model.get_model_path("mnist")

    # Construct a graph to execute
    tf.reset_default_graph()
    saver = tf.train.import_meta_graph(os.path.join(model_path, 'mnist-tf.model.meta'))
    g_tf_sess = tf.Session()
    saver.restore(g_tf_sess, os.path.join(model_path, 'mnist-tf.model'))


def run(mini_batch):
    print(f'run method start: {__file__}, run({mini_batch})')
    resultList = []
    in_tensor = g_tf_sess.graph.get_tensor_by_name("network/X:0")
    output = g_tf_sess.graph.get_tensor_by_name("network/output/MatMul:0")

    for image in mini_batch:
        # Prepare each image
        data = Image.open(image)
        np_im = np.array(data).reshape((1, 784))
        # Perform inference
        inference_result = output.eval(feed_dict={in_tensor: np_im}, session=g_tf_sess)
        # Find the best probability, and add it to the result list
        best_result = np.argmax(inference_result)
        resultList.append("{}: {}".format(os.path.basename(image), best_result))

    return resultList
```

Pokud máte jiný soubor nebo složku ve stejném adresáři jako skript pro odvození, můžete na něj odkazovat pomocí hledání aktuálního pracovního adresáře.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

### <a name="parameters-for-parallelrunconfig"></a>Parametry pro ParallelRunConfig

`ParallelRunConfig` je hlavní konfigurací `ParallelRunStep` instance v rámci Azure Machine Learning kanálu. Použijete ho k zabalení skriptu a ke konfiguraci nezbytných parametrů, včetně všech následujících položek:
- `entry_script`: Uživatelský skript jako cesta k místnímu souboru, který bude spuštěn paralelně na více uzlech. Pokud `source_directory` je k dispozici, použijte relativní cestu. V opačném případě použijte jakoukoli cestu, která je přístupná v počítači.
- `mini_batch_size`: Velikost malé dávky předaná jednomu `run()` volání. (volitelné; výchozí hodnota je `10` soubory pro `FileDataset` a `1MB` pro `TabularDataset` .)
    - V případě je `FileDataset` to počet souborů s minimální hodnotou `1` . Můžete zkombinovat více souborů do jedné Mini-dávky.
    - Pro `TabularDataset` je to velikost dat. Příklady hodnot jsou `1024` , `1024KB` , `10MB` a `1GB` . Doporučená hodnota je `1MB` . Ze zkrácené dávky `TabularDataset` nebude nikdy mezi hranicemi souborů. Například pokud máte soubory. csv s různými velikostmi, nejmenší soubor je 100 KB a největší je 10 MB. Pokud nastavíte `mini_batch_size = 1MB` , budou se soubory s velikostí menší než 1 MB považovat za jednu miniickou dávku. Soubory o velikosti větší než 1 MB budou rozděleny do několika Mini-dávek.
- `error_threshold`: Počet selhání záznamu `TabularDataset` a selhání souborů pro `FileDataset` , které by měly být během zpracování ignorovány. Pokud se počet chyb pro celý vstup překročí k této hodnotě, bude úloha přerušena. Prahová hodnota chyby je pro celý vstup a nikoli pro jednotlivé Mini-dávky odeslané do `run()` metody. Rozsah je `[-1, int.max]` . `-1`Část indikuje ignorování všech selhání během zpracování.
- `output_action`: Jedna z následujících hodnot indikuje, jak bude uspořádán výstup:
    - `summary_only`: Uživatelský skript uloží výstup. `ParallelRunStep` použije výstup pouze pro výpočet prahové hodnoty chyby.
    - `append_row`: Pro všechny vstupy se ve výstupní složce vytvoří pouze jeden soubor pro připojení všech výstupů oddělených čárou.
- `append_row_file_name`: Chcete-li přizpůsobit název výstupního souboru pro append_row output_action (volitelné; výchozí hodnota je `parallel_run_step.txt` ).
- `source_directory`: Cesty ke složkám, které obsahují všechny soubory, které mají být spuštěny na cílovém výpočetním cíli (volitelné).
- `compute_target`: `AmlCompute` Podporuje se jenom.
- `node_count`: Počet výpočetních uzlů, které se mají použít ke spuštění skriptu uživatele.
- `process_count_per_node`: Počet procesů na uzel. Osvědčeným postupem je nastavit počet GPU nebo PROCESORového jednoho uzlu (volitelné; výchozí hodnota je `1` ).
- `environment`: Definice prostředí Pythonu. Můžete ji nakonfigurovat tak, aby používala existující prostředí Pythonu, nebo nastavit dočasné prostředí. Definice je také zodpovědná za nastavení požadovaných závislostí aplikace (volitelné).
- `logging_level`: Podrobnosti protokolu. Hodnoty při zvyšování podrobností jsou: `WARNING` , `INFO` a `DEBUG` . (volitelné; výchozí hodnota je `INFO` )
- `run_invocation_timeout`: `run()` Časový limit volání metody v sekundách. (volitelné; výchozí hodnota je `60` )
- `run_max_try`: Maximální počet testovaných položek `run()` pro Mini-Batch. V `run()` případě, že dojde k výjimce, se nezdařila nebo není vrácena žádná hodnota, pokud `run_invocation_timeout` je dosaženo (volitelné; výchozí hodnota je `3` ). 

Můžete zadat `mini_batch_size` , `node_count` ,, `process_count_per_node` `logging_level` , `run_invocation_timeout` a `run_max_try` jako, aby `PipelineParameter` při opakovaném odesílání kanálu mohli doladit hodnoty parametrů. V tomto příkladu použijete `PipelineParameter` pro `mini_batch_size` a `Process_count_per_node` a tyto hodnoty změníte, pokud je znovu spustíte později. 

### <a name="parameters-for-creating-the-parallelrunstep"></a>Parametry pro vytvoření ParallelRunStep

Vytvořte ParallelRunStep pomocí skriptu, konfigurace prostředí a parametrů. Určete výpočetní cíl, který jste už připojili k pracovnímu prostoru jako cíl provádění pro váš skript pro odvození. Použijte `ParallelRunStep` k vytvoření kroku kanálu odvození dávky, který převezme všechny následující parametry:
- `name`: Název kroku s následujícími omezeními pro pojmenovávání: jedinečné, 3-32 znaky a Regex ^ \[ a-z \] ([-a-Z0-9] * [a-Z0-9])? $.
- `parallel_run_config`: `ParallelRunConfig` Objekt, jak je definováno výše.
- `inputs`: Jedna nebo více Azure Machine Learning datových sad s jedním typem, které mají být rozděleny na oddíly pro paralelní zpracování.
- `side_inputs`: Jedno nebo více referenčních dat nebo datových sad, které se používají jako vstupy na straně, není nutné rozdělit na oddíly.
- `output`: `OutputFileDatasetConfig` Objekt, který představuje cestu k adresáři, ve kterém budou uložena výstupní data.
- `arguments`: Seznam argumentů předaných uživatelskému skriptu. Pomocí unknown_args je načtěte v vstupním skriptu (volitelné).
- `allow_reuse`: Zda by měl krok při spuštění se stejnými nastaveními nebo vstupy znovu použít předchozí výsledky. Pokud je tento parametr `False` , nové spuštění bude pro tento krok vždy vygenerováno během provádění kanálu. (volitelné; výchozí hodnota je `True` .)

```python
from azureml.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="predict-digits-mnist",
    parallel_run_config=parallel_run_config,
    inputs=[input_mnist_ds_consumption],
    output=output_dir,
    allow_reuse=True
)
```

## <a name="debugging-scripts-from-remote-context"></a>Ladění skriptů ze vzdáleného kontextu

Přechod z ladění skriptu bodování místně na ladění vyhodnocovacího skriptu ve skutečném kanálu může být obtížné. Informace o hledání protokolů na portálu najdete v  [části kanály strojového učení na webu ladění skriptů ze vzdáleného kontextu](how-to-debug-pipelines.md). Informace v této části platí i pro ParallelRunStep.

Například soubor protokolu `70_driver_log.txt` obsahuje informace z kontroleru, který spouští kód ParallelRunStep.

Z důvodu distribuované povahy úloh ParallelRunStep existují protokoly z několika různých zdrojů. Vytvoří se ale dva konsolidované soubory, které poskytují informace vysoké úrovně:

- `~/logs/job_progress_overview.txt`: Tento soubor poskytuje informace vysoké úrovně o počtu Mini-dávek (označovaných také jako úlohy), které byly doposud vytvořeny a počtu mininích dávek zpracovaných zatím. Na tomto konci se zobrazuje výsledek úlohy. Pokud se úloha nezdařila, zobrazí se chybová zpráva a kde začít odstraňování potíží.

- `~/logs/sys/master_role.txt`: Tento soubor poskytuje hlavní uzel (také označovaný jako Orchestrator) zobrazení spuštěné úlohy. Zahrnuje vytvoření úlohy, monitorování průběhu, výsledek spuštění.

Protokoly vygenerované ze vstupního skriptu pomocí pomocníka EntryScript a příkazů Print budou nalezeny v následujících souborech:

- `~/logs/user/entry_script_log/<ip_address>/<process_name>.log.txt`: Tyto soubory jsou protokoly napsané z entry_script pomocí pomocné rutiny EntryScript.

- `~/logs/user/stdout/<ip_address>/<process_name>.stdout.txt`: Tyto soubory jsou protokoly z stdout (například příkaz Print) entry_script.

- `~/logs/user/stderr/<ip_address>/<process_name>.stderr.txt`: Tyto soubory jsou protokoly z stderr entry_script.

Stručné porozumění chybám ve skriptu:

- `~/logs/user/error.txt`: Tento soubor se pokusí shrnout chyby ve vašem skriptu.

Další informace o chybách ve skriptu najdete v těchto případech:

- `~/logs/user/error/`: Obsahuje úplné trasování zásobníku výjimek vyvolaných při načítání a spouštění vstupního skriptu.

Pokud potřebujete úplný přehled o tom, jak každý uzel spustil skript skóre, podívejte se na jednotlivé protokoly procesu pro každý uzel. Protokoly procesu lze nalézt ve `sys/node` složce seskupené podle uzlů pracovních procesů:

- `~/logs/sys/node/<ip_address>/<process_name>.txt`: Tento soubor poskytuje podrobné informace o každé Mini dávce, jak je vyzvednuta nebo dokončena pracovním procesem. Pro každou miniskou dávku tento soubor obsahuje:

    - IP adresa a PID pracovního procesu. 
    - Celkový počet položek, počet úspěšně zpracovaných položek a počet neúspěšných položek.
    - Čas spuštění, doba trvání, doba zpracování a metoda spuštění.

Můžete si také prohlédnout výsledky pravidelných kontrol využití prostředků pro každý uzel. Soubory protokolu a instalační soubory jsou v této složce:

- `~/logs/perf`: Nastavte `--resource_monitor_interval` pro změnu intervalu kontroly v sekundách. Výchozí interval je `600` , což je přibližně 10 minut. Chcete-li monitorování zastavit, nastavte hodnotu na `0` . Každá `<ip_address>` Složka obsahuje:

    - `os/`: Informace o všech spuštěných procesech v uzlu. Jedna z kontrol spustí příkaz operačního systému a výsledek uloží do souboru. V systému Linux je příkaz `ps` . Ve Windows použijte `tasklist` .
        - `%Y%m%d%H`: Název dílčí složky je čas do hodiny.
            - `processes_%M`: Soubor končí minutou kontrolního času.
    - `node_disk_usage.csv`: Podrobné použití disku v uzlu.
    - `node_resource_usage.csv`: Přehled využití prostředků uzlu.
    - `processes_resource_usage.csv`: Přehled využití prostředků každého procesu.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Návody se protokolovat z uživatelského skriptu ze vzdáleného kontextu?

ParallelRunStep může na jednom uzlu na základě process_count_per_node spustit více procesů. K uspořádání protokolů z každého procesu na uzlu a kombinování tiskových a log příkazů doporučujeme použít protokolovací nástroj ParallelRunStep, jak je znázorněno níže. Získáte protokolovací nástroj z EntryScript a zpřístupníte protokoly ve složce **logs/uživatel** na portálu.

**Ukázkový vstupní skript s použitím protokolovacího nástroje:**
```python
from azureml_user.parallel_run import EntryScript

def init():
    """ Initialize the node."""
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug("This will show up in files under logs/user on the Azure portal.")


def run(mini_batch):
    """ Accept and return the list back."""
    # This class is in singleton pattern and will return same instance as the one in init()
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug(f"{__file__}: {mini_batch}.")
    ...

    return mini_batch
```

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>Jak můžu předat vstup ze strany, například soubor nebo soubory obsahující vyhledávací tabulku, do všech mých pracovníků?

Uživatel může předat referenční data ke skriptu pomocí parametru side_inputs ParalleRunStep. Všechny datové sady, které jsou zadány jako side_inputs, budou připojeny do všech pracovních uzlů. Uživatel může zjistit umístění připojení pomocí argumentu.

Vytvořte [datovou sadu](/python/api/azureml-core/azureml.core.dataset.dataset?preserve-view=true&view=azure-ml-py) obsahující referenční data a zaregistrujte ji do svého pracovního prostoru. Předejte ho do `side_inputs` parametru vašeho `ParallelRunStep` . Navíc můžete přidat jeho cestu do `arguments` oddílu a snadno tak získat přístup k jeho připojené cestě:

```python
label_config = label_ds.as_named_input("labels_input")
batch_score_step = ParallelRunStep(
    name=parallel_step_name,
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    arguments=["--labels_dir", label_config],
    side_inputs=[label_config],
    parallel_run_config=parallel_run_config,
)
```

Poté, co k němu máte přístup ve skriptu pro odvození (například v metodě Init ()) následujícím způsobem:

```python
parser = argparse.ArgumentParser()
parser.add_argument('--labels_dir', dest="labels_dir", required=True)
args, _ = parser.parse_known_args()

labels_path = args.labels_dir
```

### <a name="how-to-use-input-datasets-with-service-principal-authentication"></a>Jak používat vstupní datové sady s ověřováním instančního objektu?

Uživatel může předat vstupní datové sady s ověřováním instančního objektu použitým v pracovním prostoru. Použití této datové sady v ParallelRunStep vyžaduje, aby se tato datová sada zaregistrovala pro vytvoření konfigurace ParallelRunStep.

```python
service_principal = ServicePrincipalAuthentication(
    tenant_id="***",
    service_principal_id="***",
    service_principal_password="***")
 
ws = Workspace(
    subscription_id="***",
    resource_group="***",
    workspace_name="***",
    auth=service_principal
    )
 
default_blob_store = ws.get_default_datastore() # or Datastore(ws, '***datastore-name***') 
ds = Dataset.File.from_files(default_blob_store, '**path***')
registered_ds = ds.register(ws, '***dataset-name***', create_new_version=True)
```

## <a name="next-steps"></a>Další kroky

* Podívejte se na tyto [poznámkové bloky Jupyter, které demonstrují Azure Machine Learning kanály](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines) .

* Nápovědu k balíčku [AzureML-Pipeline Steps](/python/api/azureml-pipeline-steps/azureml.pipeline.steps?preserve-view=true&view=azure-ml-py) najdete v referenčních informacích k sadě SDK. Zobrazení referenční [dokumentace](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunstep?preserve-view=true&view=azure-ml-py) pro třídu ParallelRunStep.

* Řiďte se [pokročilým kurzem](tutorial-pipeline-batch-scoring-classification.md) používání kanálů pomocí ParallelRunStep. V tomto kurzu se dozvíte, jak předat jiný soubor jako vstup ze strany.
