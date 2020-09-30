---
title: Zabezpečená prostředí Inferencing s využitím virtuálních sítí
titleSuffix: Azure Machine Learning
description: K zabezpečení prostředí pro Azure Machine Learning Inferencing použijte izolovaný Virtual Network Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 09/24/2020
ms.custom: contperfq4, tracking-python, contperfq1
ms.openlocfilehash: df3872f5675b55d845bc88e704b1dc4932091ac7
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91532410"
---
# <a name="secure-an-azure-machine-learning-inferencing-environment-with-virtual-networks"></a>Zabezpečení prostředí Azure Machine Learning Inferencing s virtuálními sítěmi

V tomto článku se dozvíte, jak zabezpečit Inferencing prostředí pomocí virtuální sítě v Azure Machine Learning.

Tento článek je třetí částí série s pěti částmi, která vás provede zabezpečením Azure Machine Learningho pracovního postupu. Důrazně doporučujeme, abyste si přečetli [část One: VNet – přehled](how-to-network-security-overview.md) , abyste si nejdřív porozuměli celkové architektuře. 

Podívejte se na další články v této sérii:

[1. virtuální síť – přehled](how-to-network-security-overview.md)  >  [Zabezpečte pracovní prostor](how-to-secure-workspace-vnet.md)  >  [3. Zabezpečte školicí prostředí](how-to-secure-training-vnet.md)  >  **4. Zabezpečte prostředí Inferencing**  >  [5. Povolit funkci studia](how-to-enable-studio-virtual-network.md)

V tomto článku se dozvíte, jak zabezpečit následující Inferencing prostředky ve virtuální síti:
> [!div class="checklist"]
> - Výchozí cluster Azure Kubernetes Service (AKS)
> - Privátní cluster AKS
> - Azure Container Instances (ACI)


## <a name="prerequisites"></a>Předpoklady

+ Přečtěte si článek [Přehled zabezpečení sítě](how-to-network-security-overview.md) , který vám pomůže pochopit běžné scénáře virtuální sítě a celkovou architekturu virtuální sítě.

+ Existující virtuální síť a podsíť pro použití s výpočetními prostředky.

