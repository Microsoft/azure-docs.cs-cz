---
title: Použití výpočetních cílů pro trénování modelu
titleSuffix: Azure Machine Learning
description: Nakonfigurujte tréninková prostředí (výpočetní cíle) pro školení modelu strojového učení. Můžete snadno přepínat mezi tréninkovými prostředími. Začněte trénovat lokálně. Pokud potřebujete horizontální navýšení kapacity, přepněte na cloudový výpočetní cíl.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/13/2020
ms.custom: seodec18
ms.openlocfilehash: 7fcfac923da1c0daee58b10d92cbc6a6ad5e7910
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383409"
---
# <a name="set-up-and-use-compute-targets-for-model-training"></a>Nastavení a použití výpočetních cílů pro trénování modelu 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

S Azure Machine Learning můžete trénovat svůj model na různých prostředků nebo prostředí, souhrnně označované jako [__výpočetní cíle__](concept-azure-machine-learning-architecture.md#compute-targets). Cílovým výpočetním objektem může být místní počítač nebo cloudový prostředek, například Výpočetní prostředky služby Machine Learning, Azure HDInsight nebo vzdálený virtuální počítač.  Můžete také vytvořit výpočetní cíle pro nasazení modelu, jak je popsáno v ["Kde a jak nasadit modely"](how-to-deploy-and-where.md).

Výpočetní cíl můžete vytvořit a spravovat pomocí azure machine learningu SDK, Azure Machine Learning studio, Azure CLI nebo Rozšíření Azure Machine Learning VS Code. Pokud máte výpočetní cíle, které byly vytvořeny prostřednictvím jiné služby (například clusterHDInsight), můžete je použít jejich připojením k pracovnímu prostoru Azure Machine Learning.
 
V tomto článku se dozvíte, jak používat různé výpočetní cíle pro trénování modelu.  Postup pro všechny výpočetní cíle se řídí stejným pracovním postupem:
1. __Vytvořte__ výpočetní cíl, pokud ho ještě nemáte.
2. __Připojte__ výpočetní cíl k pracovnímu prostoru.
3. __Nakonfigurujte__ výpočetní cíl tak, aby obsahoval prostředí Pythonu a závislosti balíčků, které váš skript potřebuje.


>[!NOTE]
> Kód v tomto článku byl testován pomocí sady Azure Machine Learning SDK verze 1.0.74.

## <a name="compute-targets-for-training"></a>Výpočetní cíle pro školení

Azure Machine Learning má různou podporu napříč různými výpočetními cíli. Typický životní cyklus vývoje modelu začíná vývojem a experimenty na malém množství dat. V této fázi doporučujeme používat místní prostředí. Například místní počítač nebo virtuální počítač na cloudu. Při škálování na výšiny školení na větší sady dat nebo dělat distribuované školení, doporučujeme pomocí Azure Machine Learning Compute vytvořit jeden nebo více uzlů clusteru, který se automaticky škáluje při každém odeslání spustit. Můžete také připojit vlastní výpočetní prostředek, i když podpora pro různé scénáře se může lišit podle níže uvedeného:

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]


> [!NOTE]
> Azure Machine Learning Compute lze vytvořit jako trvalý prostředek nebo vytvořit dynamicky, když požádáte o spuštění. Vytvoření založené na spuštění odebere cíl výpočetních prostředků po dokončení trénovacího běhu, takže nelze znovu použít výpočetní cíle vytvořené tímto způsobem.

## <a name="whats-a-run-configuration"></a>Co je konfigurace spuštění?

Při trénování je běžné spouštět v místním počítači a později spustit tento školicí skript na jiný výpočetní cíl. S Azure Machine Learning můžete spouštět skript na různých výpočetních cílů bez nutnosti měnit skript.

Vše, co musíte udělat, je definovat prostředí pro každý výpočetní cíl v rámci **konfigurace spuštění**.  Potom, když chcete spustit váš trénovací experiment na jiný výpočetní cíl, zadejte konfiguraci spuštění pro tento výpočetní. Podrobnosti o určení prostředí a vazba pro spuštění konfigurace naleznete v [tématu Vytvoření a správa prostředí pro školení a nasazení](how-to-use-environments.md).

