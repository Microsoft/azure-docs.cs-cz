---
title: Spouštění experimentů a odvození ve virtuální síti
titleSuffix: Azure Machine Learning service
description: Spouštění experimentů Machine Learning a odvození zabezpečení v rámci služby Azure Virtual Network. Naučte se vytvářet výpočetní cíle pro školení modelů a postup při odvozování v rámci virtuální sítě. Seznamte se s požadavky na zabezpečené virtuální sítě, třeba na vyžádání příchozích a odchozích portů.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 08/05/2019
ms.openlocfilehash: 7c4c4ff611b35cac9aa8be1a9697a0d11bc4dc8b
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815963"
---
# <a name="securely-run-experiments-and-inference-inside-an-azure-virtual-network"></a>Zabezpečené spouštění experimentů a odvození v rámci virtuální sítě Azure

V tomto článku se dozvíte, jak spouštět experimenty a odvozování ve virtuální síti. Virtuální síť funguje jako hranice zabezpečení a izoluje prostředky Azure od veřejného Internetu. Virtuální síť Azure se taky můžete připojit k místní síti. Umožňuje bezpečně vyškolit modely a přistupovat k nasazeným modelům pro odvození. Odvození modelu nebo Bodové hodnocení je fáze, ve které se nasazený model používá pro předpověď, nejčastěji pro produkční data.

