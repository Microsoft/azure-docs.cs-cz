---
title: Nastavení cílových výpočetních prostředí pro trénování modelu s využitím služby Azure Machine Learning | Dokumentace Microsoftu
description: Zjistěte, jak vybrat a nakonfigurovat prostředí školení (cílových výpočetních prostředí) použít k trénování modelů strojového učení. Služba Azure Machine Learning umožňuje snadno přepněte školení prostředí. Spusťte místně školení a pokud je potřeba škálovat na více systémů, přepněte do cílové výpočetní prostředí založené na cloudu.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: larryfr
manager: cgronlun
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: 7eacc475145dac61db1717f1860e22cedd022262
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51231443"
---
# <a name="select-and-use-a-compute-target-to-train-your-model"></a>Vyberte a použijte cílové výpočetní prostředí k natrénování modelu

Se službou Azure Machine Learning můžete trénování modelu v různých prostředích. Těchto prostředí volá __cílových výpočetních prostředí__, může být místní nebo v cloudu. V tomto dokumentu se dozvíte o cílových podporovaných výpočetních prostředí a jejich použití.

Cílové výpočetní prostředí je prostředek, který spouští cvičný skript nebo po jeho nasazení jako webové služby je hostitelem vašeho modelu. Jsou možné vytvářet a spravovat pomocí Azure Machine Learning SDK nebo rozhraní příkazového řádku. Pokud máte cílových výpočetních prostředí, které byly vytvořeny jiným procesem (například webu Azure portal nebo rozhraní příkazového řádku Azure), můžete jejich připojením k pracovnímu prostoru Azure Machine Learning service.

Můžete začít s místní spuštění v počítači a potom vertikálně nebo do jiných prostředí, jako je virtuálním počítačům pro datové vědy s GPU nebo Azure Batch AI. 

>[!NOTE]
> V tomto článku kódu byl testován s Azure Machine Learning SDK verze 0.168 

## <a name="supported-compute-targets"></a>Cílových podporovaných výpočetních prostředí

Služba Azure Machine Learning podporuje následující cílových výpočetních prostředí:

