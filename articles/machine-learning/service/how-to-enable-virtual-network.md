---
title: Zabezpečené experimenty a odvození ve virtuální síti
titleSuffix: Azure Machine Learning service
description: Naučte se, jak zabezpečit experimenty/školicí úlohy a úlohy odvození a bodování v Azure Machine Learning v rámci Azure Virtual Network.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 08/05/2019
ms.openlocfilehash: 1b5e3777109b13baa7d774a524664551798ba4ca
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69558202"
---
# <a name="secure-azure-ml-experimentation-and-inference-jobs-within-an-azure-virtual-network"></a>Zabezpečení experimentů s Azure ML a odvození úloh v rámci Azure Virtual Network

V tomto článku se dozvíte, jak zabezpečit úlohy experimentování/školení a úlohy odvození a bodování v Azure Machine Learning v rámci Azure Virtual Network (VNET). 

**Virtuální síť** funguje jako hranice zabezpečení a izoluje prostředky Azure od veřejného Internetu. Virtuální síť Azure se taky můžete připojit k místní síti. Připojením sítí můžete bezpečně prosazovat modely a přistupovat k nasazeným modelům pro odvození.

Služba Azure Machine Learning spoléhá na další služby Azure pro výpočetní prostředky. Výpočetní prostředky nebo [výpočetní cíle](concept-compute-target.md)se používají ke školení a nasazení modelů. Cíle lze vytvořit v rámci virtuální sítě. Můžete například použít Microsoft Data Science Virtual Machine k učení modelu a nasazení modelu do služby Azure Kubernetes Service (AKS). Další informace o virtuálních sítích najdete v tématu [Přehled služby Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

Tento článek také poskytuje podrobné informace o *pokročilých nastaveních zabezpečení*, informace, které nejsou nutné pro základní nebo experimentální případy použití. Některé části tohoto článku obsahují informace o konfiguraci různých scénářů. Nemusíte doplňovat pokyny v daném pořadí nebo v celém rozsahu.

## <a name="prerequisites"></a>Požadavky

+ [Pracovní prostor](how-to-manage-workspace.md)služby Azure Machine Learning. 

+ Obecné praktické znalosti [služby Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) a [sítě IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

+ Již existující virtuální síť a podsíť, která se má použít s výpočetními prostředky. 

## <a name="use-a-storage-account-for-your-workspace"></a>Použití účtu úložiště pro váš pracovní prostor

Pokud chcete použít účet úložiště Azure pro pracovní prostor ve virtuální síti, udělejte toto:

1. Vytvořte instanci COMPUTE experimentování (například instanci Výpočetní prostředky služby Machine Learning) za virtuální sítí nebo připojte výpočetní instanci experimentování k pracovnímu prostoru (například cluster HDInsight nebo virtuální počítač). 

   Další informace najdete v části "použití instance Výpočetní prostředky služby Machine Learning a" použití virtuálního počítače nebo clusteru HDInsight "v tomto článku.

1. V Azure Portal přejdete do úložiště, které je připojené k vašemu pracovnímu prostoru. 

   ![Úložiště, které je připojené k pracovnímu prostoru služby Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-storage.png)

1. Na stránce **Azure Storage** vyberte možnost __brány firewall a virtuální sítě__. 

   ![Oblast brány firewall a virtuální sítě na stránce Azure Storage v Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. Na stránce __brány firewall a virtuální sítě__ proveďte tyto kroky:
    - Vyberte __Vybrané sítě__.
    - V části __virtuální sítě__vyberte odkaz __Přidat existující virtuální síť__ . Tato akce přidá virtuální síť, ve které se nachází vaše výpočetní instance experimentování (viz krok 1).
    - Zaškrtněte políčko __pro přístup k tomuto účtu úložiště udělit důvěryhodné služby Microsoftu__ .

   ![Podokno brány firewall a virtuální sítě v Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)

1. Po spuštění experimentu v kódu experimentování změňte spuštění konfigurace tak, aby používalo úložiště objektů BLOB v Azure:

    ```python
    run_config.source_directory_data_store = "workspaceblobstore"
    ```

> [!IMPORTANT]
> _Výchozí účet úložiště_ pro službu Azure Machine Learning ve virtuální síti můžete umístit _jenom na experimentování_.
>
> _Nevýchozí účty úložiště_ ve virtuální síti můžete umístit _jenom_do experimentů.
>
> Výchozí i jiné účty úložiště, které se používají pro odvození, musí mít neomezený _přístup k účtu úložiště_.
>
> Pokud si nejste jistí, jestli jste nastavení změnili, přečtěte si část Změna výchozího pravidla přístupu k síti v tématu [konfigurace Azure Storagech bran firewall a virtuálních sítí](https://docs.microsoft.com/azure/storage/common/storage-network-security). Postupujte podle pokynů a povolte přístup ze všech sítí během odvození nebo model bodování.

## <a name="use-a-key-vault-instance-with-your-workspace"></a>Použití instance trezoru klíčů s vaším pracovním prostorem

Instance trezoru klíčů, která je přidružená k pracovnímu prostoru, je používána službou Azure Machine Learning k uložení následujících přihlašovacích údajů:
* Přidružený připojovací řetězec účtu úložiště
* Hesla k instancím služby Azure Container úložiště
* Připojovací řetězce k úložištím dat

Pokud chcete používat Azure Machine Learning možnosti experimentování s Azure Key Vault za virtuální sítí, udělejte toto:
1. Přejít do trezoru klíčů, který je přidružený k pracovnímu prostoru. 

   ![Trezor klíčů, který je přidružený k pracovnímu prostoru služby Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-key-vault.png)

1. Na stránce **Key Vault** v levém podokně vyberte možnost __brány firewall a virtuální sítě__. 

   ![Část "brány firewall a virtuální sítě" v podokně Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)

1. Na stránce __brány firewall a virtuální sítě__ proveďte tyto kroky:
    - V části __Povolení přístupu z__vyberte __vybrané sítě__.
    - V části __virtuální sítě__vyberte __Přidat existující virtuální sítě__ a přidejte tak virtuální síť, ve které se nachází vaše výpočetní instance experimentování.
    - V části __umožňuje důvěryhodným službám Microsoftu obejít tuto bránu firewall__, vyberte __Ano__.

   ![Část "brány firewall a virtuální sítě" v podokně Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)

## <a name="use-a-machine-learning-compute-instance"></a>Použití instance Výpočetní prostředky služby Machine Learning

Pokud chcete ve virtuální síti použít výpočetní instanci Azure Machine Learning, vezměte v úvahu následující požadavky na síť:

- Virtuální síť musí být ve stejném předplatném a oblasti jako pracovní prostor služby Azure Machine Learning.

- Podsíť určená pro výpočetní cluster musí mít k dispozici dostatek nepřiřazených IP adres pro přizpůsobení počtu virtuálních počítačů, které jsou pro cluster cílené. Pokud podsíť nemá dostatek nepřiřazených IP adres, cluster se částečně přidělí.

- Pokud máte v úmyslu zabezpečit virtuální síť omezením provozu, nechte některé porty pro výpočetní službu otevřené. Další informace najdete v části [požadované porty](#mlcports) .

- Zkontrolujte, jestli zásady zabezpečení nebo zámky v předplatném virtuální sítě nebo skupině prostředků omezují oprávnění ke správě virtuální sítě.

- Pokud hodláte do jedné virtuální sítě umístit víc výpočetních clusterů, možná budete muset požádat o zvýšení kvóty pro jeden nebo víc vašich prostředků.

    Instance Výpočetní prostředky služby Machine Learning automaticky přiděluje další síťové prostředky ve skupině prostředků, která obsahuje virtuální síť. Pro každý výpočetní cluster přiděluje služba následující prostředky:

    - Jedna skupina zabezpečení sítě

    - Jedna veřejná IP adresa

    - Jeden nástroj pro vyrovnávání zatížení

  Pro tyto prostředky platí omezení [kvót prostředků](https://docs.microsoft.com/azure/azure-subscription-service-limits) předplatného.

### <a id="mlcports"></a>Požadované porty

Výpočetní prostředky služby Machine Learning aktuálně používá službu Azure Batch k zřizování virtuálních počítačů v zadané virtuální síti. Podsíť musí umožňovat příchozí komunikaci ze služby Batch. Tato komunikace se používá k naplánování spuštění na Výpočetní prostředky služby Machine Learningch uzlech a ke komunikaci s Azure Storage a dalšími prostředky. Služba Batch přidá skupiny zabezpečení sítě (skupin zabezpečení sítě) na úrovni síťových rozhraní (nic) připojených k virtuálním počítačům. Tyto skupiny zabezpečení sítě automaticky konfigurují pravidla příchozích a odchozích přenosů, která povolují následující provoz:

- Příchozí provoz TCP na portech 29876 a 29877 ze __značky služby__ __BatchNodeManagement__.

    ![Příchozí pravidlo, které používá značku služby BatchNodeManagement](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- Volitelné Příchozí provoz TCP na portu 22 pro povolení vzdáleného přístupu. Tento port použijte pouze v případě, že se chcete připojit pomocí SSH na veřejné IP adrese.

- Odchozí provoz do virtuální sítě na jakémkoli portu.

- Odchozí provoz do internetu na jakémkoli portu.

Pokud potřebujete upravit nebo přidat pravidla příchozích nebo odchozích přenosů ve skupinách zabezpečení sítě nakonfigurovaných službou Batch, postupujte obezřetně. Pokud NSG blokuje komunikaci s výpočetními uzly, služba COMPUTE nastaví stav výpočetních uzlů na nepoužitelné.

Nemusíte zadávat skupin zabezpečení sítě na úrovni podsítě, protože služba Azure Batch konfiguruje vlastní skupin zabezpečení sítě. Pokud však Zadaná podsíť má přidruženou skupin zabezpečení sítě nebo bránu firewall, nakonfigurujte příchozí a odchozí pravidla zabezpečení, jak je uvedeno výše.

Konfigurace pravidla NSG se v Azure Portal zobrazuje na následujících obrázcích:

![Příchozí NSG pravidla pro Výpočetní prostředky služby Machine Learning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)

![Odchozí NSG pravidla pro Výpočetní prostředky služby Machine Learning](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a id="limiting-outbound-from-vnet"></a>Omezení odchozího připojení z virtuální sítě

Pokud nechcete používat výchozí odchozí pravidla a chcete omezit odchozí přístup k virtuální síti, udělejte toto:

- Odmítne odchozí připojení k Internetu pomocí pravidel NSG.

- Omezte odchozí provoz na následující:
   - Azure Storage pomocí __označení služby__ __Storage. Region_Name__ (například Storage. EastUS)
   - Azure Container Registry pomocí __označení služby__ __AzureContainerRegistry. Region_Name__ (například AzureContainerRegistry. EastUS)
   - Služba Azure Machine Learning pomocí __označení služby__ __AzureMachineLearning__

Konfigurace pravidla NSG se v Azure Portal zobrazuje na následujícím obrázku:

![Odchozí NSG pravidla pro Výpočetní prostředky služby Machine Learning](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)

### <a name="user-defined-routes-for-forced-tunneling"></a>Uživatelem definované trasy pro vynucené tunelování

Pokud používáte vynucené tunelování pomocí Výpočetní prostředky služby Machine Learning, přidejte [uživatelsky definované trasy (udr)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) do podsítě, která obsahuje výpočetní prostředky.

* Vytvořte UDR pro každou IP adresu, kterou používá služba Azure Batch, v oblasti, kde existují vaše prostředky. Tyto udr umožňují, aby služba Batch komunikovala s výpočetními uzly pro plánování úloh. Pokud chcete získat seznam IP adres služby Batch, obraťte se na podporu Azure.

* Odchozí provoz do Azure Storage se nesmí blokovat v místním síťovém zařízení. Konkrétně adresy URL jsou ve formátu `<account>.table.core.windows.net`, `<account>.queue.core.windows.net`a `<account>.blob.core.windows.net`.

Když přidáte udr, definujte trasu pro každou související předponu IP adresy dávky a nastavte __typ dalšího segmentu směrování__ na __Internet__. Následující obrázek ukazuje příklad tohoto UDR v Azure Portal:

![Příklad UDR pro předponu adresy](./media/how-to-enable-virtual-network/user-defined-route.png)

Další informace najdete v tématu [Vytvoření fondu Azure Batch ve virtuální síti](../../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling).

### <a name="create-a-machine-learning-compute-cluster-in-a-virtual-network"></a>Vytvoření clusteru Výpočetní prostředky služby Machine Learning ve virtuální síti

Pokud chcete vytvořit cluster Výpočetní prostředky služby Machine Learning, udělejte toto:

1. V [Azure Portal](https://portal.azure.com)vyberte pracovní prostor služby Azure Machine Learning.

1. V části __aplikace__ vyberte __COMPUTE__a pak vyberte __Přidat výpočetní__prostředky.

1. Pokud chcete tento výpočetní prostředek nakonfigurovat tak, aby používal virtuální síť, postupujte takto:

    a. V případě __Konfigurace sítě__vyberte __Upřesnit__.

    b. V rozevíracím seznamu __Skupina prostředků__ vyberte skupinu prostředků, která obsahuje virtuální síť.

    c. V rozevíracím seznamu __virtuální síť__ vyberte virtuální síť, která obsahuje podsíť.

    d. V rozevíracím seznamu __podsíť__ vyberte podsíť, která se má použít.

   ![Nastavení virtuální sítě pro Výpočetní prostředky služby Machine Learning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

Cluster Výpočetní prostředky služby Machine Learning můžete vytvořit také pomocí sady Azure Machine Learning SDK. Následující kód vytvoří nový cluster výpočetní prostředky služby Machine Learning v `default` podsíti virtuální sítě s názvem: `mynetwork`

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

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>Použití virtuálního počítače nebo clusteru HDInsight

> [!IMPORTANT]
> Služba Azure Machine Learning podporuje pouze virtuální počítače se systémem Ubuntu.

Pokud chcete použít virtuální počítač nebo cluster Azure HDInsight ve virtuální síti s vaším pracovním prostorem, udělejte toto:

1. Vytvořte virtuální počítač nebo cluster HDInsight pomocí Azure Portal nebo Azure CLI a vložte cluster do virtuální sítě Azure. Další informace najdete v následujících článcích:
    * [Vytváření a správa virtuálních sítí Azure pro virtuální počítače se systémem Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Rozšiřování HDInsight pomocí virtuální sítě Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. Pokud chcete službě Azure Machine Learning umožňovat komunikaci s portem SSH na virtuálním počítači nebo clusteru, nakonfigurujte položku zdroje pro skupinu zabezpečení sítě. Port SSH je obvykle port 22. Pokud chcete povolit přenos z tohoto zdroje, postupujte takto:

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

1. Připojte virtuální počítač nebo cluster HDInsight k vašemu pracovnímu prostoru služby Azure Machine Learning. Další informace najdete v tématu [Nastavení výpočetních cílů pro školení modelů](how-to-set-up-training-targets.md).

## <a name="use-azure-kubernetes-service-aks"></a>Použití služby Azure Kubernetes Service (AKS)

Pokud chcete do svého pracovního prostoru přidat AKS ve virtuální síti, udělejte toto:

> [!IMPORTANT]
> Než začnete s tímto postupem, Projděte si požadavky a naplánujte IP adresování pro váš cluster. Další informace najdete v tématu [Konfigurace pokročilé sítě ve službě Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/configure-advanced-networking).
>
> Instance AKS a virtuální síť Azure musí být ve stejné oblasti.

1. V [Azure Portal](https://portal.azure.com)se ujistěte, že NSG, který řídí virtuální síť, má příchozí pravidlo, které je povolené pro službu Azure Machine Learning pomocí __AzureMachineLearning__ jako **zdroje**.

    ![Služba Azure Machine Learning – přidat výpočetní podokno](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png)

1. Vyberte pracovní prostor služby Azure Machine Learning.

1. V části __aplikace__ vyberte __COMPUTE__a pak vyberte __Přidat výpočetní__prostředky.

1. Pokud chcete tento výpočetní prostředek nakonfigurovat tak, aby používal virtuální síť, postupujte takto:

    - V případě __Konfigurace sítě__vyberte __Upřesnit__.

    - V rozevíracím seznamu __Skupina prostředků__ vyberte skupinu prostředků, která obsahuje virtuální síť.

    - V rozevíracím seznamu __virtuální síť__ vyberte virtuální síť, která obsahuje podsíť.

    - V rozevíracím seznamu __podsíť__ vyberte podsíť.

    - Do pole __Rozsah adres služby Kubernetes__ zadejte rozsah adres služby Kubernetes. Tento rozsah adres používá pro definování IP adres, které jsou k dispozici pro cluster, rozsah IP adres zápisu CIDR (Classless Inter-Domain Routing). Nesmí se překrývat s žádnými rozsahy IP adres podsítě (například 10.0.0.0/16).

    - Do pole __IP adresa služby KUBERNETES DNS__ zadejte IP adresu služby DNS Kubernetes. Tato IP adresa se přiřadí službě DNS Kubernetes. Musí se nacházet v rozsahu adres služby Kubernetes (například 10.0.0.10).

    - Do pole __adresa mostu Docker__ zadejte adresu mostu Docker. Tato IP adresa je přiřazená k mostu Docker. Nesmí se jednat o žádné rozsahy IP adres podsítě ani rozsah adres služby Kubernetes (například 172.17.0.1/16).

   ![Služba Azure Machine Learning: Nastavení Výpočetní prostředky služby Machine Learning virtuální sítě](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Ujistěte se, že skupina NSG, která řídí virtuální síť, má pro koncový bod bodování povolené příchozí pravidlo zabezpečení, aby se mohlo volat mimo virtuální síť.
   > [!IMPORTANT]
   > Ponechte výchozí odchozí pravidla pro NSG. Další informace najdete v tématu výchozí pravidla zabezpečení ve [skupinách zabezpečení](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).
  
   ![Příchozí pravidlo zabezpečení](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)

Pomocí sady Azure Machine Learning SDK můžete také přidat službu Azure Kubernetes ve virtuální síti. Pokud už máte cluster AKS ve virtuální síti, připojte ho k pracovnímu prostoru, jak je popsáno v tématu [Jak nasadit do AKS](how-to-deploy-to-aks.md). Následující kód vytvoří novou instanci AKS v `default` podsíti virtuální sítě s názvem: `mynetwork`

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

Po dokončení procesu vytváření můžete spustit odvození nebo model bodování v clusteru AKS za virtuální sítí. Další informace najdete v tématu [Jak nasadit do AKS](how-to-deploy-to-aks.md).

## <a name="next-steps"></a>Další postup

* [Nastavení školicích prostředí](how-to-set-up-training-targets.md)
* [Kam chcete nasazovat modely](how-to-deploy-and-where.md)
* [Bezpečné nasazení modelů pomocí protokolu SSL](how-to-secure-web-service.md)

