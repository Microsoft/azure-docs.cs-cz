---
title: Ladění třídy ParallelRunStep a řešení souvisejících potíží
titleSuffix: Azure Machine Learning
description: Ladění a řešení potíží s ParallelRunStep v kanálech strojového učení v sadě Azure Machine Learning SDK pro Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: troubleshooting
ms.reviewer: jmartens, larryfr, vaidyas, laobri, tracych
ms.author: trmccorm
author: tmccrmck
ms.date: 09/23/2020
ms.openlocfilehash: 09f75e9e8f972ec44098e119dc5b30bd44638918
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630460"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>Ladění třídy ParallelRunStep a řešení souvisejících potíží


V tomto článku se dozvíte, jak ladit a řešit potíže se třídou [ParallelRunStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep?preserve-view=true&view=azure-ml-py) z [Azure Machine Learning SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py).

## <a name="testing-scripts-locally"></a>Místní testování skriptů

Přečtěte si [část testování skriptů místně](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines) pro kanály strojového učení. Vaše ParallelRunStep se spouští jako krok v kanálech ML, takže stejná odpověď platí i pro obě.

## <a name="debugging-scripts-from-remote-context"></a>Ladění skriptů ze vzdáleného kontextu

Přechod z ladění skriptu bodování místně na ladění vyhodnocovacího skriptu ve skutečném kanálu může být obtížné. Informace o hledání protokolů na portálu najdete v [části kanály strojového učení na webu ladění skriptů ze vzdáleného kontextu](how-to-debug-pipelines.md). Informace v této části platí i pro ParallelRunStep.

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

Můžete také vyhledat informace o využití prostředků pro jednotlivé pracovní procesy. Tyto informace jsou ve formátu CSV a jsou umístěné na adrese `~/logs/sys/perf/<ip_address>/node_resource_usage.csv` . Informace o každém procesu jsou k dispozici v části `~logs/sys/perf/<ip_address>/processes_resource_usage.csv` .

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
    tenant_id="**_",
    service_principal_id="_*_",
    service_principal_password="_*_")
 
ws = Workspace(
    subscription_id="_*_",
    resource_group="_*_",
    workspace_name="_*_",
    auth=service_principal
    )
 
default_blob_store = ws.get_default_datastore() # or Datastore(ws, '_*_datastore-name_*_') 
ds = Dataset.File.from_files(default_blob_store, '_*path**_')
registered_ds = ds.register(ws, '_*_dataset-name_*_', create_new_version=True)
```

## <a name="next-steps"></a>Další kroky

_ Zobrazit tyto [poznámkové bloky Jupyter, které demonstrují Azure Machine Learning kanálů](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)

* Nápovědu k balíčku [AzureML-Pipeline Steps](/python/api/azureml-pipeline-steps/azureml.pipeline.steps?preserve-view=true&view=azure-ml-py) najdete v referenčních informacích k sadě SDK. Zobrazení referenční [dokumentace](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunstep?preserve-view=true&view=azure-ml-py) pro třídu ParallelRunStep.

* Řiďte se [pokročilým kurzem](tutorial-pipeline-batch-scoring-classification.md) používání kanálů pomocí ParallelRunStep. V tomto kurzu se dozvíte, jak předat jiný soubor jako vstup ze strany.