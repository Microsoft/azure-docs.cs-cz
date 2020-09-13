---
title: YAML kanálu Machine Learning
titleSuffix: Azure Machine Learning
description: Naučte se definovat kanál strojového učení pomocí souboru YAML. Definice kanálů YAML se používají s rozšířením Machine Learning pro rozhraní příkazového řádku Azure CLI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: nilsp
author: NilsPohlmann
ms.date: 07/31/2020
ms.custom: devx-track-python
ms.openlocfilehash: 0f7ee0e18187b0a5d8ad1eb83dbda9734a9ff7de
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89650181"
---
# <a name="define-machine-learning-pipelines-in-yaml"></a>Definování kanálů strojového učení v YAML

Naučte se definovat kanály strojového učení v [YAML](https://yaml.org/). Při použití rozšíření Machine Learning pro rozhraní příkazového řádku Azure se v mnoha příkazech souvisejících s kanály očekává soubor YAML, který definuje kanál.

Následující tabulka uvádí, co je a není aktuálně podporováno při definování kanálu v YAML:

| Typ kroku | Podporované? |
| ----- | :-----: |
| PythonScriptStep | Yes |
| ParallelRunStep | Yes |
| AdlaStep | Yes |
| AzureBatchStep | Yes |
| DatabricksStep | Yes |
| DataTransferStep | Yes |
| AutoMLStep | No |
| HyperDriveStep | No |
| ModuleStep | Yes |
| MPIStep | No |
| EstimatorStep | No |

## <a name="pipeline-definition"></a>Definice kanálu

Definice kanálu používá následující klíče, které odpovídají třídě [kanálů](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py&preserve-view=true) :

| YAML klíč | Popis |
| ----- | ----- |
| `name` | Popis kanálu |
| `parameters` | Parametry kanálu. |
| `data_reference` | Definuje, jak a kde mají být data zpřístupněna při spuštění. |
| `default_compute` | Výchozí výpočetní cíl, ve kterém se spouští všechny kroky v kanálu |
| `steps` | Kroky použité v kanálu. |

## <a name="parameters"></a>Parametry

`parameters`Oddíl používá následující klíče, které odpovídají třídě [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py&preserve-view=true) :

| YAML klíč | Popis |
| ---- | ---- |
| `type` | Typ hodnoty parametru. Platné typy jsou `string` , `int` , `float` , `bool` nebo `datapath` . |
| `default` | Výchozí hodnota. |

Každý parametr má název. Například následující fragment kódu YAML definuje tři parametry s názvem `NumIterationsParameter` , `DataPathParameter` a `NodeCountParameter` :

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        NumIterationsParameter:
            type: int
            default: 40
        DataPathParameter:
            type: datapath
            default:
                datastore: workspaceblobstore
                path_on_datastore: sample2.txt
        NodeCountParameter:
            type: int
            default: 4
```

## <a name="data-reference"></a>Reference pro data

`data_references`Oddíl používá následující klíče, které odpovídají [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py&preserve-view=true):

| YAML klíč | Popis |
| ----- | ----- |
| `datastore` | Úložiště dat, na které se má odkazovat |
| `path_on_datastore` | Relativní cesta v úložišti zálohování pro datový odkaz. |

Každý odkaz na data je obsažen v klíči. Například následující fragment kódu YAML definuje odkaz na data uložený v klíči s názvem `employee_data` :

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        employee_data:
            datastore: adftestadla
            path_on_datastore: "adla_sample/sample_input.csv"
```

## <a name="steps"></a>Postup

Kroky definují výpočetní prostředí spolu se soubory, které se mají spustit v prostředí. Chcete-li definovat typ kroku, použijte `type` klíč:

| Typ kroku | Popis |
| ----- | ----- |
| `AdlaStep` | Spustí skript U-SQL s Azure Data Lake Analytics. Odpovídá třídě [AdlaStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.adlastep?view=azure-ml-py&preserve-view=true) . |
| `AzureBatchStep` | Spouští úlohy pomocí Azure Batch. Odpovídá třídě [AzureBatchStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep?view=azure-ml-py&preserve-view=true) . |
| `DatabricsStep` | Přidá Poznámkový blok datacihly, skript Pythonu nebo JAR. Odpovídá třídě [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricksstep?view=azure-ml-py&preserve-view=true) . |
| `DataTransferStep` | Přenáší data mezi možnostmi úložiště. Odpovídá třídě [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py&preserve-view=true) . |
| `PythonScriptStep` | Spustí skript Pythonu. Odpovídá třídě [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py&preserve-view=true) . |
| `ParallelRunStep` | Spustí skript Pythonu pro asynchronní zpracování velkých objemů dat a souběžně. Odpovídá třídě [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py&preserve-view=true) . |

### <a name="adla-step"></a>Krok ADLA

| YAML klíč | Popis |
| ----- | ----- |
| `script_name` | Název skriptu U-SQL (relativně k `source_directory` příponě). |
| `compute_target` | Výpočetní cíl Azure Data Lake, který se má použít pro tento krok. |
| `parameters` | [Parametry](#parameters) kanálu. |
| `inputs` | Vstupy můžou být [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py&preserve-view=true), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py&preserve-view=true), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py&preserve-view=true), [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py&preserve-view=true), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py&preserve-view=true)nebo [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py&preserve-view=true). |
| `outputs` | Výstupy můžou být buď [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py&preserve-view=true) nebo [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py&preserve-view=true). |
| `source_directory` | Adresář, který obsahuje skript, sestavení atd. |
| `priority` | Hodnota priority, která se má použít pro aktuální úlohu. |
| `params` | Slovník párů název-hodnota. |
| `degree_of_parallelism` | Stupeň paralelismu, který se má použít pro tuto úlohu. |
| `runtime_version` | Běhová verze modulu Data Lake Analytics. |
| `allow_reuse` | Určuje, zda má být při opětovném spuštění se stejným nastavením krok znovu použit předchozí výsledky. |

