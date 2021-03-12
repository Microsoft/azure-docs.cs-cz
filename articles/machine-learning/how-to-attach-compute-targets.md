---
title: Nastavení školení & odvození cílů výpočtů
titleSuffix: Azure Machine Learning
description: Přidejte výpočetní prostředky (cíle výpočtů) do pracovního prostoru, který se použije pro školení a odvozování machine learningu.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 10/02/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: 9fa6a1758bc2e2a76291efc3bb239c5249a6e21e
ms.sourcegitcommit: 6776f0a27e2000fb1acb34a8dddc67af01ac14ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103149337"
---
# <a name="set-up-compute-targets-for-model-training-and-deployment"></a>Nastavení cílových výpočetních prostředků pro školení a nasazení modelu

Naučte se, jak připojit výpočetní prostředky Azure k pracovnímu prostoru Azure Machine Learning.  Pak můžete tyto prostředky používat jako školení a odvozování [výpočetních cílů](concept-compute-target.md) v rámci úloh strojového učení.

V tomto článku se dozvíte, jak nastavit pracovní prostor pro použití těchto výpočetních prostředků:

* Váš místní počítač
* Vzdálené virtuální počítače
* Azure HDInsight
* Azure Batch
* Azure Databricks
* Azure Data Lake Analytics
* Instance kontejneru Azure

Pokud chcete použít výpočetní cíle spravované pomocí Azure Machine Learning, přečtěte si:


* [Výpočetní instance Azure Machine Learningu](how-to-create-manage-compute-instance.md)
* [Azure Machine Learning výpočetní cluster](how-to-create-attach-compute-cluster.md)
* [Cluster služby Azure Kubernetes](how-to-create-attach-kubernetes.md)

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor služby Azure Machine Learning. Další informace najdete v tématu [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md).

* [Rozšíření Azure CLI pro službu Machine Learning](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro)nebo [rozšíření Azure Machine Learning Visual Studio Code](tutorial-setup-vscode-extension.md).

## <a name="limitations"></a>Omezení

* **Nevytvářejte více souběžných příloh se stejným výpočetním** prostředím z pracovního prostoru. Například připojení jednoho clusteru služby Azure Kubernetes k pracovnímu prostoru pomocí dvou různých názvů. Každá nová příloha zruší předchozí existující přílohy.

    Pokud se chcete znovu připojit k cílovému výpočetnímu cíli, například pokud chcete změnit nastavení TLS nebo jiné konfigurace clusteru, musíte nejdřív odebrat existující přílohu.

## <a name="whats-a-compute-target"></a>Co je cílový výpočetní výkon?

