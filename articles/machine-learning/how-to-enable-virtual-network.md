---
title: Izolace sítě & ochraně osobních údajů
titleSuffix: Azure Machine Learning
description: Použijte izolovaný Virtual Network Azure s Azure Machine Learning k zabezpečení experimentů a školení a také k odvození a bodování úloh.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 06/22/2020
ms.custom: contperfq4, tracking-python
ms.openlocfilehash: 5415237a502116b597c1514f75f35203108237ec
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2020
ms.locfileid: "85299071"
---
# <a name="network-isolation-during-training--inference-with-private-virtual-networks"></a>Izolace sítě během školení & odvození s privátními virtuálními sítěmi
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak zabezpečit životní cyklus strojového učení tím, že izolujete Azure Machine Learning školení a odvozování úloh v rámci Azure Virtual Network (VNET). Azure Machine Learning spoléhá na další služby Azure pro výpočetní prostředky, označované taky jako [cíle pro výpočty](concept-compute-target.md), a to pro výuku a nasazení modelů. Cíle lze vytvořit v rámci virtuální sítě. Můžete například využít Azure Machine Learning COMPUTE pro výuku modelu a pak model nasadit do služby Azure Kubernetes Service (AKS). 

**Virtuální síť** funguje jako hranice zabezpečení a izoluje prostředky Azure od veřejného Internetu. Virtuální síť Azure se taky můžete připojit k místní síti. Připojením sítí můžete bezpečně prosazovat modely a přistupovat k nasazeným modelům pro odvození.

Pokud **je vaše základní úložiště ve virtuální síti, uživatelé nebudou moci používat webové prostředí sady Azure Machine Learning Studio**, včetně návrháře přetažení nebo uživatelského rozhraní pro automatizované strojové učení, popisků dat a datových sad a integrovaných poznámkových bloků.  Pokud se pokusíte, zobrazí se zpráva podobná následující chybě:`__Error: Unable to profile this dataset. This might be because your data is stored behind a virtual network or your data does not support profile.__`

## <a name="prerequisites"></a>Požadavky

+ [Pracovní prostor](how-to-manage-workspace.md)Azure Machine Learning.

+ Obecné praktické znalosti [služby Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) a [sítě IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).

+ Již existující virtuální síť a podsíť, která se má použít s výpočetními prostředky.

## <a name="private-endpoints"></a>Soukromé koncové body

Můžete také [Povolit privátní propojení Azure](how-to-configure-private-link.md) a připojit se k vašemu pracovnímu prostoru pomocí privátního koncového bodu. Soukromý koncový bod je sada privátních IP adres v rámci vaší virtuální sítě. [Přečtěte si, jak nastavit Tento soukromý koncový bod.](how-to-configure-private-link.md)



> [!TIP]
> Můžete kombinovat virtuální síť a privátní propojení a chránit tak komunikaci mezi vaším pracovním prostorem a dalšími prostředky Azure. Některé kombinace ale vyžadují pracovní prostor Enterprise Edition. Následující tabulka vám pomůže pochopit, jaké scénáře vyžaduje Enterprise Edition:
>
> | Scénář | Enterprise</br>Edition | Basic</br>Edition |
> | ----- |:-----:|:-----:| 
> | Žádná virtuální síť ani privátní odkaz | ✔ | ✔ |
> | Pracovní prostor bez privátního odkazu Další prostředky (kromě Azure Container Registry) ve virtuální síti | ✔ | ✔ |
> | Pracovní prostor bez privátního odkazu Další prostředky s privátním odkazem | ✔ | |
> | Pracovní prostor s privátním odkazem. Další prostředky (kromě Azure Container Registry) ve virtuální síti | ✔ | ✔ |
> | Pracovní prostor a jakýkoliv jiný prostředek s privátním odkazem | ✔ | |
> | Pracovní prostor s privátním odkazem. Další prostředky bez privátního propojení nebo virtuální sítě | ✔ | ✔ |
> | Azure Container Registry ve virtuální síti | ✔ | |
> | Klíče spravované zákazníkem pro pracovní prostor | ✔ | |
> 

