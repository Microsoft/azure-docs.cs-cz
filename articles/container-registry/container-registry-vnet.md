---
title: Omezení přístupu pomocí koncového bodu služby
description: Omezte přístup ke službě Azure Container Registry pomocí koncového bodu služby ve službě Azure Virtual Network. Přístup ke koncovému bodu služby je funkcí úrovně Premium Service.
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: 8a67a011c75a192df9ad3460458fd766b5ec1ec1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773457"
---
# <a name="restrict-access-to-a-container-registry-using-a-service-endpoint-in-an-azure-virtual-network"></a>Omezení přístupu k registru kontejneru pomocí koncového bodu služby ve službě Azure Virtual Network

[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) poskytuje zabezpečenou privátní síť pro vaše Azure a místní prostředky. [Koncový bod služby](../virtual-network/virtual-network-service-endpoints-overview.md) umožňuje zabezpečení veřejné IP adresy registru kontejneru jenom na vaši virtuální síť. Tento koncový bod poskytuje přenos optimální trasy k prostředku prostřednictvím páteřní sítě Azure. Identity virtuální sítě a podsítě jsou také přenášeny s každým požadavkem.

Tento článek ukazuje, jak nakonfigurovat koncový bod služby registru kontejneru (Preview) ve virtuální síti. 

> [!IMPORTANT]
> Azure Container Registry teď podporuje [privátní propojení Azure](container-registry-private-link.md)a povoluje umístění privátních koncových bodů z virtuální sítě do registru. Privátní koncové body jsou přístupné z virtuální sítě pomocí privátních IP adres. Ve většině síťových scénářů doporučujeme použít místo koncových bodů služby soukromé koncové body.

Konfigurace koncového bodu služby registru je dostupná na úrovni služby **Premium** Registry Service. Informace o úrovních a omezeních služby registru najdete v tématu [Azure Container Registry úrovně služeb](container-registry-skus.md).

## <a name="preview-limitations"></a>Omezení verze Preview

* Budoucí vývoj koncových bodů služby pro Azure Container Registry není aktuálně plánován. Místo toho doporučujeme použít [soukromé koncové body](container-registry-private-link.md) .
* Nemůžete použít Azure Portal ke konfiguraci koncových bodů služby v registru.
* Jenom cluster [služby Azure Kubernetes](../aks/intro-kubernetes.md) nebo [virtuální počítač](../virtual-machines/linux/overview.md) Azure se dá použít jako hostitel pro přístup k registru kontejneru pomocí koncového bodu služby. *Další služby Azure, včetně Azure Container Instances, nejsou podporované.*
* Koncové body služby pro Azure Container Registry nejsou podporované v cloudu Azure USA nebo v cloudu Azure Čína.

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="prerequisites"></a>Požadavky

* K používání kroků Azure CLI v tomto článku se vyžaduje Azure CLI verze 2.0.58 nebo novější. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli].

* Pokud ještě nemáte registr kontejnerů, vytvořte ho (je potřeba Premium úrovně) a nahrajte do něj ukázkovou image, jako je například `hello-world` Docker Hub. K vytvoření registru použijte například [Azure Portal][quickstart-portal] nebo rozhraní příkazového [řádku Azure][quickstart-cli] . 

* Pokud chcete omezit přístup k registru pomocí koncového bodu služby v jiném předplatném Azure, zaregistrujte poskytovatele prostředků pro Azure Container Registry v tomto předplatném. Například:

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="configure-network-access-for-registry"></a>Konfigurace přístupu k síti pro registr

V této části nakonfigurujete registr kontejneru tak, aby povoloval přístup z podsítě ve službě Azure Virtual Network. Kroky jsou k dispozici pomocí Azure CLI.

### <a name="add-a-service-endpoint-to-a-subnet"></a>Přidání koncového bodu služby do podsítě

Když vytvoříte virtuální počítač, Azure ve výchozím nastavení vytvoří virtuální síť ve stejné skupině prostředků. Název virtuální sítě je založený na názvu virtuálního počítače. Například pokud pojmenujete virtuální počítač *myDockerVM*, výchozí název virtuální sítě je *myDockerVMVNET* s podsítí s názvem *myDockerVMSubnet*. Ověřte to pomocí příkazu [AZ Network VNet list][az-network-vnet-list] :

```azurecli
az network vnet list \
  --resource-group myResourceGroup \
  --query "[].{Name: name, Subnet: subnets[0].name}"
```

Výstup:

```console
[
  {
    "Name": "myDockerVMVNET",
    "Subnet&quot;: &quot;myDockerVMSubnet"
  }
]
```

Pomocí příkazu [AZ Network VNet Subnet Update][az-network-vnet-subnet-update] přidejte do své podsítě koncový bod služby **Microsoft. ContainerRegistry** . V následujícím příkazu nahraďte názvy vaší virtuální sítě a podsítě:

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Pomocí příkazu [AZ Network VNet Subnet show show][az-network-vnet-subnet-show] načtěte ID prostředku podsítě. Budete ho potřebovat v pozdějším kroku ke konfiguraci pravidla přístupu k síti.