Následující příklad obsahuje definici kroku ADLA:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        employee_data:
            datastore: adftestadla
            path_on_datastore: "adla_sample/sample_input.csv"
    default_compute: adlacomp
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "AdlaStep"
            name: "MyAdlaStep"
            script_name: "sample_script.usql"
            source_directory: "D:\\scripts\\Adla"
            inputs:
                employee_data:
                    source: employee_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: adftestadla
                    bind_mode: mount
```

### <a name="azure-batch-step"></a>Azure Batch krok

| YAML klíč | Popis |
| ----- | ----- |
| `compute_target` | Výpočetní cíl Azure Batch, který se má použít pro tento krok. |
| `inputs` | Vstupy můžou být [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py&preserve-view=true), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py&preserve-view=true), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py&preserve-view=true), [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py&preserve-view=true), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py&preserve-view=true)nebo [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py&preserve-view=true). |
| `outputs` | Výstupy můžou být buď [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py&preserve-view=true) nebo [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py&preserve-view=true). |
| `source_directory` | Adresář, který obsahuje binární soubory modulu, spustitelný soubor, sestavení atd. |
| `executable` | Název příkazu nebo spustitelného souboru, který bude spuštěn jako součást této úlohy. |
| `create_pool` | Logický příznak, který označuje, zda se má před spuštěním úlohy vytvořit fond. |
| `delete_batch_job_after_finish` | Logický příznak, který označuje, jestli se má úloha Odstranit z účtu Batch po jeho dokončení. |
| `delete_batch_pool_after_finish` | Logický příznak označující, zda se má po dokončení úlohy Odstranit fond. |
| `is_positive_exit_code_failure` | Logický příznak, který označuje, jestli se úloha nezdařila, pokud se úloha ukončí s kladným kódem. |
| `vm_image_urn` | Pokud `create_pool` je `True` , a virtuální počítač používá `VirtualMachineConfiguration` . |
| `pool_id` | ID fondu, ve kterém se úloha spustí |
| `allow_reuse` | Určuje, zda má být při opětovném spuštění se stejným nastavením krok znovu použit předchozí výsledky. |

Následující příklad obsahuje Azure Batch definice kroku:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        input:
            datastore: workspaceblobstore
            path_on_datastore: "input.txt"
    default_compute: testbatch
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "AzureBatchStep"
            name: "MyAzureBatchStep"
            pool_id: "MyPoolName"
            create_pool: true
            executable: "azurebatch.cmd"
            source_directory: "D:\\scripts\\AureBatch"
            allow_reuse: false
            inputs:
                input:
                    source: input
            outputs:
                output:
                    destination: output
                    datastore: workspaceblobstore
```

### <a name="databricks-step"></a>Krok datacihly