|Cílové výpočetní prostředí| Akcelerace GPU | Automatizované hyperparametrů | Výběr automatizované modelu | Je možné v kanálech|
|----|:----:|:----:|:----:|:----:|
|[Místní počítač](#local)| Možná | &nbsp; | ✓ | &nbsp; |
|[Virtuální počítač pro datové vědy (DSVM)](#dsvm) | ✓ | ✓ | ✓ | ✓ |
|[Azure Batch AI](#batch)| ✓ | ✓ | ✓ | ✓ |
|[Azure Databricks](#databricks)| &nbsp; | &nbsp; | &nbsp; | ✓[*](#pipeline-only) |
|[Azure Data Lake Analytics](#adla)| &nbsp; | &nbsp; | &nbsp; | ✓[*](#pipeline-only) |
|[Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |

> [!IMPORTANT]
> <a id="pipeline-only"></a>* Azure Databricks a Azure Data Lake Analytics můžete __pouze__ použije v kanálu. Další informace o kanálech najdete v článku [kanály ve službě Azure Machine Learning](concept-ml-pipelines.md) dokumentu.

__[Azure Container Instances (ACI)](#aci)__  lze také použít k trénování modelů. Je bez serveru Cloudová nabídka, která je cenově dostupné a usnadňuje vytváření a používání. ACI nepodporuje GPU akcelerace, automatizované hyper parametr optimalizací, nebo automatických Výběr modelu. Také ho nelze použít v rámci kanálu.

Jsou klíčové rozdíly mezi cílových výpočetních prostředí:
* __Akcelerace GPU__: grafické procesory jsou k dispozici virtuální počítač pro datové vědy a Azure Batch AI. Můžete mít přístup k grafického procesoru v místním počítači, v závislosti na hardware, ovladače a rozhraní, které jsou nainstalovány.
* __Automatizované hyperparametrů__: Azure Machine Learning automatizované hyperparameter optimalizace vám pomůže najít nejlepší hyperparameters pro váš model.
* __Výběr modelu automatizované__: Služba Azure Machine Learning můžete inteligentně doporučujeme algoritmus a hyperparameter výběr při vytváření modelu. Výběr automatizované modelu pomáhá rychleji, než se pokoušet ručně různé kombinace sloučit do vysoce kvalitních modelu. Další informace najdete v tématu [kurz: automaticky vyškolíme model klasifikace pomocí Azure Machine Learning automatizované](tutorial-auto-train-models.md) dokumentu.
* __Kanály__: Služba Azure Machine Learning umožňuje kombinovat různé úkoly, jako jsou školení a jejich nasazení do kanálu. Kanálů může být spuštění paralelně nebo v pořadí a poskytují mechanismus spolehlivé služby automation. Další informace najdete v tématu [vytvářet kanály machine learning se službou Azure Machine Learning](concept-ml-pipelines.md) dokumentu.

Vytvoření cílových výpočetních prostředí můžete použít sadu SDK Azure Machine Learning, Azure CLI nebo Azure portal. Můžete také použít existující cílových výpočetních prostředí tak, že přidáte (připojení) je do pracovního prostoru.

> [!IMPORTANT]
> Existující Instance kontejnerů Azure nelze připojit k vašemu pracovnímu prostoru. Místo toho musíte vytvořit novou instanci.
>
> Azure HDInsight, Azure Databricks a Azure Data Lake Store nelze vytvořit v pracovním prostoru. Místo toho musíte vytvořit prostředek a připojte ji k vašemu pracovnímu prostoru.

## <a name="workflow"></a>Pracovní postup

Pracovní postup pro vývoj a nasazování modelů Azure Machine Learning zahrnuje následující kroky:

1. Vyvíjejte strojového učení školení skriptů v Pythonu.
1. Vytvoření a konfigurace nebo připojení existující cílového výpočetního prostředí.
1. Odesílání skriptů školení do cílového výpočetního prostředí.
1. Zkontrolujte výsledky a najděte nejlepší model.
1. Zaregistrujte model v registru modelu.
1. Nasazení modelu.

> [!IMPORTANT]
> Trénovací skript objektu se neváže na cíl konkrétní výpočetní prostředky. Trénování zpočátku do místního počítače a potom přepnout cílových výpočetních prostředí bez nutnosti trénovací skript přepsat.

Přechod z cílového výpočetního prostředí jeden zahrnuje vytvoření [konfigurace spuštění](concept-azure-machine-learning-architecture.md#run-configuration). Konfigurace spuštění definuje, jak spustit skript v cílové výpočetní prostředí.

## <a name="training-scripts"></a>Trénovací skripty

Spustíte-li spustit školení, se odesílá celý adresář, který obsahuje trénovací skripty. Snímek se vytvoří a odešle do cílového výpočetního prostředí. Další informace najdete v tématu [snímky](concept-azure-machine-learning-architecture.md#snapshot).

## <a id="local"></a>Místní počítač

Při tréninku místně, použijte sadu SDK k odeslání operace trénování. Trénovat pomocí prostředí spravovaným uživatele nebo systému.

### <a name="user-managed-environment"></a>Uživatel spravován prostředí

V prostředí uživatel spravován zodpovídáte za zajištění, že všechny potřebné balíčky jsou k dispozici v prostředí Python, který můžete spustit skript. Následující fragment kódu je příklad konfigurace školení pro uživatele, spravovat prostředí:

```python
from azureml.core.runconfig import RunConfiguration

# Editing a run configuration property on-fly.
run_config_user_managed = RunConfiguration()

run_config_user_managed.environment.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
#run_config.environment.python.interpreter_path = '/home/ninghai/miniconda3/envs/sdk2/bin/python'
```

Poznámkový blok Jupyter, který ukazuje školení v prostředí spravovaného uživatele, najdete v části [ https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local/02.train-on-local.ipynb ](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local/02.train-on-local.ipynb).
  
### <a name="system-managed-environment"></a>Systém spravované prostředí

Spolehněte se na conda Spravovat závislosti systému spravované prostředí. Vytvoří soubor s názvem Conda `conda_dependencies.yml` , který obsahuje seznam závislostí. Potom můžete požádat o systému sestavte nové prostředí conda a spusťte skripty v ní. Systém spravované prostředí může být později znovu používané, pokud `conda_dependencies.yml` soubory zůstávají beze změn. 

Počáteční nastavení do nového prostředí může trvat několik minut v závislosti na velikosti požadované závislosti. Následující fragment kódu ukazuje vytvoření prostředí spravované systému, která závisí na scikit-informace:

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

run_config_system_managed = RunConfiguration()

run_config_system_managed.environment.python.user_managed_dependencies = False
run_config_system_managed.auto_prepare_environment = True

# Specify conda dependencies with scikit-learn

run_config_system_managed.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
```

Poznámkový blok Jupyter, který ukazuje školení v systému spravovat prostředí, najdete v části [ https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local/02.train-on-local.ipynb ](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local/02.train-on-local.ipynb).

## <a id="dsvm"></a>Virtuální počítač pro datové vědy

Místní počítač nemusí mít výpočetní prostředky ani prostředky GPU prostředků potřebných pro trénování modelu. V takovém případě můžete vertikálně navýšit kapacitu nebo horizontální navýšení kapacity procesu trénování, tak, že přidáte další výpočetní cíle jako je například na Data virtuálních počítačů VĚDY.

> [!WARNING]
> Azure Machine Learning podporuje pouze virtuální počítače s Ubuntu. Při vytváření virtuálního počítače nebo vyberte existující, musíte vybrat jednu, která používá Ubuntu.

Následující kroky konfigurace Data virtuálního počítače VĚDY jako cíl školení pomocí sady SDK:

1. Vytvořit nebo připojit virtuální počítač
    
    * Pokud chcete vytvořit nový DSVM, nejdřív zkontrolujte, pokud budete mít DSVM se stejným názvem, není-li vytvořit nový virtuální počítač:
    
        ```python
        from azureml.core.compute import DsvmCompute
        from azureml.core.compute_target import ComputeTargetException

        compute_target_name = 'mydsvm'

        try:
            dsvm_compute = DsvmCompute(workspace = ws, name = compute_target_name)
            print('found existing:', dsvm_compute.name)
        except ComputeTargetException:
            print('creating new.')
            dsvm_config = DsvmCompute.provisioning_configuration(vm_size = "Standard_D2_v2")
            dsvm_compute = DsvmCompute.create(ws, name = compute_target_name, provisioning_configuration = dsvm_config)
            dsvm_compute.wait_for_completion(show_output = True)
        ```
    * Připojit existující virtuální počítač jako cílové výpočetní prostředí, musí poskytovat plně kvalifikovaný název domény, přihlašovací jméno a heslo pro virtuální počítač.  V tomto příkladu nahraďte ```<fqdn>``` s veřejný plně kvalifikovaný název domény virtuálního počítače nebo veřejnou IP adresu. Nahraďte ```<username>``` a ```<password>``` se heslo pro virtuální počítač a uživatele SSH:

        ```python
        from azureml.core.compute import RemoteCompute

        dsvm_compute = RemoteCompute.attach(ws,
                                        name="attach-dsvm",
                                        username='<username>',
                                        address="<fqdn>",
                                        ssh_port=22,
                                        password="<password>")

        dsvm_compute.wait_for_completion(show_output=True)
    
   It takes around 5 minutes to create the DSVM instance.

1. Create a configuration for the DSVM compute target. Docker and conda are used to create and configure the training environment on DSVM:

    ```python
    from azureml.core.runconfig import RunConfiguration
    from azureml.core.conda_dependencies import CondaDependencies

    # Load the "cpu-dsvm.runconfig" file (created by the above attach operation) in memory
    run_config = RunConfiguration(framework = "python")

    # Set compute target to the Linux DSVM
    run_config.target = compute_target_name

    # Use Docker in the remote VM
    run_config.environment.docker.enabled = True

    # Use CPU base image
    # If you want to use GPU in DSVM, you must also use GPU base Docker image azureml.core.runconfig.DEFAULT_GPU_IMAGE
    run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE
    print('Base Docker image is:', run_config.environment.docker.base_image)

    # Ask system to provision a new one based on the conda_dependencies.yml file
    run_config.environment.python.user_managed_dependencies = False

    # Prepare the Docker and conda environment automatically when used the first time.
    run_config.prepare_environment = True

    # specify CondaDependencies obj
    run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])

    ```

1. Pro výpočetní prostředky odstranit, až budete hotovi, použijte následující kód:

    ```python
    dsvm_compute.delete()
    ```

Poznámkový blok Jupyter, který ukazuje, školení na virtuální počítač pro datové vědy, naleznete v tématu [ https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/04.train-on-remote-vm/04.train-on-remote-vm.ipynb ](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/04.train-on-remote-vm/04.train-on-remote-vm.ipynb).

## <a id="batch"></a>Azure Batch AI

Pokud trvá dlouhou dobu k natrénování modelu, můžete distribuovat napříč clusterem výpočetní prostředky v cloudu se školení Azure Batch AI. Batch AI může také potřeba povolit prostředek GPU.

Následující příklad hledá existující cluster Batch AI podle názvu. Pokud jeden není nalezen, je vytvořen:

```python
from azureml.core.compute import BatchAiCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
batchai_cluster_name = os.environ.get("BATCHAI_CLUSTER_NAME", ws.name + "gpu")
cluster_min_nodes = os.environ.get("BATCHAI_CLUSTER_MIN_NODES", 1)
cluster_max_nodes = os.environ.get("BATCHAI_CLUSTER_MAX_NODES", 3)
vm_size = os.environ.get("BATCHAI_CLUSTER_SKU", "STANDARD_NC6")
autoscale_enabled = os.environ.get("BATCHAI_CLUSTER_AUTOSCALE_ENABLED", True)


if batchai_cluster_name in ws.compute_targets():
    compute_target = ws.compute_targets()[batchai_cluster_name]
    if compute_target and type(compute_target) is BatchAiCompute:
        print('found compute target. just use it. ' + batchai_cluster_name)
else:
    print('creating a new compute target...')
    provisioning_config = BatchAiCompute.provisioning_configuration(vm_size = vm_size, # NC6 is GPU-enabled
                                                                vm_priority = 'lowpriority', # optional
                                                                autoscale_enabled = autoscale_enabled,
                                                                cluster_min_nodes = cluster_min_nodes, 
                                                                cluster_max_nodes = cluster_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, batchai_cluster_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current BatchAI cluster status, use the 'status' property    
    print(compute_target.status.serialize())
```

Připojit existující cluster Batch AI jako cílové výpočetní prostředí, je nutné zadat ID prostředku Azure. Chcete-li získat ID prostředku na webu Azure Portal, postupujte následovně:
1. Vyhledejte `Batch AI` služby pod **všechny služby**
1. Klikněte na název pracovního prostoru, do které cluster patří
1. Vyberte cluster
1. Klikněte na **vlastnosti**
1. Kopírovat **ID**

Následující příklad používá sadu SDK připojit cluster do pracovního prostoru. V tomto příkladu nahraďte `<name>` s názvem pro výpočetní prostředky. Název nemusí odpovídat názvu clusteru. Nahraďte `<resource-id>` s prostředky Azure ID výše popsané:

```python
from azureml.core.compute import BatchAiCompute
BatchAiCompute.attach(workspace=ws,
                      name=<name>,
                      resource_id=<resource-id>)
```

Můžete také zkontrolovat clusteru a úlohy stavu služby Batch AI pomocí následujících příkazů Azure CLI:

- Kontrola stavu clusteru. Můžete zobrazit, kolik uzly jsou spuštěné s použitím `az batchai cluster list`.
- Zkontrolujte stav úlohy. Můžete zobrazit, kolik úlohy běží s použitím `az batchai job list`.

Vytvořte cluster Batch AI trvá přibližně 5 minut.

Poznámkový blok Jupyter, který ukazuje školení v clusteru Batch AI, naleznete v tématu [ https://github.com/Azure/MachineLearningNotebooks/blob/master/training/03.train-hyperparameter-tune-deploy-with-tensorflow/03.train-hyperparameter-tune-deploy-with-tensorflow.ipynb ](https://github.com/Azure/MachineLearningNotebooks/blob/master/training/03.train-hyperparameter-tune-deploy-with-tensorflow/03.train-hyperparameter-tune-deploy-with-tensorflow.ipynb).

## <a name='aci'></a>Instance kontejneru Azure (ACI)

Služba Azure Container Instances jsou izolovaných kontejnerech, které mají rychlejší časy spuštění a nevyžadují, aby uživatel mohl spravovat žádné virtuální počítače. Služba Azure Machine Learning používá kontejnery Linuxu, které jsou k dispozici v westus, eastus, westeurope, northeurope, westus2 a southeastasia oblastech. Další informace najdete v tématu [dostupnost v oblastech](https://docs.microsoft.com/azure/container-instances/container-instances-quotas#region-availability). 

Následující příklad ukazuje způsob použití sady SDK k vytvoření cílové výpočetní prostředí ACI a použít ho k natrénování modelu: 

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

# create a new runconfig object
run_config = RunConfiguration()

# signal that you want to use ACI to run script.
run_config.target = "containerinstance"

# ACI container group is only supported in certain regions, which can be different than the region the Workspace is in.
run_config.container_instance.region = 'eastus'

# set the ACI CPU and Memory 
run_config.container_instance.cpu_cores = 1
run_config.container_instance.memory_gb = 2

# enable Docker 
run_config.environment.docker.enabled = True

# set Docker base image to the default CPU-based image
run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE

# use conda_dependencies.yml to create a conda environment in the Docker image
run_config.environment.python.user_managed_dependencies = False

# auto-prepare the Docker image when used for the first time (if it is not already prepared)
run_config.auto_prepare_environment = True

# specify CondaDependencies obj
run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
```

Může trvat několik sekund až několik minut vytvořte cílové výpočetní prostředí ACI.

Poznámkový blok Jupyter, který ukazuje, školení na instanci kontejneru Azure, najdete v části [ https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/03.train-on-aci/03.train-on-aci.ipynb ](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/03.train-on-aci/03.train-on-aci.ipynb).

## <a id="databricks"></a>Azure Databricks

Azure Databricks je prostředí založené na Apache Spark v cloudu Azure. Může sloužit jako cílové výpočetní prostředí při tréninku modelů s kanálu služby Azure Machine Learning.

> [!IMPORTANT]
> Azure Databricks cílové výpočetní prostředí jde použít jenom v rámci kanálu strojového učení.
>
> Než použijete k natrénování modelu je nutné vytvořit pracovní prostor služby Azure Databricks. Vytvoření těchto prostředků najdete v tématu [spuštění úlohy Spark job v Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) dokumentu.

Připojení Azure Databricks jako cílové výpočetní prostředí, musíte používat sadu SDK Azure Machine Learning a zadejte následující informace:

* __Název COMPUTE__: název, kterou chcete přiřadit na tento výpočetní prostředek.
* __ID prostředku__: ID prostředku pracovního prostoru Azure Databricks. Následující text je příklad formátu pro tuto hodnotu:

    ```text
    /subscriptions/<your_subscription>/resourceGroups/<resource-group-name>/providers/Microsoft.Databricks/workspaces/<databricks-workspace-name>
    ```

    > [!TIP]
    > Pokud chcete získat ID prostředku, použijte následující příkaz rozhraní příkazového řádku Azure. Nahraďte `<databricks-ws>` s názvem vašeho pracovního prostoru Databricks:
    > ```azurecli-interactive
    > az resource list --name <databricks-ws> --query [].id
    > ```

* __Přístupový token__: přístupový token pro ověření do Azure Databricks. K vygenerování přístupového tokenu, najdete v článku [ověřování](https://docs.azuredatabricks.net/api/latest/authentication.html) dokumentu.

Následující kód ukazuje, jak se připojit jako cílové výpočetní prostředí Azure Databricks:

```python
databricks_compute_name = os.environ.get("AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_resource_id = os.environ.get("AML_DATABRICKS_RESOURCE_ID", "<databricks_resource_id>")
databricks_access_token = os.environ.get("AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_resource_id {}'.format(databricks_resource_id))
    print('databricks_access_token {}'.format(databricks_access_token))
    databricks_compute = DatabricksCompute.attach(
             workspace=ws,
             name=databricks_compute_name,
             resource_id=databricks_resource_id,
             access_token=databricks_access_token
         )
    
    databricks_compute.wait_for_completion(True)
```

## <a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics je platforma analýzy velkých objemů dat v cloudu Azure. Může sloužit jako cílové výpočetní prostředí při tréninku modelů s kanálu služby Azure Machine Learning.

> [!IMPORTANT]
> Azure Data Lake Analytics cílové výpočetní prostředí jde použít jenom v rámci kanálu strojového učení.
>
> Než použijete k natrénování modelu, musíte vytvořit účet Azure Data Lake Analytics. Chcete-li vytvořit tento prostředek, najdete v článku [Začínáme s Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal) dokumentu.

Připojit Data Lake Analytics jako cílové výpočetní prostředí, musíte používat sadu SDK Azure Machine Learning a zadejte následující informace:

* __Název COMPUTE__: název, kterou chcete přiřadit na tento výpočetní prostředek.
* __ID prostředku__: ID prostředku účtu Data Lake Analytics. Následující text je příklad formátu pro tuto hodnotu:

    ```text
    /subscriptions/<your_subscription>/resourceGroups/<resource-group-name>/providers/Microsoft.DataLakeAnalytics/accounts/<datalakeanalytics-name>
    ```

    > [!TIP]
    > Pokud chcete získat ID prostředku, použijte následující příkaz rozhraní příkazového řádku Azure. Nahraďte `<datalakeanalytics>` s názvem název účtu Data Lake Analytics:
    > ```azurecli-interactive
    > az resource list --name <datalakeanalytics> --query [].id
    > ```

Následující kód ukazuje, jak se připojit Data Lake Analytics jako cílové výpočetní prostředí:

```python
adla_compute_name = os.environ.get("AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_id = os.environ.get("AML_ADLA_RESOURCE_ID", "<adla_resource_id>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_id))
    adla_compute = AdlaCompute.attach(
             workspace=ws,
             name=adla_compute_name,
             resource_id=adla_resource_id
         )
    
    adla_compute.wait_for_completion(True)
```

> [!TIP]
> Azure Machine Learning kanály funguje jenom s daty uloženými v úložišti dat výchozího účtu Data Lake Analytics. Pokud data, je potřeba pracovat s je v jiné než výchozí úložiště, můžete použít [ `DataTransferStep` ](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) ke kopírování dat před školení.

## <a id="hdinsight"></a>Připojit cluster služby HDInsight 

HDInsight je oblíbená platforma pro analýzy velkých objemů dat. Poskytuje Apache Spark, který slouží k natrénování modelu.

> [!IMPORTANT]
> Než použijete k natrénování modelu je nutné vytvořit HDInsight cluster. Při vytváření clusteru HDInsight Spark, najdete v článku [vytvoření clusteru Spark v HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql) dokumentu.
>
> Při vytváření clusteru, musíte zadat uživatelské jméno SSH a heslo. Všimněte si těchto hodnot, podle potřeby při použití HDInsight jako cílové výpočetní prostředí.
>
> Po vytvoření clusteru má použitím plně kvalifikovaného názvu domény (FQDN) z `<clustername>.azurehdinsight.net`, kde `<clustername>` je název zadaný pro cluster. Je nutné tuto adresu (nebo veřejnou IP adresu clusteru) použít jako cílové výpočetní prostředí

Konfigurace HDInsight jako cílové výpočetní prostředí, musíte zadat plně kvalifikovaný název domény, clusteru přihlašovací jméno a heslo pro HDInsight cluster. Následující příklad používá sadu SDK připojit cluster do pracovního prostoru. V tomto příkladu nahraďte `<fqdn>` s veřejný plně kvalifikovaný název domény clusteru nebo veřejnou IP adresu. Nahraďte `<username>` a `<password>` uživatele SSH a heslo pro cluster:

> [!NOTE]
> Vyhledejte úplný název domény pro váš cluster najdete na webu Azure portal a vyberte svůj cluster HDInsight. Z __přehled__ části plně kvalifikovaný název je součástí __URL__ položka. Odeberte `https://` od začátku této hodnotě.
>
> ![Snímek obrazovky Přehled clusteru HDInsight s zvýrazněnou položkou adresy URL](./media/how-to-set-up-training-targets/hdinsight-overview.png)

```python
from azureml.core.compute import HDInsightCompute

try:
    # Attaches a HDInsight cluster as a compute target.
    HDInsightCompute.attach(ws,name = "myhdi",
                            address = "<fqdn>",
                            username = "<username>",
                            password = "<password>")
except UserErrorException as e:
    print("Caught = {}".format(e.message))
    print("Compute config already attached.")

# Configure HDInsight run
# load the runconfig object from the "myhdi.runconfig" file generated by the attach operaton above.
run_config = RunConfiguration.load(project_object = project, run_config_name = 'myhdi')

# ask system to prepare the conda environment automatically when used for the first time
run_config.auto_prepare_environment = True
```

## <a name="submit-training-run"></a>Odeslat školení spuštění

Existují dva způsoby, jak odeslat školení spuštění:

* Odesílání `ScriptRunConfig` objektu.
* Odesílání `Pipeline` objektu.

> [!IMPORTANT]
> Azure Databricks a Azure Datalake Analytics výpočetní cíle lze použít pouze v rámci kanálu.
> Cílové místní výpočetní prostředí nelze použít v rámci kanálu.

### <a name="submit-using-scriptrunconfig"></a>Odeslat pomocí `ScriptRunConfig`

Vzorek kódu pro odesílání školicí spouští pomocí `ScriptRunConfig` je stejný bez ohledu na cílové výpočetní prostředí:

* Vytvoření `ScriptRunConfig` pro cílové výpočetní prostředí pomocí konfigurace spuštění.
* Odešlete spuštění.
* Vyčkat, než běžet do dokončení.

Následující příklad použije konfiguraci pro cíl spravované systému místním výpočetním vytvořili dříve v tomto dokumentu:

```python
src = ScriptRunConfig(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
run = exp.submit(src)
run.wait_for_completion(show_output = True)
```

Poznámkový blok Jupyter, který ukazuje trénováním Spark v HDInsight, naleznete v tématu [ https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/05.train-in-spark/05.train-in-spark.ipynb ](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/05.train-in-spark/05.train-in-spark.ipynb).

### <a name="submit-using-a-pipeline"></a>Odeslat pomocí kanálu

Kód vzorku pro odesílání školicí spouští pomocí kanálu je stejný bez ohledu na cílové výpočetní prostředí:

* Přidání kroku do kanálu pro výpočetní prostředek.
* Odešlete spuštění pomocí kanálu.
* Vyčkat, než běžet do dokončení.

Následující příklad používá cílového výpočetního prostředí Azure Databricks vytvořili dříve v tomto dokumentu:

```python
dbStep = DatabricksStep(
    name="databricksmodule",
    inputs=[step_1_input],
    outputs=[step_1_output],
    num_workers=1,
    notebook_path=notebook_path,
    notebook_params={'myparam': 'testparam'},
    run_name='demo run name',
    databricks_compute=databricks_compute,
    allow_reuse=False
)
# list of steps to run
steps = [dbStep]
pipeline = Pipeline(workspace=ws, steps=steps)
pipeline_run = Experiment(ws, 'Demo_experiment').submit(pipeline)
pipeline_run.wait_for_completion()
```

Další informace o machine learning kanálů najdete v článku [kanály a Azure Machine Learning](concept-ml-pipelines.md) dokumentu.

Například Jupyter Notebooks, které ukazují výuku s využitím kanálu, naleznete v tématu [ https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline ](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline).

## <a name="view-and-set-up-compute-using-the-azure-portal"></a>Zobrazení a nastavte výpočetní pomocí webu Azure portal

Můžete zobrazit, co výpočetní cíle jsou spojeny s váš pracovní prostor z webu Azure portal. Pokud chcete vrátit do seznamu, použijte následující postup:

1. Přejděte [webu Azure portal](https://portal.azure.com) a přejděte do pracovního prostoru.
2. Klikněte na __Compute__ odkaz pod __aplikací__ oddílu.

    ![Karta výpočetní zobrazení](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)

### <a name="create-a-compute-target"></a>Vytvořte cílové výpočetní prostředí

Postupujte podle výše uvedené kroky, chcete-li zobrazit seznam cílových výpočetních prostředí a potom pomocí následujícího postupu vytvořte cílové výpočetní prostředí:

1. Klikněte na tlačítko __+__ přihlášení k přidání cílového výpočetního prostředí.

    ![Přidat Compute ](./media/how-to-set-up-training-targets/add-compute-target.png)

1. Zadejte název cílového výpočetního prostředí.
1. Vyberte typ compute připojit pro __školení__. 

    > [!IMPORTANT]
    > Ne všechny výpočetní typy lze vytvořit pomocí webu Azure portal. Aktuálně jsou typy, které lze vytvořit pro vzdělávání:
    > 
    > * Virtuální počítač
    > * Batch AI

1. Vyberte __vytvořit nový__ a vyplňte požadované formuláře. 
1. Vyberte __Vytvořit__.
1. Můžete zobrazit stav operace vytvoření cílové výpočetní prostředí výběrem ze seznamu.

    ![Zobrazení seznamu výpočetní](./media/how-to-set-up-training-targets/View_list.png) se pak zobrazí podrobnosti pro cílové výpočetní prostředí.
    ![Zobrazit podrobnosti](./media/how-to-set-up-training-targets/vm_view.PNG)
1. Nyní můžete odeslat běhu pro tyto cíle popsané výše.

### <a name="reuse-existing-compute-in-your-workspace"></a>Znovu použít existující výpočetních prostředků ve vašem pracovním prostoru

Postupujte podle výše uvedené kroky, chcete-li zobrazit seznam cílových výpočetních prostředí a pak znovu použít cílové výpočetní prostředí pomocí následujících kroků:

1. Klikněte na tlačítko **+** přihlášení k přidání cílového výpočetního prostředí.
2. Zadejte název cílového výpočetního prostředí.
3. Vyberte typ compute připojit pro vzdělávání.

    > [!IMPORTANT]
    > Ne všechny výpočetní typy lze připojit pomocí portálu.
    > Aktuálně jsou typy, které je možné připojit k trénování:
    > 
    > * Virtuální počítač
    > * Batch AI

1. Vyberte možnost použít existující.
    - Při připojování clustery Batch AI, z rozevíracího seznamu vyberte cílové výpočetní prostředí, vyberte pracovní prostor služby Batch AI a Batch AI Cluster a potom klikněte na tlačítko **vytvořit**.
    - Při připojování virtuálního počítače, zadejte IP adresu, kombinace uživatelského jména a hesla, privátního nebo veřejného klíče a Port a klikněte na tlačítko vytvořit.

    > [!NOTE]
    > Společnost Microsoft doporučuje používat klíče SSH jsou bezpečnější než hesla. Hesla jsou zranitelné vůči útokům hrubou silou klíče SSH využívají kryptografické podpisy. Informace o vytváření klíčů SSH pro použití s Azure Virtual Machines najdete v následujících dokumentech:
    >
    > * [Vytvoření a použití klíčů SSH v Linuxu nebo macOS]( https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Vytvoření a používání klíčů SSH na Windows]( https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

5. Stav zřizování stavu můžete zobrazit výběrem cílového výpočetního prostředí v seznamu.
6. Nyní můžete odeslat spuštění těchto cílů.

## <a name="examples"></a>Příklady
Tyto poznámkové bloky předvedení konceptů v tomto článku:
* [01.Getting-Started/02.Train-on-local/02.Train-on-local.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local)
* [01.Getting-Started/04.Train-on-Remote-VM/04.Train-on-Remote-VM.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/04.train-on-remote-vm)
* [01.Getting-Started/03.Train-on-aci/03.Train-on-aci.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/03.train-on-aci)
* [01.Getting-Started/05.Train-in-Spark/05.Train-in-Spark.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/05.train-in-spark)
* [tutorials/01.Train-models.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/01.train-models.ipynb)

Získejte tyto poznámkové bloky: [!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další postup

* [Referenční informace k Azure Machine Learning SDK](https://aka.ms/aml-sdk)
* [Kurz: Trénování modelu](tutorial-train-models-with-aml.md)
* [Kam chcete nasazovat modely](how-to-deploy-and-where.md)
* [Vytvořit machine learning kanálů prostřednictvím služby Azure Machine Learning](concept-ml-pipelines.md)
