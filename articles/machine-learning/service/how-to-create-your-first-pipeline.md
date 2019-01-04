---
title: Vytvoření, spuštění a sledovat kanály ML
titleSuffix: Azure Machine Learning service
description: Vytvoření a spuštění služby machine learning kanálu s využitím Azure Machine Learning SDK pro Python. Kanály můžete vytvářet a spravovat pracovní postupy této fáze spojit dohromady machine learning (ML). Tyto fáze zahrnují přípravy dat, trénování modelu, model nasazení a odvozování.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 6c6472b824eefdd1954f3645c69090d1fb5455de
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/22/2018
ms.locfileid: "53754454"
---
# <a name="create-and-run-a-machine-learning-pipeline-by-using-azure-machine-learning-sdk"></a>Vytvoření a spuštění kanálu strojového učení s využitím Azure Machine Learning SDK

V tomto článku se dozvíte, jak vytvářet, publikovat, spouštět a sledovat [kanálu strojového učení](concept-ml-pipelines.md) pomocí [Azure Machine Learning SDK](https://aka.ms/aml-sdk).  Tyto kanály pomáhá vytvářet a spravovat pracovní postupy, které spojit dohromady fáze machine learning. Každá fáze kanálu, jako je například přípravy dat a trénování modelu může zahrnovat jeden nebo více kroků.

Kanály, které vytvoříte jsou viditelná pro členy vaší služby Azure Machine Learning [pracovní prostor](how-to-manage-workspace.md). 

Kanály pomocí cílových výpočetních prostředí vzdálené pro výpočetní výkon a úložný pomocných a konečného data související s příslušný kanál. Kanály může číst a zapisovat data do a z podporované [služby Azure Storage](https://docs.microsoft.com/azure/storage/) umístění.

>[!Note]
>Pokud nemáte předplatné Azure, vytvořte si bezplatný účet, před zahájením. Zkuste [bezplatné nebo placené verzi aplikace služby Azure Machine Learning](http://aka.ms/AMLFree).

## <a name="prerequisites"></a>Požadavky

* [Konfigurace vývojového prostředí](how-to-configure-environment.md) nainstalovat sadu SDK Azure Machine Learning.

* Vytvoření [pracovního prostoru Azure Machine Learning](how-to-configure-environment.md#workspace) pro uložení všech vašich prostředků kanálu. 

 ```python
 ws = Workspace.create(
     name = '<workspace-name>',
     subscription_id = '<subscription-id>',
     resource_group = '<resource-group>',
     location = '<workspace_region>',
     exist_ok = True)
 ```

## <a name="set-up-machine-learning-resources"></a>Nastavení prostředků machine learning

Vytvoření prostředků potřebných ke spuštění kanálu:

* Nastavení úložiště dat používá pro přístup k datům, je potřeba v kanálu kroky.

* Konfigurace `DataReference` objektu tak, aby odkazoval na data, která se nachází, nebo k ní v úložišti dat přístupném.

* Nastavit [cílových výpočetních prostředí](concept-azure-machine-learning-architecture.md#compute-target) podle postupu k kanál se spouští.

### <a name="set-up-a-datastore"></a>Nastavení úložiště dat
Úložiště dat ukládá data pro kanál tak, aby přístup. Každý pracovní prostor má výchozí úložiště. Můžete zaregistrovat další úložiště. 

Při vytváření pracovního prostoru [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) a [úložiště objektů Blob v Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) jsou připojeny k pracovním prostoru ve výchozím nastavení. Služba soubory Azure je výchozí úložiště dat pro pracovní prostor, ale můžete také použít úložiště objektů Blob jako datového úložiště. Další informace najdete v tématu [rozhodování o tom, kdy se má používat soubory Azure, objekty BLOB Azure nebo Azure Disks](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks). 

```python
# Default datastore (Azure file storage)
def_data_store = ws.get_default_datastore() 

# The above call is equivalent to this 
def_data_store = Datastore(ws, "workspacefilestore")

# Get blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")
```

Nahrajte datové soubory nebo adresáře do úložiště dat, aby byla přístupná z vašich kanálů. Tento příklad používá verzi úložiště objektů Blob v úložišti dat:

```python
def_blob_store.upload_files(
    ["./data/20news.pkl"],
    target_path="20newsgroups", 
    overwrite=True)
```

Kanál se skládá z jednoho nebo více kroků. Krok je jednotka spustit na cílové výpočetní prostředí. Kroky mohou využívat zdroje dat. a vygenerovat "zprostředkující" data. Krok můžete vytvořit data, jako jsou modelu, adresář s modelem a závislé soubory, nebo dočasná data. Tato data je pak k dispozici pro další kroky později v kanálu.

### <a name="configure-data-reference"></a>Nastavit odkaz na data

Právě jste vytvořili zdroje dat, který může být odkazováno v kanálu jako vstup do kroku. Zdroj dat v kanálu je reprezentována [odkaz DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) objektu. `DataReference` Objektu odkazuje na data, která se nachází nebo je přístupný z datového úložiště.

```python
blob_input_data = DataReference(
    datastore=def_blob_store,
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")
```

Je reprezentována dočasných dat (nebo výstupní kroku) [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) objektu. `output_data1` je vytvořen jako výstup kroku a použít jako vstup jednu nebo více budoucích krocích. `PipelineData` vytvoří závislost dat mezi kroky a vytvoří objednávku implicitní spuštění v kanálu.

```python
output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

### <a name="set-up-compute"></a>Nastavte výpočetní prostředky

V Azure Machine Learning termín *compute* (nebo *cílové výpočetní prostředí*) odkazuje na počítačích nebo clustery, které provádějí výpočetní kroky ve vašem kanálu machine learning. Můžete například vytvořit výpočetní Azure Machine Learning pro spouštění svých kroků.

```python
compute_name = "aml-compute"
 if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target: ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size, # NC6 is GPU-enabled
                                                                min_nodes = 1, 
                                                                max_nodes = 4)
     # create the compute target
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # Can poll for a minimum number of nodes and for a specific timeout. 
    # If no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current cluster status, use the 'status' property    
    print(compute_target.status.serialize())
```

## <a name="construct-your-pipeline-steps"></a>Vytvoření postupu kanálu k

Nyní jste připraveni definovat kroku profilace. Nejsou k dispozici prostřednictvím sady SDK Azure Machine Learning mnoho předdefinovaných kroků. Je nejzákladnější z těchto kroků `PythonScriptStep`, která spouští skript Pythonu v cílové výpočetní prostředí zadané.

```python
trainStep = PythonScriptStep(
    script_name="train.py",
    arguments=["--input", blob_input_data, "--output", processed_data1],
    inputs=[blob_input_data],
    outputs=[processed_data1],
    compute_target=compute_target,
    source_directory=project_folder
)
```

Po definování svých kroků vytvoříte kanál pomocí některé nebo všechny tyto kroky.

>[!NOTE]
>Žádný soubor nebo datová je odeslána do služby Azure Machine Learning při definování kroky nebo vytváření kanálu.

```python
# list of steps to run
compareModels = [trainStep, extractStep, compareStep]

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compareModels])
```

## <a name="submit-the-pipeline"></a>Odeslat kanálu

Při odesílání kanál služby Azure Machine Learning kontroluje závislosti pro každý krok a odešle snímek zdrojový adresář, který jste zadali. Pokud není zadán žádný zdrojový adresář, se nahraje aktuální místní adresář.

```python
# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
```

Při prvním spuštění kanálu, Azure Machine Learning:

* Stáhne snímek projektu do cílového výpočetního prostředí z úložiště objektů Blob, který je přidružený k pracovnímu prostoru.
* Vytvoří image Dockeru odpovídající každý krok v kanálu.
* Stáhne image dockeru pro každý krok do cílového výpočetního prostředí z registru kontejneru.
* Připojí úložiště dat, pokud `DataReference` zadán objekt v kroku. Pokud připojení není podporován, data je místo toho zkopírován do cílového výpočetního prostředí.
* Spustí krok v cílové výpočetní prostředí, které jsou určené v definici kroku. 
* Vytvoří artefaktům, jako jsou protokoly stdout a stderr, metriky a výstup určené v kroku. Tyto artefakty jsou pak nahraje a uloženy v úložišti dat uživatele výchozí.

![Diagram spuštění experimentu jako kanál](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

## <a name="publish-a-pipeline"></a>Publikování kanálu

Můžete publikovat v kanálu ho později spustit s různými vstupy. Pro koncový bod REST už byla publikována kanál pro příjem parametrů musí parametrizovat kanál před publikováním. 

1. Chcete-li vytvořit kanál parametr, použijte [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) objektu s výchozí hodnotou.

 ```python
 pipeline_param = PipelineParameter(
     name="pipeline_arg", 
     default_value=10)
 ```

2. Přidejte tuto `PipelineParameter` objektu jako parametr pro některý z kroků v kanálu následujícím způsobem:

 ```python
 compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],    
     target=compute_target, 
     source_directory=project_folder)
 ```

3. Publikujte tento kanál, který přijme parametr při vyvolání.

```python
published_pipeline1 = pipeline1.publish(
    name="My_Published_Pipeline", 
    description="My Published Pipeline Description")
