---
title: Spouštění experimentů & odvození ve virtuální síti
titleSuffix: Azure Machine Learning service
description: Spouštět experimenty strojového učení a odvozování zabezpečení ve službě Azure Virtual Network. Zjistěte, jak vytvořit cílových výpočetních prostředí pro trénování modelu a jak odvození v rámci virtuální sítě Azure. Popisuje také požadavky pro zabezpečené virtuální sítě, jako například požadovat příchozí a odchozí porty.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 01/08/2019
ms.openlocfilehash: 21c4a9042a64a58f67f1f94c300b6438895eea5d
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856277"
---
# <a name="securely-run-experiments-and-inferencing-inside-an-azure-virtual-network"></a>Bezpečné spuštění experimentů a odvozování uvnitř virtuální sítě Azure

V tomto článku se dozvíte, jak pro spouštění experimentů a odvozování ve virtuální síti. Virtuální sítě slouží jako hranice zabezpečení, izolace vašich prostředků Azure z veřejného Internetu. Azure Virtual Network můžete připojit také k vaší místní síti. Umožňuje bezpečně trénování modelů a přístup k nasazených modelů pro odvozování.

Služby Azure Machine Learning spoléhá na ostatní služby Azure pro výpočetní prostředky věci. Výpočetní prostředky (cílových výpočetních prostředí) se používají k trénování a nasazování modelů. Tyto výpočetní cíle lze vytvořit ve virtuální síti. Například můžete použít virtuální počítač pro datové vědy pro trénování modelu a poté model nasaďte do služby Azure Kubernetes Service. Další informace o virtuálních sítích najdete v tématu [Přehled virtuálních sítí](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) dokumentu.

## <a name="storage-account-for-your-workspace"></a>Účet úložiště pro váš pracovní prostor

Když vytvoříte pracovní prostor služby Azure Machine Learning, vyžaduje účet služby Azure Storage. Nezapínejte pravidla brány firewall pro tento účet úložiště. Služba Azure Machine Learning vyžaduje, aby neomezený přístup k účtu úložiště.

Pokud si nejste jisti, zda jste upravili tato nastavení nebo Ne, najdete __změnit výchozí pravidlo přístupu síť__ část [virtuální sítí a bran firewall nakonfigurovat služby Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security) Dokumentovat a použít postup _povolit přístup_ z __všechny sítě__.

## <a name="use-machine-learning-compute"></a>Použití aplikace Machine Learning Compute

Použití Machine Learning Compute ve virtuální síti, následující informace slouží k pochopení požadavků na síť:

- Virtuální síť musí být ve stejném předplatném a oblasti, pracovní prostor služby Azure Machine Learning.

- Podsíť zadaná pro Machine Learning Compute cluster musí obsahovat dostatek nepřiřazených IP adres pro plnění počet virtuálních počítačů určené pro cluster. Pokud podsíť nemá dostatek nepřiřazených IP adres, cluster se částečně přidělí.

