---
title: Použití výpočetních cílů pro školení modelů
titleSuffix: Azure Machine Learning
description: Nakonfigurujte školicí prostředí (cíle výpočtů) pro školení modelů ve službě Machine Learning. Mezi školicími prostředími můžete snadno přepínat. Spusťte školení místně. Pokud potřebujete horizontální navýšení kapacity, přepněte na cloudový cíl výpočtů.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 01/16/2020
ms.custom: seodec18
ms.openlocfilehash: c7fd70ca32054b3b25e717c8c7169cf2d30ef9be
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79283521"
---
# <a name="set-up-and-use-compute-targets-for-model-training"></a>Nastavení a použití výpočetních cílů pro školení modelů 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Pomocí Azure Machine Learning můžete model vyškolit na nejrůznějších materiálech nebo prostředích, které se souhrnně označují jako [__výpočetní cíle__](concept-azure-machine-learning-architecture.md#compute-targets). Cílem výpočetní služby může být místní počítač nebo cloudový prostředek, jako je Azure Machine Learning COMPUTE, Azure HDInsight nebo vzdálený virtuální počítač.  Můžete také vytvořit výpočetní cíle pro nasazení modelu, jak je popsáno v [části "kde a jak nasadit vaše modely"](how-to-deploy-and-where.md).

Výpočetní cíl můžete vytvořit a spravovat pomocí rozšíření Azure Machine Learning SDK, Azure Machine Learning Studio, Azure CLI nebo Azure Machine Learning VS Code. Pokud máte výpočetní cíle vytvořené prostřednictvím jiné služby (například cluster HDInsight), můžete je použít tak, že je připojíte k pracovnímu prostoru Azure Machine Learning.
 
V tomto článku se dozvíte, jak používat různé výpočetní cíle pro školení modelů.  Postup pro všechny výpočetní cíle se řídí stejným pracovním postupem:
1. Pokud ho ještě nemáte, __vytvořte__ cíl výpočtů.
2. __Připojte__ výpočetní cíl k vašemu pracovnímu prostoru.
3. __Nakonfigurujte__ výpočetní cíl tak, aby obsahoval prostředí Pythonu a závislosti balíčků, které váš skript potřebuje.


>[!NOTE]
> Kód v tomto článku byl testován pomocí sady Azure Machine Learning SDK 1.0.74 verze.

## <a name="compute-targets-for-training"></a>Výpočetní cíle pro školení

Azure Machine Learning má různou podporu napříč různými výpočetními cíli. Životní cyklus vývoje typické modelu začíná dev/experimentování na malé množství dat ve službě. V této fázi doporučujeme používat místní prostředí. Například místního počítače nebo virtuálního počítače založené na cloudu. Vertikálně navýšit kapacitu trénování na větších datových sad, nebo proveďte distribuované trénování, doporučujeme vytvořit jeden nebo více node cluster tohoto pravidla automatického škálování provedou pokaždé, když odešlete spuštění pomocí Azure Machine Learning Compute. Můžete také připojit své vlastní výpočetní prostředek, ačkoli podpory pro různé scénáře se může lišit jako podrobnosti jsou dole:

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]


> [!NOTE]
> Azure Machine Learning COMPUTE se dá vytvořit jako trvalý prostředek nebo se dynamicky vytvoří při žádosti o spuštění. Vytváření na základě spuštění po dokončení školení odstraní cíl výpočtů, takže nebudete moct znovu použít výpočetní cíle vytvořené tímto způsobem.

## <a name="whats-a-run-configuration"></a>Co je konfigurace spuštění?

Po školení je běžné spustit na místním počítači a později spustit tento školicí skript na jiném cílovém výpočetním prostředí. Pomocí Azure Machine Learning můžete skript spustit na různých výpočetních cílech bez nutnosti změny skriptu.