Další informace o [odesílání experimentů](#submit) na konci tohoto článku.

## <a name="whats-an-estimator"></a>Co je odhad?

Pro usnadnění trénování modelu pomocí oblíbených architektur, Azure Machine Learning Python SDK poskytuje alternativní vyšší úroveň abstrakce, třídy odhadu.  Tato třída umožňuje snadno vytvořit konfigurace spuštění. Můžete vytvořit a použít obecný [Odhad](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) k odeslání školicích skriptů, které používají libovolný rámec učení, který zvolíte (například scikit-learn). Doporučujeme použít odhad pro školení, protože automaticky vytváří vložené objekty, jako je prostředí nebo objekty RunConfiguration za vás. Pokud chcete mít větší kontrolu nad tím, jak se tyto objekty vytvářejí, a určit, jaké balíčky se mají nainstalovat pro spuštění experimentu, odešlete [pomocí](#amlcompute) trénovacích experimentů pomocí objektu RunConfiguration na azure machine learning compute.

Pro úlohy PyTorch, TensorFlow a Chainer poskytuje Azure Machine Learning také příslušné odhady [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)a [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) pro zjednodušení používání těchto architektur.

Další informace naleznete v [tématu Train ML Models with estimators](how-to-train-ml-models.md).

## <a name="whats-an-ml-pipeline"></a>Co je to ML pipeline?

S kanály ML můžete optimalizovat pracovní postup s jednoduchostí, rychlostí, přenositelností a opětovným použitím. Při vytváření kanálů pomocí Azure Machine Learning se můžete soustředit na své odborné znalosti, strojové učení, nikoli na infrastrukturu a automatizaci.

Ml kanály jsou konstruovány z více **kroků**, které jsou odlišné výpočetní jednotky v potrubí. Každý krok může běžet nezávisle a používat izolované výpočetní prostředky. To umožňuje více datových vědců pracovat na stejném kanálu ve stejnou dobu bez přezdanění výpočetních prostředků a také umožňuje snadno používat různé typy výpočetních prostředků nebo velikosti pro každý krok.

> [!TIP]
> Ml Pipelines můžete použít spustit konfiguraci nebo odhady při trénování modelů.

Zatímco ml kanály můžete trénovat modely, mohou také připravit data před trénování a nasazovat modely po trénování. Jedním z primárních případů použití pro kanály je dávkové vyhodnocování. Další informace najdete [v tématu Potrubí: Optimalizace pracovních postupů strojového učení](concept-ml-pipelines.md).

## <a name="set-up-in-python"></a>Nastavení v Pythonu

Pomocí následujících částí nakonfigurujte tyto výpočetní cíle:

* [Místní počítač](#local)
* [Výpočetní prostředky služby Azure Machine Learning](#amlcompute)
* [Vzdálené virtuální počítače](#vm)
* [Azure HDInsight](#hdinsight)


### <a name="local-computer"></a><a id="local"></a>Místní počítač

1. **Vytvořit a připojit**: Není nutné vytvářet nebo připojovat výpočetní cíl pro použití místního počítače jako tréninkového prostředí.  

1. **Konfigurace**: Při použití místního počítače jako výpočetního cíle se tréninkový kód spustí ve [vývojovém prostředí](how-to-configure-environment.md).  Pokud toto prostředí již má balíčky Pythonu, které potřebujete, použijte prostředí spravované uživatelem.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=run_local)]

Nyní, když jste připojili výpočetní prostředky a nakonfigurovali spuštění, je dalším krokem [odeslání tréninkového běhu](#submit).

### <a name="azure-machine-learning-compute"></a><a id="amlcompute"></a>Výpočetní prostředky služby Azure Machine Learning

Azure Machine Learning Compute je infrastruktura spravovaných výpočetních prostředků, která umožňuje uživateli snadno vytvořit výpočetní prostředky s jedním nebo více uzly. Výpočetní prostředky se vytvoří v rámci oblasti pracovního prostoru jako prostředek, který lze sdílet s ostatními uživateli ve vašem pracovním prostoru. Výpočetní prostředky se automaticky zvětšují při odeslání úlohy a dají se do virtuální sítě Azure. Výpočetní prostředky se spustí v kontejnerizovaném prostředí a zabalí závislosti modelu v [kontejneru Dockeru](https://www.docker.com/why-docker).

Azure Machine Learning Compute můžete použít k distribuci procesu školení mezi clustervýpočetních uzlů CPU nebo GPU v cloudu. Další informace o velikostech virtuálních počítačů, které obsahují gpu, najdete v [tématu velikosti virtuálních počítačů optimalizované gpu](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

Azure Machine Learning Compute má výchozí limity, jako je například počet jader, které mohou být přiděleny. Další informace najdete [v tématu Správa a vyžádání kvót pro prostředky Azure](https://docs.microsoft.com/azure/machine-learning/how-to-manage-quotas).


Výpočetní prostředí Azure Machine Learning můžete vytvořit na vyžádání při plánování spuštění nebo jako trvalý prostředek.

#### <a name="run-based-creation"></a>Vytvoření založené na spuštění

Azure Machine Learning Compute můžete vytvořit jako výpočetní cíl za běhu. Výpočetní prostředky se automaticky vytvoří pro váš běh. Výpočetní prostředky se po dokončení spuštění automaticky odstraní. 

> [!IMPORTANT]
> Vytvoření výpočetních prostředků Azure Machine Learning na základě spuštění je aktuálně ve verzi Preview. Nepoužívejte vytváření na základě spuštění, pokud používáte automatické hyperparametrické ladění nebo automatizované strojové učení. Chcete-li použít hyperparametrické ladění nebo automatizované strojové učení, vytvořte místo toho [trvalý výpočetní](#persistent) cíl.

1.  **Vytvořit, připojit a nakonfigurovat**: Vytvoření na základě spuštění provádí všechny nezbytné kroky k vytvoření, připojení a konfiguraci výpočetního cíle pomocí konfigurace spuštění.  

  [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute.py?name=run_temp_compute)]


Nyní, když jste připojili výpočetní prostředky a nakonfigurovali spuštění, je dalším krokem [odeslání tréninkového běhu](#submit).

#### <a name="persistent-compute"></a><a id="persistent"></a>Trvalé výpočty

Trvalé Azure Machine Learning Compute lze znovu použít napříč úlohami. Výpočetní prostředky lze sdílet s ostatními uživateli v pracovním prostoru a je zachována mezi úlohami.

1. **Vytvořit a připojit**: Chcete-li vytvořit trvalý prostředek Azure Machine Learning Compute v Pythonu, zadejte **vlastnosti vm_size** a **max_nodes.** Azure Machine Learning pak používá inteligentní výchozí hodnoty pro ostatní vlastnosti. Výpočetní funkce se automaticky zvětšuje na nulu uzlů, když se nepoužívá.   Vyhrazené virtuální počítače se vytvoří pro spuštění úloh podle potřeby.
    
    * **vm_size**: Rodina virtuálních počítačů uzlů vytvořených azure machine learning výpočetní prostředky.
    * **max_nodes:** Maximální počet uzlů, které se mají automaticky škálovat až při spuštění úlohy v Azure Machine Learning Compute.
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   Můžete také nakonfigurovat několik pokročilých vlastností při vytváření Azure Machine Learning Compute. Vlastnosti umožňují vytvořit trvalý cluster pevné velikosti nebo v rámci existující virtuální sítě Azure ve vašem předplatném.  Podrobnosti najdete ve [třídě AmlCompute.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    )
    
   Nebo můžete vytvořit a připojit trvalý prostředek Azure Machine Learning Compute ve [studiu Azure Machine Learning](#portal-create).

1. **Konfigurace**: Vytvořte konfiguraci spuštění pro trvalý výpočetní cíl.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=run_amlcompute)]

Nyní, když jste připojili výpočetní prostředky a nakonfigurovali spuštění, je dalším krokem [odeslání tréninkového běhu](#submit).


### <a name="remote-virtual-machines"></a><a id="vm"></a>Vzdálené virtuální počítače

Azure Machine Learning také podporuje přinášet vlastní výpočetní prostředek a připojit ho k vašemu pracovnímu prostoru. Jeden takový typ prostředků je libovolný vzdálený virtuální počítač, pokud je přístupný z Azure Machine Learning. Prostředek může být virtuální počítač Azure, vzdálený server ve vaší organizaci nebo místní. Konkrétně vzhledem k IP adrese a pověření (uživatelské jméno a heslo nebo klíč SSH), můžete použít libovolný přístupný virtuální počítače pro vzdálené spuštění.

Můžete použít prostředí conda vytvořené systémem, již existující prostředí Pythonu nebo kontejner Dockeru. Chcete-li spustit na kontejneru Dockeru, musíte mít docker engine spuštěný na virtuálním počítači. Tato funkce je užitečná zejména v případě, že chcete flexibilnější cloudové prostředí pro vývoj a experimentování než místní počítač.

Použijte virtuální počítač Azure Data Science (DSVM) jako virtuální počítač Azure, který si pro tento scénář vyberete. Tento virtuální počítač je předem nakonfigurované datové vědy a vývojové prostředí AI v Azure. Virtuální počítač nabízí kurátorský výběr nástrojů a rámců pro vývoj strojového učení v plném životním cyklu. Další informace o tom, jak používat DSVM s Azure Machine Learning, najdete [v tématu Konfigurace vývojového prostředí](https://docs.microsoft.com/azure/machine-learning/how-to-configure-environment#dsvm).

1. **Vytvořit**: Vytvořte DSVM před použitím k trénování modelu. Chcete-li vytvořit tento zdroj, naleznete [v tématu zřízení virtuálního počítače pro datové vědy pro Linux (Ubuntu).](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

    > [!WARNING]
    > Azure Machine Learning podporuje jenom virtuální počítače, na kterých běží Ubuntu. Když vytvoříte virtuální hod nebo vyberete existující virtuální hod, musíte vybrat virtuální hod, který používá Ubuntu.

1. **Připojit**: Chcete-li připojit existující virtuální počítač jako výpočetní cíl, musíte zadat ID prostředku, uživatelské jméno a heslo pro virtuální počítač. ID prostředku virtuálního aplikace lze sestavit pomocí ID předplatného, názvu skupiny prostředků a názvu virtuálního aplikace pomocí následujícího formátu řetězce:`/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Compute/virtualMachines/<vm_name>`

 
   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   
   attach_config = RemoteCompute.attach_configuration(resource_id='<resource_id>',
                                                   ssh_port=22,
                                                   username='<username>',
                                                   password="<password>")

   # If you authenticate with SSH keys instead, use this code:
   #                                                  ssh_port=22,
   #                                                  username='<username>',
   #                                                  password=None,
   #                                                  private_key_file="<path-to-file>",
   #                                                  private_key_passphrase="<passphrase>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   Nebo můžete připojit DSVM k vašemu pracovnímu prostoru [pomocí Azure Machine Learning studio](#portal-reuse).

1. **Konfigurace**: Vytvořte konfiguraci spuštění pro výpočetní cíl DSVM. Docker a conda se používají k vytvoření a konfiguraci tréninkového prostředí na DSVM.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


Nyní, když jste připojili výpočetní prostředky a nakonfigurovali spuštění, je dalším krokem [odeslání tréninkového běhu](#submit).

### <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight je oblíbená platforma pro analýzu velkých objemů dat. Platforma poskytuje Apache Spark, který lze použít k trénování vašeho modelu.

1. **Vytvořit**: Před použitím k trénování modelu vytvořte cluster HDInsight. Pokud chcete vytvořit Spark v clusteru HDInsight, přečtěte [si tématu Vytvoření clusteru Spark ve službě HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql). 

    Při vytváření clusteru je nutné zadat uživatelské jméno a heslo SSH. Tyto hodnoty si všimněte, protože je potřebujete k použití HDInsightu jako výpočetního cíle.
    
    Po vytvoření clusteru se k němu \<připojte názvem clusteru \<>-ssh.azurehdinsight.net, kde název clusteru> je název, který jste pro cluster zadali. 

1. **Připojit**: Chcete-li připojit cluster HDInsight jako cíl výpočetního výkonu, musíte zadat ID prostředku, uživatelské jméno a heslo pro cluster HDInsight. ID prostředku clusteru HDInsight lze sestavit pomocí ID předplatného, názvu skupiny prostředků a názvu clusteru HDInsight pomocí následujícího formátu řetězce:`/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.HDInsight/clusters/<cluster_name>`

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

   Nebo můžete připojit cluster HDInsight k pracovnímu prostoru [pomocí studia Azure Machine Learning studio](#portal-reuse).

1. **Konfigurace**: Vytvořte konfiguraci spuštění pro výpočetní cíl HDI. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Nyní, když jste připojili výpočetní prostředky a nakonfigurovali spuštění, je dalším krokem [odeslání tréninkového běhu](#submit).


### <a name="azure-batch"></a><a id="azbatch"></a>Azure Batch 

Azure Batch se používá k efektivnímu spouštění rozsáhlých paralelních a vysoce výkonných výpočetních aplikací (HPC) v cloudu. AzureBatchStep se dá použít v kanálu Azure Machine Learning k odeslání úloh do fondu počítačů Azure Batch.

Chcete-li připojit Azure Batch jako výpočetní cíl, musíte použít Azure Machine Learning SDK a poskytnout následující informace:

-    **Azure Batch výpočetní název:** Popisný název, který se má použít pro výpočetní prostředky v pracovním prostoru
-    **Název účtu Azure Batch:** Název účtu Azure Batch
-    **Skupina prostředků:** Skupina prostředků, která obsahuje účet Azure Batch.

Následující kód ukazuje, jak připojit Azure Batch jako cíl výpočetního výkonu:

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

## <a name="set-up-in-azure-machine-learning-studio"></a>Nastavení ve studiu Azure Machine Learning

Můžete přistupovat k výpočetní cíle, které jsou přidruženy k vašemu pracovnímu prostoru v Azure Machine Learning studio.  Studio můžete použít k:

* [Zobrazení výpočetních cílů](#portal-view) připojených k pracovnímu prostoru
* [Vytvoření výpočetního cíle](#portal-create) v pracovním prostoru
* [Připojení výpočetního cíle,](#portal-reuse) který byl vytvořen mimo pracovní prostor


Po vytvoření cíle a připojení k pracovnímu prostoru jej použijete v `ComputeTarget` konfiguraci spuštění s objektem: 

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

### <a name="view-compute-targets"></a><a id="portal-view"></a>Zobrazit výpočetní cíle


Chcete-li zobrazit výpočetní cíle pro váš pracovní prostor, použijte následující kroky:

1. Přejděte do [studia Azure Machine Learning studio](https://ml.azure.com).
 
1. V části __Aplikace__vyberte __Vypočítat__.

    [![Zobrazit kartu výpočetních prostředků](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace-expanded.png)

### <a name="create-a-compute-target"></a><a id="portal-create"></a>Vytvoření výpočetního cíle

Podle předchozích kroků zobrazte seznam výpočetních cílů. Potom pomocí těchto kroků vytvořte výpočetní cíl: 

1. Vyberte znaménko plus (+) pro přidání výpočetního cíle.

    ![Přidání výpočetního cíle](./media/how-to-set-up-training-targets/add-compute-target.png) 

1. Zadejte název výpočetního cíle. 

1. Vyberte **Strojové učení Compute** jako typ výpočetních prostředků, které se mají použít pro __školení__. 

    >[!NOTE]
    >Azure Machine Learning Compute je jediný prostředek spravovaných výpočetních prostředků, který můžete vytvořit ve studiu Azure Machine Learning.  Všechny ostatní výpočetní prostředky lze připojit po jejich vytvoření.

1. Vyplňte formulář. Zadejte hodnoty pro požadované vlastnosti, zejména **rodinu virtuálních počítačů**a **maximální uzly,** které se mají použít ke spočítání výpočetních prostředků.  

1. Vyberte __Vytvořit__.


1. Zobrazení stavu operace vytvoření výběrem výpočetního cíle ze seznamu:

    ![Výběr výpočetního cíle pro zobrazení stavu operace vytvoření](./media/how-to-set-up-training-targets/View_list.png)

1. Pak se zobrazí podrobnosti o výpočetní cíl: 

    ![Zobrazit podrobnosti o cíli počítače](./media/how-to-set-up-training-targets/compute-target-details.png) 

### <a name="attach-compute-targets"></a><a id="portal-reuse"></a>Připojení výpočetních cílů

Chcete-li použít výpočetní cíle vytvořené mimo pracovní prostor Azure Machine Learning, musíte je připojit. Připojení výpočetnícíl zpřístupní pro váš pracovní prostor.

Podle výše popsaných kroků zobrazte seznam výpočetních cílů. Pak pomocí následujících kroků připojte výpočetní cíl: 

1. Vyberte znaménko plus (+) pro přidání výpočetního cíle. 
1. Zadejte název výpočetního cíle. 
1. Vyberte typ výpočetních prostředků, které chcete připojit pro __školení__:

    > [!IMPORTANT]
    > Ne všechny typy výpočetních prostředků lze připojit ze studia Azure Machine Learning. Výpočetní typy, které lze aktuálně připojit pro školení patří:
    >
    > * Vzdálený virtuální virtuální ms
    > * Azure Databricks (pro použití v kanálech strojového učení)
    > * Azure Data Lake Analytics (pro použití v kanálech strojového učení)
    > * Azure HDInsight

1. Vyplňte formulář a zadejte hodnoty pro požadované vlastnosti.

    > [!NOTE]
    > Společnost Microsoft doporučuje používat klíče SSH, které jsou bezpečnější než hesla. Hesla jsou zranitelná vůči útokům hrubou silou. Klíče SSH spoléhají na kryptografické podpisy. Informace o tom, jak vytvořit klíče SSH pro použití s virtuálními počítači Azure, najdete v následujících dokumentech:
    >
    > * [Vytváření a používání klíčů SSH na Linuxu nebo macOS](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Vytvoření a použití klíčů SSH ve Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Vyberte __Připojit__. 
1. Zobrazení stavu operace připojení výběrem výpočetního cíle ze seznamu.

## <a name="set-up-with-cli"></a>Nastavení pomocí cli

Výpočetní cíle, které jsou přidruženy k vašemu pracovnímu prostoru, můžete získat pomocí [rozšíření cli](reference-azure-machine-learning-cli.md) pro Azure Machine Learning.  Pomocí vykreslování letového provozu můžete:

* Vytvoření cíle spravovaného výpočetního výkonu
* Aktualizace cíle spravovaného výpočetního výkonu
* Připojení nespravovaného výpočetního cíle

Další informace naleznete v [tématu Resource management](reference-azure-machine-learning-cli.md#resource-management).

## <a name="set-up-with-vs-code"></a>Nastavení s kódem VS

Výpočetní cíle, které jsou přidruženy k vašemu pracovnímu prostoru, můžete přistupovat, vytvářet je a spravovat pomocí [rozšíření VS Code](tutorial-train-deploy-image-classification-model-vscode.md#configure-compute-targets) pro Azure Machine Learning.

## <a name="submit-training-run-using-azure-machine-learning-sdk"></a><a id="submit"></a>Odeslat spuštění školení pomocí sady Azure Machine Learning SDK

Po vytvoření konfigurace spuštění ji použijete ke spuštění experimentu.  Vzor kódu pro odeslání trénovacího běhu je stejný pro všechny typy výpočetních cílů:

1. Vytvoření experimentu ke spuštění
1. Pošlete běh.
1. Počkejte na dokončení běhu.

> [!IMPORTANT]
> Při odeslání spuštění školení, snímek adresáře, který obsahuje vaše skripty školení je vytvořen a odeslán do výpočetního cíle. Je také uložen jako součást experimentu ve vašem pracovním prostoru. Pokud změníte soubory a odešlete spuštění znovu, budou odeslány pouze změněné soubory.
>
> Chcete-li zabránit zahrnutí souborů do snímku, `.amlignore` vytvořte v adresáři [soubor .gitignore](https://git-scm.com/docs/gitignore) nebo soubor a přidejte do něj soubory. Soubor `.amlignore` používá stejnou syntaxi a vzorky jako soubor [.gitignore.](https://git-scm.com/docs/gitignore) Pokud oba soubory `.amlignore` existují, má soubor přednost.
> 
> Další informace naleznete v [tématu Snímky](concept-azure-machine-learning-architecture.md#snapshots).

### <a name="create-an-experiment"></a>Vytvoření experimentu

Nejprve vytvořte experiment v pracovním prostoru.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=experiment)]

### <a name="submit-the-experiment"></a>Odeslání experimentu

Odešlete experiment `ScriptRunConfig` s objektem.  Tento objekt zahrnuje:

* **source_directory**: Zdrojový adresář, který obsahuje trénovací skript
* **skript**: Identifikujte školicí skript
* **run_config**: Konfigurace spuštění, která zase definuje, kde dojde k tréninku.

Chcete-li například použít [místní cílovou](#local) konfiguraci:

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=local_submit)]

Přepněte stejný experiment spustit v jiném výpočetním cíli pomocí jiné konfigurace spuštění, jako je například [cíl amlcompute](#amlcompute):

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=amlcompute_submit)]

> [!TIP]
> Tento příklad výchozí použití pouze jeden uzel výpočetní cíl pro školení. Chcete-li použít více než `node_count` jeden uzel, nastavte konfiguraci spuštění na požadovaný počet uzlů. Například následující kód nastaví počet uzlů používaných pro školení na čtyři:
>
> ```python
> src.run_config.node_count = 4
> ```

Nebo můžete:

* Odešlete experiment `Estimator` s objektem, jak je znázorněno v [modelech Train ML s odhady](how-to-train-ml-models.md).
* Odešlete spuštění hyperdrauše pro [hyperparametry](how-to-tune-hyperparameters.md).
* Odeslat experiment prostřednictvím [rozšíření VS Code](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model).

Další informace naleznete v dokumentaci [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) a [RunConfiguration.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py)

## <a name="create-run-configuration-and-submit-run-using-azure-machine-learning-cli"></a>Vytvoření konfigurace spuštění a odeslání spuštění pomocí příkazového příkazu KZP Azure Machine Learning

Pomocí rozšíření [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) a [Machine Learning CLI](reference-azure-machine-learning-cli.md) můžete vytvořit konfigurace spuštění a odeslat spuštění na různých výpočetních cílech. Následující příklady předpokládají, že máte existující Pracovní prostor Azure Machine Learning `az login` a přihlásili jste se k Azure pomocí příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu PŘÍKAZ. 

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

### <a name="create-run-configuration"></a>Vytvořit konfiguraci spuštění

Nejjednodušší způsob, jak vytvořit konfiguraci spuštění, je procházet složku, která obsahuje skripty Pythonu pro strojové učení, a použít příkaz CLI.

```azurecli
az ml folder attach
```

Tento příkaz vytvoří `.azureml` podsložku, která obsahuje konfigurační soubory spuštění šablony pro různé výpočetní cíle. Tyto soubory můžete zkopírovat a upravit a přizpůsobit konfiguraci, například přidat balíčky Pythonu nebo změnit nastavení Dockeru.  

### <a name="structure-of-run-configuration-file"></a>Struktura spustit konfigurační soubor

Spustit konfigurační soubor je formátován YAML, s následujícími oddíly
 * Skript, který má být spuštěn, a jeho argumenty
 * Vypočítat cílový název, buď "místní" nebo název výpočetní ho pod pracovním prostorem.
 * Parametry pro spuštění spuštění: framework, communicator pro distribuované spuštění, maximální doba trvání a počet výpočetních uzlů.
 * Prostředí. Podrobnosti o polích v této části najdete v [tématu Vytváření a správa prostředí pro školení a nasazení.](how-to-use-environments.md)
   * Chcete-li určit balíčky Pythonu, které chcete nainstalovat pro spuštění, [vytvořte soubor prostředí conda](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#create-env-file-manually)a nastavte pole __condaDependenciesFile.__
 * Spusťte podrobnosti historie a určete složku souboru protokolu a povolte nebo zakažte výstupní kolekci a spusťte snímky historie.
 * Podrobnosti konfigurace specifické pro vybranou architekturu.
 * Podrobnosti o referencích na data a úložišti dat.
 * Podrobnosti o konfiguraci specifické pro výpočetní prostředky Machine Learning pro vytvoření nového clusteru.

Viz příklad [souboru JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json) pro úplné runconfig schéma.

### <a name="create-an-experiment"></a>Vytvoření experimentu

Nejprve vytvořte experiment pro vaše běhy

```azurecli
az ml experiment create -n <experiment>
```

### <a name="script-run"></a>Spuštění skriptu

Chcete-li odeslat spuštění skriptu, spusťte příkaz

```azurecli
az ml run submit-script -e <experiment> -c <runconfig> my_train.py
```

### <a name="hyperdrive-run"></a>Spuštění HyperDrivu

Pomocí hyperdružiky s rozhraním příkazového řádku Azure můžete provádět spuštění optimalizace parametrů. Nejprve vytvořte konfigurační soubor HyperDrive v následujícím formátu. Podrobnosti o parametrech optimalizace hyperparametrů pro váš model najdete v článku [optimalizace](how-to-tune-hyperparameters.md) hyperparametrů.

```yml
# hdconfig.yml
sampling: 
    type: random # Supported options: Random, Grid, Bayesian
    parameter_space: # specify a name|expression|values tuple for each parameter.
    - name: --penalty # The name of a script parameter to generate values for.
      expression: choice # supported options: choice, randint, uniform, quniform, loguniform, qloguniform, normal, qnormal, lognormal, qlognormal
      values: [0.5, 1, 1.5] # The list of values, the number of values is dependent on the expression specified.
policy: 
    type: BanditPolicy # Supported options: BanditPolicy, MedianStoppingPolicy, TruncationSelectionPolicy, NoTerminationPolicy
    evaluation_interval: 1 # Policy properties are policy specific. See the above link for policy specific parameter details.
    slack_factor: 0.2
primary_metric_name: Accuracy # The metric used when evaluating the policy
primary_metric_goal: Maximize # Maximize|Minimize
max_total_runs: 8 # The maximum number of runs to generate
max_concurrent_runs: 2 # The number of runs that can run concurrently.
max_duration_minutes: 100 # The maximum length of time to run the experiment before cancelling.
```

Přidejte tento soubor vedle spustit konfigurační soubory. Potom odešlete spuštění HyperDrivu pomocí:
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

Poznamenejte si oddíl *argumentů* v souboru runconfig a *parametru* v konfiguraci hyperdrive. Obsahují argumenty příkazového řádku, které mají být předány trénovacímu skriptu. Hodnota v runconfig zůstává stejná pro každou iteraci, zatímco rozsah v konfiguraci HyperDrive je iterován. Nezadávejte stejný argument v obou souborech.

Další podrobnosti ```az ml``` o těchto příkazech příkazu příkazu příkazu příkazu a úplnou sadu argumentů naleznete [v referenční dokumentaci](reference-azure-machine-learning-cli.md).

<a id="gitintegration"></a>

## <a name="git-tracking-and-integration"></a>Git sledování a integrace

Když spustíte trénovací běh, kde zdrojový adresář je místní úložiště Git, informace o úložišti jsou uloženy v historii spuštění. Další informace najdete v [tématu Integrace Gitu pro Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="notebook-examples"></a>Příklady poznámkových bloků

Podívejte se na tyto poznámkové bloky příklady školení s různými výpočetními cíli:
* [jak používat azureml/školení](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další kroky

* [Kurz: Trénování modelu](tutorial-train-models-with-aml.md) používá spravovaný výpočetní cíl k trénování modelu.
* Naučte se [efektivně ladit hyperparametry](how-to-tune-hyperparameters.md) a vytvářet lepší modely.
* Jakmile budete mít trénovaný model, zjistěte, [jak a kde nasadit modely](how-to-deploy-and-where.md).
* Zobrazení odkazu [sady RunConfiguration třídy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) SDK.
* [Použití Azure Machine Learning u virtuálních sítí Azure](how-to-enable-virtual-network.md)
