---
title: Vytvoření a připojení služby Azure Kubernetes
titleSuffix: Azure Machine Learning
description: Naučte se, jak vytvořit nový cluster služby Azure Kubernetes prostřednictvím Azure Machine Learning, nebo jak připojit existující cluster AKS k vašemu pracovnímu prostoru.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 10/02/2020
ms.openlocfilehash: e485c2f0a7deeffe68c932688658ef099fec510e
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492751"
---
# <a name="create-and-attach-an-azure-kubernetes-service-cluster"></a>Vytvoření a připojení clusteru služby Azure Kubernetes

Azure Machine Learning můžou nasazovat školicí modely strojového učení do služby Azure Kubernetes. Musíte ale nejdřív __vytvořit__ cluster Azure Kubernetes Service (AKS) z pracovního prostoru Azure ml nebo __připojit__ existující cluster AKS. Tento článek poskytuje informace o tom, jak vytvořit a připojit cluster.

## <a name="prerequisites"></a>Požadavky

- Pracovní prostor služby Azure Machine Learning. Další informace najdete v tématu [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md).

- [Rozšíření Azure CLI pro službu Machine Learning](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)nebo [rozšíření Azure Machine Learning Visual Studio Code](tutorial-setup-vscode-extension.md).

- Pokud plánujete pomocí Virtual Network Azure zabezpečit komunikaci mezi pracovním prostorem Azure ML a clusterem AKS, přečtěte si [izolaci sítě během školení & článku o odvozování](./how-to-network-security-overview.md) .

## <a name="limitations"></a>Omezení

- Pokud v clusteru potřebujete nasadit **Standard Load Balancer (SLB)** místo základního Load BALANCER (BLB), vytvořte cluster na portálu AKS/CLI/SDK a pak ho **Připojte** k pracovnímu prostoru AML.

- Pokud máte Azure Policy, která omezuje vytváření veřejných IP adres, vytvoření clusteru AKS se nezdaří. AKS vyžaduje veřejnou IP adresu pro [odchozí přenosy](../aks/limit-egress-traffic.md). Článek o odchozím provozu taky poskytuje pokyny pro uzamknutí odchozího provozu z clusteru prostřednictvím veřejné IP adresy s výjimkou několika plně kvalifikovaných názvů domén. Existují dva způsoby, jak povolit veřejnou IP adresu:
    - Cluster může používat veřejnou IP adresu vytvořenou ve výchozím nastavení s BLB nebo SLB nebo
    - Cluster se dá vytvořit bez veřejné IP adresy a pak je u veřejné IP adresy nakonfigurovaná brána firewall s trasou definovanou uživatelem. Další informace najdete v tématu [přizpůsobení výstupů clusteru pomocí uživatelsky definovaného postupu](../aks/egress-outboundtype.md).
    
    Rovina ovládacího prvku AML nehovoří s touto veřejnou IP adresou. Mluví s rovinou ovládacího prvku AKS pro nasazení. 