Služba Azure Machine Learning spoléhá na další služby Azure pro výpočetní prostředky. Výpočetní prostředky (cíle výpočtů) se používají ke školení a nasazení modelů. Tyto výpočetní cíle je možné vytvořit ve virtuální síti. Například můžete použít Microsoft Data Science Virtual Machine pro výuku modelu a pak model nasadit do služby Azure Kubernetes Service (AKS). Další informace o virtuálních sítích najdete v tématu [Přehled Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

Tento článek poskytuje podrobné informace o **pokročilém nastavení zabezpečení**a není nutný pro základní nebo experimentální případy použití. Oddíly v tomto článku obsahují informace o konfiguraci různých scénářů, ale nemusí být dokončeny v daném pořadí nebo v celém rozsahu.

## <a name="prerequisites"></a>Požadavky

Vytvořte [pracovní prostor](setup-create-workspace.md) služby Azure Machine Learning, pokud ho ještě nemáte. V tomto dokumentu se předpokládá, že máte zkušenosti s virtuálními sítěmi Azure a obecně se jedná o sítě IP. Tento dokument také předpokládá, že jste vytvořili virtuální síť a podsíť pro použití s výpočetními prostředky. Pokud nejste obeznámeni s Azure Virtual Networks, přečtěte si následující články, které vám pomohou o této službě:

* [Přidělování IP adres](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)
* [Skupiny zabezpečení](https://docs.microsoft.com/azure/virtual-network/security-overview)
* [Rychlé zprovoznění: Vytvoření virtuální sítě](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)
* [Filtrování provozu sítě](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

## <a name="storage-account-for-your-workspace"></a>Účet úložiště pro váš pracovní prostor

Pokud chcete použít výchozí účet Azure Storage pro pracovní prostor ve virtuální síti, použijte následující postup:

1. Vytvořte si výpočet experimentu ex. Výpočetní prostředky služby Machine Learning za virtuální sítí nebo připojte k pracovnímu prostoru výpočetní výkon v ex. Cluster HDInsight nebo virtuální počítač. Další informace najdete v tématu [použití výpočetní prostředky služby Machine Learning](#use-machine-learning-compute) a [použití oddílů virtuálního počítače nebo clusteru HDInsight](#use-a-virtual-machine-or-hdinsight-cluster) v tomto dokumentu.
2. Přejít do úložiště připojeného k pracovnímu prostoru. ![Obrázek Azure Portal se zobrazením Azure Storage připojeného k pracovnímu prostoru služby Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-storage.png)
3. Na stránce Azure Storage vyberte možnost __brány firewall a virtuální sítě__. ![Obrázek Azure Portal, na kterém se zobrazuje oddíl brány firewall a virtuální sítě na stránce Azure Storage](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)
4. Na stránce __brány firewall a virtuální sítě__ vyberte následující položky:
    - Vyberte __Vybrané sítě__.
    - V části __virtuální sítě__vyberte __Přidat existující virtuální síť__ a přidejte tak virtuální síť, ve které se nachází vaše výpočetní experimenty. (Viz krok 1.)
    - Vyberte možnost __dovolit přístup k tomuto účtu úložiště důvěryhodným službám Microsoftu__.
![Obrázek Azure Portal zobrazení stránky brány firewall a virtuální sítě v části Azure Storage](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)

5. Při spouštění experimentu v kódu experimentování změňte spuštění konfigurace tak, aby používalo úložiště objektů BLOB:
    ```python
    run_config.source_directory_data_store = "workspaceblobstore"
    ```

> [!IMPORTANT]
> __Výchozí účet úložiště__ pro vaši službu Azure Machine Learning můžete umístit do virtuální sítě __jenom při experimentování__.
>
> Pro __jiné než výchozí účty úložiště pro experimentování__nebo pokud pro odvození používáte účet úložiště, musíte mít __neomezený přístup k účtu úložiště__.
>
> Pokud si nejste jistí, jestli jste tato nastavení změnili, nebo ne, přečtěte si téma __Změna výchozího pravidla přístupu k síti__ v tématu [konfigurace Azure Storagech bran firewall a virtuálních sítí](https://docs.microsoft.com/azure/storage/common/storage-network-security). Použijte postup povolení přístupu ze všech sítí během odvození nebo vyhodnocování modelu.

## <a name="key-vault-for-your-workspace"></a>Trezor klíčů pro váš pracovní prostor

Instance Key Vault přidružená k pracovnímu prostoru je používána službou Azure Machine Learning k ukládání přihlašovacích údajů různých druhů:
* Přidružený připojovací řetězec účtu úložiště
* Hesla k instancím služby Azure Container úložiště
* Připojovací řetězce k úložištím dat

Pokud chcete používat Azure Machine Learning možnosti experimentování s Key Vault za virtuální sítí, použijte následující postup:
1. Přejít na Key Vault přidružené k pracovnímu prostoru. ![Obrázek Azure Portal se zobrazením Key Vault přidruženého k pracovnímu prostoru služby Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-key-vault.png)
2. Na stránce Key Vault vyberte možnost __brány firewall a virtuální sítě__ . ![Obrázek Azure Portal, na kterém se zobrazuje oddíl brány firewall a virtuální sítě na stránce Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)
3. Na stránce __brány firewall a virtuální sítě__ vyberte následující položky:
    - Vyberte __Vybrané sítě__.
    - V části __virtuální sítě__vyberte __Přidat existující virtuální sítě__ a přidejte tak virtuální síť, ve které se nachází vaše výpočetní experimenty.
    - Pokud __Chcete tuto bránu firewall obejít, vyberte možnost dovolit důvěryhodným službám Microsoftu__.
![Obrázek Azure Portal zobrazení stránky brány firewall a virtuální sítě v části Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)


## <a name="use-machine-learning-compute"></a>Použít Výpočetní prostředky služby Machine Learning

Pokud chcete ve virtuální síti použít výpočetní prostředky Azure Machine Learning, vezměte v úvahu následující informace o požadavcích sítě:

- Virtuální síť musí být ve stejném předplatném a oblasti jako pracovní prostor služby Azure Machine Learning.

- Podsíť zadaná pro výpočetní cluster musí mít k dispozici dostatek nepřiřazených IP adres pro přizpůsobení počtu virtuálních počítačů, které jsou pro cluster cílené. Pokud podsíť nemá dostatek nepřiřazených IP adres, cluster se částečně přidělí.

- Pokud máte v úmyslu zabezpečit virtuální síť omezením provozu, nechte některé porty pro výpočetní službu otevřené. Další informace najdete v tématu [požadované porty](#mlcports).

- Ověřte, jestli zásady zabezpečení nebo zámky v předplatném virtuální sítě nebo skupině prostředků omezují oprávnění ke správě virtuální sítě.

- Pokud budete chtít do jedné virtuální sítě umístit několik výpočetních clusterů, možná budete muset požádat o zvýšení kvóty pro jeden nebo více vašich prostředků.

    Azure Machine Learning COMPUTE automaticky přiděluje další síťové prostředky ve skupině prostředků, která obsahuje virtuální síť. Pro každý výpočetní cluster přiděluje služba následující prostředky:

    - Jedna skupina zabezpečení sítě

    - Jedna veřejná IP adresa

    - Jeden nástroj pro vyrovnávání zatížení

  Pro tyto prostředky platí omezení [kvót prostředků](https://docs.microsoft.com/azure/azure-subscription-service-limits) předplatného.

### <a id="mlcports"></a>Požadované porty

Výpočetní prostředky služby Machine Learning aktuálně používá službu Azure Batch k zřizování virtuálních počítačů v zadané virtuální síti. Podsíť musí umožňovat příchozí komunikaci ze služby Batch. Tato komunikace se používá k naplánování spuštění na Výpočetní prostředky služby Machine Learningch uzlech a ke komunikaci s Azure Storage a dalšími prostředky. Batch přidá skupiny zabezpečení sítě (**NSG**) na úrovni síťových rozhraní (**nic**) připojených k virtuálním počítačům. Tyto skupiny zabezpečení sítě automaticky konfigurují pravidla příchozích a odchozích přenosů, která povolují následující provoz:

- Příchozí provoz TCP na portech 29876 a 29877 ze __značky služby__ __BatchNodeManagement__.

    ![Obrázek Azure Portal zobrazující příchozí pravidlo pomocí značky služby BatchNodeManagement](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- volitelné Příchozí provoz TCP na portu 22 pro povolení vzdáleného přístupu. Tento port je potřeba jenom v případě, že se chcete připojit pomocí SSH na veřejné IP adrese.

- Odchozí provoz do virtuální sítě na jakémkoli portu.

- Odchozí provoz do internetu na jakémkoli portu.

Pokud upravíte nebo přidáte pravidla příchozí/odchozí komunikace v skupin zabezpečení sítě s nakonfigurovaným dávkou, vyzkoušejte opatrnost. Pokud NSG blokuje komunikaci s výpočetními uzly, služba COMPUTE služby nastaví stav výpočetních uzlů na nepoužitelné.

Nemusíte zadávat skupin zabezpečení sítě na úrovni podsítě, protože služba Azure Batch konfiguruje vlastní skupin zabezpečení sítě. Pokud však Zadaná podsíť má přidruženou skupin zabezpečení sítě a/nebo bránu firewall, nakonfigurujte příchozí a odchozí pravidla zabezpečení, jak je uvedeno výše.

Následující snímek obrazovky ukazuje, jak konfigurace pravidla NSG vypadá v Azure Portal:

![Snímek obrazovky příchozích pravidel NSG pro Výpočetní prostředky služby Machine Learning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)

![Snímek obrazovky s odchozími NSG pravidly pro Výpočetní prostředky služby Machine Learning](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a id="limiting-outbound-from-vnet"></a>Omezení odchozího připojení z virtuální sítě

Pokud nechcete používat výchozí odchozí pravidla a chcete omezit odchozí přístup k virtuální síti, použijte následující postup:

- Odepřít odchozí připojení k Internetu pomocí pravidel NSG

- Omezte odchozí provoz na Azure Storage (pomocí __označení služby__ __Storage. Region_Name__ ex. Storage. EastUS) Azure Container Registry (použití __značky služby__ __AzureContainerRegistry. Region_Name__ ex. AzureContainerRegistry. EastUS) a služba Azure Machine Learning (s použitím __značky služby__ __AzureMachineLearning__)

Následující snímek obrazovky ukazuje, jak konfigurace pravidla NSG vypadá v Azure Portal:

![Snímek obrazovky s odchozími NSG pravidly pro Výpočetní prostředky služby Machine Learning](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)

### <a name="user-defined-routes-for-forced-tunneling"></a>Uživatelem definované trasy pro vynucené tunelování

Pokud používáte vynucené tunelování s Azure Machine Learning COMPUTE, musíte přidat [trasy definované uživatelem (udr)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) do podsítě, která obsahuje výpočetní prostředky.

* Pro každou IP adresu, kterou používá služba Azure Batch, se musí vytvořit trasa definovaná uživatelem v oblasti, kde existují vaše prostředky. Tyto udr umožňují, aby služba Batch komunikovala s výpočetními uzly pro plánování úloh. Pokud chcete získat seznam IP adres služby Batch, obraťte se na podporu Azure.

* Odchozí provoz do Azure Storage (konkrétně adresy URL formuláře `<account>.table.core.windows.net`, `<account>.queue.core.windows.net`a `<account>.blob.core.windows.net`) nesmí být zablokován vaším místním síťovým zařízením.

Když přidáte trasy definované uživatelem, definujte trasu pro každou související předponu IP adresy dávky a nastavte __typ dalšího segmentu směrování__ na hodnotu __Internet__. Následující obrázek ukazuje příklad tohoto UDR v Azure Portal:

![Příklad uživatelsky definované trasy pro předponu adresy](./media/how-to-enable-virtual-network/user-defined-route.png)

Další informace najdete v článku [Vytvoření fondu Azure Batch ve virtuální síti](../../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling) .

### <a name="create-machine-learning-compute-in-a-virtual-network"></a>Vytvoření Výpočetní prostředky služby Machine Learning ve virtuální síti

Pokud chcete vytvořit Azure Machine Learning výpočetní cluster pomocí Azure Portal, použijte následující postup:

1. V [Azure Portal](https://portal.azure.com)vyberte pracovní prostor služby Azure Machine Learning.

1. V části __aplikace__ vyberte __COMPUTE__. Pak vyberte __Přidat výpočetní__prostředky.

1. Pokud chcete tento výpočetní prostředek nakonfigurovat tak, aby používal virtuální síť, použijte tyto možnosti:

    - __Konfigurace sítě__: Vyberte __Upřesnit__.

    - __Skupina prostředků__: Vyberte skupinu prostředků, která obsahuje virtuální síť.

    - __Virtuální síť__: Vyberte virtuální síť, která obsahuje podsíť.

    - __Podsíť__: Vyberte podsíť, která se má použít.

   ![Snímek obrazovky, který ukazuje nastavení virtuální sítě pro výpočetní prostředky machine learningu](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

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

    # Wait for the cluster to complete, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

Po dokončení procesu vytváření můžete model pomocí clusteru v experimentu proškolit. Další informace najdete v tématu [Výběr a použití výpočetní cíle pro školení](how-to-set-up-training-targets.md).

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>Použití virtuálního počítače nebo clusteru HDInsight

Pokud chcete použít virtuální počítač nebo cluster Azure HDInsight ve virtuální síti s vaším pracovním prostorem, použijte následující postup:

1. Vytvořte virtuální počítač nebo cluster HDInsight pomocí Azure Portal nebo Azure CLI a vložte ho do služby Azure Virtual Network. Další informace najdete v následujících dokumentech:
    * [Vytváření a správa virtuálních sítí Azure pro virtuální počítače se systémem Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Rozšiřování HDInsight pomocí virtuální sítě Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. Pokud chcete službě Azure Machine Learning umožňovat komunikaci s portem SSH na virtuálním počítači nebo clusteru, musíte nakonfigurovat položku zdroje pro skupinu zabezpečení sítě. Port SSH je obvykle port 22. K povolení provozu z tohoto zdroje použijte následující postup:

    * __Zdroj__: Vyberte __značku služby__.

    * __Značka zdrojové služby__: Vyberte __AzureMachineLearning__.

    * __Rozsahy zdrojových portů__: Vyberte __*__ .

    * __Cíl__: Vyberte __libovolný__.

    * __Rozsahy cílových portů__: Vyberte __22__.

    * __Protokol__: Vyberte __libovolný__.

    * __Akce__: Vyberte možnost __udělit__.

   ![Snímek obrazovky příchozích pravidel pro účely experimentování na virtuálním počítači nebo clusteru HDInsight ve virtuální síti](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Ponechte výchozí odchozí pravidla pro skupinu zabezpečení sítě. Další informace najdete v tématu výchozí pravidla zabezpečení ve [skupinách zabezpečení](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

    Pokud nechcete používat výchozí odchozí pravidla a chcete omezit odchozí přístup k virtuální síti, přečtěte si téma [omezení odchozího připojení z virtuální sítě](#limiting-outbound-from-vnet) .

1. Připojte virtuální počítač nebo cluster HDInsight k vašemu pracovnímu prostoru služby Azure Machine Learning. Další informace najdete v tématu [Nastavení výpočetních cílů pro školení modelů](how-to-set-up-training-targets.md).

> [!IMPORTANT]
> Služba Azure Machine Learning podporuje pouze virtuální počítače se systémem Ubuntu.

## <a name="use-azure-kubernetes-service"></a>Použití služby Azure Kubernetes

Pokud chcete přidat službu Azure Kubernetes ve virtuální síti do svého pracovního prostoru, postupujte podle následujících kroků v Azure Portal:

1. Ujistěte se, že skupina zabezpečení sítě (NSG), která řídí virtuální síť, má povolené příchozí pravidlo pro Azure Machine Learning službu pomocí __AzureMachineLearning__ jako **zdroje**.

    ![Postup přidání výpočetní služby ve službě Azure Machine Learning](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png)

1. V [Azure Portal](https://portal.azure.com)vyberte pracovní prostor služby Azure Machine Learning.

1. V části __aplikace__ vyberte __COMPUTE__. Pak vyberte __Přidat výpočetní__prostředky.

1. Pokud chcete tento výpočetní prostředek nakonfigurovat tak, aby používal virtuální síť, použijte tyto možnosti:

    - __Konfigurace sítě__: Vyberte __Upřesnit__.

    - __Skupina prostředků__: Vyberte skupinu prostředků, která obsahuje virtuální síť.

    - __Virtuální síť__: Vyberte virtuální síť, která obsahuje podsíť.

    - __Podsíť__: Vyberte podsíť.

    - __Rozsah adres služby Kubernetes__: Vyberte rozsah adres služby Kubernetes. Tento rozsah adres používá rozsah IP adres zápisu CIDR k definování IP adres, které jsou k dispozici pro cluster. Nesmí se překrývat s žádnými rozsahy IP adres podsítě. Příklad: 10.0.0.0/16.

    - __IP adresa služby KUBERNETES DNS__: Vyberte IP adresu služby Kubernetes DNS. Tato IP adresa se přiřadí službě DNS Kubernetes. Musí se nacházet v rozsahu adres služby Kubernetes. Příklad: 10.0.0.10.

    - __Adresa mostu Docker__: Vyberte adresu mostu Docker. Tato IP adresa je přiřazená k mostu Docker. Nesmí se jednat o žádné rozsahy IP adres podsítě ani rozsah adres služby Kubernetes. Příklad: 172.17.0.1/16.

   ![Služba Azure Machine Learning: Nastavení Výpočetní prostředky služby Machine Learning virtuální sítě](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Ujistěte se, že skupina NSG, která řídí virtuální síť, má povolené příchozí pravidlo zabezpečení pro koncový bod bodování, aby ho bylo možné volat mimo virtuální síť.

    ![Postup přidání výpočetní služby ve službě Azure Machine Learning](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)

    > [!TIP]
    > Pokud už máte cluster AKS ve virtuální síti, můžete ho připojit k pracovnímu prostoru. Další informace najdete v tématu [Jak nasadit do AKS](how-to-deploy-to-aks.md).

> [!IMPORTANT]
> Než budete pokračovat s výše uvedeným postupem, ověřte požadavky a naplánujte IP adresování pro svůj cluster. Další informace najdete v tématu [Konfigurace pokročilé sítě ve službě Azure Kubernetes](https://docs.microsoft.com/azure/aks/configure-advanced-networking).
>
>
> Ponechte výchozí odchozí pravidla pro NSG. Další informace najdete v tématu výchozí pravidla zabezpečení ve [skupinách zabezpečení](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).
>
> Služba Azure Kubernetes a virtuální síť Azure by měly být ve stejné oblasti.

Pomocí **sady Azure Machine Learning SDK** můžete také přidat službu Azure Kubernetes ve virtuální síti. Následující kód vytvoří novou instanci služby Azure Kubernetes v `default` podsíti virtuální sítě s názvem: `mynetwork`

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

Po dokončení procesu vytváření můžete pro cluster AKS za virtuální sítí vytvořit odvození/skóre. Další informace najdete v tématu [Jak nasadit do AKS](how-to-deploy-to-aks.md).

## <a name="next-steps"></a>Další postup

* [Nastavení školicích prostředí](how-to-set-up-training-targets.md)
* [Kam chcete nasazovat modely](how-to-deploy-and-where.md)
* [Bezpečné nasazení modelů pomocí protokolu SSL](how-to-secure-web-service.md)