```azurecli
az network vnet subnet show \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --query "id"
  --output tsv
``` 

Výstup:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

### <a name="change-default-network-access-to-registry"></a>Změna výchozího přístupu k síti do registru

Ve výchozím nastavení umožňuje služba Azure Container Registry připojení z hostitelů v libovolné síti. Chcete-li omezit přístup k vybrané síti, změňte výchozí akci na odepřít přístup. Název registru nahraďte následujícím příkazem [AZ ACR Update][az-acr-update] :

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

### <a name="add-network-rule-to-registry"></a>Přidat síťové pravidlo do registru

Pomocí příkazu [AZ ACR Network-Rule Add][az-acr-network-rule-add] přidejte do registru síťové pravidlo, které umožňuje přístup z podsítě virtuálního počítače. V následujícím příkazu nahraďte název registru kontejneru a ID prostředku podsítě: 

 ```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --subnet <subnet-resource-id>
```

## <a name="verify-access-to-the-registry"></a>Ověřte přístup k registru.

Až několik minut počkejte, než se konfigurace aktualizuje, ověřte, že virtuální počítač má přístup k registru kontejneru. Vytvořte připojení SSH k vašemu VIRTUÁLNÍmu počítači a spuštěním příkazu [AZ ACR Login][az-acr-login] se přihlaste k vašemu registru. 

```bash
az acr login --name mycontainerregistry
```

Můžete provést operace v registru, jako je třeba spustit `docker pull` , a načíst z registru ukázkový obraz. Nahraďte image a hodnotu značky, která je vhodná pro váš registr, s předponou názvu přihlašovacího serveru registru (všechna malá písmena):

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker úspěšně načte image do virtuálního počítače.

Tento příklad ukazuje, že máte přístup k privátnímu registru kontejneru prostřednictvím pravidla přístupu k síti. K registru ale nelze přistupovat z hostitele přihlášení, který nemá nakonfigurováno pravidlo přístupu k síti. Pokud se pokusíte přihlásit z jiného hostitele pomocí `az acr login` příkazu nebo `docker login` příkazu, výstup je podobný následujícímu:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Obnovit výchozí přístup k registru

Pokud chcete obnovit registr tak, aby byl ve výchozím nastavení povolený přístup, odeberte všechna nakonfigurovaná Síťová pravidla. Pak nastavte výchozí akci na povolený přístup. 

### <a name="remove-network-rules"></a>Odebrat Síťová pravidla

Chcete-li zobrazit seznam síťových pravidel nakonfigurovaných pro váš registr, spusťte následující příkaz [AZ ACR Network-Rule list][az-acr-network-rule-list] :

```azurecli
az acr network-rule list --name mycontainerregistry 
```

Pro každé konfigurované pravidlo spusťte příkaz [AZ ACR Network-Rule Remove][az-acr-network-rule-remove] a odeberte ho. Například:

```azurecli
# Remove a rule that allows access for a subnet. Substitute the subnet resource ID.

az acr network-rule remove \
  --name mycontainerregistry \
  --subnet /subscriptions/ \
  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

### <a name="allow-access"></a>Allow access

Název registru nahraďte následujícím příkazem [AZ ACR Update][az-acr-update] :
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste vytvořili všechny prostředky Azure ve stejné skupině prostředků a už je nepotřebujete, můžete je případně odstranit pomocí jednoho příkazu [AZ Group Delete](/cli/azure/group) :

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

* Pokud chcete omezit přístup k registru pomocí privátního koncového bodu ve virtuální síti, přečtěte si téma [konfigurace privátního odkazu Azure pro službu Azure Container Registry](container-registry-private-link.md).
* Pokud potřebujete nastavit pravidla přístupu k registru z za bránou firewall klienta, přečtěte si téma [Konfigurace pravidel pro přístup ke službě Azure Container Registry za bránou firewall](container-registry-firewall-access-rules.md).


<!-- IMAGES -->

[acr-subnet-service-endpoint]: ./media/container-registry-vnet/acr-subnet-service-endpoint.png


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-acr-repository-show]: /cli/azure/acr/repository#az_acr_repository_show
[az-acr-repository-list]: /cli/azure/acr/repository#az_acr_repository_list
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az_acr_network_rule_add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az_acr_network_rule_remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az_acr_network_rule_list
[az-acr-run]: /cli/azure/acr#az_acr_run
[az-acr-update]: /cli/azure/acr#az_acr_update
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-vm-create]: /cli/azure/vm#az_vm_create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az_network_vnet_subnet_show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az_network_vnet_subnet_update
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az_network_vnet_subnet_show
[az-network-vnet-list]: /cli/azure/network/vnet/#az_network_vnet_list
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
