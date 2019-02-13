---
title: Spouštění experimentů a odvození ve virtuální síti
titleSuffix: Azure Machine Learning service
description: Spouštět experimenty strojového učení a odvození zabezpečení ve službě Azure virtual network. Zjistěte, jak vytvořit cílových výpočetních prostředí pro trénování modelu a jak odvození v rámci virtuální sítě. Další informace o požadavcích pro zabezpečené virtuální sítě, jako například požadovat příchozí a odchozí porty.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 01/08/2019
ms.openlocfilehash: 60a76df6360ca66e8f55b03d5914283f669eb402
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56118101"
---
# <a name="securely-run-experiments-and-inferencing-inside-an-azure-virtual-network"></a>Bezpečné spuštění experimentů a odvozování uvnitř virtuální sítě Azure

V tomto článku se dozvíte, jak pro spouštění experimentů a odvozování ve virtuální síti. Virtuální sítě slouží jako hranice zabezpečení, izolace vašich prostředků Azure z veřejného Internetu. Služby Azure virtual network můžete připojit také k vaší místní síti. Umožňuje bezpečně trénování modelů a přístup k nasazených modelů pro odvozování.

Služba Azure Machine Learning spoléhá na ostatní služby Azure pro výpočetní prostředky. Výpočetní prostředky (cílových výpočetních prostředí) se používají k trénování a nasazování modelů. Tyto výpočetní cíle lze vytvořit ve virtuální síti. Například můžete použít virtuální počítač Microsoft datové vědy pro trénování modelu a poté model nasaďte do Azure Kubernetes Service (AKS). Další informace o virtuálních sítích najdete v tématu [Přehled služby Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

## <a name="storage-account-for-your-workspace"></a>Účet úložiště pro váš pracovní prostor

Když vytvoříte pracovní prostor služby Azure Machine Learning, vyžaduje účet služby Azure Storage. Nikdy nezapínat na pravidla brány firewall pro tento účet úložiště. Služba Azure Machine Learning vyžaduje, aby neomezený přístup k účtu úložiště.

Pokud si nejste jistí, jestli jste upravili tato nastavení nebo Ne, viz __změnit výchozí pravidlo přístupu síť__ v [virtuální sítí a bran firewall nakonfigurovat služby Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security). Pokud chcete povolit přístup ze všech sítí pomocí kroků.

## <a name="use-machine-learning-compute"></a>Použití aplikace Machine Learning Compute

Pokud chcete používat Azure Machine Learning Compute ve virtuální síti, použijte následující informace o požadavcích na síť:

- Virtuální síť musí být ve stejném předplatném a oblasti, pracovní prostor služby Azure Machine Learning.

- Podsíť zadaná pro Machine Learning Compute cluster musí obsahovat dostatek nepřiřazených IP adres pro plnění počet virtuálních počítačů určené pro cluster. Pokud podsíť nemá dostatek nepřiřazených IP adres, cluster se částečně přidělí.

- Pokud máte v plánu pro zabezpečení virtuální sítě tím, že omezují provoz, ponechte některé porty otevřené pro službu Machine Learning Compute. Další informace najdete v tématu [požadované porty](#mlcports).

- Zkontrolujte, jestli zásady zabezpečení a uzamčení na předplatné nebo skupinu prostředků ve virtuální síti omezit oprávnění ke správě služby virtuální sítě.

- Pokud se chystáte vložit více clusterů Machine Learning Compute v jedné virtuální síti, budete muset požádat o zvýšení kvóty pro jeden nebo více prostředků.

    Machine Learning Compute automaticky přiděluje další síťové prostředky ve skupině prostředků, která obsahuje virtuální síť. Pro každý cluster Machine Learning Compute přiděluje služba Azure Machine Learning na následujících odkazech:

    - Skupina zabezpečení jedné sítě (NSG)

    - Jedna veřejná IP adresa

    - Jedna služba Vyrovnávání zatížení

   Pro tyto prostředky platí omezení [kvót prostředků](https://docs.microsoft.com/azure/azure-subscription-service-limits) předplatného.

### <a id="mlcports"></a> Požadované porty

Machine Learning Compute v současnosti využívá službu Azure Batch ke zřízení virtuálních počítačů v zadané virtuální sítě. Podsíť musí umožňovat příchozí komunikaci ze služby Batch. Tato komunikace se používá k naplánování spuštění na uzlech Machine Learning Compute a komunikovat s Azure Storage a dalším prostředkům. Batch přidá skupiny Nsg na úrovni síťových rozhraní (NIC), které jsou připojené k virtuálním počítačům. Tyto skupiny zabezpečení sítě automaticky konfigurují pravidla příchozích a odchozích přenosů, která povolují následující provoz:

- Příchozí provoz přes protokol TCP na portech 29876 a 29877 z IP adres role služby Batch.
 
- Příchozí přenosy TCP na portu 22 pro povolení vzdáleného přístupu.
 
- Odchozí provoz do virtuální sítě na jakémkoli portu.

- Odchozí provoz do internetu na jakémkoli portu.

Je-li upravit nebo přidat příchozí/odchozí pravidla skupin zabezpečení sítě nakonfigurované služby Batch, je třeba postupovat opatrně. Pokud bloky NSG komunikace s výpočetními uzly služby Machine Learning Compute nastaví stav výpočetních uzlů na nepoužitelné.

Není nutné zadat skupiny Nsg na úrovni podsítě, protože Batch nakonfiguruje vlastní skupiny zabezpečení sítě. Ale pokud zadanou podsíť má přidruženou skupiny Nsg nebo brány firewall, nakonfigurujte pravidla zabezpečení příchozích a odchozích, jak bylo zmíněno dříve. Na následujících snímcích obrazovky ukazují, jak vypadá konfigurace pravidla na webu Azure Portal:

![Snímek obrazovky příchozí pravidla NSG pro Machine Learning Compute](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)

![Snímek obrazovky odchozí pravidla NSG pro Machine Learning Compute](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="create-machine-learning-compute-in-a-virtual-network"></a>Vytvořit Machine Learning Compute ve virtuální síti

K vytvoření clusteru Machine Learning Compute pomocí webu Azure portal, postupujte podle těchto kroků:

1. V [webu Azure portal](https://portal.azure.com), vyberte pracovní prostor služby Azure Machine Learning.

1. V __aplikace__ vyberte __Compute__. Potom vyberte __přidat compute__. 

    ![Postup přidání výpočetní prostředí ve službě Azure Machine Learning](./media/how-to-enable-virtual-network/add-compute.png)

1. Pokud chcete nakonfigurovat tento výpočetní prostředek se má použít virtuální síť, použijte tyto možnosti:

    - __Konfigurace sítě__: Vyberte __Upřesnit__.

    - __Skupina prostředků__: Vyberte skupinu prostředků, která obsahuje virtuální síť.

    - __Virtuální síť__: Vyberte virtuální síť, která obsahuje podsíť.

    - __Podsíť__: Vyberte podsíť, kterou chcete použít.

   ![Snímek obrazovky zobrazující nastavení virtuální sítě pro machine learning compute](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

Můžete také vytvořit cluster Machine Learning Compute pomocí sady SDK Azure Machine Learning. Následující kód vytvoří nový Machine Learning Compute cluster v `default` podsítě virtuální sítě s názvem `mynetwork`:

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
                                                           vnet_resourcegroup_name = vnet_resourcegroup_name,
                                                           vnet_name = vnet_name,
                                                           subnet_name = subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    
    # Wait for the cluster to complete, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

Po dokončení procesu vytváření můžete s použitím clusteru natrénování modelu. Další informace najdete v tématu [vyberte a použijte cílové výpočetní prostředí pro školení](how-to-set-up-training-targets.md).

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>Použití virtuálního počítače nebo clusteru HDInsight

Použití virtuálního počítače nebo clusteru Azure HDInsight ve virtuální síti s pracovního prostoru, postupujte takto:

> [!IMPORTANT]
> Služba Azure Machine Learning podporuje pouze virtuální počítače, na kterých běží Ubuntu.

1. Vytvoření clusteru HDInsight nebo virtuálního počítače pomocí webu Azure portal nebo rozhraní příkazového řádku Azure a vložit ho do služby Azure virtual network. Další informace najdete v tématu v následujících dokumentech:
    * [Vytváření a správa virtuálních sítí Azure pro virtuální počítače s Linuxem](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Rozšíření HDInsight pomocí služby Azure virtual network](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network) 

1. Pokud chcete povolit službu Azure Machine Learning ke komunikaci s portem SSH na virtuálním počítači nebo clusteru, musí nakonfigurovat záznam zdroje pro skupiny zabezpečení sítě. SSH port je obvykle port 22. Pokud chcete povolit provoz z tohoto zdroje, použijte následující informace:

    * __Zdroj__: Vyberte __značka služby__.

    * __Značka zdrojové služby__: Vyberte __AzureMachineLearning__.

    * __Zdrojové rozsahy portů__: Vyberte __*__.

    * __Určení__: Vyberte __jakékoli__.

    * __Rozsahy cílových portů__: Vyberte __22__.

    * __Protokol__: Vyberte __jakékoli__.

    * __Akce__: Vyberte __povolit__.

   ![Snímek obrazovky příchozích pravidel pro provádění experimentování v clusteru HDInsight nebo virtuálního počítače ve virtuální síti](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Ponechte výchozí odchozí pravidla pro skupiny zabezpečení sítě. Další informace najdete v tématu výchozí pravidla zabezpečení v [skupiny zabezpečení](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).
    
1. Připojení clusteru HDInsight nebo virtuálního počítače do pracovního prostoru služby Azure Machine Learning. Další informace najdete v tématu [nastavení cílových výpočetních prostředí k tréninku modelu](how-to-set-up-training-targets.md).

## <a name="use-azure-kubernetes-service"></a>Použití služby Azure Kubernetes

> [!IMPORTANT]
> Kontrola požadavků a naplánujte IP adresování pro váš cluster před provedením kroků. Další informace najdete v tématu [pokročilé konfigurace sítě ve službě Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/configure-advanced-networking).
> 
> Ponechte výchozí odchozí pravidla pro skupiny zabezpečení sítě. Další informace najdete v tématu výchozí pravidla zabezpečení v [skupiny zabezpečení](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).
>
> Azure Kubernetes Service a virtuální síť Azure musí být ve stejné oblasti.

Přidání služby Azure Kubernetes Service ve virtuální síti do pracovního prostoru, postupujte podle těchto kroků na webu Azure Portal:

1. V [webu Azure portal](https://portal.azure.com), vyberte pracovní prostor služby Azure Machine Learning.

1. V __aplikace__ vyberte __Compute__. Potom vyberte __přidat compute__. 

    ![Postup přidání výpočetní prostředí ve službě Azure Machine Learning](./media/how-to-enable-virtual-network/add-compute.png)

1. Pokud chcete nakonfigurovat tento výpočetní prostředek se má použít virtuální síť, použijte tyto možnosti:

    - __Konfigurace sítě__: Vyberte __Upřesnit__.

    - __Skupina prostředků__: Vyberte skupinu prostředků, která obsahuje virtuální síť.

    - __Virtuální síť__: Vyberte virtuální síť, která obsahuje podsíť.

    - __Podsíť__: Vyberte podsíť.

    - __Rozsah adres služby Kubernetes__: Vyberte rozsah adres služby Kubernetes. Tento rozsah adres IP rozsahu notaci CIDR používá k definování IP adresy, které jsou k dispozici pro cluster. Nesmí překrývat s žádnými rozsahy IP adres podsítě. Příklad: 10.0.0.0/16.

    - __IP adresa služby Kubernetes DNS__: Vyberte IP adresu služby Kubernetes DNS. Tato IP adresa se přiřadí ve službě Kubernetes DNS. Musí být v rozsahu adres služby Kubernetes. Příklad: 10.0.0.10.

    - __Adresa mostu docker__: Vyberte adresa mostu Docker. Tato IP adresa je přiřazená mostu Docker. Nesmí být v žádnými rozsahy IP adres podsítě a rozsah adres služby Kubernetes. Příklad: 172.17.0.1/16.

   ![Služba Azure Machine Learning: Machine Learning Compute nastavení virtuální sítě](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

    > [!TIP]
    > Pokud už máte AKS cluster ve virtuální síti, připojíte ho do pracovního prostoru. Další informace najdete v tématu [nasazení do AKS](how-to-deploy-to-aks.md).

Můžete také použít **SDK služby Azure Machine Learning** pro přidání služby Azure Kubernetes Service ve virtuální síti. Následující kód vytvoří novou instanci služby Azure Kubernetes Service v `default` podsítě virtuální sítě s názvem `mynetwork`:

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

Po dokončení procesu vytváření vám pomůžou odvozování v clusteru AKS za bránou virtuální sítě. Další informace najdete v tématu [nasazení do AKS](how-to-deploy-to-aks.md).

## <a name="next-steps"></a>Další postup

* [Nastavení prostředí pro školení](how-to-set-up-training-targets.md)
* [Kam chcete nasazovat modely](how-to-deploy-and-where.md)
* [Bezpečně nasazujte modely pomocí protokolu SSL](how-to-secure-web-service.md)

