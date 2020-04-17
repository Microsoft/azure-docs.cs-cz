---
title: Kanál strojového učení YAML
titleSuffix: Azure Machine Learning
description: Zjistěte, jak definovat kanál strojového učení pomocí souboru YAML. Definice kanálu YAML se používají s rozšířením strojového učení pro azure cli.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: sanpil
author: sanpil
ms.date: 11/11/2019
ms.openlocfilehash: 40e6d7f3d9c28708c5adec26ddc3c0463e75adc0
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81529701"
---
# <a name="define-machine-learning-pipelines-in-yaml"></a>Definování kanálů strojového učení v YAML

Zjistěte, jak definovat kanály strojového učení v [YAML](https://yaml.org/). Při použití rozšíření strojového učení pro Azure CLI, mnoho příkazů souvisejících s kanálem očekávat soubor YAML, který definuje kanál.

V následující tabulce je uvedeno, co je a není aktuálně podporováno při definování kanálu v YAML:

| Typ kroku | Podporované? |
| ----- | :-----: |
| PythonScriptStep | Ano |
| AdlaStep | Ano |
| AzureBatchStep | Ano |
| DatabricksStep | Ano |
| DataTransferStep | Ano |
| Automatický krok MLStep | Ne |
| Krok HyperDriveStep | Ne |
| ModulKrok | Ano |
| Krok MPIStep | Ne |
| Krok odhadu | Ne |

## <a name="pipeline-definition"></a>Definice kanálu

Definice kanálu používá následující klíče, které odpovídají [Pipelines třídy:](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py)

| Klíč YAML | Popis |
| ----- | ----- |
| `name` | Popis kanálu. |
| `parameters` | Parametry do kanálu. |
| `data_reference` | Definuje, jak a kde by měla být data k dispozici v běhu. |
| `default_compute` | Výchozí výpočetní cíl, kde běží všechny kroky v kanálu. |
| `steps` | Kroky použité v kanálu. |

## <a name="parameters"></a>Parametry

V `parameters` části používají následující klíče, které odpovídají [pipelineparameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py) třídy:

| Klíč YAML | Popis |
| ---- | ---- |
| `type` | Typ hodnoty parametru. Platné typy `string` `int`jsou `float` `bool`, `datapath`, , , nebo . |
| `default` | Výchozí hodnota. |

Každý parametr je pojmenován. Například následující úryvek YAML definuje tři `NumIterationsParameter`parametry s názvem , `DataPathParameter`a `NodeCountParameter`:

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

V `data_references` části jsou uvedeny následující klávesy, které odpovídají [datareference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py):

| Klíč YAML | Popis |
| ----- | ----- |
| `datastore` | Úložiště dat odkaz. |
| `path_on_datastore` | Relativní cesta v záložním úložišti pro odkaz na data. |

Každý odkaz na data je obsažen v klíči. Například následující úryvek YAML definuje odkaz na data `employee_data`uložený v klíči s názvem :

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

## <a name="steps"></a>Kroky

Kroky definovat výpočetní prostředí, spolu se soubory pro spuštění v prostředí. Chcete-li definovat typ kroku, `type` použijte klíč:

| Typ kroku | Popis |
| ----- | ----- |
| `AdlaStep` | Spustí skript U-SQL s Azure Data Lake Analytics. Odpovídá [adlaStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.adlastep?view=azure-ml-py) třídy. |
| `AzureBatchStep` | Spustí úlohy pomocí Azure Batch. Odpovídá Třídě [AzureBatchStep.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep?view=azure-ml-py) |
| `DatabricsStep` | Přidá poznámkový blok Databricks, skript Pythonu nebo JAR. Odpovídá [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricksstep?view=azure-ml-py) třídy. |
| `DataTransferStep` | Přenáší data mezi možnostmi úložiště. Odpovídá třídě [DataTransferStep.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py) |
| `PythonScriptStep` | Spustí skript Pythonu. Odpovídá třídě [PythonScriptStep.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) |

### <a name="adla-step"></a>Krok ADLA

| Klíč YAML | Popis |
| ----- | ----- |
| `script_name` | Název skriptu U-SQL (vzhledem `source_directory`k ). |
| `compute_target` | Výpočetní cíl Azure Data Lake, který se má použít pro tento krok. |
| `parameters` | [Parametry](#parameters) do kanálu. |
| `inputs` | Vstupy mohou být [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)nebo [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | Výstupy mohou být [pipelinedata](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) nebo [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `source_directory` | Adresář, který obsahuje skript, sestavení atd. |
| `priority` | Hodnota priority, která má být pro aktuální úlohu. |
| `params` | Slovník párů název-hodnota. |
| `degree_of_parallelism` | Stupeň paralelismu použít pro tuto práci. |
| `runtime_version` | Runtime verze modulu Data Lake Analytics. |
| `allow_reuse` | Určuje, zda má krok znovu použít předchozí výsledky při opětovném spuštění se stejným nastavením. |

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

### <a name="azure-batch-step"></a>Krok dávky Azure

| Klíč YAML | Popis |
| ----- | ----- |
| `compute_target` | Azure Batch výpočetní cíl použít pro tento krok. |
| `inputs` | Vstupy mohou být [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)nebo [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | Výstupy mohou být [pipelinedata](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) nebo [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `source_directory` | Adresář, který obsahuje binární soubory modulu, spustitelný soubor, sestavení atd. |
| `executable` | Název příkazu/spustitelného souboru, který bude spuštěn jako součást této úlohy. |
| `create_pool` | Logický příznak označující, zda chcete vytvořit fond před spuštěním úlohy. |
| `delete_batch_job_after_finish` | Logický příznak označující, zda má být úloha po dokončení odstraněna z účtu Dávka. |
| `delete_batch_pool_after_finish` | Logický příznak označující, zda má být fond po dokončení úlohy odstraněn. |
| `is_positive_exit_code_failure` | Logický příznak označující, pokud se úloha nezdaří, pokud se úloha ukončí s kladným kódem. |
| `vm_image_urn` | Pokud `create_pool` `True`je , a `VirtualMachineConfiguration`virtuální hod používá . |
| `pool_id` | ID fondu, kde bude úloha spuštěna. |
| `allow_reuse` | Určuje, zda má krok znovu použít předchozí výsledky při opětovném spuštění se stejným nastavením. |

Následující příklad obsahuje definici kroku Azure Batch:

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

### <a name="databricks-step"></a>Krok Databricks

| Klíč YAML | Popis |
| ----- | ----- |
| `compute_target` | Azure Databricks výpočetní cíl použít pro tento krok. |
| `inputs` | Vstupy mohou být [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)nebo [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | Výstupy mohou být [pipelinedata](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) nebo [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `run_name` | Název v Databricks pro tento běh. |
| `source_directory` | Adresář, který obsahuje skript a další soubory. |
| `num_workers` | Statický počet pracovníků pro Databricks spustit clusteru. |
| `runconfig` | Cesta k `.runconfig` souboru. Tento soubor je reprezentace YAML třídy [RunConfiguration.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) Další informace o struktuře tohoto souboru naleznete v tématu [runconfigschema.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Určuje, zda má krok znovu použít předchozí výsledky při opětovném spuštění se stejným nastavením. |

Následující příklad obsahuje krok Databricks:

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

| Klíč YAML | Popis |
| ----- | ----- |
| `compute_target` | Výpočetní cíl Azure Data Factory, který se má použít pro tento krok. |
| `source_data_reference` | Vstupní připojení, které slouží jako zdroj operací přenosu dat. Podporované hodnoty jsou [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)nebo [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `destination_data_reference` | Vstupní připojení, které slouží jako cíl operací přenosu dat. Podporované hodnoty jsou [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) a [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `allow_reuse` | Určuje, zda má krok znovu použít předchozí výsledky při opětovném spuštění se stejným nastavením. |

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

| Klíč YAML | Popis |
| ----- | ----- |
| `inputs` | Vstupy mohou být [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)nebo [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | Výstupy mohou být [pipelinedata](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) nebo [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `script_name` | Název skriptu Pythonu (vzhledem k `source_directory`). |
| `source_directory` | Adresář, který obsahuje skript, conda prostředí, atd. |
| `runconfig` | Cesta k `.runconfig` souboru. Tento soubor je reprezentace YAML třídy [RunConfiguration.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) Další informace o struktuře tohoto souboru naleznete v tématu [runconfig.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Určuje, zda má krok znovu použít předchozí výsledky při opětovném spuštění se stejným nastavením. |

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

### <a name="pipeline-with-multiple-steps"></a>Kanál s několika kroky 

| Klíč YAML | Popis |
| ----- | ----- |
| `steps` | Posloupnost jedné nebo více definic PipelineStep. Všimněte `destination` si, že `outputs` jeden krok se `inputs` stal klíčem k .| 

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

Při definování plánu pro kanál, může být buď úložiště dat aktivované nebo opakované na základě časového intervalu. Níže jsou uvedeny klíče používané k definování plánu:

| Klíč YAML | Popis |
| ----- | ----- |
| `description` | Popis plánu. |
| `recurrence` | Obsahuje nastavení opakování, pokud se plán opakuje. |
| `pipeline_parameters` | Všechny parametry, které jsou vyžadovány kanálu. |
| `wait_for_provisioning` | Zda čekat na zřizování plánu k dokončení. |
| `wait_timeout` | Počet sekund čekání před vypršením časového limitu. |
| `datastore_name` | Úložiště dat pro sledování změněných nebo přidaných objektů BLOB. |
| `polling_interval` | Jak dlouho, v minutách, mezi dotazování pro upravené/přidané objekty BLOB. Výchozí hodnota: 5 minut. Podporováno pouze pro plány úložiště dat. |
| `data_path_parameter_name` | Název parametru kanálu cesty dat nastavit se změněnou cestu objektu blob. Podporováno pouze pro plány úložiště dat. |
| `continue_on_step_failure` | Zda pokračovat v provádění dalších kroků v odeslané PipelineRun, pokud krok selže. Pokud je k dispozici, `continue_on_step_failure` bude přepsat nastavení kanálu.
| `path_on_datastore` | Nepovinný parametr. Cesta na úložiště dat sledovat upravené/přidané objekty BLOB. Cesta je pod kontejnerem pro úložiště dat, takže skutečná cesta,`path_on_datastore`kterou plán monitoruje, je kontejner/ . Pokud žádný, je sledován kontejner úložiště dat. Dodatky/úpravy provedené v podsložce nejsou sledovány. `path_on_datastore` Podporováno pouze pro plány úložiště dat. |

Následující příklad obsahuje definici plánu aktivovaná úložištěm dat:

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

Při definování **opakovaného plánu**použijte následující `recurrence`klávesy v části :

| Klíč YAML | Popis |
| ----- | ----- |
| `frequency` | Jak často se plán opakuje. Platné hodnoty `"Minute"` `"Hour"`jsou `"Day"` `"Week"`, `"Month"`, , , nebo . |
| `interval` | Jak často se plán požáry. Hodnota celého čísla je počet časových jednotek, které mají čekat, až se plán znovu aktivuje. |
| `start_time` | Čas zahájení plánu. Formát řetězce hodnoty je `YYYY-MM-DDThh:mm:ss`. Pokud není k dispozici žádný čas zahájení, první úloha se spustí okamžitě a budoucí úlohy jsou spuštěny na základě plánu. Pokud je čas zahájení v minulosti, první úloha se spustí při dalším vypočítaném běhu. |
| `time_zone` | Časové pásmo pro čas zahájení. Pokud není k dispozici žádné časové pásmo, používá se čas UTC. |
| `hours` | Pokud `frequency` `"Day"` je `"Week"`nebo , můžete zadat jedno nebo více celá čísla od 0 do 23, oddělené čárkami, jako hodiny dne, kdy by měl být spuštěn kanál. Pouze `time_of_day` `hours` nebo `minutes` a mohou být použity. |
| `minutes` | Pokud `frequency` `"Day"` je `"Week"`nebo , můžete zadat jedno nebo více celá čísla od 0 do 59, oddělené čárkami, jako minuty hodiny, kdy by měl být spuštěn kanál. Pouze `time_of_day` `hours` nebo `minutes` a mohou být použity. |
| `time_of_day` | Pokud `frequency` `"Day"` je `"Week"`nebo , můžete zadat denní dobu pro spuštění plánu. Formát řetězce hodnoty je `hh:mm`. Pouze `time_of_day` `hours` nebo `minutes` a mohou být použity. |
| `week_days` | Pokud `frequency` `"Week"`je , můžete zadat jeden nebo více dnů, oddělené čárkami, kdy by měl být plán spuštěn. Platné hodnoty `"Monday"` `"Tuesday"`jsou `"Wednesday"` `"Thursday"`, `"Friday"` `"Saturday"`, `"Sunday"`, , , a . |

Následující příklad obsahuje definici opakovaného plánu:

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

Přečtěte si, jak [používat rozšíření příkazového příkazového příkazu pro Azure Machine Learning](reference-azure-machine-learning-cli.md).