> [!WARNING]
> V pracovním prostoru, kde je povolený privátní odkaz, se nepodporuje Azure Machine Learning výpočetních instancí Preview.
> 
> Azure Machine Learning nepodporuje použití služby Azure Kubernetes s povoleným privátním odkazem. Místo toho můžete použít službu Azure Kubernetes ve virtuální síti. Další informace najdete v tématu [zabezpečení experimentů s Azure ml a odvozování úloh v rámci Azure Virtual Network](how-to-enable-virtual-network.md).


<a id="amlcompute"></a>

## <a name="compute-clusters--instances"></a><a name="compute-instance"></a>Výpočetní clustery & instance 

Pokud chcete ve virtuální síti použít [spravovaný Azure Machine Learning **výpočetní cíl** ](concept-compute-target.md#azure-machine-learning-compute-managed) nebo [Azure Machine Learning výpočetní **instanci** ](concept-compute-instance.md) , musí být splněné následující požadavky na síť:

> [!div class="checklist"]
> * Virtuální síť musí být ve stejném předplatném a oblasti jako pracovní prostor Azure Machine Learning.
> * Podsíť zadaná pro výpočetní instanci nebo cluster musí mít k dispozici dostatek nepřiřazených IP adres pro přizpůsobení počtu cílových virtuálních počítačů. Pokud podsíť nemá dostatek nepřiřazených IP adres, výpočetní cluster se částečně přidělí.
> * Zkontrolujte, jestli zásady zabezpečení nebo zámky v předplatném virtuální sítě nebo skupině prostředků omezují oprávnění ke správě virtuální sítě. Pokud máte v úmyslu zabezpečit virtuální síť omezením provozu, nechte některé porty pro výpočetní službu otevřené. Další informace najdete v části [požadované porty](#mlcports) .
> * Pokud hodláte do jedné virtuální sítě umístit víc výpočetních instancí nebo clusterů, možná budete muset požádat o zvýšení kvóty pro jeden nebo víc vašich prostředků.
> * Pokud jsou účty Azure Storage v pracovním prostoru zabezpečeny i ve virtuální síti, musí být ve stejné virtuální síti jako Azure Machine Learning výpočetní instance nebo cluster. 
> * Aby funkce COMPUTE instance Jupyter fungovala, ujistěte se, že komunikace webového soketu není zakázána.

> [!TIP]
> Instance Machine Learning COMPUTE nebo cluster automaticky přiděluje další síťové prostředky __ve skupině prostředků, která obsahuje virtuální síť__. Pro každou výpočetní instanci nebo cluster přiděluje služba následující prostředky:
> 
> * Jedna skupina zabezpečení sítě
> * Jedna veřejná IP adresa
> * Jeden nástroj pro vyrovnávání zatížení
> 
> V případě clusterů těchto prostředků se odstraní (a znovu vytvoří), když se cluster škáluje na 0 uzlů, ale u instance, na kterou se prostředky ukládají, ale do úplného odstranění instance (zastavování neodebere prostředky). 
> Pro tyto prostředky platí omezení [kvót prostředků](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) předplatného.


### <a name="required-ports"></a><a id="mlcports"></a>Požadované porty

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



![Odchozí NSG pravidla pro Výpočetní prostředky služby Machine Learning](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="limit-outbound-connectivity-from-the-virtual-network"></a><a id="limiting-outbound-from-vnet"></a>Omezení odchozího připojení z virtuální sítě

Pokud nechcete používat výchozí odchozí pravidla a chcete omezit odchozí přístup k virtuální síti, použijte následující postup:

- Odmítne odchozí připojení k Internetu pomocí pravidel NSG.

- V případě __výpočetní instance__ nebo __výpočetního clusteru__omezte odchozí provoz na následující položky:
   - Azure Storage pomocí __označení služby__ __Storage. RegionName__. Kde `{RegionName}` je název oblasti Azure.
   - Azure Container Registry pomocí __označení služby__ __AzureContainerRegistry. RegionName__. Kde `{RegionName}` je název oblasti Azure.
   - Azure Machine Learning pomocí __označení služby__ __AzureMachineLearning__
   - Azure Resource Manager pomocí __označení služby__ __AzureResourceManager__
   - Azure Active Directory pomocí __označení služby__ __azureactivedirectory selhala__

Konfigurace pravidla NSG se v Azure Portal zobrazuje na následujícím obrázku:

[![Odchozí NSG pravidla pro Výpočetní prostředky služby Machine Learning](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png#lightbox)

> [!NOTE]
> Pokud plánujete používání výchozích imagí Docker poskytovaných Microsoftem a povolením spravovaných závislostí uživatelů, musíte také použít __značku služby__ __MicrosoftContainerRegistry. Region_Name__ (například MicrosoftContainerRegistry. EastUS).
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

### <a name="user-defined-routes-for-forced-tunneling"></a>Uživatelem definované trasy pro vynucené tunelování

Pokud používáte vynucené tunelování s Výpočetní prostředky služby Machine Learning, přidejte [uživatelsky definované trasy (udr)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) do podsítě, která obsahuje výpočetní prostředky.

* Vytvořte UDR pro každou IP adresu, kterou používá služba Azure Batch, v oblasti, kde existují vaše prostředky. Tyto udr umožňují, aby služba Batch komunikovala s výpočetními uzly pro plánování úloh. Přidejte také IP adresu pro službu Azure Machine Learning, kde existují prostředky, jak je to nutné pro přístup k výpočetním instancím. Chcete-li získat seznam IP adres služby Batch a služby Azure Machine Learning, použijte jednu z následujících metod:

    * Stáhněte si [rozsahy IP adres Azure a značky služby](https://www.microsoft.com/download/details.aspx?id=56519) a vyhledejte v něm soubor `BatchNodeManagement.<region>` a `AzureMachineLearning.<region>` , kde `<region>` je vaše oblast Azure.

    * Informace si můžete stáhnout pomocí [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) . Následující příklad stáhne informace o IP adrese a odfiltruje informace o Východní USA 2 oblasti:

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
        ```

* Odchozí provoz do Azure Storage se nesmí blokovat v místním síťovém zařízení. Konkrétně adresy URL jsou ve formátu `<account>.table.core.windows.net` , `<account>.queue.core.windows.net` a `<account>.blob.core.windows.net` .

Když přidáte udr, definujte trasu pro každou související předponu IP adresy dávky a nastavte __typ dalšího segmentu směrování__ na __Internet__. Následující obrázek ukazuje příklad tohoto UDR v Azure Portal:

![Příklad UDR pro předponu adresy](./media/how-to-enable-virtual-network/user-defined-route.png)

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

## <a name="use-a-storage-account-for-your-workspace"></a>Použití účtu úložiště pro váš pracovní prostor

Pokud chcete použít účet úložiště Azure pro pracovní prostor ve virtuální síti, použijte následující postup:

1. Vytvořte výpočetní prostředek (například Machine Learning výpočetní instance nebo cluster) za virtuální sítí nebo připojte výpočetní prostředek k pracovnímu prostoru (například cluster HDInsight, virtuální počítač nebo cluster služby Azure Kubernetes). Výpočetní prostředek může být pro experimentování nebo nasazení modelu.

   Další informace najdete v částech [použití výpočetní služby Machine Learning](#amlcompute), [použití virtuálního počítače nebo clusteru HDInsight](#vmorhdi)a [používání služeb Azure Kubernetes](#aksvnet) v tomto článku.

1. V Azure Portal přejdete do úložiště, které je připojené k vašemu pracovnímu prostoru.

   [![Úložiště, které je připojené k pracovnímu prostoru Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. Na stránce **Azure Storage** vyberte možnost __brány firewall a virtuální sítě__.

   ![Oblast brány firewall a virtuální sítě na stránce Azure Storage v Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. Na stránce __brány firewall a virtuální sítě__ proveďte následující akce:
    - Vyberte __Vybrané sítě__.
    - V části __virtuální sítě__vyberte odkaz __Přidat existující virtuální síť__ . Tato akce přidá virtuální síť, ve které se nachází vaše výpočetní výkon (viz krok 1).

        > [!IMPORTANT]
        > Účet úložiště musí být ve stejné virtuální síti a podsíti jako výpočetní instance nebo clustery používané pro školení nebo odvození.

    - Zaškrtněte políčko __pro přístup k tomuto účtu úložiště udělit důvěryhodné služby Microsoftu__ .

    > [!IMPORTANT]
    > Při práci s Azure Machine Learning SDK musí být vaše vývojové prostředí schopné se připojit k účtu Azure Storage. Pokud je účet úložiště ve virtuální síti, musí brána firewall umožňovat přístup z IP adresy vývojového prostředí.
    >
    > Pokud chcete povolit přístup k účtu úložiště, přejděte na téma __brány firewall a virtuální sítě__ pro účet úložiště *z webového prohlížeče ve vývojovém klientovi*. Pak pomocí zaškrtávacího políčka __Přidat IP adresu klienta__ přidejte IP adresu klienta do __rozsahu adres__. Můžete také použít pole __Rozsah adres__ k ručnímu zadání IP adresy vývojového prostředí. Po přidání IP adresy pro klienta bude mít přístup k účtu úložiště pomocí sady SDK.

   [![Podokno brány firewall a virtuální sítě v Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

> [!IMPORTANT]
> Do virtuální sítě můžete umístit _výchozí účet úložiště_ pro Azure Machine Learning nebo _jiné než výchozí účty úložiště_ .
>
> Výchozí účet úložiště se automaticky zřídí při vytváření pracovního prostoru.
>
> U jiných než výchozích účtů úložiště `storage_account` vám parametr ve [ `Workspace.create()` funkci](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) umožní zadat vlastní účet úložiště podle ID prostředku Azure.


<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Pokud chcete do svého pracovního prostoru přidat službu Azure Kubernetes Service (AKS) ve virtuální síti, použijte následující postup:

> [!IMPORTANT]
> Než začnete s následujícím postupem, postupujte podle pokynů v tématu [Konfigurace pokročilé sítě ve službě Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/configure-azure-cni#prerequisites) postupy a naplánování IP adres pro váš cluster.
>
> Instance AKS a virtuální síť Azure musí být ve stejné oblasti. Pokud zabezpečujete Azure Storage účty používané pracovním prostorem ve virtuální síti, musí být ve stejné virtuální síti jako instance AKS.

> [!WARNING]
> Azure Machine Learning nepodporuje použití služby Azure Kubernetes s povoleným privátním odkazem.

1. Přihlaste se k [Azure Machine Learning Studiu](https://ml.azure.com/)a pak vyberte své předplatné a pracovní prostor.

1. Na levé straně vyberte __COMPUTE__ .

1. Vyberte __odvozené clustery__ z centra a pak vyberte __+__ .

1. V dialogu __nový cluster pro odvození__ vyberte v části __Konfigurace sítě__možnost __Upřesnit__ .

1. Pokud chcete tento výpočetní prostředek nakonfigurovat tak, aby používal virtuální síť, proveďte následující akce:

    1. V rozevíracím seznamu __Skupina prostředků__ vyberte skupinu prostředků, která obsahuje virtuální síť.
    1. V rozevíracím seznamu __virtuální síť__ vyberte virtuální síť, která obsahuje podsíť.
    1. V rozevíracím seznamu __podsíť__ vyberte podsíť.
    1. Do pole __Rozsah adres služby Kubernetes__ zadejte rozsah adres služby Kubernetes. Tento rozsah adres používá pro definování IP adres, které jsou k dispozici pro cluster, rozsah IP adres zápisu CIDR (Classless Inter-Domain Routing). Nesmí se překrývat s žádnými rozsahy IP adres podsítě (například 10.0.0.0/16).
    1. Do pole __IP adresa služby KUBERNETES DNS__ zadejte IP adresu služby DNS Kubernetes. Tato IP adresa se přiřadí službě DNS Kubernetes. Musí se nacházet v rozsahu adres služby Kubernetes (například 10.0.0.10).
    1. Do pole __adresa mostu Docker__ zadejte adresu mostu Docker. Tato IP adresa je přiřazená k mostu Docker. Nesmí se jednat o žádné rozsahy IP adres podsítě ani rozsah adres služby Kubernetes (například 172.17.0.1/16).

   ![Azure Machine Learning: Výpočetní prostředky služby Machine Learning nastavení virtuální sítě](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Ujistěte se, že skupina NSG, která řídí virtuální síť, má pro koncový bod bodování povolené příchozí pravidlo zabezpečení, aby se mohlo volat mimo virtuální síť.
   > [!IMPORTANT]
   > Ponechte výchozí odchozí pravidla pro NSG. Další informace najdete v tématu výchozí pravidla zabezpečení ve [skupinách zabezpečení](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

   [![Příchozí pravidlo zabezpečení](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

Pomocí sady Azure Machine Learning SDK můžete také přidat službu Azure Kubernetes ve virtuální síti. Pokud už máte cluster AKS ve virtuální síti, připojte ho k pracovnímu prostoru, jak je popsáno v tématu [Jak nasadit do AKS](how-to-deploy-and-where.md). Následující kód vytvoří novou instanci AKS v `default` podsíti virtuální sítě s názvem `mynetwork` :

```python
from azureml.core.compute import ComputeTarget, AksCompute

# Create the compute configuration and set virtual network information
config = AksCompute.provisioning_configuration(location="eastus2")
config.vnet_resourcegroup_name = "mygroup"
config.vnet_name = "mynetwork"
config.subnet_name = "default"
config.service_cidr = "10.0.0.0/16"
config.dns_service_ip = "10.0.0.10"
config.docker_bridge_cidr = "172.17.0.1/16"

# Create the compute target
aks_target = ComputeTarget.create(workspace=ws,
                                  name="myaks",
                                  provisioning_configuration=config)
```

Po dokončení procesu vytváření můžete spustit odvození nebo model bodování v clusteru AKS za virtuální sítí. Další informace najdete v tématu [Jak nasadit do AKS](how-to-deploy-and-where.md).

### <a name="use-private-ips-with-azure-kubernetes-service"></a>Použití privátních IP adres ve službě Azure Kubernetes

Ve výchozím nastavení je veřejná IP adresa přiřazená AKS nasazením. Při použití AKS v rámci virtuální sítě můžete místo toho použít privátní IP adresu. Privátní IP adresy jsou dostupné jenom v rámci virtuální sítě nebo v připojených sítích.

Privátní IP adresa je povolena konfigurací AKS k použití _interního nástroje pro vyrovnávání zatížení_. 

> [!IMPORTANT]
> Při vytváření clusteru služby Azure Kubernetes není možné povolit privátní IP adresu. Musí být povolená jako Aktualizace existujícího clusteru.

Následující fragment kódu ukazuje, jak **vytvořit nový cluster AKS**a pak ho aktualizovat tak, aby používal privátní IP/interní nástroj pro vyrovnávání zatížení:

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute, ComputeTarget

# Verify that cluster does not exist already
try:
    aks_target = AksCompute(workspace=ws, name=aks_cluster_name)
    print("Found existing aks cluster")

except:
    print("Creating new aks cluster")

    # Subnet to use for AKS
    subnet_name = "default"
    # Create AKS configuration
    prov_config = AksCompute.provisioning_configuration(location = "eastus2")
    # Set info for existing virtual network to create the cluster in
    prov_config.vnet_resourcegroup_name = "myvnetresourcegroup"
    prov_config.vnet_name = "myvnetname"
    prov_config.service_cidr = "10.0.0.0/16"
    prov_config.dns_service_ip = "10.0.0.10"
    prov_config.subnet_name = subnet_name
    prov_config.docker_bridge_cidr = "172.17.0.1/16"

    # Create compute target
    aks_target = ComputeTarget.create(workspace = ws, name = "myaks", provisioning_configuration = prov_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
    
    # Update AKS configuration to use an internal load balancer
    update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
    aks_target.update(update_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
```

__Azure CLI__

```azurecli-interactive
az rest --method put --uri https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace>/computes/<compute>?api-version=2018-11-19 --body @body.json
```

Obsah souboru, na `body.json` který se odkazuje pomocí příkazu, je podobný následujícímu dokumentu JSON:

```json
{ 
    "location": "<region>", 
    "properties": { 
        "resourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-name>", 
        "computeType": "AKS", 
        "provisioningState": "Succeeded", 
        "properties": { 
            "loadBalancerType": "InternalLoadBalancer", 
            "agentCount": <agent-count>, 
            "agentVmSize": "vm-size", 
            "clusterFqdn": "<cluster-fqdn>" 
        } 
    } 
} 
```

> [!NOTE]
> Nástroj pro vyrovnávání zatížení se v současné době nedá nakonfigurovat při provádění operace __připojení__ pro existující cluster. Musíte nejdřív připojit cluster a pak provést operaci aktualizace pro změnu nástroje pro vyrovnávání zatížení.

Další informace o používání interního nástroje pro vyrovnávání zatížení s AKS najdete v tématu [použití interního nástroje pro vyrovnávání zatížení se službou Azure Kubernetes Service](/azure/aks/internal-lb).

## <a name="use-azure-container-instances-aci"></a>Použít Azure Container Instances (ACI)

Azure Container Instances se dynamicky vytvářejí při nasazování modelu. Pokud chcete povolit Azure Machine Learning vytváření ACI uvnitř virtuální sítě, musíte povolit __delegování podsítě__ pro podsíť, kterou používá nasazení.

> [!WARNING]
> Pokud chcete použít Azure Container Instances v rámci virtuální sítě, Azure Container Registry (ACR) pro váš pracovní prostor se taky ve virtuální síti nedá.

Pokud chcete použít ACI ve virtuální síti k vašemu pracovnímu prostoru, použijte následující postup:

1. K povolení delegování podsítě ve virtuální síti použijte informace v článku [Přidání nebo odebrání delegování podsítě](../virtual-network/manage-subnet-delegation.md) . Delegování můžete povolit při vytváření virtuální sítě nebo jejich přidání do existující sítě.

    > [!IMPORTANT]
    > Při povolování delegování použijte `Microsoft.ContainerInstance/containerGroups` jako hodnotu __pro podsíť delegáta na službu__ .

2. Nasaďte model pomocí [AciWebservice. deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-), použijte `vnet_name` parametry a `subnet_name` . Nastavte tyto parametry na název virtuální sítě a podsíť, ve které jste povolili delegování.

## <a name="azure-firewall"></a>Brána Azure Firewall

Informace o použití Azure Machine Learning s Azure Firewall najdete v tématu [použití Azure Machine Learningho pracovního prostoru za Azure firewall](how-to-access-azureml-behind-firewall.md).

## <a name="azure-container-registry"></a>Azure Container Registry

> [!IMPORTANT]
> Azure Container Registry (ACR) lze umístit do virtuální sítě, ale musíte splňovat následující požadavky:
>
> * Váš pracovní prostor Azure Machine Learning musí být Enterprise Edition. Informace o upgradu nástroje najdete v tématu [upgrade na verzi Enterprise Edition](how-to-manage-workspace.md#upgrade).
> * Vaše Azure Container Registry musí být verze Premium. Další informace o upgradu najdete v tématu [Změna SKU](/azure/container-registry/container-registry-skus#changing-skus).
> * Vaše Azure Container Registry musí být ve stejné virtuální síti a podsíti jako účet úložiště a cíle výpočtů používané pro školení nebo odvození.
> * Váš pracovní prostor Azure Machine Learning musí obsahovat [Azure Machine Learning výpočetní cluster](how-to-set-up-training-targets.md#amlcompute).
>
>     Když je ACR za virtuální sítí, Azure Machine Learning ji nemůže použít k přímému vytváření imagí Docker. Místo toho se k sestavení imagí používá výpočetní cluster.

1. Chcete-li najít název Azure Container Registry pro váš pracovní prostor, použijte jednu z následujících metod:

    __portál Azure__

    V části Přehled pracovního prostoru se hodnota __registru__ odkazuje na Azure Container Registry.

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="Azure Container Registry pracovního prostoru" border="true":::

    __Azure CLI__

    Pokud jste [nainstalovali rozšíření Machine Learning pro rozhraní příkazového řádku Azure CLI](reference-azure-machine-learning-cli.md), můžete použít `az ml workspace show` příkaz k zobrazení informací o pracovním prostoru.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    Tento příkaz vrátí hodnotu podobnou `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"` . Poslední část řetězce je název Azure Container Registry pracovního prostoru.

1. Pokud chcete omezit přístup k virtuální síti, použijte postup v části [Konfigurace přístupu k síti pro registr](../container-registry/container-registry-vnet.md#configure-network-access-for-registry). Při přidávání virtuální sítě vyberte virtuální síť a podsíť pro prostředky Azure Machine Learning.

1. Pomocí sady Azure Machine Learning Python SDK můžete nakonfigurovat výpočetní cluster pro vytváření imagí Docker. Následující fragment kódu ukazuje, jak to provést:

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    ```

    > [!IMPORTANT]
    > Váš účet úložiště, výpočetní cluster a Azure Container Registry musí být všechny ve stejné podsíti virtuální sítě.
    
    Další informace najdete v referenčních informacích k metodě [Update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-) .

1. Je nutné použít následující šablonu Azure Resource Manager. Tato šablona umožňuje vašemu pracovnímu prostoru komunikovat s ACR.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "keyVaultArmId": {
        "type": "string"
        },
        "workspaceName": {
        "type": "string"
        },
        "containerRegistryArmId": {
        "type": "string"
        },
        "applicationInsightsArmId": {
        "type": "string"
        },
        "storageAccountArmId": {
        "type": "string"
        },
        "location": {
        "type": "string"
        }
    },
    "resources": [
        {
        "type": "Microsoft.MachineLearningServices/workspaces",
        "apiVersion": "2019-11-01",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "sku": {
            "tier": "enterprise",
            "name": "enterprise"
        },
        "properties": {
            "sharedPrivateLinkResources":
    [{"Name":"Acr","Properties":{"PrivateLinkResourceId":"[concat(parameters('containerRegistryArmId'), '/privateLinkResources/registry')]","GroupId":"registry","RequestMessage":"Approve","Status":"Pending"}}],
            "keyVault": "[parameters('keyVaultArmId')]",
            "containerRegistry": "[parameters('containerRegistryArmId')]",
            "applicationInsights": "[parameters('applicationInsightsArmId')]",
            "storageAccount": "[parameters('storageAccountArmId')]"
        }
        }
    ]
    }
    ```
    
## <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

Azure Data Lake Storage Gen 2 je sada funkcí pro analýzy velkých objemů dat, která je založená na službě Azure Blob Storage. Dá se použít k ukládání dat používaných ke vzdělávání modelů pomocí Azure Machine Learning. 

Pokud chcete použít Data Lake Storage Gen 2 uvnitř virtuální sítě pracovního prostoru Azure Machine Learning, použijte následující postup:

1. Vytvořte účet Azure Data Lake Storage Gen 2. Další informace najdete v tématu [Vytvoření účtu úložiště Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-quickstart-create-account.md).

1. Pomocí kroků 2-4 v předchozí části použijte [účet úložiště pro váš pracovní prostor](#use-a-storage-account-for-your-workspace)a vložte účet do virtuální sítě.

Pokud používáte Azure Machine Learning s Data Lake Storage Gen 2 v rámci virtuální sítě, postupujte podle následujících pokynů:

* Použijete-li sadu __SDK k vytvoření datové sady__a systém, který spouští kód, není __ve virtuální síti__, použijte `validate=False` parametr. Tento parametr přeskočí ověřování, které se nezdařilo, pokud systém není ve stejné virtuální síti jako účet úložiště. Další informace naleznete v tématu metoda [from_files ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) .

* Při použití Azure Machine Learning výpočetní instance nebo výpočetního clusteru ke školení modelu s datovou sadou se musí nacházet ve stejné virtuální síti jako účet úložiště.

## <a name="key-vault-instance"></a>Instance trezoru klíčů 

Instance trezoru klíčů, která je přidružená k pracovnímu prostoru, se používá Azure Machine Learning k uložení následujících přihlašovacích údajů:
* Přidružený připojovací řetězec účtu úložiště
* Hesla k instancím služby Azure Container úložiště
* Připojovací řetězce k úložištím dat

Pokud chcete používat Azure Machine Learning možnosti experimentování s Azure Key Vault za virtuální sítí, použijte následující postup:

1. Přejít do trezoru klíčů, který je přidružený k pracovnímu prostoru.

   [![Trezor klíčů, který je přidružený k pracovnímu prostoru Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-key-vault.png)](./media/how-to-enable-virtual-network/workspace-key-vault.png#lightbox)

1. Na stránce **Key Vault** v levém podokně vyberte možnost __brány firewall a virtuální sítě__.

   ![Část "brány firewall a virtuální sítě" v podokně Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)

1. Na stránce __brány firewall a virtuální sítě__ proveďte následující akce:
    - V části __Povolení přístupu z__vyberte __vybrané sítě__.
    - V části __virtuální sítě__vyberte __Přidat existující virtuální sítě__ a přidejte tak virtuální síť, ve které se nachází vaše výpočetní služby experimentování.
    - V části __umožňuje důvěryhodným službám Microsoftu obejít tuto bránu firewall__, vyberte __Ano__.

   [![Část "brány firewall a virtuální sítě" v podokně Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)


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

Pokud chcete použít virtuální počítač nebo cluster Azure HDInsight ve virtuální síti s vaším pracovním prostorem, použijte následující postup:

1. Vytvořte virtuální počítač nebo cluster HDInsight pomocí Azure Portal nebo Azure CLI a vložte cluster do virtuální sítě Azure. Další informace najdete v následujících článcích:
    * [Vytváření a správa virtuálních sítí Azure pro virtuální počítače se systémem Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Rozšiřování HDInsight pomocí virtuální sítě Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. Pokud chcete Azure Machine Learning komunikovat s portem SSH na virtuálním počítači nebo v clusteru, nakonfigurujte položku zdroje pro skupinu zabezpečení sítě. Port SSH je obvykle port 22. Pokud chcete povolit přenos z tohoto zdroje, proveďte následující akce:

    * V rozevíracím seznamu __zdroj__ vyberte možnost __značka služby__.

    * V rozevíracím seznamu __značka zdrojové služby__ vyberte možnost __AzureMachineLearning__.

    * V rozevíracím seznamu __rozsahy zdrojových portů__ vyberte __*__ .

    * V rozevíracím seznamu __cíl__ vyberte možnost __libovolný__.

    * V rozevíracím seznamu __rozsahy cílových portů__ vyberte __22__.

    * V části __protokol__vyberte __libovolný__.

    * V části __Akce__vyberte možnost __povoleno__.

   ![Příchozí pravidla pro experimentování na virtuálním počítači nebo clusteru HDInsight ve virtuální síti](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Ponechte výchozí odchozí pravidla pro skupinu zabezpečení sítě. Další informace najdete v tématu výchozí pravidla zabezpečení ve [skupinách zabezpečení](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

    Pokud nechcete používat výchozí odchozí pravidla a chcete omezit odchozí přístup k virtuální síti, přečtěte si část [omezení odchozího připojení z virtuální sítě](#limiting-outbound-from-vnet) .

1. Připojte virtuální počítač nebo cluster HDInsight k pracovnímu prostoru Azure Machine Learning. Další informace najdete v tématu [Nastavení výpočetních cílů pro školení modelů](how-to-set-up-training-targets.md).


## <a name="next-steps"></a>Další kroky

* [Nastavení prostředí trénování](how-to-set-up-training-targets.md)
* [Nastavení privátních koncových bodů](how-to-configure-private-link.md)
* [Kam se mají modely nasadit](how-to-deploy-and-where.md)
* [Použití protokolu TLS k zabezpečení webové služby prostřednictvím Azure Machine Learning](how-to-secure-web-service.md)