- Pokud **připojíte** cluster AKS, který má [povolený povolený rozsah IP adres pro přístup k serveru rozhraní API](../aks/api-server-authorized-ip-ranges.md), povolte rozsahy IP adres řídicí plochy AML pro cluster AKS. Rovina ovládacího prvku AML se nasadí mezi spárované oblasti a nasadí odvození lusků do clusteru AKS. Bez přístupu k serveru rozhraní API nejde nasadit odvozené lusky. Při povolování rozsahů IP adres v clusteru AKS použijte [rozsahy IP adres](https://www.microsoft.com/download/confirmation.aspx?id=56519) pro obě [spárované oblasti](../best-practices-availability-paired-regions.md) .

    Rozsahy autorizovaných IP adres fungují jenom s Standard Load Balancer.

- Když **připojíte** cluster AKS, musí být ve stejném předplatném Azure jako pracovní prostor Azure Machine Learning.

- Pokud chcete použít privátní cluster AKS (pomocí privátního odkazu Azure), musíte nejdřív vytvořit cluster a pak ho **připojit** k pracovnímu prostoru. Další informace najdete v tématu [Vytvoření privátního clusteru služby Azure Kubernetes](../aks/private-clusters.md).

- Název COMPUTE pro cluster AKS musí být jedinečný v rámci pracovního prostoru Azure ML.
    - Název je povinný a musí mít délku 3 až 24 znaků.
    - Platné znaky jsou velká a malá písmena, číslice a znak-znaku.
    - Název musí začínat písmenem.
    - Název musí být jedinečný v rámci všech stávajících výpočtů v oblasti Azure. Pokud zvolený název není jedinečný, zobrazí se upozornění.
   
 - Pokud chcete nasadit modely do uzlů **GPU** nebo **FPGAch** uzlů (nebo jakékoli konkrétní SKU), musíte vytvořit cluster s konkrétní SKU. Neexistuje žádná podpora pro vytváření fondu sekundárních uzlů v existujícím clusteru a nasazování modelů do fondu sekundárních uzlů.
 
- Při vytváření nebo připojování clusteru můžete vybrat, jestli se má cluster vytvořit pro __vývoj a testování__ nebo pro __produkční__ prostředí. Pokud chcete vytvořit cluster AKS pro __vývoj__, __ověřování__ a __testování__ namísto produkčního prostředí, nastavte __účel clusteru__ na __dev-test__. Pokud neurčíte účel clusteru, vytvoří se __produkční__ cluster. 

    > [!IMPORTANT]
    > Cluster pro __vývoj a testování__ není vhodný pro provoz na úrovni produkčního prostředí a může prodloužit dobu odvození. Clustery pro vývoj a testování také nezaručují odolnost proti chybám.

- Pokud se cluster bude používat pro __produkční__ prostředí, musí mít při vytváření nebo připojení clusteru aspoň 12 __virtuálních procesorů__. Počet virtuálních procesorů se dá vypočítat vynásobením __počtu uzlů__ v clusteru __počtem jader__ poskytovaných vybranou velikostí virtuálního počítače. Pokud například použijete velikost virtuálního počítače "Standard_D3_v2", který má 4 virtuální jádra, měli byste vybrat 3 nebo vyšší jako počet uzlů.

    V případě clusteru pro __vývoj a testování__ budeme znovu zadarmo aspoň 2 virtuální procesory.

- Sada SDK pro Azure Machine Learning neposkytuje podporu škálování clusteru AKS. Pokud chcete škálovat uzly v clusteru, použijte uživatelské rozhraní pro cluster AKS v nástroji Azure Machine Learning Studio. Můžete změnit jenom počet uzlů, nikoli velikost virtuálního počítače v clusteru. Další informace o škálování uzlů v clusteru AKS najdete v následujících článcích:

    - [Ruční škálování počtu uzlů v clusteru AKS](../aks/scale-cluster.md)
    - [Nastavení automatického škálování clusteru v AKS](../aks/cluster-autoscaler.md)

- __Neaktualizujte přímo cluster pomocí konfigurace YAML__. I když služba Azure Kubernetes Services podporuje aktualizace prostřednictvím konfigurace YAML, Azure Machine Learning nasazení přepíše vaše změny. Pouze dvě YAML pole, která nebudou přepsána, jsou __omezení požadavků__ a __CPU a paměť__.

## <a name="azure-kubernetes-service-version"></a>Verze Azure Kubernetes Service

Služba Azure Kubernetes umožňuje vytvářet clustery s využitím nejrůznějších verzí Kubernetes. Další informace o dostupných verzích najdete v tématu [podporované verze Kubernetes ve službě Azure Kubernetes Service](../aks/supported-kubernetes-versions.md).

Při **vytváření** clusteru služby Azure Kubernetes pomocí jedné z následujících metod nemusíte *ve verzi* vytvořeného clusteru volit tyto možnosti:

* Azure Machine Learning Studio nebo část Azure Machine Learning Azure Portal.
* Machine Learning rozšíření pro rozhraní příkazového řádku Azure
* Azure Machine Learning SDK.

Tyto metody vytvoření clusteru AKS používají __výchozí__ verzi clusteru. *Změna výchozí verze v průběhu času* bude k dispozici pro nové verze Kubernetes.

Když **připojíte** existující cluster AKS, podporujeme všechny aktuálně podporované verze AKS.

> [!NOTE]
> Mohou nastat hraniční případy, kdy máte starší cluster, který již není podporován. V takovém případě operace připojení vrátí chybu a zobrazí seznam aktuálně podporovaných verzí.
>
> Můžete připojit verze **Preview** . Funkce Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Podpora používání verze Preview může být omezená. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="available-and-default-versions"></a>Dostupné a výchozí verze

K vyhledání dostupných a výchozích verzí AKS použijte příkaz [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) [AZ AKS get-versions](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_get_versions). Například následující příkaz vrátí verze, které jsou k dispozici v Západní USA oblasti:

```azurecli-interactive
az aks get-versions -l westus -o table
```

Výstup tohoto příkazu je podobný následujícímu textu:

```text
KubernetesVersion    Upgrades
-------------------  ----------------------------------------
1.18.6(preview)      None available
1.18.4(preview)      1.18.6(preview)
1.17.9               1.18.4(preview), 1.18.6(preview)
1.17.7               1.17.9, 1.18.4(preview), 1.18.6(preview)
1.16.13              1.17.7, 1.17.9
1.16.10              1.16.13, 1.17.7, 1.17.9
1.15.12              1.16.10, 1.16.13
1.15.11              1.15.12, 1.16.10, 1.16.13
```

Pokud chcete najít výchozí verzi, která se používá při **vytváření** clusteru prostřednictvím Azure Machine Learning, můžete `--query` k výběru výchozí verze použít parametr:

```azurecli-interactive
az aks get-versions -l westus --query "orchestrators[?default == `true`].orchestratorVersion" -o table
```

Výstup tohoto příkazu je podobný následujícímu textu:

```text
Result
--------
1.16.13
```

Pokud byste chtěli **programově kontrolovat dostupné verze**, použijte [orchestraci seznamu klient-seznam klienta služby Container Service](/rest/api/container-service/container%20service%20client/listorchestrators) REST API. Pokud chcete zjistit dostupné verze, podívejte se na položky, kde `orchestratorType` je `Kubernetes` . Přidružené `orchestrationVersion` položky obsahují dostupné verze, které je možné **připojit** k vašemu pracovnímu prostoru.

Pokud chcete najít výchozí verzi, která se používá při **vytváření** clusteru prostřednictvím Azure Machine Learning, Najděte položku, kde `orchestratorType` je `Kubernetes` a `default` `true` . Přidružená `orchestratorVersion` hodnota je výchozí verze. Následující fragment kódu JSON ukazuje příklad položky:

```json
...
 {
        "orchestratorType": "Kubernetes",
        "orchestratorVersion": "1.16.13",
        "default": true,
        "upgrades": [
          {
            "orchestratorType": "",
            "orchestratorVersion": "1.17.7",
            "isPreview": false
          }
        ]
      },
...
```

## <a name="create-a-new-aks-cluster"></a>Vytvoření nového clusteru AKS

**Časový odhad**: přibližně 10 minut.

Vytvoření nebo připojení clusteru AKS je jednorázový proces pro váš pracovní prostor. Tento cluster můžete použít pro více nasazení. Pokud odstraníte cluster nebo skupinu prostředků, která ho obsahuje, musíte při příštím nasazení vytvořit nový cluster. K vašemu pracovnímu prostoru můžete připojit více clusterů AKS.

Následující příklad ukazuje, jak vytvořit nový cluster AKS pomocí sady SDK a rozhraní příkazového řádku:

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

# Example configuration to use an existing virtual network
# prov_config.vnet_name = "mynetwork"
# prov_config.vnet_resourcegroup_name = "mygroup"
# prov_config.subnet_name = "default"
# prov_config.service_cidr = "10.0.0.0/16"
# prov_config.dns_service_ip = "10.0.0.10"
# prov_config.docker_bridge_cidr = "172.17.0.1/16"

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

Další informace o třídách, metodách a parametrech použitých v tomto příkladu naleznete v následujících referenčních dokumentech:

* [AksCompute.ClusterPurpose](/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?preserve-view=true&view=azure-ml-py)
* [AksCompute.provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py&preserve-view=true#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget. Create](/python/api/azureml-core/azureml.core.compute.computetarget?preserve-view=true&view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget.wait_for_completion](/python/api/azureml-core/azureml.core.compute.computetarget?preserve-view=true&view=azure-ml-py#wait-for-completion-show-output-false-)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az ml computetarget create aks -n myaks
```

Další informace najdete v tématu [AZ ml computetarget Create AKS](/cli/azure/ext/azure-cli-ml/ml/computetarget/create?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks) reference.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Informace o vytvoření clusteru AKS na portálu najdete v tématu [Vytvoření výpočetních cílů v Azure Machine Learning Studiu](how-to-create-attach-compute-studio.md#inference-clusters).

---

## <a name="attach-an-existing-aks-cluster"></a>Připojit existující cluster AKS

**Časový odhad:** Přibližně 5 minut.

Pokud už máte cluster AKS ve svém předplatném Azure a verze 1,17 nebo nižší, můžete ho použít k nasazení image.

> [!TIP]
> Stávající cluster AKS může být v jiné oblasti Azure, než je váš pracovní prostor Azure Machine Learning.


> [!WARNING]
> Nevytvářejte více souběžných příloh ke stejnému AKS clusteru z vašeho pracovního prostoru. Například připojení jednoho clusteru AKS k pracovnímu prostoru pomocí dvou různých názvů. Každá nová příloha zruší předchozí existující přílohy.
>
> Pokud chcete cluster AKS znovu připojit, například pokud chcete změnit nastavení TLS nebo jiné konfigurace clusteru, musíte nejdřív odebrat existující přílohu pomocí [AksCompute. detach ()](/python/api/azureml-core/azureml.core.compute.akscompute?preserve-view=true&view=azure-ml-py#detach--).

Další informace o vytvoření clusteru AKS pomocí Azure CLI nebo portálu najdete v následujících článcích:

* [Vytvoření clusteru AKS (rozhraní příkazového řádku)](/cli/azure/aks?bc=%2fazure%2fbread%2ftoc.json&preserve-view=true&toc=%2fazure%2faks%2fTOC.json&view=azure-cli-latest#az-aks-create)
* [Vytvoření clusteru AKS (portál)](../aks/kubernetes-walkthrough-portal.md?preserve-view=true&view=azure-cli-latest)
* [Vytvoření clusteru AKS (šablona ARM v šablonách rychlého startu Azure)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aks-azml-targetcompute)

Následující příklad ukazuje, jak připojit existující cluster AKS k vašemu pracovnímu prostoru:

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'myexistingcluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'myaks', attach_config)

# Wait for the attach process to complete
aks_target.wait_for_completion(show_output = True)
```

Další informace o třídách, metodách a parametrech použitých v tomto příkladu naleznete v následujících referenčních dokumentech:

* [AksCompute.attach_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py&preserve-view=true#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?preserve-view=true&view=azure-ml-py)
* [AksCompute. Attach](/python/api/azureml-core/azureml.core.compute.computetarget?preserve-view=true&view=azure-ml-py#attach-workspace--name--attach-configuration-)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete připojit existující cluster pomocí rozhraní příkazového řádku, musíte získat ID prostředku pro existující cluster. Tuto hodnotu získáte pomocí následujícího příkazu. Nahraďte `myexistingcluster` názvem vašeho clusteru AKS. Nahraďte `myresourcegroup` skupinou prostředků, která obsahuje cluster:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

Tento příkaz vrátí hodnotu podobnou následujícímu textu:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Pokud chcete připojit existující cluster k pracovnímu prostoru, použijte následující příkaz. Nahraďte `aksresourceid` hodnotou vrácenou předchozím příkazem. Nahraďte `myresourcegroup` skupinou prostředků, která obsahuje váš pracovní prostor. Nahraďte `myworkspace` názvem vašeho pracovního prostoru.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

Další informace najdete v referenčních informacích [AZ ml computetarget Attach AKS](/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks) .

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Informace o připojení clusteru AKS na portálu najdete v tématu [Vytvoření výpočetních cílů v Azure Machine Learning Studiu](how-to-create-attach-compute-studio.md#inference-clusters).

---

## <a name="create-or-attach-an-aks-cluster-with-tls-termination"></a>Vytvoření nebo připojení clusteru AKS s ukončením TLS
Když [vytvoříte nebo připojíte cluster AKS](how-to-create-attach-kubernetes.md), můžete povolit ukončení protokolu TLS s objekty konfigurace **[AksCompute.provisioning_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py&preserve-view=true#&preserve-view=trueprovisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)** a **[AksCompute.attach_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py&preserve-view=true#&preserve-view=trueattach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** . Obě metody vrátí objekt konfigurace, který má metodu **Enable_ssl** , a můžete použít metodu **Enable_ssl** k povolení protokolu TLS.

Následující příklad ukazuje, jak povolit ukončení protokolu TLS s automatickým generováním a konfigurací certifikátů TLS pomocí certifikátu společnosti Microsoft v digestoři.
```python
   from azureml.core.compute import AksCompute, ComputeTarget
   
   # Enable TLS termination when you create an AKS cluster by using provisioning_config object enable_ssl method

   # Leaf domain label generates a name using the formula
   # "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
   # where "######" is a random series of characters
   provisioning_config.enable_ssl(leaf_domain_label = "contoso")
   
   # Enable TLS termination when you attach an AKS cluster by using attach_config object enable_ssl method

   # Leaf domain label generates a name using the formula
   # "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
   # where "######" is a random series of characters
   attach_config.enable_ssl(leaf_domain_label = "contoso")


```
Následující příklad ukazuje, jak povolit ukončení protokolu TLS s vlastním certifikátem a vlastním názvem domény. S vlastní doménou a certifikátem je potřeba aktualizovat si záznam DNS tak, aby odkazoval na IP adresu koncového bodu, viz [aktualizace DNS](how-to-secure-web-service.md#update-your-dns) .

```python
   from azureml.core.compute import AksCompute, ComputeTarget

   # Enable TLS termination with custom certificate and custom domain when creating an AKS cluster
   
   provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    
   # Enable TLS termination with custom certificate and custom domain when attaching an AKS cluster

   attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")


```
>[!NOTE]
> Další informace o tom, jak zabezpečit nasazení modelu v clusteru AKS, najdete v tématu [použití protokolu TLS k zabezpečení webové služby prostřednictvím Azure Machine Learning](how-to-secure-web-service.md)

## <a name="create-or-attach-an-aks-cluster-to-use-internal-load-balancer-with-private-ip"></a>Vytvoření nebo připojení clusteru AKS pro použití interních Load Balancer s privátní IP adresou
Když vytvoříte nebo připojíte cluster AKS, můžete nakonfigurovat cluster tak, aby používal interní Load Balancer. Pomocí interního Load Balancer budou koncové body bodování pro vaše nasazení do AKS používat privátní IP adresu v rámci virtuální sítě. Následující fragmenty kódu ukazují, jak nakonfigurovat interní Load Balancer pro cluster AKS.
```python
   
   from azureml.core.compute.aks import AksUpdateConfiguration
   from azureml.core.compute import AksCompute, ComputeTarget
   
   # When you create an AKS cluster, you can specify Internal Load Balancer to be created with provisioning_config object
   provisioning_config = AksCompute.provisioning_configuration(load_balancer_type = 'InternalLoadBalancer')

   # when you attach an AKS cluster, you can update the cluster to use internal load balancer after attach
   aks_target = AksCompute(ws,"myaks")

   # Change to the name of the subnet that contains AKS
   subnet_name = "default"
   # Update AKS configuration to use an internal load balancer
   update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
   aks_target.update(update_config)
   # Wait for the operation to complete
   aks_target.wait_for_completion(show_output = True)
   
   
```
>[!IMPORTANT]
> Azure Machine Learning nepodporuje ukončení protokolu TLS s interním Load Balancer. Interní Load Balancer má privátní IP adresu a privátní IP adresa by mohla být v jiné síti a certifikát může být recused. 

>[!NOTE]
> Další informace o tom, jak zabezpečit prostředí Inferencing, najdete v tématu [zabezpečení prostředí Azure Machine Learning Inferencing](how-to-secure-inferencing-vnet.md) .

## <a name="detach-an-aks-cluster"></a>Odpojení clusteru AKS

Pokud chcete odpojit cluster od pracovního prostoru, použijte jednu z následujících metod:

> [!WARNING]
> Pomocí sady Azure Machine Learning Studio, sady SDK nebo rozšíření Azure CLI pro Machine Learning k odpojení clusteru AKS **neodstraňujte cluster AKS**. Pokud chcete cluster odstranit, přečtěte si téma [použití Azure CLI se službou AKS](../aks/kubernetes-walkthrough.md#delete-the-cluster).

# <a name="python"></a>[Python](#tab/python)

```python
aks_target.detach()
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete odpojit stávající cluster k vašemu pracovnímu prostoru, použijte následující příkaz. Nahraďte `myaks` názvem, který je připojen ke svému pracovnímu prostoru cluster AKS, jako. Nahraďte `myresourcegroup` skupinou prostředků, která obsahuje váš pracovní prostor. Nahraďte `myworkspace` názvem vašeho pracovního prostoru.

```azurecli
az ml computetarget detach -n myaks -g myresourcegroup -w myworkspace
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

V Azure Machine Learning Studiu vyberte __výpočetní__ prostředí, __odvození clusterů__ a cluster, který chcete odebrat. K odpojení clusteru použijte odkaz __Odpojit__ .

---

## <a name="troubleshooting"></a>Řešení potíží
### <a name="update-the-cluster"></a>Aktualizace clusteru

Aktualizace komponent Azure Machine Learning nainstalovaných v clusteru služby Azure Kubernetes se musí použít ručně. 

Tyto aktualizace můžete použít tak, že cluster odpojíte z pracovního prostoru Azure Machine Learning a pak cluster znovu připojíte k pracovnímu prostoru. Pokud je v clusteru povolený protokol TLS, budete muset při opětovném připojení clusteru dodat certifikát TLS/SSL a privátní klíč. 

```python
compute_target = ComputeTarget(workspace=ws, name=clusterWorkspaceName)
compute_target.detach()
compute_target.wait_for_completion(show_output=True)

attach_config = AksCompute.attach_configuration(resource_group=resourceGroup, cluster_name=kubernetesClusterName)

## If SSL is enabled.
attach_config.enable_ssl(
    ssl_cert_pem_file="cert.pem",
    ssl_key_pem_file="key.pem",
    ssl_cname=sslCname)

attach_config.validate_configuration()

compute_target = ComputeTarget.attach(workspace=ws, name=args.clusterWorkspaceName, attach_configuration=attach_config)
compute_target.wait_for_completion(show_output=True)
```

Pokud již nemáte certifikát TLS/SSL a privátní klíč nebo používáte certifikát vygenerovaný Azure Machine Learning, můžete načíst soubory před odpojením clusteru, a to tak, že se připojíte ke clusteru pomocí `kubectl` a načtete tajný klíč `azuremlfessl` .

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes ukládá tajné klíče ve formátu kódování Base-64. `cert.pem` `key.pem` Před poskytnutím těchto tajných kódů bude nutné základní-64 dekódovat `attach_config.enable_ssl` . 

### <a name="webservice-failures"></a>Selhání webové služby

Mnoho selhání webové služby v AKS se dá ladit tak, že se připojí ke clusteru pomocí `kubectl` . Cluster AKS můžete získat `kubeconfig.json` spuštěním

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="next-steps"></a>Další kroky

* [Použití Azure RBAC pro autorizaci Kubernetes](../aks/manage-azure-rbac.md)
* [Jak a kde nasadit model](how-to-deploy-and-where.md)
* [Nasazení modelu do clusteru služby Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md)