```

## <a name="run-a-published-pipeline"></a>Spuštění publikované kanálu

Všechny publikované kanály obsahovaly koncového bodu REST. Tento koncový bod vyvolá spuštění kanálu z externích systémů, jako je například klienti bez Python. Tento koncový bod umožňuje "spravovaný opakovatelnosti" v dávkové bodování a přetrénování scénáře.

K vyvolání spuštění předchozího kanálu, budete potřebovat tokenu hlavičky ověřování Azure Active Directory podle popisu v [AzureCliAuthentication třídy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py).

```python
response = requests.post(published_pipeline1.endpoint, 
    headers=aad_token, 
    json={"ExperimentName": "My_Pipeline",
        "ParameterAssignments": {"pipeline_arg": 20}})
```
## <a name="view-results"></a>Zobrazení výsledků

Zobrazit seznam všech vašich kanálů a jejich podrobnosti o spuštění:
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).  

1. [Zobrazení pracovního prostoru](how-to-manage-workspace.md#view-a-workspace) k nalezení seznamu kanálů.
 ![seznam kanálů strojového učení](./media/how-to-create-your-first-pipeline/list_of_pipelines.png)
 
1. Vyberte konkrétní kanálu pro zobrazení výsledků spuštění.

## <a name="next-steps"></a>Další postup
- Použití [tyto poznámkové bloky Jupyter v Githubu](https://aka.ms/aml-pipeline-readme) prozkoumat machine learning další kanály.
- Přečtěte si nápovědu odkazu sady SDK pro [azureml. kanály core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) balíčku a [azureml. kanály kroky](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) balíčku.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
