---
title: Cílových výpočetních prostředí pro trénování modelu
titleSuffix: Azure Machine Learning service
description: Konfigurace prostředí školení (cílových výpočetních prostředí) pro strojové učení cvičení modelu. Školení prostředí lze snadno přepínat. Spusťte místně školení a pokud je potřeba škálovat na více systémů, přepněte do cílové výpočetní prostředí založené na cloudu. Databricks
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: larryfr
manager: cgronlun
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 664d56daf3e70e2e5699d0c07331c466c60e06c5
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53338854"
---
# <a name="set-up-compute-targets-for-model-training"></a>Nastavení cílových výpočetních prostředí pro trénování modelu

Se službou Azure Machine Learning můžete trénování modelu v různých výpočetních prostředků. Tyto výpočetní prostředky, volá __cílových výpočetních prostředí__, může být místní nebo v cloudu. V tomto dokumentu se dozvíte o cílových podporovaných výpočetních prostředí a jejich použití.

Cílové výpočetní prostředí je prostředek, kde spustit trénovací skript, nebo váš model je hostována, když nasadit jako webovou službu. Můžete vytvořit a spravovat cílové výpočetní prostředí pomocí Azure Machine Learning SDK, webu Azure portal nebo rozhraní příkazového řádku Azure. Pokud máte cílových výpočetních prostředí, které byly vytvořené pomocí jiné služby (například cluster HDInsight), můžete jejich připojením do pracovního prostoru služby Azure Machine Learning.

Existují tři různé kategorie cílových výpočetních prostředí, které podporuje Azure Machine Learning:

* __Místní__: Místního počítače nebo virtuálního počítače založené na cloudu, který používáte jako prostředí dev/experimentování. 

* __Spravovat výpočetní__: Azure Machine Learning Compute je výpočetní prostředí nabídka, která je spravované službou Azure Machine Learning. Umožňuje snadno vytvořit jeden nebo více node výpočetní prostředky pro trénování, testování a odvozování služby batch.

* __Připojené výpočetní__: Můžete také přinést výpočetní cloudu Azure a připojení k Azure Machine Learning. Přečtěte si další informace najdete níže na typy podporovaných výpočetních a způsob jejich použití.


## <a name="supported-compute-targets"></a>Cílových podporovaných výpočetních prostředí

Služba Azure Machine Learning nabízí různé podporu napříč různými cílových výpočetních prostředí. Životní cyklus vývoje typické modelu začíná dev/experimentování na malé množství dat ve službě. V této fázi doporučujeme používat místní prostředí. Například místního počítače nebo virtuálního počítače založené na cloudu. Vertikálně navýšit kapacitu trénování na větších datových sad, nebo proveďte distribuované trénování, doporučujeme vytvořit jeden nebo více node cluster tohoto pravidla automatického škálování provedou pokaždé, když odešlete spuštění pomocí Azure Machine Learning Compute. Můžete také připojit své vlastní výpočetní prostředek, ačkoli podpory pro různé scénáře se může lišit jako podrobnosti jsou dole:

|Cílové výpočetní prostředí| Akcelerace GPU | Automatizované hyperparametrů | Automatizované strojové učení | Friendly kanálu|
|----|:----:|:----:|:----:|:----:|
|[Místní počítač](#local)| Možná | &nbsp; | ✓ | &nbsp; |
|[Azure Machine Learning Compute](#amlcompute)| ✓ | ✓ | ✓ | ✓ |
|[Vzdáleném virtuálním počítači](#vm) | ✓ | ✓ | ✓ | ✓ |
|[Azure Databricks](#databricks)| &nbsp; | &nbsp; | ✓ | ✓[*](#pipeline-only) |
|[Azure Data Lake Analytics](#adla)| &nbsp; | &nbsp; | &nbsp; | ✓[*](#pipeline-only) |
|[Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |

> [!IMPORTANT]
> <a id="pipeline-only"></a>__*__ Azure Databricks a Azure Data Lake Analytics můžete __pouze__ použije v kanálu. Další informace o kanálech najdete v článku [kanály ve službě Azure Machine Learning](concept-ml-pipelines.md) dokumentu.

> [!IMPORTANT]
> Azure Machine Learning Compute musí být vytvořené v rámci pracovního prostoru. Existující instance nelze připojit k pracovnímu prostoru.
>
> Další cílových výpočetních prostředí musí vytvořit mimo Azure Machine Learning a potom připojit k vašemu pracovnímu prostoru.

> [!NOTE]
> Některé výpočetní cíle využívají imagí kontejnerů Dockeru při cvičení modelu. Základní image GPU musí použít pouze na služby Microsoft Azure. K tréninku modelu, jsou tyto služby:
>
> * Azure Machine Learning Compute
> * Azure Kubernetes Service
> * Virtuální počítač pro datové vědy.

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

> [!TIP]
> Pokaždé, když přiřadíte cílové výpočetní prostředí pomocí pracovního prostoru, tak, že vytvoříte spravované výpočetní nebo připojení existující výpočetní, je potřeba zadat název, který výpočetních prostředků. To by měl být dlouhý 2 až 16 znaků.

Přechod z cílového výpočetního prostředí jeden zahrnuje vytvoření [konfigurace spuštění](concept-azure-machine-learning-architecture.md#run-configuration). Konfigurace spuštění definuje, jak spustit skript v cílové výpočetní prostředí.

## <a name="training-scripts"></a>Trénovací skripty

Při spuštění spustit trénovací snímek adresáře, který obsahuje trénovací skripty se vytvoří a odešle do cílového výpočetního prostředí. Další informace najdete v tématu [snímky](concept-azure-machine-learning-architecture.md#snapshot).

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

## <a id="amlcompute"></a>Azure Machine Learning Compute

Azure Machine Learning Compute je spravovaná výpočetní infrastruktura, která umožňuje uživatelům snadno vytvořit výpočetní single - do více - node. Vytvoří se __v rámci oblasti vašeho pracovního prostoru__ a je prostředek, který můžete sdílet s ostatními uživateli ve vašem pracovním prostoru. Škálování automaticky při odeslání úlohy a můžete umístit do služby Azure Virtual Network. Spustí v __kontejnerové prostředí__, vytváření balíčků závislostí váš model v kontejneru Dockeru.

Azure Machine Learning Compute můžete použít k procesu trénování distribuovat napříč clusterem CPU nebo GPU výpočetních uzlů v cloudu. Další informace o velikostech virtuálních počítačů, které zahrnují GPU, najdete v článku [GPU optimalizované velikosti virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) dokumentaci.

> [!NOTE]
> Azure Machine Learning Compute má výchozí omezení na věci, jako je počet jader, které mohou být přiděleny. Další informace najdete v tématu [spravovat a žádosti o kvóty pro prostředky Azure](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas) dokumentu.

Azure Machine Learning Compute podle potřeby můžete vytvořit při spuštění naplánovat, nebo jako trvalý prostředek.

### <a name="run-based-creation"></a>Tvorby založené na spuštění

Azure Machine Learning Compute můžete vytvořit jako cílové výpočetní prostředí v době běhu. V tomto případě tak výpočetní prostředky se automaticky vytvoří pro spuštění, škálování až na max_nodes, který zadáte v konfiguraci spuštění a pak je __automaticky odstraní__ po dokončení spuštění.

> [!IMPORTANT]
> Na základě spuštění vytvoření výpočetní prostředky Azure Machine Learning je aktuálně ve verzi Preview. Nepoužívejte tvorby založené na spuštění, pokud používáte Hyperparametrů nebo automatizované Machine Learning. Pokud je potřeba použít Hyperparametrů nebo automatizované Machine Learning, vytvořte tak výpočetní prostředky Azure Machine Learning před odesláním spuštění.

```python
from azureml.core.compute import ComputeTarget, AmlCompute

#Let us first list the supported VM families for Azure Machine Learning Compute
AmlCompute.supported_vmsizes()

from azureml.core.runconfig import RunConfiguration

# create a new runconfig object
run_config = RunConfiguration()

# signal that you want to use AmlCompute to execute script.
run_config.target = "amlcompute"

# AmlCompute will be created in the same region as workspace. Set vm size for AmlCompute from the list returned above
run_config.amlcompute.vm_size = 'STANDARD_D2_V2'

```

### <a name="persistent-compute-basic"></a>Trvalé výpočetní (Basic)

Trvalé Azure Machine Learning Compute můžete použít opakovaně napříč více úloh. Můžete sdílet s ostatními uživateli v pracovním prostoru kde je udržována mezi úlohami.

Vytvořit prostředek služby Azure Machine Learning Compute trvalé, zadejte `vm_size` a `max_nodes` parametry. Azure Machine Learning potom použije inteligentních výchozích hodnot pro ostatní parametry.  Například výpočetní nastavení automatického škálování na 0 uzlů, pokud se nepoužívá a chcete-li vytvořit vyhrazených virtuálních počítačích ke spouštění úloh podle potřeby. 

* **vm_size**: Virtuální počítač řady uzlů vytvořené pomocí Azure Machine Learning Compute.
* **max_nodes**: Maximální počet uzlů k automatickému škálování při spuštění úlohy v Azure Machine Learning Compute.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           max_nodes=4)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)

```

### <a name="persistent-compute-advanced"></a>Trvalé výpočetní (Upřesnit)

Můžete také nakonfigurovat několik upřesňující vlastnosti při vytváření, Azure Machine Learning Compute.  Tyto vlastnosti umožňují vytvářet trvalé clusteru s pevnou velikostí, nebo v rámci existující virtuální síť Azure v rámci vašeho předplatného.

Kromě `vm_size` a `max_nodes`, můžete použít následující vlastnosti:

* **min_nodes**: Minimální počet uzlů (výchozí hodnota 0 uzly) Chcete-li snížit při spuštění úlohy v Azure Machine Learning Compute.
* **vm_priority**: Při vytváření, Azure Machine Learning Compute si vyberte mezi "vyhrazených" (výchozí) a "lowpriority" virtuální počítače. Virtuální počítače s nízkou prioritou použít nadbytečnou kapacitu Azure a jsou tedy levnější ale riziko, přičemž dojde ke zrušení spuštění.
* **idle_seconds_before_scaledown**: Dobu nečinnosti (výchozí 120 sekund) po dokončení běhu, než použití automatického škálování na min_nodes.
* **vnet_resourcegroup_name**: Skupina prostředků __existující__ virtuální sítě. Azure Machine Learning Compute se vytvoří v rámci této virtuální síti.
* **vnet_name**: Název virtuální sítě. Virtuální síť musí být ve stejné oblasti jako váš pracovní prostor Azure Machine Learning.
* **subnet_name**: Název podsítě v rámci virtuální sítě. Prostředky Azure Machine Learning Compute přiřadí IP adresy z tohoto rozsahu podsítě.

> [!TIP]
> Když vytvoříte prostředek trvalé Azure Machine Learning Compute máte také možnost aktualizovat jeho vlastnosti, jako je min_nodes nebo max_nodes. Jednoduše zavoláte `update()` funkce pro něj.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           vm_priority='lowpriority',
                                                           min_nodes=2,
                                                           max_nodes=4,
                                                           idle_seconds_before_scaledown='300',
                                                           vnet_resourcegroup_name='<my-resource-group>',
                                                           vnet_name='<my-vnet-name>',
                                                           subnet_name='<my-subnet-name>')
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)

```


## <a id="vm"></a>Vzdáleném virtuálním počítači

Azure Machine Learning podporuje také přináší vlastní výpočetních prostředků a připojíte ho do pracovního prostoru. Jeden takový typ prostředku je libovolný vzdáleném virtuálním počítači, dokud je přístupná ze služby Azure Machine Learning. To může být buď virtuální počítač Azure nebo na vzdáleném serveru ve vaší organizaci nebo v místním. Konkrétně zadaný IP adresu a přihlašovací údaje (uživatelské jméno/heslo nebo klíč SSH), můžete použít jakýkoli dostupný virtuální počítač pro vzdálené spuštění.
Můžete použít prostředí integrovaného systému conda, již existující prostředí Python nebo kontejneru Docker. Spuštění pomocí kontejneru Dockeru vyžaduje modul Docker na virtuálním počítači spuštěná. Tato funkce je zvlášť užitečné, pokud chcete dev/experimentování flexibilnější a cloudové prostředí než místního počítače.

> [!TIP]
> Doporučujeme používat virtuální počítač pro datové vědy jako virtuální počítač Azure podle výběru v tomto scénáři. Je předem nakonfigurované datové vědy a AI vývojové prostředí v Azure s možností volby kurátorované sady nástrojů a architektur pro celý životní cyklus vývoje ML. Další informace o používání virtuální počítač pro datové vědy se službou Azure Machine Learning najdete v tématu [nakonfigurovat prostředí pro vývoj](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm) dokumentu.

> [!WARNING]
> Azure Machine Learning podporuje pouze virtuální počítače s Ubuntu. Při vytváření virtuálního počítače nebo vyberte existující, musíte vybrat jednu, která používá Ubuntu.

Následující kroky konfigurace Data virtuálního počítače VĚDY jako cíl školení pomocí sady SDK:

1. Připojit existující virtuální počítač jako cílové výpočetní prostředí, musí poskytovat plně kvalifikovaný název domény, přihlašovací jméno a heslo pro virtuální počítač.  V tomto příkladu nahraďte ```<fqdn>``` s veřejný plně kvalifikovaný název domény virtuálního počítače nebo veřejnou IP adresu. Nahraďte ```<username>``` a ```<password>``` se heslo pro virtuální počítač a uživatele SSH:

    ```python
    from azureml.core.compute import RemoteCompute, ComputeTarget
    
    # Create compute config.
    attach_config = RemoteCompute.attach_configuration(address = "ipaddress",
                                                       ssh_port=22,
                                                       username='<username>',
                                                       password="<password>")
    # If using SSH instead of a password, use this:
    #                                                  ssh_port=22,
    #                                                   username='<username>',
    #                                                   password=None,
    #                                                   private_key_file="path-to-file",
    #                                                   private_key_passphrase="passphrase")

    # Attach the compute
    compute = ComputeTarget.attach(ws, "attach-dsvm", attach_config)

    compute.wait_for_completion(show_output=True)
    ```

1. Vytvořte konfiguraci pro DSVM cílového výpočetního prostředí. Docker a systému conda se používají k vytvoření a konfigurace prostředí pro školení na DSVM:

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

## <a id="databricks"></a>Azure Databricks

Azure Databricks je prostředí založené na Apache Spark v cloudu Azure. Může sloužit jako cílové výpočetní prostředí při tréninku modelů s kanálu služby Azure Machine Learning.

> [!IMPORTANT]
> Azure Databricks cílové výpočetní prostředí jde použít jenom v rámci kanálu strojového učení.
>
> Než použijete k natrénování modelu je nutné vytvořit pracovní prostor služby Azure Databricks. Vytvoření těchto prostředků najdete v tématu [spuštění úlohy Spark job v Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) dokumentu.

Připojení Azure Databricks jako cílové výpočetní prostředí, musíte používat sadu SDK Azure Machine Learning a zadejte následující informace:

* __Název COMPUTE__: Název, který chcete přiřadit tento výpočetní prostředek.
* __Název pracovního prostoru Databricks__: Název pracovního prostoru Azure Databricks.
* __Přístupový token__: Přístupový token pro ověření do Azure Databricks. K vygenerování přístupového tokenu, najdete v článku [ověřování](https://docs.azuredatabricks.net/api/latest/authentication.html) dokumentu.

Následující kód ukazuje, jak se připojit jako cílové výpočetní prostředí Azure Databricks:

```python
databricks_compute_name = os.environ.get("AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get("AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get("AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get("AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group = databricks_resource_group,
                                                           workspace_name = databricks_workspace_name,
                                                           access_token = databricks_access_token)
    databricks_compute = ComputeTarget.attach(
             ws,
             databricks_compute_name,
             attach_config
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

* __Název COMPUTE__: Název, který chcete přiřadit tento výpočetní prostředek.
* __Skupina prostředků__: Skupina prostředků obsahující účet Data Lake Analytics.
* __Název účtu__: Název účtu Data Lake Analytics.

Následující kód ukazuje, jak se připojit Data Lake Analytics jako cílové výpočetní prostředí:

```python
adla_compute_name = os.environ.get("AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get("AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get("AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group = adla_resource_group,
                                                     account_name = adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
             ws,
             adla_compute_name,
             attach_config
         )
    
    adla_compute.wait_for_completion(True)
```

> [!TIP]
> Azure Machine Learning kanály funguje jenom s daty uloženými v úložišti dat výchozího účtu Data Lake Analytics. Pokud data, je potřeba pracovat s je v jiné než výchozí úložiště, můžete použít [ `DataTransferStep` ](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) ke kopírování dat před školení.

## <a id="hdinsight"></a>Azure HDInsight 

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
from azureml.core.compute import HDInsightCompute, ComputeTarget

try:
    # Attaches a HDInsight cluster as a compute target.
    attach_config = HDInsightCompute.attach_configuration(address = "fqdn-or-ipaddress",
                                                          ssh_port = 22,
                                                          username = "username",
                                                          password = None, #if using ssh key
                                                          private_key_file = "path-to-key-file",
                                                          private_key_phrase = "key-phrase")
    compute = ComputeTarget.attach(ws, "myhdi", attach_config)
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

1. Zadejte název cílové výpočetní prostředí
1. Vyberte **Machine Learning Compute** jako typ výpočetních prostředků a použít pro __školení__

    > [!IMPORTANT]
    > Můžete vytvořit pouze Azure Machine Learning Compute jako spravované výpočetní prostředky pro školení

1. Vyplňte požadované formulář zejména řadu virtuálních počítačů a maximální počet uzlů pro rozbíhání tak výpočetní prostředky 
1. Vyberte __Vytvořit__.
1. Stav operace vytvoření můžete zobrazit výběrem cílového výpočetního prostředí v seznamu

    ![Zobrazení seznamu výpočetní prostředky](./media/how-to-set-up-training-targets/View_list.png)

1. Zobrazí se podrobnosti pro cílové výpočetní prostředí.

    ![Zobrazit podrobnosti](./media/how-to-set-up-training-targets/compute-target-details.png)

1. Nyní můžete odeslat běhu pro tyto cíle popsané výše


### <a name="reuse-existing-compute-in-your-workspace"></a>Znovu použít existující výpočetních prostředků ve vašem pracovním prostoru

Postupujte podle výše uvedené kroky, chcete-li zobrazit seznam cílových výpočetních prostředí a pak znovu použít cílové výpočetní prostředí pomocí následujících kroků:

1. Klikněte na tlačítko **+** přihlášení k přidání cílového výpočetního prostředí
2. Zadejte název cílové výpočetní prostředí
3. Vyberte typ compute připojit pro __školení__

    > [!IMPORTANT]
    > Ne všechny výpočetní typy lze připojit pomocí portálu.
    > Aktuálně jsou typy, které je možné připojit k trénování:
    > 
    > * Vzdáleném virtuálním počítači
    > * Databricks
    > * Data Lake Analytics
    > * HDInsight

1. Vyplňte požadované formuláře

    > [!NOTE]
    > Společnost Microsoft doporučuje používat klíče SSH jsou bezpečnější než hesla. Hesla jsou zranitelné vůči útokům hrubou silou klíče SSH využívají kryptografické podpisy. Informace o vytváření klíčů SSH pro použití s Azure Virtual Machines najdete v následujících dokumentech:
    >
    > * [Vytvoření a použití klíčů SSH v Linuxu nebo macOS]( https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Vytvoření a používání klíčů SSH na Windows]( https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Vyberte připojení
1. Stav operace připojení můžete zobrazit výběrem cílového výpočetního prostředí v seznamu
1. Nyní můžete odeslat běhu pro tyto cíle popsané výše

## <a name="examples"></a>Příklady
Podívejte se poznámkových bloků v následujících umístěních:
* [postupy-k-použití azureml a školení](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)

* [kurzy/img – klasifikace – část 1 – training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další postup

* [Referenční informace k Azure Machine Learning SDK](https://aka.ms/aml-sdk)
* [Kurz: Trénování modelu](tutorial-train-models-with-aml.md)
* [Kam chcete nasazovat modely](how-to-deploy-and-where.md)
* [Vytvořit machine learning kanálů prostřednictvím služby Azure Machine Learning](concept-ml-pipelines.md)