| YAML klíč | Popis |
| ----- | ----- |
| `compute_target` | Výpočetní cíl Azure Databricks, který se má použít pro tento krok. |
| `inputs` | Vstupy můžou být [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py&preserve-view=true), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py&preserve-view=true), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py&preserve-view=true), [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py&preserve-view=true), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py&preserve-view=true)nebo [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py&preserve-view=true). |
| `outputs` | Výstupy můžou být buď [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py&preserve-view=true) nebo [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py&preserve-view=true). |
| `run_name` | Název v datacihlách pro tento běh. |
| `source_directory` | Adresář, který obsahuje skript a další soubory. |
| `num_workers` | Statický počet pracovníků pro spuštění clusteru datacihly. |
| `runconfig` | Cesta k `.runconfig` souboru Tento soubor je YAML reprezentace třídy [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py&preserve-view=true) . Další informace o struktuře tohoto souboru najdete v tématu [runconfigschema.js](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Určuje, zda má být při opětovném spuštění se stejným nastavením krok znovu použit předchozí výsledky. |

Následující příklad obsahuje krok datacihly:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        adls_test_data:
            datastore: adftestadla
            path_on_datastore: "testdata"
        blob_test_data:
            datastore: workspaceblobstore
            path_on_datastore: "dbtest"
    default_compute: mydatabricks
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "DatabricksStep"
            name: "MyDatabrickStep"
            run_name: "DatabricksRun"
            python_script_name: "train-db-local.py"
            source_directory: "D:\\scripts\\Databricks"
            num_workers: 1
            allow_reuse: true
            inputs:
                blob_test_data:
                    source: blob_test_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="data-transfer-step"></a>Krok přenosu dat

| YAML klíč | Popis |
| ----- | ----- |
| `compute_target` | Výpočetní cíl Azure Data Factory, který se má použít pro tento krok. |
| `source_data_reference` | Vstupní připojení, které slouží jako zdroj operací přenosu dat. Podporované hodnoty jsou [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py&preserve-view=true), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py&preserve-view=true), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py&preserve-view=true), [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py&preserve-view=true), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py&preserve-view=true)nebo [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py&preserve-view=true). |
| `destination_data_reference` | Vstupní připojení, které slouží jako cíl operací přenosu dat. Podporované hodnoty jsou [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py&preserve-view=true) a [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py&preserve-view=true). |
| `allow_reuse` | Určuje, zda má být při opětovném spuštění se stejným nastavením krok znovu použit předchozí výsledky. |

Následující příklad obsahuje krok přenosu dat:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        adls_test_data:
            datastore: adftestadla
            path_on_datastore: "testdata"
        blob_test_data:
            datastore: workspaceblobstore
            path_on_datastore: "testdata"
    default_compute: adftest
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "DataTransferStep"
            name: "MyDataTransferStep"
            adla_compute_name: adftest
            source_data_reference:
                adls_test_data:
                    source: adls_test_data
            destination_data_reference:
                blob_test_data:
                    source: blob_test_data