- Pokud máte v plánu pro zabezpečení virtuální sítě tak, že omezíte přenos, je třeba ponechat některé porty otevřené pro službu Machine Learning Compute. Další informace najdete v tématu [požadované porty](#mlcports) oddílu.

- Zkontrolujte, jestli zásady zabezpečení a uzamčení na předplatné nebo skupinu prostředků ve virtuální síti omezit oprávnění ke správě služby virtuální sítě.

- Pokud chcete umístit více clusterů Machine Learning Compute v jedné virtuální síti, budete muset požádat o zvýšení kvóty pro jeden nebo více prostředků.

    Machine Learning Compute automaticky přiděluje další síťové prostředky ve skupině prostředků, která obsahuje virtuální síť. Pro každý cluster Machine Learning Compute přiděluje služba Azure Machine Learning na následujících odkazech: 

    - Skupina zabezpečení jedné sítě (NSG)

    - Jedna veřejná IP adresa

    - Jedna služba Vyrovnávání zatížení

    Pro tyto prostředky platí omezení [kvót prostředků](https://docs.microsoft.com/azure/azure-subscription-service-limits) předplatného. 

### <a id="mlcports"></a> Požadované porty

Machine Learning Compute v současnosti využívá službu Azure Batch ke zřízení virtuálních počítačů v zadané virtuální sítě. Podsíť musí umožňovat příchozí komunikaci ze služby Batch. Tato komunikace se používá k naplánování spuštění na uzlech Machine Learning Compute a komunikovat s Azure Storage a dalším prostředkům. Batch přidá skupiny Nsg na úrovni síťových rozhraní (NIC) připojených k virtuálním počítačům. Tyto skupiny zabezpečení sítě automaticky konfigurují pravidla příchozích a odchozích přenosů, která povolují následující provoz:

- Příchozí provoz přes protokol TCP na portech 29876 a 29877 z IP adres role služby Batch. 
 
- Příchozí přenosy TCP na portu 22 pro povolení vzdáleného přístupu.
 
- Odchozí provoz do virtuální sítě na jakémkoli portu.

- Odchozí provoz do internetu na jakémkoli portu.

Je-li upravit nebo přidat příchozí/odchozí pravidla skupin zabezpečení sítě nakonfigurované služby Batch, je třeba postupovat opatrně. Pokud bloky NSG komunikace s výpočetními uzly služby Machine Learning Compute nastaví stav výpočetních uzlů na nepoužitelné.

Nemusíte zadávat skupiny zabezpečení sítě na úrovni podsítě, protože Batch konfiguruje vlastní skupiny zabezpečení sítě. Ale pokud zadanou podsíť má přidruženou skupiny Nsg nebo brány firewall, nakonfigurujte pravidla zabezpečení příchozích a odchozích, jak bylo zmíněno dříve. Na následujících snímcích obrazovky ukazují, jak vypadá konfigurace pravidla na webu Azure Portal:

![Snímek obrazovky příchozí pravidla NSG pro Machine Learning Compute](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)

![Snímek obrazovky odchozí pravidla NSG pro Machine Learning Compute](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="create-machine-learning-compute-in-a-virtual-network"></a>Vytvořit Machine Learning Compute ve virtuální síti

K vytvoření clusteru s Machine Learning Compute **webu Azure portal**, použijte následující postup:

1. Z [webu Azure portal](https://portal.azure.com), vyberte pracovní prostor služby Azure Machine Learning.

1. Z __aplikace__ vyberte __Compute__. Potom vyberte __přidat compute__. 

    ![Postup přidání výpočetní prostředí ve službě Azure Machine Learning](./media/how-to-enable-virtual-network/add-compute.png)

1. Pokud chcete nakonfigurovat tento výpočetní prostředek se má použít virtuální síť, použijte tyto možnosti:

    - __Konfigurace sítě__: Vyberte __Upřesnit__.

    - __Skupina prostředků__: Vyberte skupinu prostředků, která obsahuje virtuální síť.

    - __Virtuální síť__: Vyberte virtuální síť, která obsahuje podsíť.

    - __Podsíť__: Vyberte podsíť, kterou chcete použít.

    ![Snímek obrazovky zobrazující nastavení virtuální sítě pro machine learning compute](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

Můžete také vytvořit Machine Learning Compute clusteru pomocí **SDK služby Azure Machine Learning**. Následující kód vytvoří nový Machine Learning Compute cluster v `default` podsítě virtuální sítě s názvem `mynetwork`:

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# The Azure Virtual Network name, subnet, and resource group
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
                                                           vnet_resourcegroup_name = vnet_resourcegroup_name,
                                                           vnet_name = vnet_name,
                                                           subnet_name = subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    
    # Wait for the cluster to complete, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

Po dokončení procesu vytváření můžete trénování modelu pomocí clusteru. Další informace najdete v tématu [vyberte a použijte cílové výpočetní prostředí pro školení](how-to-set-up-training-targets.md) dokumentu.

## <a name="use-a-virtual-machine-or-hdinsight"></a>Použití virtuálního počítače nebo HDInsight

Použití virtuálního počítače nebo clusteru HDInsight ve virtuální síti s pracovního prostoru, postupujte následovně:

> [!IMPORTANT]
> Služba Azure Machine Learning podporuje pouze virtuální počítače s Ubuntu.

1. Vytvoření clusteru HDInsight nebo virtuálního počítače pomocí webu Azure portal, rozhraní příkazového řádku Azure, atd. a vložit ho do služby Azure Virtual Network. Další informace najdete v tématu v následujících dokumentech:
    * [Vytváření a správa virtuálních sítí Azure pro virtuální počítače s Linuxem](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Rozšíření HDInsight pomocí Azure Virtual Network](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network) 

1. Pokud chcete povolit službu Azure Machine Learning ke komunikaci s portem SSH na virtuálním počítači nebo clusteru, musí nakonfigurovat záznam zdroje pro skupiny zabezpečení sítě. SSH port je obvykle port 22. Pokud chcete povolit provoz z tohoto zdroje, použijte následující informace:

    * __Zdroj__: Vyberte __Značka služby__.

    * __Značka zdrojové služby__: Select __AzureMachineLearning__

    * __Zdrojové rozsahy portů__: Vyberte __*__

    * __Určení__: Vyberte __jakékoli__

    * __Rozsahy cílových portů__: Vyberte __22__

    * __Protokol__: Vyberte __jakékoli__

    * __Akce__: Vyberte __povolit__

   ![Snímek obrazovky příchozích pravidel pro provádění experimentování na virtuálním počítači nebo HDInsight ve virtuální síti](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Ponechte výchozí odchozí pravidla pro skupiny zabezpečení sítě. Další informace najdete v části pravidla zabezpečení výchozí sady [skupiny zabezpečení](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules) dokumentaci.
    
1. Připojení clusteru HDInsight nebo virtuálního počítače do pracovního prostoru služby Azure Machine Learning. Další informace najdete v tématu [nastavení cílových výpočetních prostředí k tréninku modelu](how-to-set-up-training-targets.md) dokumentu.

## <a name="use-azure-kubernetes-service-aks"></a>Použití Azure Kubernetes Service (AKS)

> [!IMPORTANT]
> Kontrola požadavků a naplánujte IP adresování pro váš cluster před pokračováním kroků uvedených níže. Můžete se podívat do [pokročilé konfigurace sítě ve službě Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/configure-advanced-networking)
> 
> Ponechte výchozí odchozí pravidla pro skupiny zabezpečení sítě. Další informace najdete v části pravidla zabezpečení výchozí sady [skupiny zabezpečení](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules) dokumentaci.
>
> Azure Kubernetes Service a Azure Virtual Network by měl být ve stejné oblasti.

K přidání služby Azure Kubernetes Service ve virtuální síti do pracovního prostoru, použijte následující kroky v __webu Azure portal__:

1. Z [webu Azure portal](https://portal.azure.com), vyberte pracovní prostor služby Azure Machine Learning.

1. Z __aplikace__ vyberte __Compute__. Potom vyberte __přidat compute__. 

    ![Postup přidání výpočetní prostředí ve službě Azure Machine Learning](./media/how-to-enable-virtual-network/add-compute.png)

1. Pokud chcete nakonfigurovat tento výpočetní prostředek se má použít virtuální síť, použijte tyto možnosti:

    - __Konfigurace sítě__: Vyberte __Upřesnit__.

    - __Skupina prostředků__: Vyberte skupinu prostředků, která obsahuje virtuální síť.

    - __Virtuální síť__: Vyberte virtuální síť, která obsahuje podsíť.

    - __Podsíť__: Vyberte podsíť.

    - __Rozsah adres služby Kubernetes__: Vyberte rozsah adres služby Kubernetes. Tento rozsah adres IP rozsahu notaci CIDR používá k definování IP adresy, které jsou k dispozici pro cluster. Nesmí překrývat s žádnými rozsahy adres podsítě IP. Příklad: 10.0.0.0/16.

    - __IP adresa služby Kubernetes DNS__: Vyberte IP adresu služby Kubernetes DNS. Tato IP adresa se přiřadí ve službě Kubernetes DNS. Musí být v rozsahu adres služby Kubernetes. Příklad: 10.0.0.10.

    - __Adresa mostu docker__: Vyberte adresa mostu Docker. Tato IP adresa je přiřazená mostu Docker. Nesmí být v žádné rozsahy IP adres podsítě a rozsah adres služby Kubernetes. Příklad: 172.17.0.1/16

   ![Služba Azure Machine Learning: Machine Learning Compute nastavení virtuální sítě](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

    > [!TIP]
    > Pokud už máte AKS cluster ve virtuální síti, připojíte ho do pracovního prostoru. Další informace najdete v tématu [nasazení do AKS](how-to-deploy-to-aks.md).

Můžete také použít **SDK služby Azure Machine Learning** pro přidání služby Azure Kubernetes ve službě Virtual Network. Následující kód vytvoří novou službu Azure Kubernetes v `default` podsítě virtuální sítě s názvem `mynetwork`:

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
aks_target = ComputeTarget.create(workspace = ws,
                                  name = "myaks",
                                  provisioning_configuration = config)
```

Po dokončení procesu vytváření můžete provádět odvozování v clusteru AKS za bránou virtuální sítě. Další informace najdete v tématu [nasazení do AKS](how-to-deploy-to-aks.md).

## <a name="next-steps"></a>Další postup

* [Nastavení prostředí pro školení](how-to-set-up-training-targets.md)
* [Kam chcete nasazovat modely](how-to-deploy-and-where.md)
* [Zabezpečení nasazených modelů s protokolem SSL](how-to-secure-web-service.md)