+ Pokud chcete nasadit prostředky do virtuální sítě nebo podsítě, musí mít váš uživatelský účet oprávnění k následujícím akcím v řízení přístupu na základě role (RBAC) v Azure.

    - "Microsoft. Network/virtualNetworks/JOIN/Action" na prostředku virtuální sítě.
    - "Microsoft. Network/virtualNetworks/podsíť/JOIN/Action" na prostředku podsítě.

    Další informace o RBAC se sítí najdete v tématu [předdefinované role sítě](/azure/role-based-access-control/built-in-roles#networking) .

<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Pokud chcete použít cluster AKS ve virtuální síti, musí být splněné následující požadavky na síť:

> [!div class="checklist"]
> * Postupujte podle požadavků v části [Konfigurace pokročilé sítě ve službě Azure Kubernetes Service (AKS)](../aks/configure-azure-cni.md#prerequisites).
> * Instance AKS a virtuální síť musí být ve stejné oblasti. Pokud Zabezpečete Azure Storage účty používané pracovním prostorem ve virtuální síti, musí být ve stejné virtuální síti jako instance AKS.


Pokud chcete do svého pracovního prostoru přidat AKS ve virtuální síti, použijte následující postup:

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

## <a name="secure-vnet-traffic"></a>Zabezpečený provoz virtuální sítě

Existují dva přístupy k izolaci provozu do a z clusteru AKS do virtuální sítě:

* __Privátní cluster AKS__: Tento přístup používá privátní odkaz Azure k vytvoření privátního koncového bodu pro cluster AKS v rámci virtuální sítě.
* __Interní nástroj pro vyrovnávání zatížení AKS__: Tento přístup nakonfiguruje Nástroj pro vyrovnávání zatížení clusteru, aby používal interní IP adresu ve virtuální síti.

> [!WARNING]
> Obě konfigurace jsou různými způsoby, jak dosáhnout stejného cíle (zabezpečení provozu do clusteru AKS v rámci virtuální sítě). **Použijte jeden nebo druhý, ale ne obojí**.

### <a name="private-aks-cluster"></a>Privátní cluster AKS

Ve výchozím nastavení mají clustery AKS řídicí plochu nebo Server API s veřejnými IP adresami. AKS můžete nakonfigurovat tak, aby používala privátní rovinu ovládacího prvku vytvořením privátního clusteru AKS. Další informace najdete v tématu [Vytvoření privátního clusteru služby Azure Kubernetes](../aks/private-clusters.md).

Po vytvoření privátního clusteru AKS [Připojte cluster k virtuální síti](how-to-create-attach-kubernetes.md) , která se má používat s Azure Machine Learning.

> [!IMPORTANT]
> Předtím, než použijete AKS cluster s podporou privátního propojení s Azure Machine Learning, je nutné pro povolení této funkce otevřít incident podpory. Další informace najdete v tématu [Správa a zvýšení kvót](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases).

## <a name="internal-aks-load-balancer"></a>Interní nástroj pro vyrovnávání zatížení AKS

Ve výchozím nastavení používají nasazení AKS [veřejný Nástroj pro vyrovnávání zatížení](../aks/load-balancer-standard.md). V této části se dozvíte, jak nakonfigurovat AKS pro používání interního nástroje pro vyrovnávání zatížení. Interní (nebo soukromý) Nástroj pro vyrovnávání zatížení se používá v případě, že jsou jako front-endu povoleny pouze privátní IP adresy. Interní nástroje pro vyrovnávání zatížení se používají k vyrovnávání zatížení provozu ve virtuální síti.

Privátní Nástroj pro vyrovnávání zatížení je povolen konfigurací AKS k použití _interního nástroje pro vyrovnávání zatížení_. 

#### <a name="network-contributor-role"></a>Role Přispěvatel sítě

> [!IMPORTANT]
> Pokud vytvoříte nebo připojíte cluster AKS tím, že zadáte dříve vytvořenou virtuální síť, musíte instančnímu objektu (SP) nebo spravované identitě pro svůj cluster AKS udělit roli _Přispěvatel sítě_ do skupiny prostředků, která obsahuje virtuální síť. Tato operace se musí provést předtím, než se pokusíte změnit interní nástroj pro vyrovnávání zatížení na soukromou IP adresu.
>
> Chcete-li přidat identitu jako Přispěvatel sítě, použijte následující postup:

1. Pokud chcete najít instanční objekt nebo ID spravované identity pro AKS, použijte následující příkazy rozhraní příkazového řádku Azure CLI. Nahraďte `<aks-cluster-name>` názvem clusteru. Nahraďte `<resource-group-name>` názvem skupiny prostředků, která _obsahuje cluster AKS_:

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query servicePrincipalProfile.clientId
    ``` 

    Pokud tento příkaz vrátí hodnotu `msi` , použijte následující příkaz k identifikaci ID objektu zabezpečení pro spravovanou identitu:

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query identity.principalId
    ```

1. Pokud chcete najít ID skupiny prostředků, která obsahuje vaši virtuální síť, použijte následující příkaz. Nahraďte `<resource-group-name>` názvem skupiny prostředků, která _obsahuje virtuální síť_:

    ```azurecli-interactive
    az group show -n <resource-group-name> --query id
    ```

1. K přidání instančního objektu nebo spravované identity jako přispěvatele sítě použijte následující příkaz. Nahraďte `<SP-or-managed-identity>` identifikátorem vráceným pro instanční objekt nebo spravovanou identitu. Nahraďte `<resource-group-id>` identifikátorem vráceným pro skupinu prostředků, která obsahuje virtuální síť:

    ```azurecli-interactive
    az role assignment create --assignee <SP-or-managed-identity> --role 'Network Contributor' --scope <resource-group-id>
    ```
Další informace o používání interního nástroje pro vyrovnávání zatížení s AKS najdete v tématu [použití interního nástroje pro vyrovnávání zatížení se službou Azure Kubernetes Service](/azure/aks/internal-lb).

#### <a name="enable-private-load-balancer"></a>Povolit privátní Nástroj pro vyrovnávání zatížení

> [!IMPORTANT]
> Při vytváření clusteru služby Azure Kubernetes v nástroji Azure Machine Learning Studio nemůžete povolit privátní IP adresu. Můžete ho vytvořit pomocí interního nástroje pro vyrovnávání zatížení při použití sady Python SDK nebo rozšíření Azure CLI pro Machine Learning.

Následující příklady ukazují, jak __vytvořit nový cluster AKS s privátní IP/interním nástrojem pro vyrovnávání zatížení__ pomocí sady SDK a rozhraní příkazového řádku:

# <a name="python"></a>[Python](#tab/python)

```python
import azureml.core
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
    prov_config=AksCompute.provisioning_configuration(load_balancer_type="InternalLoadBalancer")
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
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az ml computetarget create aks -n myaks --load-balancer-type InternalLoadBalancer
```

Další informace najdete v tématu [AZ ml computetarget Create AKS](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-create-aks) reference.

---

Když k pracovnímu prostoru __připojíte existující cluster__ , musíte počkat, až po operaci připojit ke konfiguraci nástroje pro vyrovnávání zatížení. Informace o připojení clusteru najdete v tématu [připojení existujícího clusteru AKS](how-to-create-attach-kubernetes.md).

Po připojení existujícího clusteru můžete cluster aktualizovat tak, aby používal interní nástroj pro vyrovnávání zatížení nebo privátní IP adresu:

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute

# ws = workspace object. Creation not shown in this snippet
aks_target = AksCompute(ws,"myaks")

# Change to the name of the subnet that contains AKS
subnet_name = "default"
# Update AKS configuration to use an internal load balancer
update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
aks_target.update(update_config)
# Wait for the operation to complete
aks_target.wait_for_completion(show_output = True)
```

## <a name="enable-azure-container-instances-aci"></a>Povolit Azure Container Instances (ACI)

Azure Container Instances se dynamicky vytvářejí při nasazování modelu. Pokud chcete povolit Azure Machine Learning vytváření ACI uvnitř virtuální sítě, musíte povolit __delegování podsítě__ pro podsíť, kterou používá nasazení.

> [!WARNING]
> Při použití Azure Container Instances ve virtuální síti musí být virtuální síť ve stejné skupině prostředků jako pracovní prostor Azure Machine Learning.
>
> Při použití Azure Container Instances uvnitř virtuální sítě se Azure Container Registry (ACR) pro váš pracovní prostor nemůže nacházet i ve virtuální síti.

Pokud chcete použít ACI ve virtuální síti k vašemu pracovnímu prostoru, použijte následující postup:

1. K povolení delegování podsítě ve virtuální síti použijte informace v článku [Přidání nebo odebrání delegování podsítě](../virtual-network/manage-subnet-delegation.md) . Delegování můžete povolit při vytváření virtuální sítě nebo jejich přidání do existující sítě.

    > [!IMPORTANT]
    > Při povolování delegování použijte `Microsoft.ContainerInstance/containerGroups` jako hodnotu __pro podsíť delegáta na službu__ .

2. Nasaďte model pomocí [AciWebservice. deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py&preserve-view=true#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-&preserve-view=true), použijte `vnet_name` parametry a `subnet_name` . Nastavte tyto parametry na název virtuální sítě a podsíť, ve které jste povolili delegování.


## <a name="next-steps"></a>Další kroky

Tento článek je třetí částí v řadě virtuálních sítí se čtyřmi částmi. Další informace o tom, jak zabezpečit virtuální síť, najdete v dalších článcích:

* [Část 1: Přehled virtuální sítě](how-to-network-security-overview.md)
* [Část 2: zabezpečení prostředků pracovního prostoru](how-to-secure-workspace-vnet.md)
* [Část 3: zabezpečení školicího prostředí](how-to-secure-training-vnet.md)
* [Část 5: povolení funkcí studia](how-to-enable-studio-virtual-network.md)