```

### <a name="python-script-step"></a>Krok skriptu Pythonu

| YAML klíč | Popis |
| ----- | ----- |
| `inputs` | Vstupy můžou být [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py&preserve-view=true), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py&preserve-view=true), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py&preserve-view=true), [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py&preserve-view=true), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py&preserve-view=true)nebo [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py&preserve-view=true). |
| `outputs` | Výstupy můžou být buď [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py&preserve-view=true) nebo [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py&preserve-view=true). |
| `script_name` | Název skriptu Pythonu (relativní vzhledem k `source_directory` ) |
| `source_directory` | Adresář, který obsahuje skript, prostředí conda atd. |
| `runconfig` | Cesta k `.runconfig` souboru Tento soubor je YAML reprezentace třídy [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py&preserve-view=true) . Další informace o struktuře tohoto souboru najdete v tématu [runconfig.js](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Určuje, zda má být při opětovném spuštění se stejným nastavením krok znovu použit předchozí výsledky. |

Následující příklad obsahuje krok skriptu Pythonu:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        DataReference1:
            datastore: workspaceblobstore
            path_on_datastore: testfolder/sample.txt
    default_compute: cpu-cluster
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "PythonScriptStep"
            name: "MyPythonScriptStep"
            script_name: "train.py"
            allow_reuse: True
            source_directory: "D:\\scripts\\PythonScript"
            inputs:
                InputData:
                    source: DataReference1
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="parallel-run-step"></a>Krok paralelního spuštění

| YAML klíč | Popis |
| ----- | ----- |
| `inputs` | Vstupy můžou být [datová sada](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py&preserve-view=true), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py&preserve-view=true)nebo [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py&preserve-view=true). |
| `outputs` | Výstupy můžou být buď [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py&preserve-view=true) nebo [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py&preserve-view=true). |
| `script_name` | Název skriptu Pythonu (relativní vzhledem k `source_directory` ) |
| `source_directory` | Adresář, který obsahuje skript, prostředí conda atd. |
| `parallel_run_config` | Cesta k `parallel_run_config.yml` souboru Tento soubor je YAML reprezentace třídy [ParallelRunConfig](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunconfig?view=azure-ml-py&preserve-view=true) . |
| `allow_reuse` | Určuje, zda má být při opětovném spuštění se stejným nastavením krok znovu použit předchozí výsledky. |

Následující příklad obsahuje krok paralelního spuštění:

```yaml
pipeline:
    description: SamplePipelineFromYaml
    default_compute: cpu-cluster
    data_references:
        MyMinistInput:
            dataset_name: mnist_sample_data
    parameters:
        PipelineParamTimeout:
            type: int
            default: 600
    steps:        
        Step1:
            parallel_run_config: "yaml/parallel_run_config.yml"
            type: "ParallelRunStep"
            name: "parallel-run-step-1"
            allow_reuse: True
            arguments:
            - "--progress_update_timeout"
            - parameter:timeout_parameter
            - "--side_input"
            - side_input:SideInputData
            parameters:
                timeout_parameter:
                    source: PipelineParamTimeout
            inputs:
                InputData:
                    source: MyMinistInput
            side_inputs:
                SideInputData:
                    source: Output4
                    bind_mode: mount
            outputs:
                OutputDataStep2:
                    destination: Output5
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="pipeline-with-multiple-steps"></a>Kanál s více kroky 

| YAML klíč | Popis |
| ----- | ----- |
| `steps` | Sekvence jedné nebo více definicí PipelineStep. Všimněte si, že `destination` klíče jednoho kroku `outputs` se stanou `source` klíči pro `inputs` Další krok.| 

```yaml
pipeline:
    name: SamplePipelineFromYAML
    description: Sample multistep YAML pipeline
    data_references:
        TitanicDS:
            dataset_name: 'titanic_ds'
            bind_mode: download
    default_compute: cpu-cluster
    steps:
        Dataprep:
            type: "PythonScriptStep"
            name: "DataPrep Step"
            compute: cpu-cluster
            runconfig: ".\\default_runconfig.yml"
            script_name: "prep.py"
            arguments:
            - '--train_path'
            - output:train_path
            - '--test_path'
            - output:test_path
            allow_reuse: True
            inputs:
                titanic_ds:
                    source: TitanicDS
                    bind_mode: download
            outputs:
                train_path:
                    destination: train_csv
                    datastore: workspaceblobstore
                test_path:
                    destination: test_csv
        Training:
            type: "PythonScriptStep"
            name: "Training Step"
            compute: cpu-cluster
            runconfig: ".\\default_runconfig.yml"
            script_name: "train.py"
            arguments:
            - "--train_path"
            - input:train_path
            - "--test_path"
            - input:test_path
            inputs:
                train_path:
                    source: train_csv
                    bind_mode: download
                test_path:
                    source: test_csv
                    bind_mode: download

```

## <a name="schedules"></a>Plány

Při definování plánu pro kanál může to být buď úložiště dat, aktivované nebo opakované v závislosti na časovém intervalu. Níže jsou uvedené klíče, které slouží k definování plánu:

| YAML klíč | Popis |
| ----- | ----- |
| `description` | Popis plánu |
| `recurrence` | Obsahuje nastavení opakování, pokud se plán opakuje. |
| `pipeline_parameters` | Všechny parametry, které kanál vyžaduje. |
| `wait_for_provisioning` | Zda se má čekat na dokončení zřizování plánu. |
| `wait_timeout` | Počet sekund, po které se má čekat před vypršením časového limitu |
| `datastore_name` | Úložiště dat, které se má monitorovat pro upravené nebo přidané objekty blob |
| `polling_interval` | Doba mezi dotazem na změněné nebo přidané objekty BLOB v minutách Výchozí hodnota: 5 minut. Podporováno pouze pro plány úložiště dat. |
| `data_path_parameter_name` | Název parametru kanálu pro cestu k datům, který se má nastavit se změněnou cestou k objektu BLOB. Podporováno pouze pro plány úložiště dat. |
| `continue_on_step_failure` | Určuje, zda pokračovat v provádění dalších kroků v odeslaných PipelineRun, pokud nějaký krok selhává. Pokud je tato možnost zadána, přepíše `continue_on_step_failure` Nastavení kanálu.
| `path_on_datastore` | Nepovinný parametr. Cesta v úložišti dat, která se má monitorovat pro upravené nebo přidané objekty blob Cesta je pod kontejnerem úložiště dat, takže skutečná cesta, ke které je plán monitorování, je kontejnerem `path_on_datastore` . Pokud žádný není, je monitorovaný kontejner úložiště dat. Přidání/úpravy provedené v podsložce `path_on_datastore` nejsou monitorovány. Podporováno pouze pro plány úložiště dat. |

