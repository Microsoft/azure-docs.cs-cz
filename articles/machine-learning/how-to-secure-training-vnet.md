---
title: Zabezpečená školicí prostředí s virtuálními sítěmi
titleSuffix: Azure Machine Learning
description: K zabezpečení školicího prostředí v Azure Machine Learning použijte izolovaný Virtual Network Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 07/16/2020
ms.custom: contperfq4, tracking-python, contperfq1
ms.openlocfilehash: 2b0a56bac1652881e9d1733bcb52b02610e27e9e
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93314161"
---
# <a name="secure-an-azure-machine-learning-training-environment-with-virtual-networks"></a>Zabezpečení Azure Machine Learningho školicího prostředí s využitím virtuálních sítí

V tomto článku se dozvíte, jak zabezpečit školicí prostředí pomocí virtuální sítě v Azure Machine Learning.

Tento článek je třetí částí série s pěti částmi, která vás provede zabezpečením Azure Machine Learningho pracovního postupu. Důrazně doporučujeme, abyste si přečetli [část One: VNet – přehled](how-to-network-security-overview.md) , abyste si nejdřív porozuměli celkové architektuře. 

Podívejte se na další články v této sérii:

[1. virtuální síť – přehled](how-to-network-security-overview.md)  >  [Zabezpečte pracovní prostor](how-to-secure-workspace-vnet.md)  >  **3. Zabezpečte školicí prostředí**  >  [4. Zabezpečte prostředí Inferencing](how-to-secure-inferencing-vnet.md)   >  [5. Povolit funkci studia](how-to-enable-studio-virtual-network.md)

V tomto článku se dozvíte, jak zabezpečit následující výpočetní prostředky ve virtuální síti:
> [!div class="checklist"]
> - Azure Machine Learning výpočetní cluster
> - Výpočetní instance Azure Machine Learningu
> - Azure Databricks
> - Virtuální počítač
> - Cluster HDInsight

## <a name="prerequisites"></a>Předpoklady

+ Přečtěte si článek [Přehled zabezpečení sítě](how-to-network-security-overview.md) , který vám pomůže pochopit běžné scénáře virtuální sítě a celkovou architekturu virtuální sítě.

+ Existující virtuální síť a podsíť pro použití s výpočetními prostředky.

