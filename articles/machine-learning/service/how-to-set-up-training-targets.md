---
title: Cílových výpočetních prostředí pro trénování modelu
titleSuffix: Azure Machine Learning service
description: Konfigurace prostředí školení (cílových výpočetních prostředí) pro strojové učení cvičení modelu. Můžete snadno přepínat mezi prostředími školení. Spuštění trénovací místně. Pokud je potřeba škálovat na více systémů, přepněte do cílové výpočetní prostředí založené na cloudu.
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
ms.openlocfilehash: 9037f6d7602f186bc30e55acbc050280bca134ee
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53794460"
---
# <a name="set-up-compute-targets-for-model-training"></a>Nastavení cílových výpočetních prostředí pro trénování modelu

Se službou Azure Machine Learning můžete trénování modelu v různých výpočetních prostředků. Tyto výpočetní prostředky, volá __cílových výpočetních prostředí__, může být místní nebo v cloudu. V tomto článku se dozvíte o cílových podporovaných výpočetních prostředí a jejich použití.

Cílové výpočetní prostředí je prostředek, ve kterém jste spustili cvičný skript nebo hostujete modelu při nasazení jako webové služby. Můžete vytvořit a spravovat cílové výpočetní prostředí pomocí sady SDK Azure Machine Learning, na webu Azure portal nebo rozhraní příkazového řádku Azure. Pokud máte cílových výpočetních prostředí, které jsou vytvořené pomocí jiné služby (například cluster Azure HDInsight), můžete tyto cíle připojením k pracovnímu prostoru Azure Machine Learning service.

Existují tři různé kategorie cílových výpočetních prostředí, které podporuje Azure Machine Learning:

* __Místní__: Místní počítač nebo založené na cloudu virtuálního počítače (VM), který používáte jako prostředí pro vývoj a služby experimentování ve službě. 
* __Spravovat výpočetní__: Azure Machine Learning Compute je výpočetní prostředí nabídky, který je spravovaný službou Azure Machine Learning. Tato nabídka umožňuje snadno vytvořit jeden nebo více uzly výpočetní prostředky pro trénování, testování a odvozování služby batch.
* __Připojené výpočetní__: Můžete také přinést výpočetní cloudu Azure a připojení k Azure Machine Learning. Přečtěte si další informace o podporované typy výpočetních a jak je používat v následujících částech.


## <a name="supported-compute-targets"></a>Cílových podporovaných výpočetních prostředí

Služba Azure Machine Learning nabízí různé podporu napříč různými cílových výpočetních prostředí. Životní cyklus vývoje typické modelu začíná vývoj a experimentování na malé množství dat. V této fázi doporučujeme že používat místní prostředí jako místního počítače nebo virtuálního počítače založené na cloudu. Vertikálně navýšit kapacitu trénování na větších datových sad nebo distribuované trénování, použití Azure Machine Learning výpočetní prostředí k vytvoření jedné nebo několika uzly clusteru tohoto pravidla automatického škálování provedou pokaždé, když odešlete spuštění. Můžete také připojit své vlastní výpočetní prostředek, ačkoli podpory pro různé scénáře se může lišit jako je popsáno v následující tabulce:

|Cílové výpočetní prostředí| Akcelerace GPU | Automatizované<br/> hyperparametrů | Automatizované</br> Strojové učení | Friendly kanálu|
|----|:----:|:----:|:----:|:----:|
|[Místní počítač](#local)| Možná | &nbsp; | ✓ | &nbsp; |
|[Azure Machine Learning Compute](#amlcompute)| ✓ | ✓ | ✓ | ✓ |
|[Vzdáleném virtuálním počítači](#vm) | ✓ | ✓ | ✓ | ✓ |
|[Azure Databricks](#databricks)| &nbsp; | &nbsp; | ✓ | ✓[*](#pipeline-only) |
|[Azure Data Lake Analytics](#adla)| &nbsp; | &nbsp; | &nbsp; | ✓[*](#pipeline-only) |
|[Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |

> [!IMPORTANT]
> <a id="pipeline-only"></a>__*__ _Azure Databricks a Azure Data Lake Analytics můžete použít jenom v rámci kanálu._<br/>
> Další informace o kanálech najdete v tématu [kanály ve službě Azure Machine Learning](concept-ml-pipelines.md).
>
> Prostředí Azure Machine Learning compute musí být vytvořené v rámci pracovního prostoru. Existující instance nelze připojit k pracovnímu prostoru.
>
> Další cílových výpočetních prostředí musí vytvořit mimo Azure Machine Learning a potom připojit k vašemu pracovnímu prostoru.
>
> Při tréninku modelů některé výpočetní cíle využívají imagí kontejnerů Dockeru. Základní image GPU musí použít pouze na služby Microsoft Azure. K tréninku modelu služby patří:
> * Azure Machine Learning Compute
> * Azure Kubernetes Service
> * Windows virtuální počítač pro datové vědy (DSVM)

## <a name="workflow"></a>Pracovní postup

Pracovní postup pro vývoj a nasazování modelů Azure Machine Learning zahrnuje následující kroky:

1. Vyvíjejte strojového učení trénovací skripty v jazyce Python.
1. Vytvoření a konfigurace cílové výpočetní prostředí nebo připojte existující cílové výpočetní prostředí.
1. Odesílání skriptů školení do cílového výpočetního prostředí.
1. Zkontrolujte výsledky a najděte nejlepší model.
1. Zaregistrujte model v registru modelu.
1. Nasazení modelu.

> [!NOTE]
> Trénovací skript objektu se neváže na cíl konkrétní výpočetní prostředky. Trénování zpočátku do místního počítače a potom přepnout cílových výpočetních prostředí bez nutnosti trénovací skript přepsat.
> 
> Když přiřadíte cílové výpočetní prostředí pomocí pracovního prostoru tak, že vytvoříte spravované compute nebo připojením existující výpočetní prostředky, zadejte název do výpočetních prostředků. Název by měl být mezi 2 a 16 znaků.

Přepnutí z cílového výpočetního prostředí jednoho do jiného, je nutné [konfigurace spuštění](concept-azure-machine-learning-architecture.md#run-configuration). Konfigurace spuštění definuje, jak spustit skript v cílové výpočetní prostředí.

## <a name="training-scripts"></a>Trénovací skripty

Spustíte-li spustit školení, vytvoří snímek adresáře, který obsahuje trénovací skripty a odesílá je do cílového výpočetního prostředí. Další informace najdete v tématu [snímky](concept-azure-machine-learning-architecture.md#snapshot).

## <a id="local"></a>Místní počítač

Při tréninku místně pomocí sady SDK k odeslání operace trénování. Vám trénovat, s použitím prostředí spravovaným uživatele nebo systému.

### <a name="user-managed-environment"></a>Uživatel spravován prostředí

V prostředí spravovaného uživatele Ujistěte se, že všechny potřebné balíčky jsou k dispozici v kterém jste spustili skript prostředí Pythonu. Následující fragment kódu je příklad, jak nakonfigurovat školení pro uživatele, spravovat prostředí:

```python
from azureml.core.runconfig import RunConfiguration

# Edit a run configuration property on the fly.
run_config_user_managed = RunConfiguration()

run_config_user_managed.environment.python.user_managed_dependencies = True

# Choose a specific Python environment by pointing to a Python path. For example:
# run_config.environment.python.interpreter_path = '/home/ninghai/miniconda3/envs/sdk2/bin/python'
```

  
### <a name="system-managed-environment"></a>Systém spravované prostředí

Spolehněte se na conda Spravovat závislosti systému spravované prostředí. Vytvoří soubor s názvem Conda **conda_dependencies.yml** , který obsahuje seznam závislostí. Můžete požádat o systému vytvářet nové prostředí conda a spustit skripty existuje. Systém spravované prostředí můžete použít opakovaně později, pokud je soubor conda_dependencies.yml beze změny. 

Počáteční nastavení do nového prostředí může trvat několik minut na dokončení, na základě velikosti požadované závislosti. Následující fragment kódu ukazuje, jak vytvořit prostředí spravované systému, který závisí na scikit-informace:

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

run_config_system_managed = RunConfiguration()

run_config_system_managed.environment.python.user_managed_dependencies = False
run_config_system_managed.auto_prepare_environment = True

# Specify the conda dependencies with scikit-learn

run_config_system_managed.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
```

## <a id="amlcompute"></a>Azure Machine Learning Compute

Azure Machine Learning Compute je spravované výpočetní infrastruktura, která umožňuje uživatelům snadno vytvořit jeden nebo více uzly výpočetní. Výpočetní prostředky se vytvoří v rámci oblasti vašeho pracovního prostoru jako prostředek, který můžete sdílet s ostatními uživateli ve vašem pracovním prostoru. Výpočetní prostředky automaticky škáluje při odeslání úlohy a můžete umístit do služby Azure virtual network. Výpočetní provede v prostředí kontejnerizovaných a balíčky závislostí modelu v kontejneru Dockeru.

Azure Machine Learning Compute můžete použít k procesu trénování distribuovat napříč clusterem CPU nebo GPU výpočetních uzlů v cloudu. Další informace o velikostech virtuálních počítačů, které zahrnují GPU najdete v tématu [velikosti virtuálního počítače GPU optimalizované](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

> [!NOTE]
> Azure Machine Learning Compute má výchozí omezení, jako je počet jader, které mohou být přiděleny. Další informace najdete v tématu [spravovat a žádosti o kvóty pro prostředky Azure](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas).

Výpočetní prostředí Azure Machine Learning můžete vytvořit na vyžádání při spuštění naplánovat, nebo jako trvalý prostředek.

### <a name="run-based-creation"></a>Tvorby založené na spuštění

Můžete vytvořit prostředí Azure Machine Learning compute jako cílové výpočetní prostředí za běhu. Výpočetní prostředky se automaticky vytvoří pro spuštění a škálování počtu instancí **max_nodes** , který zadáte v běhu konfiguraci. Výpočetní automaticky odstraněna po dokončení spuštění.

> [!IMPORTANT]
> Zřízení spuštění výpočetního prostředí Azure Machine Learning je aktuálně ve verzi Preview. Nepoužívejte tvorby založené na spuštění, pokud používáte automatické hyperparametrů nebo automatizované strojového učení. Použít hyperparametrů nebo automatizované strojového učení, vytvořte před odesláním spuštění prostředí Azure Machine Learning compute.

```python
from azureml.core.compute import ComputeTarget, AmlCompute

# First, list the supported VM families for Azure Machine Learning Compute
AmlCompute.supported_vmsizes()

from azureml.core.runconfig import RunConfiguration

# Create a new runconfig object
run_config = RunConfiguration()

# Signal that you want to use AmlCompute to execute the script
run_config.target = "amlcompute"

# AmlCompute is created in the same region as your workspace
# Set the VM size for AmlCompute from the list of supported_vmsizes
run_config.amlcompute.vm_size = 'STANDARD_D2_V2'

```

### <a name="persistent-compute-basic"></a>Trvalé compute: Basic

Trvalé výpočetní prostředí Azure Machine Learning můžete použít opakovaně napříč úlohy. Výpočetní prostředky můžete sdílet s ostatními uživateli v pracovním prostoru a zůstane mezi úlohami.

K vytvoření trvalého prostředek výpočetní prostředí Azure Machine Learning, zadejte **vm_size** a **max_nodes** vlastnosti. Azure Machine Learning potom použije inteligentních výchozích hodnot pro ostatní vlastnosti. Bude automaticky škálovat výpočetní prostředky na 0 uzlů, pokud se nepoužívá a vytvoří vyhrazených virtuálních počítačů ke spouštění úloh podle potřeby. 

* **vm_size**: Virtuální počítač řady uzly vytvořené pomocí Azure Machine Learning Compute.
* **max_nodes**: Maximální počet uzlů pro automatické škálování až při spuštění úlohy v Azure Machine Learning Compute.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that the cluster doesn't already exist
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           max_nodes=4)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)

```

### <a name="persistent-compute-advanced"></a>Trvalé compute: Rozšířený 

Můžete také nakonfigurovat několik upřesňující vlastnosti při vytváření prostředí pro výpočetní prostředky Azure Machine Learning. Vlastnosti umožňují vytvářet trvalé clusteru s pevnou velikostí, nebo v rámci existující virtuální síť Azure v rámci vašeho předplatného.

Spolu s **vm_size** a **max_nodes** vlastnosti, můžete použít také následující vlastnosti:

* **min_nodes**: Minimální počet uzlů na snížit na při spuštění úlohy na výpočetním prostředí Azure Machine Learning. Výchozí minimální hodnota je nula (0) uzlů.
* **vm_priority**: Typ virtuálního počítače pro použití při vytváření Azure Machine Learning compute resource prostředí. Výběr mezi **vyhrazené** (výchozí) a **lowpriority**. Virtuální počítače s nízkou prioritou použijte nadbytečnou kapacitu v Azure. Tyto virtuální počítače jsou levnější, ale spuštění můžete pre-empted, když se používají tyto virtuální počítače.
* **idle_seconds_before_scaledown**: Dobu nečinnosti doba čekání po dokončení spuštění a před automatické škálování až počet **min_nodes**. Výchozí doba nečinnosti je 120 sekund.
* **vnet_resourcegroup_name**: Skupina prostředků __existující__ virtuální sítě. Azure Machine Learning výpočetním prostředí se vytvoří v rámci této virtuální sítě.
* **vnet_name**: Název virtuální sítě. Virtuální síť musí být ve stejné oblasti jako váš pracovní prostor Azure Machine Learning.
* **subnet_name**: Název podsítě v rámci virtuální sítě. Azure Machine Learning výpočetní prostředí prostředky jsou přiřazené IP adresy z tohoto rozsahu podsítě.

> [!TIP]
> Když vytvoříte trvalý prostředek Azure Machine Learning výpočetní prostředí, můžete aktualizovat vlastnosti, jako je počet **min_nodes** nebo **max_nodes**. Chcete-li aktualizovat vlastnosti, zavolejte `update()` funkce pro vlastnost.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that the cluster doesn't already exist 
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

Azure Machine Learning podporuje také přináší vlastní výpočetních prostředků a připojíte ho do pracovního prostoru. Jeden takový typ prostředku je libovolné vzdáleném virtuálním počítači, pokud je přístupná ze služby Azure Machine Learning. Prostředek může být virtuální počítač Azure, vzdáleném serveru ve vaší organizaci, nebo místní. Konkrétně zadaný IP adresu a přihlašovací údaje (uživatelské jméno a heslo nebo klíč SSH), můžete použít jakýkoli dostupný virtuální počítač pro vzdálené spuštění.
Můžete použít prostředí integrovaného systému conda, stávající prostředí Python nebo kontejneru Docker. Při spouštění pomocí kontejneru Dockeru, musíte být spuštěná na virtuálním počítači modul Docker. Vzdálené funkce virtuálního počítače je zvlášť užitečné, pokud chcete cloudové služby experimentování ve službě prostředí pro vývoj a, které je flexibilnější než místního počítače.

> [!TIP]
> Pomocí datové VĚDY jako virtuální počítač Azure podle výběru v tomto scénáři. Tento virtuální počítač je předem nakonfigurované datové vědy a AI vývojové prostředí v Azure virtuální počítač nabízí kurátorované řadu nástrojů a architektur pro úplné životního cyklu strojového učení vývoje. Další informace o tom, jak používat datové VĚDY s Azure Machine Learning najdete v tématu [nakonfigurovat prostředí pro vývoj](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm).

> [!WARNING]
> Azure Machine Learning podporuje pouze virtuální počítače se systémem Ubuntu. Při vytváření virtuálního počítače nebo vyberte existující virtuální počítač, je nutné vybrat virtuální počítač, který používá Ubuntu.

Následující kroky konfigurace DSVM jako cíl školení pomocí sady SDK:

1. Připojit existující virtuální počítač jako cílové výpočetní prostředí, musíte zadat plně kvalifikovaný název domény (FQDN), uživatelské jméno a heslo pro virtuální počítač. V tomto příkladu nahraďte \<plně kvalifikovaný název domény > s veřejný plně kvalifikovaný název domény virtuálního počítače nebo veřejnou IP adresu. Nahraďte \<uživatelské jméno > a \<heslo > s SSH uživatelské jméno a heslo pro virtuální počítač.

    ```python
    from azureml.core.compute import RemoteCompute, ComputeTarget
    
    # Create the compute config
    attach_config = RemoteCompute.attach_configuration(address = "ipaddress",
                                                       ssh_port=22,
                                                       username='<username>',
                                                       password="<password>")

    # If you use SSH instead of a password, use this code:
    #                                                  ssh_port=22,
    #                                                  username='<username>',
    #                                                  password=None,
    #                                                  private_key_file="path-to-file",
    #                                                  private_key_passphrase="passphrase")

    # Attach the compute target
    compute = ComputeTarget.attach(ws, "attach-dsvm", attach_config)

    compute.wait_for_completion(show_output=True)
    ```

1. Vytvořte konfiguraci pro DSVM cílového výpočetního prostředí. Docker a conda slouží k vytvoření a konfigurace prostředí pro školení na datové VĚDY.

    ```python
    from azureml.core.runconfig import RunConfiguration
    from azureml.core.conda_dependencies import CondaDependencies

    # Load into memory the cpu-dsvm.runconfig file created in the previous attach operation
    run_config = RunConfiguration(framework = "python")

    # Set the compute target to the Linux DSVM
    run_config.target = compute_target_name

    # Use Docker in the remote VM
    run_config.environment.docker.enabled = True

    # Use the CPU base image
    # To use GPU in DSVM, you must also use the GPU base Docker image "azureml.core.runconfig.DEFAULT_GPU_IMAGE"
    run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE
    print('Base Docker image is:', run_config.environment.docker.base_image)

    # Ask the system to provision a new conda environment based on the conda_dependencies.yml file
    run_config.environment.python.user_managed_dependencies = False

    # Prepare the Docker and conda environment automatically when they're used for the first time
    run_config.prepare_environment = True

    # Specify the CondaDependencies object
    run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])

    ```

## <a id="databricks"></a>Azure Databricks

Azure Databricks je prostředí založené na Apache Spark v cloudu Azure. Při tréninku modelů pomocí kanálu služby Azure Machine Learning může sloužit jako cílové výpočetní prostředí.

> [!IMPORTANT]
> Azure Databricks cílové výpočetní prostředí jde použít jenom v rámci kanálu strojového učení.
>
> Pracovní prostor služby Azure Databricks musíte vytvořit předtím, než ho použijete k natrénování modelu. Vytvoření těchto prostředků najdete v tématu [spuštění úlohy Spark job v Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal).

Připojení Azure Databricks jako cílové výpočetní prostředí, musíte používat sadu SDK Azure Machine Learning a zadejte následující informace:

* __Název COMPUTE__: Název, který se má přiřadit na tento výpočetní prostředek.
* __Název pracovního prostoru Databricks__: Název pracovního prostoru Azure Databricks.
* __Přístupový token__: Přístupový token pro ověření do Azure Databricks. K vygenerování přístupového tokenu, naleznete v tématu [ověřování](https://docs.azuredatabricks.net/api/latest/authentication.html).

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

    # Create the attach config
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

Azure Data Lake Analytics je platforma analýzy velkých objemů dat v cloudu Azure. Při tréninku modelů pomocí kanálu služby Azure Machine Learning může sloužit jako cílové výpočetní prostředí na platformu.

> [!IMPORTANT]
> Azure Data Lake Analytics cílové výpočetní prostředí jde použít jenom v rámci kanálu strojového učení.
>
> Předtím, než ho použijete k natrénování modelu, musíte vytvořit účet Azure Data Lake Analytics. Chcete-li vytvořit tento prostředek, naleznete v tématu [Začínáme s Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal).

Připojit Data Lake Analytics jako cílové výpočetní prostředí, musíte používat sadu SDK Azure Machine Learning a zadejte následující informace:

* __Název COMPUTE__: Název, který se má přiřadit na tento výpočetní prostředek.
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
    
    # Create the attach config
    attach_config = AdlaCompute.attach_configuration(resource_group = adla_resource_group,
                                                     account_name = adla_account_name)
    # Attach the ADLA
    adla_compute = ComputeTarget.attach(
             ws,
             adla_compute_name,
             attach_config
         )
    
    adla_compute.wait_for_completion(True)
```

> [!TIP]
> Azure Machine Learning kanály pouze práce s daty, která je uložena v úložišti dat výchozího účtu Data Lake Analytics. Pokud jsou data, je nutné v jiné než výchozí úložiště, můžete použít [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) operace kopírování dat před trénování modelu.

## <a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight je oblíbená platforma pro analýzy velkých objemů dat. Tato platforma poskytuje Apache Spark, který slouží k natrénování modelu.

> [!IMPORTANT]
> Clusteru HDInsight musíte vytvořit předtím, než ho použijete k natrénování modelu. Vytvoření Spark na clusteru HDInsight najdete v tématu [vytvoření clusteru Spark v HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql).
>
> Při vytváření clusteru, musíte zadat uživatelské jméno SSH a heslo. Poznamenejte si tyto hodnoty, podle potřeby je používat jako cílové výpočetní prostředí HDInsight.
>
> Po vytvoření clusteru se plně kvalifikovaný název \<Název_clusteru >. azurehdinsight.net, kde \<Název_clusteru > je název, který jste zadali pro cluster. Budete potřebovat adresu plně kvalifikovaného názvu domény (nebo veřejnou IP adresu clusteru), že cluster používat jako cílové výpočetní prostředí.

Konfigurace HDInsight jako cílové výpočetní prostředí, musíte zadat plně kvalifikovaný název domény, uživatelské jméno a heslo pro HDInsight cluster. Následující příklad používá sadu SDK připojit cluster do pracovního prostoru. V tomto příkladu nahraďte \<plně kvalifikovaný název domény > s veřejný plně kvalifikovaný název domény clusteru nebo veřejnou IP adresu. Nahraďte \<uživatelské jméno > a \<heslo > s SSH uživatelské jméno a heslo pro cluster.

> [!NOTE]
> Vyhledejte úplný název domény pro váš cluster, přejděte na web Azure Portal a vyberte svůj cluster HDInsight. V části __přehled__, se zobrazí plně kvalifikovaný název domény v __URL__ položka. Chcete-li získat úplný název domény, odeberte https:\// předpony od začátku vstupu.

![Získat plně kvalifikovaný název pro cluster HDInsight na webu Azure Portal](./media/how-to-set-up-training-targets/hdinsight-overview.png)

```python
from azureml.core.compute import HDInsightCompute, ComputeTarget

try:
    # Attach an HDInsight cluster as a compute target
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

# Configure the HDInsight run
# Load the runconfig object from the myhdi.runconfig file generated in the previous attach operation
run_config = RunConfiguration.load(project_object = project, run_config_name = 'myhdi')

# Ask the system to prepare the conda environment automatically when it's used for the first time
run_config.auto_prepare_environment = True
```

## <a name="submit-training-runs"></a>Odeslat tréninkových spuštění

Existují dva způsoby, jak odeslat školení spuštění:

* Odeslat školení spustíte s použitím `ScriptRunConfig` objektu.
* Odeslat školení spustíte s použitím `Pipeline` objektu.

> [!IMPORTANT]
> Azure Databricks a Azure Datalake Analytics výpočetní cíle lze použít pouze v rámci kanálu.
>
> Cílové místní výpočetní prostředí nelze použít v rámci kanálu.

### <a name="scriptrunconfig-object"></a>Objekt ScriptRunConfig

Vzorek kódu k odeslání školicí spustit s `ScriptRunConfig` objekt je stejný pro všechny typy cílových výpočetních prostředí:

1. Vytvoření `ScriptRunConfig` s použitím konfigurace spuštění pro cílové výpočetní prostředí.
1. Odešlete spuštění.
1. Vyčkat, než běžet do dokončení.

Následující příklad použije konfiguraci pro cíl spravované systému místním výpočetním vytvořili dříve:

```python
src = ScriptRunConfig(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
run = exp.submit(src)
run.wait_for_completion(show_output = True)
```


### <a name="pipeline-object"></a>Kanál objektů

Vzorek kódu k odeslání školicí spustit s `Pipeline` objekt je stejný pro všechny typy cílových výpočetních prostředí:

1. Přidejte krok `Pipeline` objekt pro výpočetní prostředek.
1. Odešlete spustit pomocí kanálu.
1. Vyčkat, než běžet do dokončení.

Následující příklad používá cílového výpočetního prostředí Azure Databricks vytvořili dříve:

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

# List of steps to run
steps = [dbStep]
pipeline = Pipeline(workspace=ws, steps=steps)
pipeline_run = Experiment(ws, 'Demo_experiment').submit(pipeline)
pipeline_run.wait_for_completion()
```

Další informace o machine learning kanálů najdete v tématu [kanály a Azure Machine Learning](concept-ml-pipelines.md).

Například Jupyter notebooks, které ukazují, jak pro trénování modelu s použitím kanálu, naleznete v tématu [ https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline ](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline).

## <a name="access-computes-in-the-azure-portal"></a>Vypočítá přístup na webu Azure Portal

Můžete přistupovat cílových výpočetních prostředí, které jsou spojeny s pracovního prostoru na webu Azure Portal. 

### <a name="view-compute-targets"></a>Zobrazení cílových výpočetních prostředí

Pokud chcete zobrazit cílových výpočetních prostředí pro váš pracovní prostor, postupujte následovně:

1. Přejděte [webu Azure portal](https://portal.azure.com) a otevřete pracovní prostor.
1. V části __aplikací__vyberte __Compute__.

    ![Zobrazení cílových výpočetních prostředí](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)

### <a name="create-a-compute-target"></a>Vytvořte cílové výpočetní prostředí

Postupujte podle předchozích kroků a zobrazí se seznam cílových výpočetních prostředí. Potom vytvořte cílové výpočetní prostředí pomocí těchto kroků:

1. Vyberte znaménko Plus (+) Chcete-li přidat cílové výpočetní prostředí.

    ![Přidat cílové výpočetní prostředí](./media/how-to-set-up-training-targets/add-compute-target.png)

1. Zadejte název cílového výpočetního prostředí.
1. Vyberte **Machine Learning Compute** jako typ výpočetních prostředků a použít pro __školení__.

    > [!IMPORTANT]
    > Výpočetní prostředí Azure Machine Learning můžete vytvořit pouze jako spravované výpočetní prostředek pro vzdělávání.

1. Vyplňte formulář. Zadejte hodnoty pro požadované vlastnosti, zejména **řadu virtuálních počítačů**a **maximální počet uzlů** můžete aktivovat tak výpočetní prostředky. 
1. Vyberte __Vytvořit__.
1. Zobrazení stavu operace vytvoření cílové výpočetní prostředí výběrem ze seznamu:

    ![Vyberte cílové výpočetní prostředí, chcete-li zobrazit stav operace create](./media/how-to-set-up-training-targets/View_list.png)

1. Zobrazí podrobnosti pro cílové výpočetní prostředí:

    ![Zobrazit podrobnosti o cílovém počítači](./media/how-to-set-up-training-targets/compute-target-details.png)

Jak je popsáno výše, teď můžete zadat spustit proti počítačové cíle.


### <a name="reuse-existing-compute-targets"></a>Znovu použít existující cílových výpočetních prostředí

Postupujte podle kroků popsaných dříve zobrazí seznam cílových výpočetních prostředí. Pak pomocí těchto kroků můžete znovu použít cílového výpočetního prostředí:

1. Vyberte znaménko Plus (+) Chcete-li přidat cílové výpočetní prostředí.
1. Zadejte název cílového výpočetního prostředí.
1. Vyberte typ compute připojit pro __školení__:

    > [!IMPORTANT]
    > Ne všechny výpočetní typy je možné připojit na webu Azure Portal.
    > Typy výpočetní prostředky, které může být aktuálně připojena k trénování zahrnují:
    >
    > * Vzdáleném virtuálním počítači
    > * Azure Databricks
    > * Azure Data Lake Analytics
    > * Azure HDInsight

1. Vyplňte formulář a zadejte hodnoty pro požadované vlastnosti.

    > [!NOTE]
    > Společnost Microsoft doporučuje používat klíče SSH, které jsou bezpečnější než hesla. Hesla se stát terčem útoku hrubou silou. Klíče SSH využívají kryptografické podpisy. Informace o tom, jak vytvořit klíče SSH pro použití s Azure Virtual Machines najdete v následujících dokumentech:
    >
    > * [Vytvoření a použití klíčů SSH v Linuxu nebo macOS](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Vytvoření a používání klíčů SSH na Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Vyberte __připojit__.
1. Zobrazení stavu operace připojení tak, že vyberete cílového výpočetního prostředí v seznamu.

Nyní můžete odeslat spuštění na těchto cílových výpočetních prostředí jak je popsáno výše.

## <a name="notebook-examples"></a>Příklady poznámkového bloku

Příklady najdete v tématu poznámkových bloků v následujících umístěních:

* [postupy-k-použití azureml a školení](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [kurzy/img – klasifikace – část 1 – training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další postup

* [Referenční informace k Azure Machine Learning SDK](https://aka.ms/aml-sdk)
* [Kurz: Trénování modelu](tutorial-train-models-with-aml.md)
* [Kam chcete nasazovat modely](how-to-deploy-and-where.md)
* [Vytvořit machine learning kanálů prostřednictvím služby Azure Machine Learning](concept-ml-pipelines.md)