Následující příklad obsahuje definici pro plán aktivovaný úložištěm dat:

```yaml
Schedule: 
      description: "Test create with datastore" 
      recurrence: ~ 
      pipeline_parameters: {} 
      wait_for_provisioning: True 
      wait_timeout: 3600 
      datastore_name: "workspaceblobstore" 
      polling_interval: 5 
      data_path_parameter_name: "input_data" 
      continue_on_step_failure: None 
      path_on_datastore: "file/path" 
```

Při definování **opakovaného plánu**použijte následující klíče `recurrence` :

| YAML klíč | Popis |
| ----- | ----- |
| `frequency` | Jak často se plán opakuje. Platné hodnoty jsou `"Minute"` , `"Hour"` , `"Day"` , `"Week"` nebo `"Month"` . |
| `interval` | Jak často je plán aktivován. Celočíselná hodnota představuje počet časových jednotek, které se mají počkat, dokud se plán znovu neaktivuje. |
| `start_time` | Čas spuštění plánu. Formát řetězce hodnoty je `YYYY-MM-DDThh:mm:ss` . Pokud se nezadá žádný počáteční čas, první zatížení se spustí okamžitě a v závislosti na plánu se spustí další úlohy. Pokud je čas začátku v minulosti, první úloha se spustí při další vypočtené době spuštění. |
| `time_zone` | Časové pásmo pro čas spuštění. Pokud není zadáno žádné časové pásmo, bude použit čas UTC. |
| `hours` | Pokud `frequency` je `"Day"` nebo `"Week"` , můžete zadat jedno nebo více celých čísel od 0 do 23, které jsou odděleny čárkami, jako hodiny dne, kdy se má kanál spustit. `time_of_day` `hours` Lze použít pouze nebo a `minutes` . |
| `minutes` | Pokud `frequency` je `"Day"` nebo `"Week"` , můžete zadat jedno nebo více celých čísel od 0 do 59, které jsou odděleny čárkami, jako minuty hodiny, kdy se má kanál spustit. `time_of_day` `hours` Lze použít pouze nebo a `minutes` . |
| `time_of_day` | Pokud `frequency` je `"Day"` nebo `"Week"` , můžete zadat denní dobu, po kterou má být plán spuštěn. Formát řetězce hodnoty je `hh:mm` . `time_of_day` `hours` Lze použít pouze nebo a `minutes` . |
| `week_days` | Pokud `frequency` je `"Week"` , můžete zadat jeden nebo více dní, které jsou odděleny čárkami, kdy se má plán spustit. Platné hodnoty jsou `"Monday"` , `"Tuesday"` , `"Wednesday"` , `"Thursday"` , `"Friday"` , a `"Saturday"` `"Sunday"` . |

Následující příklad obsahuje definici pro opakovaný plán:

```yaml
Schedule: 
    description: "Test create with recurrence" 
    recurrence: 
        frequency: Week # Can be "Minute", "Hour", "Day", "Week", or "Month". 
        interval: 1 # how often fires 
        start_time: 2019-06-07T10:50:00 
        time_zone: UTC 
        hours: 
        - 1 
        minutes: 
        - 0 
        time_of_day: null 
        week_days: 
        - Friday 
    pipeline_parameters: 
        'a': 1 
    wait_for_provisioning: True 
    wait_timeout: 3600 
    datastore_name: ~ 
    polling_interval: ~ 
    data_path_parameter_name: ~ 
    continue_on_step_failure: None 
    path_on_datastore: ~ 
```

## <a name="next-steps"></a>Další kroky

Naučte se [používat rozšíření CLI pro Azure Machine Learning](reference-azure-machine-learning-cli.md).
