---
title: Vytvoření výpočtů pro školení & nasazení (Python)
titleSuffix: Azure Machine Learning
description: Použití sady Azure Machine Learning Python SDK k vytváření školicích a nasazování výpočetních prostředků (cíle výpočtů) pro Machine Learning
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 07/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: cc7ca9d217e405b0b39779cf256edcf0669afd6b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91302430"
---
# <a name="create-compute-targets-for-model-training-and-deployment-with-python-sdk"></a>Vytváření výpočetních cílů pro školení modelů a nasazení pomocí sady Python SDK

V tomto článku vytvoříte a spravujete výpočetní cíle pomocí sady Azure Machine Learning Python SDK. Můžete také vytvořit a spravovat výpočetní cíle pomocí:
* [Azure Machine Learning Studio](how-to-create-attach-compute-studio.md), 
* [Rozšíření CLI](reference-azure-machine-learning-cli.md#resource-management) pro Azure Machine Learning
* [Vs Code rozšíření](how-to-manage-resources-vscode.md#compute-clusters) pro Azure Machine Learning.


## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes
* [Sada SDK Azure Machine Learning pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)
* [Pracovní prostor Azure Machine Learning](how-to-manage-workspace.md)

## <a name="limitations"></a>Omezení

* **Nevytvářejte více souběžných příloh se stejným výpočetním** prostředím z pracovního prostoru. Například připojení jednoho clusteru služby Azure Kubernetes k pracovnímu prostoru pomocí dvou různých názvů. Každá nová příloha zruší předchozí existující přílohy.

    Pokud chcete změnit připojení cíle výpočtů, například pokud chcete změnit nastavení TLS nebo jiné konfigurace clusteru, musíte nejdřív odebrat existující přílohu.

* Některé scénáře uvedené v tomto dokumentu jsou označeny jako __Náhled__. Funkce Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="whats-a-compute-target"></a>Co je cílový výpočetní výkon?

Pomocí Azure Machine Learning můžete model vyškolit na nejrůznějších materiálech nebo prostředích, které se souhrnně označují jako [__výpočetní cíle__](concept-azure-machine-learning-architecture.md#compute-targets). Cílem výpočetní služby může být místní počítač nebo cloudový prostředek, jako je Azure Machine Learning COMPUTE, Azure HDInsight nebo vzdálený virtuální počítač.  Můžete také vytvořit výpočetní cíle pro nasazení modelu, jak je popsáno v [části "kde a jak nasadit vaše modely"](how-to-deploy-and-where.md).

## <a name="compute-targets-for-training"></a>Výpočetní cíle pro školení

Azure Machine Learning má různou podporu napříč různými výpočetními cíli. Typický životní cyklus vývoje modelu začíná vývojem a experimentováním s malým množstvím dat. V této fázi doporučujeme použít místní prostředí. Například váš místní počítač nebo cloudový virtuální počítač. Při horizontálním navýšení kapacity nebo provádění distribuovaných školení doporučujeme pomocí Azure Machine Learning COMPUTE vytvořit cluster s jedním nebo několika uzly, který při každém odeslání běhu provede automatické škálování. Můžete také připojit vlastní výpočetní prostředek, i když se podpora různých scénářů může lišit, jak je popsáno níže:

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

> [!NOTE]
> Azure Machine Learning výpočetní clustery je možné vytvořit jako trvalý prostředek nebo dynamicky vytvořit, když požádáte o spuštění. Vytváření na základě spuštění po dokončení školení odstraní cíl výpočtů, takže nebudete moct znovu použít výpočetní cíle vytvořené tímto způsobem.

Pro konfiguraci těchto výpočetních cílů použijte následující části:

* [Místní počítač](#local)
* [Azure Machine Learning výpočetní cluster](#amlcompute)
* [Výpočetní instance Azure Machine Learningu](#instance)
* [Vzdálené virtuální počítače](#vm)
* [Azure HDInsight](#hdinsight)

## <a name="compute-targets-for-inference"></a>Výpočetní cíle pro odvození

Při vykonávání odvození Azure Machine Learning vytvoří kontejner Docker, který je hostitelem modelu a přidružených prostředků potřebných k jeho použití. Tento kontejner se pak použije v jednom z následujících scénářů nasazení:

* Jako __webovou službu__ , která se používá pro odvození v reálném čase. Nasazení webové služby používají jeden z následujících výpočetních cílů:

    * [Místní počítač](#local)
    * [Výpočetní instance Azure Machine Learningu](#instance)
    * [Azure Container Instances](#aci)
    * [Azure Kubernetes Service](how-to-create-attach-kubernetes.md)
    * Azure Functions (Preview). Nasazení na Azure Functions spoléhá jenom na Azure Machine Learning k sestavení kontejneru Docker. Odtud je nasazena pomocí Azure Functions. Další informace najdete v tématu [nasazení modelu Machine Learning do Azure Functions (Preview)](how-to-deploy-functions.md).

* Jako koncový bod __odvození dávky__ , který slouží k pravidelnému zpracování dávek dat. Odvození dávky využívají [Azure Machine Learning výpočetní cluster](#amlcompute).

* Do __zařízení IoT__ (Preview). Nasazení do zařízení IoT spoléhá jenom na Azure Machine Learning k sestavení kontejneru Docker. Odtud je nasazena pomocí Azure IoT Edge. Další informace najdete v tématu [nasazení jako modulu IoT Edge (Preview)](/azure/iot-edge/tutorial-deploy-machine-learning).

## <a name="local-computer"></a><a id="local"></a>Místní počítač

Pokud používáte místní počítač pro **školení**, není nutné vytvářet cíl výpočtů.  Stačí jenom [Odeslat školicí běh](how-to-set-up-training-targets.md) z místního počítače.

Použijete-li místní počítač pro **odvození**, je nutné mít nainstalovaný Docker. Chcete-li provést nasazení, použijte [LocalWebservice. deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py&preserve-view=true#deploy-configuration-port-none-) a definujte port, který bude webová služba používat. Pak použijte normální proces nasazení, jak je popsáno v tématu [nasazení modelů pomocí Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="azure-machine-learning-compute-cluster"></a><a id="amlcompute"></a>Azure Machine Learning výpočetní cluster

Výpočetní cluster Azure Machine Learning je spravovaná výpočetní infrastruktura, která umožňuje snadno vytvořit výpočetní prostředí s jedním uzlem nebo několika uzly. Výpočetní prostředí se vytvoří v rámci vaší oblasti pracovního prostoru jako prostředek, který se dá sdílet s ostatními uživateli v pracovním prostoru. Výpočetní výkon se při odeslání úlohy automaticky škáluje a dá se umístit do Azure Virtual Network. Výpočetní výkon se spouští v kontejnerovém prostředí a zabalí závislosti vašich modelů v [kontejneru Docker](https://www.docker.com/why-docker).

Azure Machine Learning výpočetní prostředí můžete použít k distribuci procesu vybírání školení nebo dávek v rámci clusteru výpočetních uzlů procesoru nebo GPU v cloudu. Další informace o velikostech virtuálních počítačů, které zahrnují GPU, najdete v tématu [velikosti virtuálních počítačů optimalizované pro GPU](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu). 

Azure Machine Learning COMPUTE má výchozí omezení, například počet jader, které se dají přidělit. Další informace najdete v tématu [Správa a vyžádání kvót pro prostředky Azure](how-to-manage-quotas.md).

> [!TIP]
> Clustery můžou obecně škálovat až 100 uzlů, pokud máte dostatečnou kvótu pro požadovaný počet jader. Ve výchozím nastavení jsou clustery nastavené s povolenou komunikací mezi uzly mezi uzly clusteru za účelem podpory MPI úloh. Můžete ale škálovat clustery na tisíce uzlů pouhým vyvoláním [lístku podpory](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)a žádostí o povolení seznamu pro vaše předplatné nebo pracovní prostor nebo konkrétního clusteru pro zakázání komunikace mezi uzly. 

Azure Machine Learning výpočetní prostředí je možné znovu použít v rámci spuštění. Výpočetní prostředky je možné sdílet s ostatními uživateli v pracovním prostoru a jsou mezi nimi zachované, automaticky škálovat uzly nahoru nebo dolů na základě počtu odeslaných běhů a max_nodes nastavených v clusteru. Nastavení min_nodes řídí minimální dostupné uzly.

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

1. **Vytvoření a připojení**: Chcete-li v Pythonu vytvořit trvalý Azure Machine Learning výpočetní prostředek, zadejte vlastnosti **vm_size** a **max_nodes** . Azure Machine Learning pak pro ostatní vlastnosti používá inteligentní výchozí hodnoty. Výpočetní výkon se při použití vymění až na nula uzlů.   Vyhrazené virtuální počítače se vytvářejí ke spouštění vašich úloh podle potřeby.
    
    * **vm_size**: rodina virtuálních počítačů uzlů vytvořená Azure Machine Learning Compute.
    * **max_nodes**: maximální počet uzlů pro automatické horizontální navýšení kapacity při spuštění úlohy v Azure Machine Learning Compute.
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   Při vytváření Azure Machine Learning výpočetních prostředků můžete také nakonfigurovat několik pokročilých vlastností. Vlastnosti umožňují vytvořit trvalý cluster s pevnou velikostí nebo v rámci stávajícího Virtual Network Azure v rámci vašeho předplatného.  Podrobnosti najdete v tématu [Třída AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py&preserve-view=true) .

    Nebo můžete vytvořit a připojit trvalé Azure Machine Learning výpočetní prostředky v [Azure Machine Learning Studiu](how-to-create-attach-compute-studio.md#portal-create).

Teď, když jste připojili výpočetní prostředky, je dalším krokem [odeslání školicího běhu](how-to-set-up-training-targets.md) nebo [spuštění odvození dávky](how-to-use-parallel-run-step.md).

 ### <a name="lower-your-compute-cluster-cost"></a><a id="low-pri-vm"></a> Snižte náklady na výpočetní cluster.

Můžete se také rozhodnout použít pro spuštění některých nebo všech úloh [virtuální počítače s nízkou prioritou](concept-plan-manage-cost.md#low-pri-vm) . Tyto virtuální počítače nemají zaručenou dostupnost a můžou být při použití přerušeny. Přerušená úloha se restartuje, není obnovená. 

K určení virtuálního počítače s nízkou prioritou použijte libovolný z těchto způsobů:
    
* V nástroji Studio při vytváření virtuálního počítače vyberte možnost **Nízká priorita** .
    
* V sadě Python SDK nastavte `vm_priority` atribut v konfiguraci zřizování.  
    
    ```python
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                vm_priority='lowpriority',
                                                                max_nodes=4)
    ```
    
* Pomocí rozhraní příkazového řádku nastavte `vm-priority` :
    
    ```azurecli-interactive
    az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
    ```

 ### <a name="set-up-managed-identity"></a><a id="managed-identity"></a> Nastavení spravované identity

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

Konfigurace spravované identity v konfiguraci zřizování:  
    
* Spravovaná identita přiřazená systémem:
    ```python
    # configure cluster with a system-assigned managed identity
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=5,
                                                            identity_type="SystemAssigned",
                                                            )
    ```

* Spravovaná identita přiřazená uživatelem: 

    ```python
    # configure cluster with a user-assigned managed identity
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=5,
                                                            identity_type="UserAssigned",
                                                            identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])

    cpu_cluster_name = "cpu-cluster"
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    ```

Přidat spravovanou identitu do existujícího výpočetního clusteru:  
    
* Spravovaná identita přiřazená systémem:

    ```python
    # add a system-assigned managed identity
    cpu_cluster.add_identity(identity_type="SystemAssigned")
    ````

* Spravovaná identita přiřazená uživatelem:

    ```python
    # add a user-assigned managed identity
    cpu_cluster.add_identity(identity_type="UserAssigned", 
                                identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
    ```

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

#### <a name="managed-identity-usage"></a>Využití spravované identity

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]


## <a name="azure-machine-learning-compute-instance"></a><a id="instance"></a>Výpočetní instance Azure Machine Learningu

[Výpočetní instance Azure Machine Learning](concept-compute-instance.md) je spravovaná výpočetní infrastruktura, která umožňuje snadno vytvořit jeden virtuální počítač. Výpočetní prostředí se vytvoří v rámci vaší oblasti pracovního prostoru, ale na rozdíl od výpočetního clusteru se instance nedá sdílet s ostatními uživateli v pracovním prostoru. Instance se také automaticky nezvětšuje.  Je nutné zastavit prostředek, aby nedocházelo k průběžným poplatkům.

Výpočetní instance může spouštět více úloh paralelně a má frontu úloh. 

Výpočetní instance můžou úlohy spouštět bezpečně ve [virtuálním síťovém prostředí](how-to-enable-virtual-network.md#compute-instance), aniž by museli podniky otevírat porty SSH. Úloha se spustí v kontejnerovém prostředí a zabalí závislosti vašich modelů v kontejneru Docker. 

1. **Vytvořit a připojit**: 
    
    ```python
    import datetime
    import time
    
    from azureml.core.compute import ComputeTarget, ComputeInstance
    from azureml.core.compute_target import ComputeTargetException
    
    # Choose a name for your instance
    # Compute instance name should be unique across the azure region
    compute_name = "ci{}".format(ws._workspace_id)[:10]
    
    # Verify that instance does not exist already
    try:
        instance = ComputeInstance(workspace=ws, name=compute_name)
        print('Found existing instance, use it.')
    except ComputeTargetException:
        compute_config = ComputeInstance.provisioning_configuration(
            vm_size='STANDARD_D3_V2',
            ssh_public_access=False,
            # vnet_resourcegroup_name='<my-resource-group>',
            # vnet_name='<my-vnet-name>',
            # subnet_name='default',
            # admin_user_ssh_public_key='<my-sshkey>'
        )
        instance = ComputeInstance.create(ws, compute_name, compute_config)
        instance.wait_for_completion(show_output=True)
    ```

Teď, když jste připojili výpočetní prostředky a nakonfigurovali vaše spuštění, je dalším krokem [odeslání školicího běhu](how-to-set-up-training-targets.md) nebo [nasazení modelu pro odvození](how-to-deploy-local-container-notebook-vm.md).

## <a name="azure-container-instance"></a><a id="aci"></a>Instance kontejneru Azure

Azure Container Instances (ACI) se vytváří dynamicky při nasazení modelu. Nemůžete vytvořit ani připojit ACI k pracovnímu prostoru jiným způsobem. Další informace najdete v tématu [nasazení modelu pro Azure Container Instances](how-to-deploy-azure-container-instance.md).

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Služba Azure Kubernetes Service (AKS) umožňuje různým možnostem konfigurace při použití s Azure Machine Learning. Další informace najdete v tématu [Vytvoření a připojení služby Azure Kubernetes](how-to-create-attach-kubernetes.md).

## <a name="remote-virtual-machines"></a><a id="vm"></a>Vzdálené virtuální počítače

Azure Machine Learning také podporuje uvedení vlastního výpočetního prostředku a jeho připojení k pracovnímu prostoru. Jedním z těchto typů prostředků je libovolný vzdálený virtuální počítač, pokud je dostupný z Azure Machine Learning. Prostředkem může být virtuální počítač Azure, vzdálený server ve vaší organizaci nebo místní. Konkrétně pro vzdálené spuštění s ohledem na IP adresu a přihlašovací údaje (uživatelské jméno a heslo nebo klíč SSH) můžete použít libovolný dostupný virtuální počítač.

Můžete použít systémem sestavené prostředí Conda, již existující prostředí Pythonu nebo kontejner Docker. Aby bylo možné provést v kontejneru Docker, je nutné mít na virtuálním počítači spuštěný modul Docker. Tato funkce je užitečná hlavně v případě, že chcete pružně flexibilní prostředí pro vývoj a experimentování v cloudu než na vašem místním počítači.

Pro tento scénář použijte Azure Data Science Virtual Machine (DSVM) jako virtuální počítač Azure s možností výběru. Tento virtuální počítač je předem konfigurovaným vývojovým prostředím pro datové vědy a AI v Azure. Virtuální počítač nabízí uspořádané možnosti nástrojů a platforem pro vývoj v rámci služby Machine Learning pro celou dobu životního cyklu. Další informace o tom, jak používat DSVM s Azure Machine Learning, najdete v tématu [Konfigurace vývojového prostředí](https://docs.microsoft.com/azure/machine-learning/how-to-configure-environment#dsvm).

1. **Vytvořit**: Vytvořte DSVM ještě před tím, než ho použijete ke školení svého modelu. Pokud chcete tento prostředek vytvořit, přečtěte si téma [zřízení Data Science Virtual Machine pro Linux (Ubuntu)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro).

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

Teď, když jste připojili výpočetní prostředky a nakonfigurovali svůj běh, je dalším krokem [odeslání školicího běhu](how-to-set-up-training-targets.md).

## <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight je oblíbená platforma pro analýzu velkých objemů dat. Platforma poskytuje Apache Spark, které je možné použít ke školení modelu.

1. **Vytvořit**: Vytvořte cluster HDInsight předtím, než ho použijete ke školení svého modelu. Informace o vytvoření clusteru Spark v HDInsight najdete [v tématu Vytvoření clusteru Spark v HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql). 

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

Před použitím vytvořte pracovní prostor Azure Databricks. Pokud chcete vytvořit prostředek pracovního prostoru, přečtěte si téma [spuštění úlohy Spark v dokumentu Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) .

Pokud chcete připojit Azure Databricks jako cíl výpočetních prostředků, zadejte následující informace:

* __Výpočetní název datacihly__: název, který chcete přiřadit k tomuto výpočetnímu prostředku.
* __Název pracovního prostoru datacihly__: název pracovního prostoru Azure Databricks.
* __Přístupový token datacihly__: přístupový token, který se používá k ověření Azure Databricks. Přístup k vygenerování přístupového tokenu najdete v dokumentu [ověřování](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html) .

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

Před použitím vytvořte účet Azure Data Lake Analytics. Informace o vytvoření tohoto prostředku najdete v dokumentu [Začínáme s Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal) .

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
> Kanály Azure Machine Learning můžou pracovat jenom s daty uloženými ve výchozím úložišti dat účtu Data Lake Analytics. Pokud jsou data, se kterými pracujete, v nevýchozím úložišti, můžete použít [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py&preserve-view=true) ke zkopírování dat před školeními.

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
* [Použití Azure Machine Learning s Azure Virtual Networks](how-to-enable-virtual-network.md)