Vše, co potřebujete udělat, je definovat prostředí pro každý cíl výpočtů v rámci **Konfigurace spuštění**.  Pak, pokud chcete spustit experiment pro školení na jiném cílovém výpočetním prostředí, zadejte konfiguraci spuštění pro výpočetní výkon. Podrobnosti o určení prostředí a jeho navázání ke spuštění konfigurace najdete v tématu [vytváření a Správa prostředí pro účely školení a nasazení](how-to-use-environments.md).

Přečtěte si další informace o [odesílání experimentů](#submit) na konci tohoto článku.

## <a name="whats-an-estimator"></a>Co je Estimator?

Pro usnadnění školení modelů pomocí oblíbených rozhraní Azure Machine Learning Python SDK nabízí alternativní abstrakci vyšší úrovně, třídu Estimator.  Tato třída umožňuje snadno vytvořit konfigurace spuštění. Můžete vytvořit a použít obecné [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) k odesílání školicích skriptů, které používají všechny vámi zvolené vzdělávací architektury (například scikit-učení). Doporučujeme používat Estimator pro školení, protože automaticky sestaví vložené objekty, jako je prostředí nebo RunConfiguration objekty. Pokud chcete mít větší kontrolu nad tím, jak jsou tyto objekty vytvořeny, a určete, jaké balíčky chcete nainstalovat pro váš experiment, postupujte podle [těchto kroků](#amlcompute) a odešlete své školicí experimenty pomocí objektu RunConfiguration ve výpočetním prostředí Azure Machine Learning.

Pro úlohy PyTorch, TensorFlow a řetězení Azure Machine Learning poskytuje také příslušné [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)a [Chain](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) odhady pro zjednodušení používání těchto rozhraní.

Další informace najdete v tématu o [modelech vlak ml pomocí odhady](how-to-train-ml-models.md).

## <a name="whats-an-ml-pipeline"></a>Co je to kanál ML?

Pomocí kanálů ML můžete optimalizovat svůj pracovní postup Díky jednoduchosti, rychlosti, přenositelnosti a opakovanému použití. Při sestavování kanálů pomocí Azure Machine Learning se můžete soustředit na vaše odbornosti, strojové učení, nikoli na infrastrukturu a automatizaci.

Kanály ML jsou vytvořené z více **kroků**, které jsou v kanálu odlišné výpočetní jednotky. Každý krok může běžet nezávisle a používat izolované výpočetní prostředky. Díky tomu může více pracovníků dat pracovat na stejném kanálu současně bez navýšení výpočetních prostředků a také usnadňuje používání různých výpočetních typů/velikostí pro jednotlivé kroky.

> [!TIP]
> Kanály ML můžou při výuce modelů použít rutinu Run Configuration nebo odhady.

I když kanály ML můžou prosazovat modely, můžou také připravit data před školením a nasazením modelů po školení. Jedním z hlavních případů použití pro kanály je dávkové vyhodnocování. Další informace najdete v tématu [kanály: optimalizace pracovních postupů strojového učení](concept-ml-pipelines.md).

## <a name="set-up-in-python"></a>Nastavení v Pythonu

Pro konfiguraci těchto výpočetních cílů použijte následující části:

* [Místní počítač](#local)
* [Azure Machine Learning COMPUTE](#amlcompute)
* [Vzdálené virtuální počítače](#vm)
* [Azure HDInsight](#hdinsight)


### <a id="local"></a>Místní počítač

1. **Vytvoření a připojení**: není nutné vytvářet ani připojovat výpočetní cíl pro použití místního počítače jako školicího prostředí.  

1. **Konfigurace**: když použijete místní počítač jako výpočetní cíl, kód školení se spustí ve vašem [vývojovém prostředí](how-to-configure-environment.md).  Pokud už toto prostředí obsahuje balíčky Pythonu, které potřebujete, použijte prostředí spravované uživatelem.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=run_local)]

Teď, když jste připojili výpočetní prostředky a nakonfigurovali svůj běh, je dalším krokem [odeslání školicího běhu](#submit).

### <a id="amlcompute"></a>Azure Machine Learning COMPUTE

Azure Machine Learning COMPUTE je spravovaná a výpočetní infrastruktura, která umožňuje uživateli snadno vytvořit výpočetní výkon s jedním uzlem nebo několika uzly. Výpočetní prostředí se vytvoří v rámci vaší oblasti pracovního prostoru jako prostředek, který se dá sdílet s ostatními uživateli v pracovním prostoru. Výpočetní výkon se při odeslání úlohy automaticky škáluje a dá se umístit do Azure Virtual Network. Výpočetní výkon se spouští v kontejnerovém prostředí a zabalí závislosti vašich modelů v [kontejneru Docker](https://www.docker.com/why-docker).

Azure Machine Learning Compute můžete použít k procesu trénování distribuovat napříč clusterem CPU nebo GPU výpočetních uzlů v cloudu. Další informace o velikostech virtuálních počítačů, které zahrnují GPU, najdete v tématu [velikosti virtuálních počítačů optimalizované pro GPU](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

Azure Machine Learning COMPUTE má výchozí omezení, například počet jader, které se dají přidělit. Další informace najdete v tématu [Správa a vyžádání kvót pro prostředky Azure](https://docs.microsoft.com/azure/machine-learning/how-to-manage-quotas).


Při plánování běhu nebo jako trvalého prostředku můžete vytvořit prostředí Azure Machine Learning COMPUTE na vyžádání.

#### <a name="run-based-creation"></a>Tvorby založené na spuštění

V době běhu můžete vytvořit Azure Machine Learning COMPUTE jako cíl výpočtu. Výpočty se automaticky vytvoří pro váš běh. Výpočty se odstraní automaticky po dokončení spuštění. 

> [!IMPORTANT]
> Vytváření Azure Machine Learning výpočtů na základě spuštění je momentálně ve verzi Preview. Nepoužívejte vytváření založené na spuštění, pokud používáte automatizované ladění parametrů nebo automatizované strojové učení. Pokud chcete použít ladění pomocí parametrů nebo automatizované strojové učení, vytvořte místo toho [trvalý cíl výpočtů](#persistent) .

1.  **Vytvoření, připojení a konfigurace**: vytvoření založené na spuštění provádí všechny nezbytné kroky k vytvoření, připojení a konfiguraci cílového výpočetního prostředí s konfigurací spuštění.  

  [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute.py?name=run_temp_compute)]


Teď, když jste připojili výpočetní prostředky a nakonfigurovali svůj běh, je dalším krokem [odeslání školicího běhu](#submit).

#### <a id="persistent"></a>Trvalé výpočetní prostředky

Trvalé Azure Machine Learning výpočetní prostředí se dá opakovaně použít napříč úlohami. Výpočetní prostředky můžete sdílet s ostatními uživateli v pracovním prostoru a jsou mezi úlohami zachované.

1. **Vytvoření a připojení**: Chcete-li v Pythonu vytvořit trvalý Azure Machine Learning výpočetní prostředek, zadejte vlastnosti **vm_size** a **max_nodes** . Azure Machine Learning pak pro ostatní vlastnosti používá inteligentní výchozí hodnoty. Výpočetní výkon se při použití vymění až na nula uzlů.   Vyhrazené virtuální počítače se vytvářejí ke spouštění vašich úloh podle potřeby.
    
    * **vm_size**: rodina virtuálních počítačů uzlů vytvořená Azure Machine Learning Compute.
    * **max_nodes**: maximální počet uzlů pro automatické horizontální navýšení kapacity při spuštění úlohy v Azure Machine Learning Compute.
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   Při vytváření Azure Machine Learning výpočetních prostředků můžete také nakonfigurovat několik pokročilých vlastností. Vlastnosti umožňují vytvořit trvalý cluster s pevnou velikostí nebo v rámci stávajícího Virtual Network Azure v rámci vašeho předplatného.  Podrobnosti najdete v tématu [Třída AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    ) .
    
   Nebo můžete vytvořit a připojit trvalé Azure Machine Learning výpočetní prostředky v [Azure Machine Learning Studiu](#portal-create).

1. **Konfigurace**: Vytvořte konfiguraci spuštění pro trvalý cíl služby Compute.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=run_amlcompute)]

Teď, když jste připojili výpočetní prostředky a nakonfigurovali svůj běh, je dalším krokem [odeslání školicího běhu](#submit).


### <a id="vm"></a>Vzdálené virtuální počítače

Azure Machine Learning podporuje také přináší vlastní výpočetních prostředků a připojíte ho do pracovního prostoru. Jedním z těchto typů prostředků je libovolný vzdálený virtuální počítač, pokud je dostupný z Azure Machine Learning. Prostředkem může být virtuální počítač Azure, vzdálený server ve vaší organizaci nebo místní. Konkrétně pro vzdálené spuštění s ohledem na IP adresu a přihlašovací údaje (uživatelské jméno a heslo nebo klíč SSH) můžete použít libovolný dostupný virtuální počítač.

Můžete použít prostředí integrovaného systému conda, již existující prostředí Python nebo kontejneru Docker. Aby bylo možné provést v kontejneru Docker, je nutné mít na virtuálním počítači spuštěný modul Docker. Tato funkce je zvlášť užitečné, pokud chcete dev/experimentování flexibilnější a cloudové prostředí než místního počítače.

Pro tento scénář použijte Azure Data Science Virtual Machine (DSVM) jako virtuální počítač Azure s možností výběru. Tento virtuální počítač je předem konfigurovaným vývojovým prostředím pro datové vědy a AI v Azure. Virtuální počítač nabízí uspořádané možnosti nástrojů a platforem pro vývoj v rámci služby Machine Learning pro celou dobu životního cyklu. Další informace o tom, jak používat DSVM s Azure Machine Learning, najdete v tématu [Konfigurace vývojového prostředí](https://docs.microsoft.com/azure/machine-learning/how-to-configure-environment#dsvm).

1. **Vytvořit**: Vytvořte DSVM ještě před tím, než ho použijete ke školení svého modelu. Pokud chcete tento prostředek vytvořit, přečtěte si téma [zřízení Data Science Virtual Machine pro Linux (Ubuntu)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro).

    > [!WARNING]
    > Azure Machine Learning podporuje jenom virtuální počítače, které spouštějí Ubuntu. Když vytváříte virtuální počítač nebo zvolíte existující virtuální počítač, musíte vybrat virtuální počítač, který používá Ubuntu.

1. **Připojit**: Chcete-li připojit existující virtuální počítač jako cíl služby COMPUTE, je nutné zadat plně kvalifikovaný název domény (FQDN), uživatelské jméno a heslo pro virtuální počítač. V tomto příkladu nahraďte \<plně kvalifikovaný název domény > veřejným plně kvalifikovaným názvem domény virtuálního počítače nebo veřejnou IP adresou. Pro tento virtuální počítač nahraďte \<username > a \<heslo > a uživatelské jméno a heslo SSH.

   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   attach_config = RemoteCompute.attach_configuration(address = "<fqdn>",
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

   Nebo můžete připojit DSVM k vašemu pracovnímu prostoru [pomocí Azure Machine Learning studia](#portal-reuse).

1. **Konfigurace**: Vytvořte konfiguraci spuštění pro cíl služby DSVM Compute. Docker a conda slouží k vytvoření a konfiguraci školicího prostředí na DSVM.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


Teď, když jste připojili výpočetní prostředky a nakonfigurovali svůj běh, je dalším krokem [odeslání školicího běhu](#submit).

### <a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight je oblíbená platforma pro analýzu velkých objemů dat. Platforma poskytuje Apache Spark, které je možné použít ke školení modelu.

1. **Vytvořit**: Vytvořte cluster HDInsight předtím, než ho použijete ke školení svého modelu. Informace o vytvoření clusteru Spark v HDInsight najdete [v tématu Vytvoření clusteru Spark v HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql). 

    Při vytváření clusteru je nutné zadat uživatelské jméno a heslo SSH. Tyto hodnoty si poznamenejte, protože je budete potřebovat k použití HDInsight jako cíle výpočtů.
    
    Po vytvoření clusteru se k němu připojte pomocí názvu hostitele \<název_clusteru >-ssh.azurehdinsight.net, kde \<název_clusteru > je název, který jste zadali pro cluster. 

1. **Připojit**: Pokud chcete připojit cluster HDInsight jako cíl výpočetní služby, musíte zadat název hostitele, uživatelské jméno a heslo pro cluster HDInsight. Následující příklad používá sadu SDK připojit cluster do pracovního prostoru. V příkladu nahraďte \<název_clusteru > názvem vašeho clusteru. Pro tento cluster nahraďte \<username > a \<heslo > a uživatelské jméno a heslo SSH.

   ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase
    attach_config = HDInsightCompute.attach_configuration(address='<clustername>-ssh.azurehdinsight.net', 
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

   Nebo můžete připojit cluster HDInsight k vašemu pracovnímu prostoru [pomocí Azure Machine Learning studia](#portal-reuse).

1. **Konfigurace**: Vytvořte konfiguraci spuštění pro cíl služby HDI Compute. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Teď, když jste připojili výpočetní prostředky a nakonfigurovali svůj běh, je dalším krokem [odeslání školicího běhu](#submit).


### <a id="azbatch"></a>Azure Batch 

Azure Batch se používá ke efektivnímu spouštění rozsáhlých paralelních a vysoce výkonných aplikací pro výpočetní prostředí (HPC) v cloudu. AzureBatchStep se dá použít v kanálu Azure Machine Learning k odesílání úloh do fondu Azure Batch počítačů.

Chcete-li připojit Azure Batch jako cíl výpočtů, je nutné použít sadu Azure Machine Learning SDK a zadat následující informace:

-   **Azure Batch výpočetní název**: popisný název, který se má použít pro výpočetní prostředky v pracovním prostoru.
-   **Azure Batch název účtu**: název účtu Azure Batch
-   **Skupina prostředků**: Skupina prostředků, která obsahuje účet Azure Batch.

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

## <a name="set-up-in-azure-machine-learning-studio"></a>Nastavení v Azure Machine Learning Studiu

Ke výpočetním cílům, které jsou přidružené k vašemu pracovnímu prostoru v Azure Machine Learning studiu, získáte přístup.  Můžete použít Studio k těmto akcím:

* [Zobrazení cílů služby COMPUTE](#portal-view) připojených k vašemu pracovnímu prostoru
* [Vytvoření cíle služby COMPUTE](#portal-create) v pracovním prostoru
* [Připojte výpočetní cíl](#portal-reuse) , který byl vytvořen mimo pracovní prostor.


Jakmile je cíl vytvořen a připojen k vašemu pracovnímu prostoru, budete ho používat v konfiguraci spuštění s objektem `ComputeTarget`: 

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

### <a id="portal-view"></a>Zobrazit cíle výpočtů


Chcete-li zobrazit výpočetní cíle pro váš pracovní prostor, použijte následující postup:

1. Přejděte do [Azure Machine Learning studia](https://ml.azure.com).
 
1. V části __aplikace__vyberte __COMPUTE__.

    [karta COMPUTE ![zobrazení](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace-expanded.png)

### <a id="portal-create"></a>Vytvořit cíl výpočtů

Podle předchozích kroků zobrazte seznam cílů výpočtů. Pak pomocí těchto kroků vytvořte cíl výpočtů: 

1. Vyberte znaménko plus (+) a přidejte cíl výpočtů.

    ![Přidat cíl výpočtů](./media/how-to-set-up-training-targets/add-compute-target.png) 

1. Zadejte název pro cíl služby Compute. 

1. Jako typ výpočetních prostředků, které se mají použít pro __školení__, vyberte **výpočetní prostředky služby Machine Learning** . 

    >[!NOTE]
    >Výpočetním prostředkem Azure Machine Learning, který se dá vytvořit, je jediný spravovaný výpočetní prostředek, který můžete vytvořit v Azure Machine Learning Studiu.  Po vytvoření můžete připojit všechny ostatní výpočetní prostředky.

1. Vyplňte formulář. Zadejte hodnoty požadovaných vlastností, zejména **rodinu virtuálních počítačů**, a **maximální počet uzlů** , které se mají použít ke spuštění výpočtů.  

1. Vyberte __Vytvořit__.


1. Stav operace vytvoření si zobrazíte tak, že v seznamu vyberete cíl služby Compute:

    ![Vyberte výpočetní cíl pro zobrazení stavu operace vytvoření.](./media/how-to-set-up-training-targets/View_list.png)

1. Zobrazí se podrobnosti o cíli služby Compute: 

    ![Zobrazit podrobnosti o cíli počítače](./media/how-to-set-up-training-targets/compute-target-details.png) 

### <a id="portal-reuse"></a>Připojit cíle výpočtů

Chcete-li použít výpočetní cíle vytvořené mimo Azure Machine Learning pracovní prostor, je nutné je připojit. Připojení k cílovému cíli zpřístupníte pracovnímu prostoru.

Podle výše popsaného postupu zobrazte seznam cílů výpočtů. Pak pomocí následujících kroků Připojte cíl výpočetních prostředků: 

1. Vyberte znaménko plus (+) a přidejte cíl výpočtů. 
1. Zadejte název pro cíl služby Compute. 
1. Vyberte typ výpočetních prostředků, které se mají připojit ke __školení__:

    > [!IMPORTANT]
    > Z Azure Machine Learning studia se nedají připojit všechny výpočetní typy. Typy výpočetních prostředků, které se dají v současnosti připojit ke školením, zahrnují:
    >
    > * Vzdálený virtuální počítač
    > * Azure Databricks (pro použití v kanálech strojového učení)
    > * Azure Data Lake Analytics (pro použití v kanálech strojového učení)
    > * Azure HDInsight

1. Vyplňte formulář a zadejte hodnoty požadovaných vlastností.

    > [!NOTE]
    > Microsoft doporučuje používat klíče SSH, které jsou bezpečnější než hesla. Hesla jsou zranitelná proti útokům hrubou silou. Klíče SSH spoléhají na kryptografické signatury. Informace o tom, jak vytvořit klíče SSH pro použití s Azure Virtual Machines, najdete v následujících dokumentech:
    >
    > * [Vytvoření a použití klíčů SSH v systému Linux nebo macOS](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Vytvoření a použití klíčů SSH ve Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Vyberte __připojit__. 
1. Pokud chcete zobrazit stav operace připojení, vyberte ze seznamu cíl služby Compute.

## <a name="set-up-with-cli"></a>Nastavení pomocí rozhraní příkazového řádku

K cílovým cílům, které jsou přidruženy k vašemu pracovnímu prostoru, získáte přístup pomocí [rozšíření CLI](reference-azure-machine-learning-cli.md) pro Azure Machine Learning.  Rozhraní příkazového řádku můžete použít k těmto akcím:

* Vytvoření spravovaného cílového výpočetního prostředí
* Aktualizace spravovaného cíle výpočtů
* Připojit nespravovaný cíl služby COMPUTE

Další informace najdete v tématu [Správa prostředků](reference-azure-machine-learning-cli.md#resource-management).

## <a name="set-up-with-vs-code"></a>Nastavení pomocí VS Code

K pracovním prostorům, které jsou přidruženy k pracovnímu prostoru pomocí [rozšíření vs Code](tutorial-train-deploy-image-classification-model-vscode.md#configure-compute-targets) pro Azure Machine Learning, můžete přistupovat, vytvářet a spravovat výpočetní cíle.

## <a id="submit"></a>Odeslání školicích běhů pomocí sady Azure Machine Learning SDK

Po vytvoření konfigurace spuštění ji použijete ke spuštění experimentu.  Vzor kódu pro odeslání školicího běhu je stejný pro všechny typy výpočetních cílů:

1. Vytvoření experimentu ke spuštění
1. Odešlete spuštění.
1. Vyčkat, než běžet do dokončení.

> [!IMPORTANT]
> Po odeslání školicího cvičení se vytvoří snímek adresáře, který obsahuje vaše školicí skripty, a pošle se do cíle služby Compute. Je také uložen jako součást experimentu v pracovním prostoru. Pokud změníte soubory a znovu odešlete spuštění, nahrají se jenom změněné soubory.
>
> Chcete-li zabránit zahrnutí souborů do snímku, vytvořte soubor [. gitignore](https://git-scm.com/docs/gitignore) nebo `.amlignore` v adresáři a přidejte do něj soubory. `.amlignore` soubor používá stejnou syntaxi a vzory jako soubor [. gitignore](https://git-scm.com/docs/gitignore) . Pokud oba soubory existují, má `.amlignore` soubor přednost.
> 
> Další informace najdete v tématu [snímky](concept-azure-machine-learning-architecture.md#snapshots).

### <a name="create-an-experiment"></a>Vytvoření experimentu

Nejdřív vytvořte experiment v pracovním prostoru.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=experiment)]

### <a name="submit-the-experiment"></a>Odeslání experimentu

Odešlete experiment s objektem `ScriptRunConfig`.  Tento objekt obsahuje:

* **source_directory**: zdrojový adresář, který obsahuje školicí skript.
* **skript**: identifikace školicího skriptu
* **run_config**: konfigurace spuštění, která zase definuje, kde se bude probíhat školení.

Chcete-li například použít [místní cílovou](#local) konfiguraci:

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=local_submit)]

Přepněte stejný experiment ke spuštění v jiném výpočetním cíli pomocí jiné konfigurace spuštění, jako je například [amlcompute cíl](#amlcompute):

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=amlcompute_submit)]

> [!TIP]
> Tento příklad standardně používá jenom jeden uzel výpočetního cíle pro školení. Chcete-li použít více než jeden uzel, nastavte `node_count` konfigurace spuštění na požadovaný počet uzlů. Například následující kód nastaví počet uzlů používaných pro školení na čtyři:
>
> ```python
> src.run_config.node_count = 4
> ```

Nebo můžete:

* Odešlete experiment s objektem `Estimator`, jak je znázorněno v [modelech vlak ml pomocí odhady](how-to-train-ml-models.md).
* Odešlete HyperDrive spuštění pro [ladění pomocí parametrů](how-to-tune-hyperparameters.md).
* Odešlete experiment prostřednictvím [rozšíření vs Code](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model).

Další informace najdete v dokumentaci k [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) a [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) .

## <a name="create-run-configuration-and-submit-run-using-azure-machine-learning-cli"></a>Vytvoření konfigurace spuštění a odeslání běhu pomocí Azure Machine Learning CLI

Pomocí rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) a [rozšíření CLI Machine Learning](reference-azure-machine-learning-cli.md) můžete vytvářet konfigurace spouštění a odesílat běhy na různých výpočetních cílech. V následujících příkladech se předpokládá, že máte existující pracovní prostor Azure Machine Learning a jste přihlášeni k Azure pomocí příkazu `az login` CLI. 

### <a name="create-run-configuration"></a>Vytvořit konfiguraci spuštění

Nejjednodušší způsob, jak vytvořit konfiguraci spuštění, je procházení složky, která obsahuje skripty služby Machine Learning v Pythonu, a použití příkazu CLI.

```azurecli
az ml folder attach
```

Tento příkaz vytvoří podsložku `.azureml` obsahující šablonu pro spouštění konfiguračních souborů pro různé výpočetní cíle. Tyto soubory můžete zkopírovat a upravit, abyste mohli přizpůsobit konfiguraci, například přidat balíčky Pythonu nebo změnit nastavení Docker.  

### <a name="structure-of-run-configuration-file"></a>Struktura konfiguračního souboru spuštění

Konfigurační soubor spuštění je YAML formátovaný s následujícími oddíly.
 * Skript, který se má spustit, a jeho argumenty
 * Název cíle výpočtů, buď místní, nebo název COMPUTE v pracovním prostoru.
 * Parametry pro spuštění příkazu Run: Framework, Communicator pro distribuované běhy, maximální doba trvání a počet výpočetních uzlů.
 * Oddíl prostředí. Podrobnosti o polích v této části najdete v tématu [Vytvoření a Správa prostředí pro školení a nasazení](how-to-use-environments.md) .
   * Chcete-li určit balíčky Pythonu, které mají být nainstalovány pro příkaz Spustit, vytvořit [soubor prostředí conda](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#create-env-file-manually)a nastavte pole __condaDependenciesFile__ .
 * Chcete-li určit složku souboru protokolu a povolit nebo zakázat shromažďování výstupu a snímky historie spuštění, podrobnosti o historii spuštění.
 * Podrobnosti konfigurace specifické pro vybrané rozhraní.
 * Odkaz na data a podrobnosti úložiště dat.
 * Podrobnosti konfigurace specifické pro Výpočetní prostředky služby Machine Learning pro vytvoření nového clusteru.

Úplné schéma RunConfig najdete v ukázkovém [souboru JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json) .

### <a name="create-an-experiment"></a>Vytvoření experimentu

Nejdřív vytvořte experiment pro vaše běhy.

```azurecli
az ml experiment create -n <experiment>
```

### <a name="script-run"></a>Spuštění skriptu

Pokud chcete odeslat skript, spusťte příkaz.

```azurecli
az ml run submit-script -e <experiment> -c <runconfig> my_train.py
```

### <a name="hyperdrive-run"></a>HyperDrive spuštění

K provedení ladění parametrů můžete použít HyperDrive s Azure CLI. Nejprve vytvořte konfigurační soubor HyperDrive v následujícím formátu. Podrobnosti o parametrech ladění parametrů naleznete v tématu [vyladění parametrů pro modelový](how-to-tune-hyperparameters.md) článek.

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

Přidejte tento soubor spolu s konfiguračními soubory spuštění. Pak odešlete HyperDrive běh pomocí:
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

Všimněte si oddílu *argumenty* v RunConfig a *prostoru parametrů* v souboru Hyperdrive config. Obsahují argumenty příkazového řádku, které se mají předat skriptu pro školení. Hodnota v RunConfig zůstává pro každou iteraci stejná, zatímco rozsah v HyperDrive config se prochází. Nezadávejte v obou souborech stejný argument.

Další podrobnosti o těchto ```az ml``` příkazy rozhraní příkazového řádku a úplnou sadu argumentů naleznete v [referenční dokumentaci](reference-azure-machine-learning-cli.md).

<a id="gitintegration"></a>

## <a name="git-tracking-and-integration"></a>Sledování a integrace Git

Když spustíte školicí kurz, kde zdrojový adresář je místní úložiště Git, informace o úložišti se ukládají v historii spuštění. Další informace najdete v tématu [integrace Gitu pro Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="notebook-examples"></a>Příklady poznámkových bloků

Příklady školení s různými cíli výpočtů najdete v těchto poznámkových blocích:
* [postupy – použití – AzureML/školení](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [kurzy/img-Classification-part1-Training. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další kroky

* [Kurz: výuka modelu](tutorial-train-models-with-aml.md) používá ke školení modelu spravovaný výpočetní cíl.
* Naučte se [efektivně ladit parametry](how-to-tune-hyperparameters.md) pro vytváření lepších modelů.
* Jakmile budete mít školený model, zjistěte, [jak a kde nasadit modely](how-to-deploy-and-where.md).
* Zobrazit odkaz na sadu SDK [třídy RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py)
* [Použití Azure Machine Learning s Azure Virtual Networks](how-to-enable-virtual-network.md)