Pomocí Azure Machine Learning můžete model vyškolit na nejrůznějších materiálech nebo prostředích, které se souhrnně označují jako [__výpočetní cíle__](concept-azure-machine-learning-architecture.md#compute-targets). Cílem výpočetní služby může být místní počítač nebo cloudový prostředek, jako je Azure Machine Learning COMPUTE, Azure HDInsight nebo vzdálený virtuální počítač.  Pro nasazení modelu můžete také použít výpočetní cíle, jak je popsáno v [části "kde a jak nasadit vaše modely"](how-to-deploy-and-where.md).


## <a name="local-computer"></a><a id="local"></a>Místní počítač

Pokud používáte místní počítač pro **školení**, není nutné vytvářet cíl výpočtů.  Stačí jenom [Odeslat školicí běh](how-to-set-up-training-targets.md) z místního počítače.

Použijete-li místní počítač pro **odvození**, je nutné mít nainstalovaný Docker. K provedení nasazení použijte [LocalWebservice.deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.local.localwebservice#deploy-configuration-port-none-) k definování portu, který bude webová služba používat. Pak použijte normální proces nasazení, jak je popsáno v tématu [nasazení modelů pomocí Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="remote-virtual-machines"></a><a id="vm"></a>Vzdálené virtuální počítače

Azure Machine Learning podporuje také připojení virtuálního počítače Azure. Virtuální počítač musí být Azure Data Science Virtual Machine (DSVM). Tento virtuální počítač je předem konfigurovaným vývojovým prostředím pro datové vědy a AI v Azure. Virtuální počítač nabízí uspořádané možnosti nástrojů a platforem pro vývoj v rámci služby Machine Learning pro celou dobu životního cyklu. Další informace o tom, jak používat DSVM s Azure Machine Learning, najdete v tématu [Konfigurace vývojového prostředí](./how-to-configure-environment.md#dsvm).

1. **Vytvořit**: Vytvořte DSVM ještě před tím, než ho použijete ke školení svého modelu. Pokud chcete tento prostředek vytvořit, přečtěte si téma [zřízení Data Science Virtual Machine pro Linux (Ubuntu)](./data-science-virtual-machine/dsvm-ubuntu-intro.md).

    > [!WARNING]
    > Azure Machine Learning podporuje jenom virtuální počítače, které spouštějí **Ubuntu**. Když vytváříte virtuální počítač nebo zvolíte existující virtuální počítač, musíte vybrat virtuální počítač, který používá Ubuntu.
    > 
    > Azure Machine Learning také vyžaduje, aby virtuální počítač měl __veřejnou IP adresu__.

1. **Připojit**: Chcete-li připojit existující virtuální počítač jako cíl služby COMPUTE, je nutné zadat ID prostředku, uživatelské jméno a heslo pro virtuální počítač. ID prostředku virtuálního počítače se dá vytvořit pomocí ID předplatného, názvu skupiny prostředků a názvu virtuálního počítače pomocí následujícího formátu řetězce: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Compute/virtualMachines/<vm_name>`

 
   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   
   attach_config = RemoteCompute.attach_configuration(resource_id='<resource_id>',
                                                   ssh_port=22,
                                                   username='<username>',
                                                   password="<password>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   Nebo můžete připojit DSVM k vašemu pracovnímu prostoru [pomocí Azure Machine Learning studia](how-to-create-attach-compute-studio.md#attached-compute).

    > [!WARNING]
    > Nevytvářejte více souběžných příloh stejného DSVM z vašeho pracovního prostoru. Každá nová příloha zruší předchozí existující přílohy.

1. **Konfigurace**: Vytvořte konfiguraci spuštění pro cíl služby DSVM Compute. Docker a conda slouží k vytvoření a konfiguraci školicího prostředí na DSVM.

   ```python
   from azureml.core import ScriptRunConfig
   from azureml.core.environment import Environment
   from azureml.core.conda_dependencies import CondaDependencies
   
   # Create environment
   myenv = Environment(name="myenv")
   
   # Specify the conda dependencies
   myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
   
   # If no base image is explicitly specified the default CPU image "azureml.core.runconfig.DEFAULT_CPU_IMAGE" will be used
   # To use GPU in DSVM, you should specify the default GPU base Docker image or another GPU-enabled image:
   # myenv.docker.enabled = True
   # myenv.docker.base_image = azureml.core.runconfig.DEFAULT_GPU_IMAGE
   
   # Configure the run configuration with the Linux DSVM as the compute target and the environment defined above
   src = ScriptRunConfig(source_directory=".", script="train.py", compute_target=compute, environment=myenv) 
   ```

## <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight je oblíbená platforma pro analýzu velkých objemů dat. Platforma poskytuje Apache Spark, které je možné použít ke školení modelu.

1. **Vytvořit**: Vytvořte cluster HDInsight předtím, než ho použijete ke školení svého modelu. Informace o vytvoření clusteru Spark v HDInsight najdete [v tématu Vytvoření clusteru Spark v HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md). 

    > [!WARNING]
    > Azure Machine Learning vyžaduje, aby cluster HDInsight měl __veřejnou IP adresu__.

    Při vytváření clusteru je nutné zadat uživatelské jméno a heslo SSH. Tyto hodnoty si poznamenejte, protože je budete potřebovat k použití HDInsight jako cíle výpočtů.
    
    Po vytvoření clusteru se k němu připojte pomocí \<clustername> názvu hostitele – SSH.azurehdinsight.NET, kde \<clustername> je název, který jste zadali pro cluster. 

1. **Připojit**: Pokud chcete připojit cluster HDInsight jako cíl výpočetní služby, musíte zadat ID prostředku, uživatelské jméno a heslo pro cluster HDInsight. ID prostředku clusteru HDInsight se dá vytvořit pomocí ID předplatného, názvu skupiny prostředků a názvu clusteru HDInsight pomocí následujícího formátu řetězce: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.HDInsight/clusters/<cluster_name>`

    ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase

    attach_config = HDInsightCompute.attach_configuration(resource_id='<resource_id>',
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

   except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

   hdi_compute.wait_for_completion(show_output=True)
   ```

   Nebo můžete připojit cluster HDInsight k vašemu pracovnímu prostoru [pomocí Azure Machine Learning studia](how-to-create-attach-compute-studio.md#attached-compute).

    > [!WARNING]
    > Nevytvářejte více souběžných příloh ke stejné službě HDInsight z vašeho pracovního prostoru. Každá nová příloha zruší předchozí existující přílohy.

1. **Konfigurace**: Vytvořte konfiguraci spuštění pro cíl služby HDI Compute. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Teď, když jste připojili výpočetní prostředky a nakonfigurovali svůj běh, je dalším krokem [odeslání školicího běhu](how-to-set-up-training-targets.md).

## <a name="azure-batch"></a><a id="azbatch"></a>Azure Batch 

Azure Batch se používá ke efektivnímu spouštění rozsáhlých paralelních a vysoce výkonných aplikací pro výpočetní prostředí (HPC) v cloudu. AzureBatchStep se dá použít v kanálu Azure Machine Learning k odesílání úloh do fondu Azure Batch počítačů.

Chcete-li připojit Azure Batch jako cíl výpočtů, je nutné použít sadu Azure Machine Learning SDK a zadat následující informace:

-    **Azure Batch výpočetní název**: popisný název, který se má použít pro výpočetní prostředky v pracovním prostoru.
-    **Azure Batch název účtu**: název účtu Azure Batch
-    **Skupina prostředků**: Skupina prostředků, která obsahuje účet Azure Batch.

Následující kód ukazuje, jak připojit Azure Batch jako cíl výpočtů:

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

# Name to associate with new compute in workspace
batch_compute_name = 'mybatchcompute'

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>"  # Name of the Batch account
# Name of the resource group which contains this account
batch_resource_group = "<batch_resource_group>"

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(
        resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(
        ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

> [!WARNING]
> Nevytvářejte více souběžných příloh stejného Azure Batch z vašeho pracovního prostoru. Každá nová příloha zruší předchozí existující přílohy.

### <a name="azure-databricks"></a><a id="databricks"></a>Azure Databricks

Azure Databricks je prostředí založené na Apache Spark v cloudu Azure. Dá se použít jako cíl služby COMPUTE s kanálem Azure Machine Learning.

Před použitím vytvořte pracovní prostor Azure Databricks. Pokud chcete vytvořit prostředek pracovního prostoru, přečtěte si téma [spuštění úlohy Spark v dokumentu Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal) .

Pokud chcete připojit Azure Databricks jako cíl výpočetních prostředků, zadejte následující informace:

* __Výpočetní název datacihly__: název, který chcete přiřadit k tomuto výpočetnímu prostředku.
* __Název pracovního prostoru datacihly__: název pracovního prostoru Azure Databricks.
* __Přístupový token datacihly__: přístupový token, který se používá k ověření Azure Databricks. Přístup k vygenerování přístupového tokenu najdete v dokumentu [ověřování](/azure/databricks/dev-tools/api/latest/authentication) .

Následující kód ukazuje, jak připojit Azure Databricks jako výpočetní cíl se sadou Azure Machine Learning SDK (__pracovní prostor datacihly musí být přítomen ve stejném předplatném jako váš pracovní prostor AML__):

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

Podrobnější příklad najdete v [ukázkovém poznámkovém bloku](https://aka.ms/pl-databricks) na GitHubu.

> [!WARNING]
> Nevytvářejte více souběžných příloh stejného Azure Databricks z vašeho pracovního prostoru. Každá nová příloha zruší předchozí existující přílohy.

### <a name="azure-data-lake-analytics"></a><a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics je platforma pro analýzu velkých objemů dat v cloudu Azure. Dá se použít jako cíl služby COMPUTE s kanálem Azure Machine Learning.

Před použitím vytvořte účet Azure Data Lake Analytics. Informace o vytvoření tohoto prostředku najdete v dokumentu [Začínáme s Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md) .

Chcete-li připojit Data Lake Analytics jako cíl výpočtů, je nutné použít sadu Azure Machine Learning SDK a zadat následující informace:

* __Název výpočtu__: název, který chcete přiřadit k tomuto výpočetnímu prostředku.
* __Skupina prostředků__: Skupina prostředků, která obsahuje účet Data Lake Analytics.
* __Název účtu__: název Data Lake Analytics účtu.

Následující kód ukazuje, jak připojit Data Lake Analytics jako cíl výpočtů:

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

Podrobnější příklad najdete v [ukázkovém poznámkovém bloku](https://aka.ms/pl-adla) na GitHubu.

> [!WARNING]
> Nevytvářejte více souběžných příloh stejného ADLA z vašeho pracovního prostoru. Každá nová příloha zruší předchozí existující přílohy.

> [!TIP]
> Kanály Azure Machine Learning můžou pracovat jenom s daty uloženými ve výchozím úložišti dat účtu Data Lake Analytics. Pokud jsou data, se kterými pracujete, v nevýchozím úložišti, můžete použít [`DataTransferStep`](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep) ke zkopírování dat před školeními.

## <a name="azure-container-instance"></a><a id="aci"></a>Instance kontejneru Azure

Azure Container Instances (ACI) se vytváří dynamicky při nasazení modelu. Nemůžete vytvořit ani připojit ACI k pracovnímu prostoru jiným způsobem. Další informace najdete v tématu [nasazení modelu pro Azure Container Instances](how-to-deploy-azure-container-instance.md).

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Služba Azure Kubernetes Service (AKS) umožňuje různým možnostem konfigurace při použití s Azure Machine Learning. Další informace najdete v tématu [Vytvoření a připojení služby Azure Kubernetes](how-to-create-attach-kubernetes.md).


## <a name="notebook-examples"></a>Příklady poznámkových bloků

Příklady školení s různými cíli výpočtů najdete v těchto poznámkových blocích:
* [postupy – použití – AzureML/školení](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [kurzy/img-Classification-part1-Training. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další kroky

* Pomocí výpočetního prostředku můžete [nakonfigurovat a odeslat školicí běh](how-to-set-up-training-targets.md).
* [Kurz: výuka modelu](tutorial-train-models-with-aml.md) používá ke školení modelu spravovaný výpočetní cíl.
* Naučte se [efektivně ladit parametry](how-to-tune-hyperparameters.md) pro vytváření lepších modelů.
* Jakmile budete mít školený model, zjistěte, [jak a kde nasadit modely](how-to-deploy-and-where.md).
* [Použití Azure Machine Learning s Azure Virtual Networks](./how-to-network-security-overview.md)