+ Aby bylo možné nasadit prostředky do virtuální sítě nebo podsítě, musí mít váš uživatelský účet oprávnění k následujícím akcím v řízení přístupu na základě role Azure (Azure RBAC):

    - "Microsoft. Network/virtualNetworks/JOIN/Action" na prostředku virtuální sítě.
    - "Microsoft. Network/virtualNetworks/podsíť/JOIN/Action" na prostředku podsítě.

    Další informace o RBAC v Azure s využitím sítě najdete v tématu [předdefinované role sítě](../role-based-access-control/built-in-roles.md#networking) .


## <a name="compute-clusters--instances"></a><a name="compute-instance"></a>Výpočetní clustery & instance 

Pokud chcete ve virtuální síti použít [spravovaný Azure Machine Learning __výpočetní cíl__](concept-compute-target.md#azure-machine-learning-compute-managed) nebo [Azure Machine Learning výpočetní __instanci__](concept-compute-instance.md) , musí být splněné následující požadavky na síť:

> [!div class="checklist"]
> * Virtuální síť musí být ve stejném předplatném a oblasti jako pracovní prostor Azure Machine Learning.
> * Podsíť zadaná pro výpočetní instanci nebo cluster musí mít k dispozici dostatek nepřiřazených IP adres pro přizpůsobení počtu cílových virtuálních počítačů. Pokud podsíť nemá dostatek nepřiřazených IP adres, výpočetní cluster se částečně přidělí.
> * Zkontrolujte, jestli zásady zabezpečení nebo zámky v předplatném virtuální sítě nebo skupině prostředků omezují oprávnění ke správě virtuální sítě. Pokud máte v úmyslu zabezpečit virtuální síť omezením provozu, nechte některé porty pro výpočetní službu otevřené. Další informace najdete v části [požadované porty](#mlcports) .
> * Pokud hodláte do jedné virtuální sítě umístit víc výpočetních instancí nebo clusterů, možná budete muset požádat o zvýšení kvóty pro jeden nebo víc vašich prostředků.
> * Pokud jsou účty Azure Storage v pracovním prostoru zabezpečeny i ve virtuální síti, musí být ve stejné virtuální síti jako Azure Machine Learning výpočetní instance nebo cluster. 
> * Aby funkce COMPUTE instance Jupyter fungovala, ujistěte se, že komunikace webového soketu není zakázána. Ujistěte se prosím, že vaše síť povoluje připojení pomocí protokolu WebSocket k *. instances.azureml.net a *. instances.azureml.ms. 
> * Když je instance služby COMPUTE nasazená v pracovním prostoru privátního propojení, dá se k ní dostat jenom z virtuální sítě. Pokud používáte vlastní soubor DNS nebo hostitele, přidejte položku pro `<instance-name>.<region>.instances.azureml.ms` s privátní IP adresou privátního koncového bodu pracovního prostoru. Další informace najdete v článku o [vlastním serveru DNS](./how-to-custom-dns.md) .
    
> [!TIP]
> Instance Machine Learning COMPUTE nebo cluster automaticky přiděluje další síťové prostředky __ve skupině prostředků, která obsahuje virtuální síť__. Pro každou výpočetní instanci nebo cluster přiděluje služba následující prostředky:
> 
> * Jedna skupina zabezpečení sítě
> * Jedna veřejná IP adresa
> * Jeden nástroj pro vyrovnávání zatížení
> 
> V případě clusterů těchto prostředků se odstraní (a znovu vytvoří), když se cluster škáluje na 0 uzlů, ale u instance, na kterou se prostředky ukládají, ale do úplného odstranění instance (zastavování neodebere prostředky). 
> Pro tyto prostředky platí omezení [kvót prostředků](../azure-resource-manager/management/azure-subscription-service-limits.md) předplatného.


### <a name="required-ports"></a><a id="mlcports"></a> Požadované porty

Pokud plánujete zabezpečit virtuální síť tím, že omezíte síťový provoz na veřejný Internet, musíte povolit příchozí komunikaci ze služby Azure Batch.

Služba Batch přidá skupiny zabezpečení sítě (skupin zabezpečení sítě) na úrovni síťových rozhraní (nic) připojených k virtuálním počítačům. Tyto skupiny zabezpečení sítě automaticky konfigurují pravidla příchozích a odchozích přenosů, která povolují následující provoz:

- Příchozí provoz TCP na portech 29876 a 29877 ze __značky služby__ __BatchNodeManagement__.

    ![Příchozí pravidlo, které používá značku služby BatchNodeManagement](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- Volitelné Příchozí provoz TCP na portu 22 pro povolení vzdáleného přístupu. Tento port použijte pouze v případě, že se chcete připojit pomocí SSH na veřejné IP adrese.

- Odchozí provoz do virtuální sítě na jakémkoli portu.

- Odchozí provoz do internetu na jakémkoli portu.

- Pro příchozí provoz TCP pro výpočetní instance na portu 44224 ze __značky služby__ __AzureMachineLearning__.

> [!IMPORTANT]
> Pokud potřebujete upravit nebo přidat pravidla příchozích nebo odchozích přenosů ve skupinách zabezpečení sítě nakonfigurovaných službou Batch, postupujte obezřetně. Pokud NSG blokuje komunikaci s výpočetními uzly, služba COMPUTE nastaví stav výpočetních uzlů na nepoužitelné.
>
> Nemusíte zadávat skupin zabezpečení sítě na úrovni podsítě, protože služba Azure Batch konfiguruje vlastní skupin zabezpečení sítě. Pokud však podsíť, která obsahuje Azure Machine Learning COMPUTE, má přidruženou skupin zabezpečení sítě nebo bránu firewall, musíte také povolený provoz.

Konfigurace pravidla NSG se v Azure Portal zobrazuje na následujících obrázcích:

:::image type="content" source="./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png" alt-text="Příchozí NSG pravidla pro Výpočetní prostředky služby Machine Learning" border="true":::



![Příchozí NSG pravidla pro Výpočetní prostředky služby Machine Learning](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="limit-outbound-connectivity-from-the-virtual-network"></a><a id="limiting-outbound-from-vnet"></a> Omezení odchozího připojení z virtuální sítě

Pokud nechcete používat výchozí odchozí pravidla a chcete omezit odchozí přístup k virtuální síti, použijte následující postup:

- Odmítne odchozí připojení k Internetu pomocí pravidel NSG.

- V případě __výpočetní instance__ nebo __výpočetního clusteru__ omezte odchozí provoz na následující položky:
   - Azure Storage pomocí __označení služby__ __Storage. RegionName__. Kde `{RegionName}` je název oblasti Azure.
   - Azure Container Registry pomocí __označení služby__ __AzureContainerRegistry. RegionName__. Kde `{RegionName}` je název oblasti Azure.
   - Azure Machine Learning pomocí __označení služby__ __AzureMachineLearning__
   - Azure Resource Manager pomocí __označení služby__ __AzureResourceManager__
   - Azure Active Directory pomocí __označení služby__ __azureactivedirectory selhala__

Konfigurace pravidla NSG se v Azure Portal zobrazuje na následujícím obrázku:

[![Odchozí NSG pravidla pro Výpočetní prostředky služby Machine Learning](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png#lightbox)

> [!NOTE]
> Pokud plánujete používání výchozích imagí Docker poskytovaných Microsoftem a povolením spravovaných závislostí uživatelů, musíte použít taky následující __značky služby__ :
>
> * __MicrosoftContainerRegistry__
> * __AzureFrontDoor.FirstParty__
>
> Tato konfigurace je nutná, pokud máte kód podobný následujícímu fragmentu kódu jako součást vašich školicích skriptů:
>
> __Školení RunConfig__
> ```python
> # create a new runconfig object
> run_config = RunConfiguration()
> 
> # configure Docker 
> run_config.environment.docker.enabled = True
> # For GPU, use DEFAULT_GPU_IMAGE
> run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE 
> run_config.environment.python.user_managed_dependencies = True
> ```
>
> __Školení Estimator__
> ```python
> est = Estimator(source_directory='.',
>                 script_params=script_params,
>                 compute_target='local',
>                 entry_script='dummy_train.py',
>                 user_managed=True)
> run = exp.submit(est)
> ```

### <a name="forced-tunneling"></a>Vynucené tunelování

Pokud používáte [vynucené tunelování](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) s využitím Azure Machine Learning COMPUTE, musíte z podsítě, která obsahuje výpočetní prostředky, umožňovat komunikaci s veřejným internetem. Tato komunikace se používá pro plánování úloh a přístup k Azure Storage.

Můžete to provést dvěma způsoby:

* Použijte [Virtual Network překlad adres (NAT)](../virtual-network/nat-overview.md). Brána NAT poskytuje odchozí připojení k Internetu pro jednu nebo více podsítí ve vaší virtuální síti. Informace najdete v tématu [navrhování virtuálních sítí s využitím prostředků brány NAT](../virtual-network/nat-gateway-resource.md).

* Přidejte [uživatelsky definované trasy (udr)](../virtual-network/virtual-networks-udr-overview.md) do podsítě, která obsahuje výpočetní prostředek. Vytvořte UDR pro každou IP adresu, kterou používá služba Azure Batch, v oblasti, kde existují vaše prostředky. Tyto udr umožňují, aby služba Batch komunikovala s výpočetními uzly pro plánování úloh. Přidejte také IP adresu pro službu Azure Machine Learning, kde existují prostředky, jak je to nutné pro přístup k výpočetním instancím. Chcete-li získat seznam IP adres služby Batch a služby Azure Machine Learning, použijte jednu z následujících metod:

    * Stáhněte si [rozsahy IP adres Azure a značky služby](https://www.microsoft.com/download/details.aspx?id=56519) a vyhledejte v něm soubor `BatchNodeManagement.<region>` a `AzureMachineLearning.<region>` , kde `<region>` je vaše oblast Azure.

    * Informace si můžete stáhnout pomocí [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest) . Následující příklad stáhne informace o IP adrese a odfiltruje informace o Východní USA 2 oblasti:

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
        ```

        > [!TIP]
        > Pokud používáte oblasti US-) – Virginia, US-Arizona oblasti nebo Čínu – východ-2, tyto příkazy nevrátí žádné IP adresy. Místo toho použijte jeden z následujících odkazů ke stažení seznamu IP adres:
        >
        > * [Rozsahy IP adres a značek služeb Azure pro Azure Government](https://www.microsoft.com/download/details.aspx?id=57063)
        > * [Rozsahy IP adres a značky služeb Azure pro Azure Čína](https://www.microsoft.com//download/details.aspx?id=57062)
    
    Když přidáte udr, definujte trasu pro každou související předponu IP adresy dávky a nastavte __typ dalšího segmentu směrování__ na __Internet__. Následující obrázek ukazuje příklad tohoto UDR v Azure Portal:

    ![Příklad UDR pro předponu adresy](./media/how-to-enable-virtual-network/user-defined-route.png)

    > [!IMPORTANT]
    > IP adresy se můžou v průběhu času měnit.

    Kromě všech udr, které definujete, musí být odchozí provoz do Azure Storage povolen prostřednictvím místního síťového zařízení. Konkrétně adresy URL tohoto provozu jsou v následujících formulářích: `<account>.table.core.windows.net` , `<account>.queue.core.windows.net` a `<account>.blob.core.windows.net` . 

    Další informace najdete v tématu [Vytvoření fondu Azure Batch ve virtuální síti](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling).


### <a name="create-a-compute-cluster-in-a-virtual-network"></a>Vytvoření výpočetního clusteru ve virtuální síti

Chcete-li vytvořit cluster Výpočetní prostředky služby Machine Learning, použijte následující postup:

1. Přihlaste se k [Azure Machine Learning Studiu](https://ml.azure.com/)a pak vyberte své předplatné a pracovní prostor.

1. Na levé straně vyberte __COMPUTE__ .

1. V centru vyberte __školicí clustery__ a pak vyberte __+__ .

1. V dialogovém okně __Nový školicí cluster__ rozbalte oddíl __Upřesnit nastavení__ .

1. Pokud chcete tento výpočetní prostředek nakonfigurovat tak, aby používal virtuální síť, proveďte v části __Konfigurace virtuální sítě__ následující akce:

    1. V rozevíracím seznamu __Skupina prostředků__ vyberte skupinu prostředků, která obsahuje virtuální síť.
    1. V rozevíracím seznamu __virtuální síť__ vyberte virtuální síť, která obsahuje podsíť.
    1. V rozevíracím seznamu __podsíť__ vyberte podsíť, která se má použít.

   ![Nastavení virtuální sítě pro Výpočetní prostředky služby Machine Learning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

Cluster Výpočetní prostředky služby Machine Learning můžete vytvořit také pomocí sady Azure Machine Learning SDK. Následující kód vytvoří nový cluster Výpočetní prostředky služby Machine Learning v `default` podsíti virtuální sítě s názvem `mynetwork` :

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# The Azure virtual network name, subnet, and resource group
vnet_name = 'mynetwork'
subnet_name = 'default'
vnet_resourcegroup_name = 'mygroup'

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print("Found existing cpucluster")
except ComputeTargetException:
    print("Creating new cpucluster")

    # Specify the configuration for the new cluster
    compute_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                           min_nodes=0,
                                                           max_nodes=4,
                                                           vnet_resourcegroup_name=vnet_resourcegroup_name,
                                                           vnet_name=vnet_name,
                                                           subnet_name=subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

    # Wait for the cluster to be completed, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

Po dokončení procesu vytváření můžete model pomocí clusteru v experimentu proškolit. Další informace najdete v tématu [Výběr a použití výpočetní cíle pro školení](how-to-set-up-training-targets.md).

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="access-data-in-a-compute-instance-notebook"></a>Přístup k datům v poznámkovém bloku instance COMPUTE

Pokud používáte poznámkové bloky ve službě Azure COMPUTE instance, musíte zajistit, aby váš Poznámkový blok běžel na výpočetním prostředku za stejnou virtuální sítí a podsítí jako vaše data. 

Instanci služby COMPUTE musíte nakonfigurovat tak, aby byla ve stejné virtuální síti během vytváření v části **Upřesnit nastavení**  >  **Konfigurace služby Virtual Network**. Existující výpočetní instanci nemůžete přidat do virtuální sítě.

## <a name="azure-databricks"></a>Azure Databricks

Pokud chcete použít Azure Databricks ve virtuální síti s vaším pracovním prostorem, musí být splněné následující požadavky:

> [!div class="checklist"]
> * Virtuální síť musí být ve stejném předplatném a oblasti jako pracovní prostor Azure Machine Learning.
> * Pokud jsou účty Azure Storage v pracovním prostoru zabezpečeny i ve virtuální síti, musí být ve stejné virtuální síti jako cluster Azure Databricks.
> * Kromě __datacihly –__ veřejné podsítě a __datacihly__ , které používají Azure Databricks, je také nutná __výchozí__ podsíť vytvořená pro virtuální síť.

Konkrétní informace o použití Azure Databricks s virtuální sítí najdete v tématu [nasazení Azure Databricks ve službě Azure Virtual Network](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html).

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>Virtuální počítač nebo cluster HDInsight

> [!IMPORTANT]
> Azure Machine Learning podporuje pouze virtuální počítače se systémem Ubuntu.

V této části se dozvíte, jak používat virtuální počítač nebo cluster Azure HDInsight ve virtuální síti s vaším pracovním prostorem.

### <a name="create-the-vm-or-hdinsight-cluster"></a>Vytvoření virtuálního počítače nebo clusteru HDInsight

Vytvořte virtuální počítač nebo cluster HDInsight pomocí Azure Portal nebo Azure CLI a vložte cluster do virtuální sítě Azure. Další informace najdete v následujících článcích:
* [Vytváření a správa virtuálních sítí Azure pro virtuální počítače se systémem Linux](../virtual-machines/linux/tutorial-virtual-network.md)

* [Rozšiřování HDInsight pomocí virtuální sítě Azure](../hdinsight/hdinsight-plan-virtual-network-deployment.md)

### <a name="configure-network-ports"></a>Konfigurace síťových portů 

Povolí Azure Machine Learning komunikaci s portem SSH na virtuálním počítači nebo v clusteru, nakonfigurujte položku zdroje pro skupinu zabezpečení sítě. Port SSH je obvykle port 22. Pokud chcete povolit přenos z tohoto zdroje, proveďte následující akce:

1. V rozevíracím seznamu __zdroj__ vyberte možnost __značka služby__.

1. V rozevíracím seznamu __značka zdrojové služby__ vyberte možnost __AzureMachineLearning__.

    ![Příchozí pravidla pro experimentování na virtuálním počítači nebo clusteru HDInsight ve virtuální síti](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

1. V rozevíracím seznamu __rozsahy zdrojových portů__ vyberte __*__ .

1. V rozevíracím seznamu __cíl__ vyberte možnost __libovolný__.

1. V rozevíracím seznamu __rozsahy cílových portů__ vyberte __22__.

1. V části __protokol__ vyberte __libovolný__.

1. V části __Akce__ vyberte možnost __povoleno__.

Ponechte výchozí odchozí pravidla pro skupinu zabezpečení sítě. Další informace najdete v tématu výchozí pravidla zabezpečení ve [skupinách zabezpečení](../virtual-network/network-security-groups-overview.md#default-security-rules).

Pokud nechcete používat výchozí odchozí pravidla a chcete omezit odchozí přístup k virtuální síti, přečtěte si část [omezení odchozího připojení z virtuální sítě](#limiting-outbound-from-vnet) .

### <a name="attach-the-vm-or-hdinsight-cluster"></a>Připojit virtuální počítač nebo cluster HDInsight

Připojte virtuální počítač nebo cluster HDInsight k pracovnímu prostoru Azure Machine Learning. Další informace najdete v tématu [Nastavení výpočetních cílů pro školení modelů](how-to-set-up-training-targets.md).

## <a name="next-steps"></a>Další kroky

Tento článek je třetí částí v řadě virtuálních sítí se čtyřmi částmi. Další informace o tom, jak zabezpečit virtuální síť, najdete v dalších článcích:

* [Část 1: Přehled virtuální sítě](how-to-network-security-overview.md)
* [Část 2: zabezpečení prostředků pracovního prostoru](how-to-secure-workspace-vnet.md)
* [4. část: zabezpečení prostředí Inferencing](how-to-secure-inferencing-vnet.md)
* [Část 5: povolení funkcí studia](how-to-enable-studio-virtual-network.md)