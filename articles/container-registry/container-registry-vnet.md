---
title: Omezení přístupu pomocí virtuální sítě
description: Povolte přístup ke službě Azure Container Registry jenom z prostředků ve službě Azure Virtual Network nebo z rozsahů veřejných IP adres.
ms.topic: article
ms.date: 07/01/2019
ms.openlocfilehash: a6b89b074c25ea0948597ede7e5681b100c7f429
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "74454344"
---
# <a name="restrict-access-to-an-azure-container-registry-using-an-azure-virtual-network-or-firewall-rules"></a>Omezení přístupu ke službě Azure Container Registry pomocí virtuální sítě Azure nebo pravidel brány firewall

[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) poskytuje zabezpečenou privátní síť pro vaše Azure a místní prostředky. Omezením přístupu k privátní službě Azure Container Registry z Azure Virtual Network zajistíte, aby se do registru přistupují pouze prostředky ve virtuální síti. U různých scénářů můžete také nakonfigurovat pravidla brány firewall tak, aby povolovala přístup k registru jenom z konkrétních IP adres.

Tento článek popisuje dva scénáře konfigurace příchozího pravidla přístupu k síti v registru kontejnerů: z virtuálního počítače nasazeného ve virtuální síti nebo z veřejné IP adresy virtuálního počítače.

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview a [platí některá omezení](#preview-limitations). Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.
>

Pokud místo toho potřebujete nastavit pravidla přístupu pro prostředky, abyste dosáhli registru kontejneru za bránou firewall, přečtěte si téma [Konfigurace pravidel pro přístup ke službě Azure Container Registry za bránou firewall](container-registry-firewall-access-rules.md).


## <a name="preview-limitations"></a>Omezení verze Preview

* S pravidly přístupu k síti se dá nakonfigurovat jenom registr kontejnerů **Premium** . Informace o úrovních služby registru najdete v tématu [Azure Container Registry SKU](container-registry-skus.md). 

* Jenom cluster [služby Azure Kubernetes](../aks/intro-kubernetes.md) nebo [virtuální počítač](../virtual-machines/linux/overview.md) Azure se dá použít jako hostitel pro přístup k registru kontejneru ve virtuální síti. *Další služby Azure, včetně Azure Container Instances, se v tuto chvíli nepodporují.*

* Operace [ACR Tasks](container-registry-tasks-overview.md) nejsou aktuálně podporovány v registru kontejneru, který je k dispozici ve virtuální síti.

* Každý registr podporuje maximálně 100 pravidel virtuální sítě.

## <a name="prerequisites"></a>Požadavky

* K používání kroků Azure CLI v tomto článku se vyžaduje Azure CLI verze 2.0.58 nebo novější. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli].

* Pokud ještě nemáte registr kontejneru, vytvořte si ho (vyžaduje se Premium SKU) a nahrajte do něj ukázkovou image `hello-world` , jako je například Docker Hub. K vytvoření registru použijte například [Azure Portal][quickstart-portal] nebo rozhraní příkazového [řádku Azure][quickstart-cli] . 

* Pokud chcete omezit přístup k registru pomocí virtuální sítě v jiném předplatném Azure, musíte zaregistrovat poskytovatele prostředků pro Azure Container Registry v tomto předplatném. Příklad:

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

## <a name="about-network-rules-for-a-container-registry"></a>O síťových pravidlech pro Registry kontejneru

Služba Azure Container Registry ve výchozím nastavení přijímá připojení přes Internet z hostitelů v libovolné síti. Pomocí virtuální sítě můžete pro zabezpečený přístup k registru použít jenom prostředky Azure, jako je cluster AKS nebo virtuální počítač Azure, aniž byste museli přepracovat přes hranici sítě. Můžete také nakonfigurovat pravidla brány firewall sítě tak, aby povolovala jenom specifické rozsahy veřejných internetových IP adres. 

Chcete-li omezit přístup k registru, nejprve změňte výchozí akci registru tak, aby odepřela všechna síťová připojení. Pak přidejte pravidla přístupu k síti. Klienti s uděleným přístupem přes Síťová pravidla musí pokračovat [v ověřování v registru kontejnerů](https://docs.microsoft.com/azure/container-registry/container-registry-authentication) a mít oprávnění k přístupu k datům.

### <a name="service-endpoint-for-subnets"></a>Koncový bod služby pro podsítě

Pro povolení přístupu z podsítě ve virtuální síti musíte přidat [koncový bod služby](../virtual-network/virtual-network-service-endpoints-overview.md) pro službu Azure Container Registry. 

Víceklientské služby, jako je Azure Container Registry, používají jednu sadu IP adres pro všechny zákazníky. Koncový bod služby přiřadí koncový bod pro přístup k registru. Tento koncový bod poskytuje přenos optimální trasy k prostředku prostřednictvím páteřní sítě Azure. Identity virtuální sítě a podsítě jsou také přenášeny s každým požadavkem.

### <a name="firewall-rules"></a>Pravidla brány firewall

V případě pravidel sítě IP zadejte povolené rozsahy internetových adres pomocí zápisu CIDR, jako je například *16.17.18.0/24* nebo jednotlivé IP adresy, jako je *16.17.18.19*. Pravidla sítě IP jsou povolená jenom pro *veřejné* internetové IP adresy. Rozsahy IP adres rezervované pro privátní sítě (definované v dokumentu RFC 1918) nejsou povolené v pravidlech protokolu IP.

## <a name="create-a-docker-enabled-virtual-machine"></a>Vytvoření virtuálního počítače s podporou Docker

V tomto článku pro přístup ke službě Azure Container Registry použijte virtuální počítač Ubuntu s podporou Docker. Pokud chcete v registru použít Azure Active Directory ověřování, nainstalujte také rozhraní příkazového [řádku Azure CLI][azure-cli] na virtuálním počítači. Pokud už máte virtuální počítač Azure, přeskočte tento krok vytvoření.

Můžete použít stejnou skupinu prostředků pro váš virtuální počítač a registr kontejneru. Tato instalace zjednodušuje vyčištění na konci, ale nevyžaduje se. Pokud se rozhodnete vytvořit samostatnou skupinu prostředků pro virtuální počítač a virtuální síť, spusťte příkaz [AZ Group Create][az-group-create]. Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *westcentralus* :

```azurecli
az group create --name myResourceGroup --location westus
```

Teď nasaďte výchozí Ubuntu virtuální počítač Azure pomocí [AZ VM Create][az-vm-create]. Následující příklad vytvoří virtuální počítač s názvem *myDockerVM*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Vytvoření virtuálního počítače trvá několik minut. Po dokončení příkazu si poznamenejte `publicIpAddress` zobrazené v Azure CLI. Tato adresa slouží k vytvoření připojení SSH k virtuálnímu počítači a volitelně pro pozdější nastavení pravidel brány firewall.

### <a name="install-docker-on-the-vm"></a>Instalace Docker na virtuálním počítači

Po spuštění virtuálního počítače vytvořte připojení SSH k virtuálnímu počítači. Nahraďte *publicIpAddress* veřejnou IP adresou vašeho virtuálního počítače.

```bash
ssh azureuser@publicIpAddress
```

Spusťte následující příkaz pro instalaci Docker na virtuálním počítači s Ubuntu:

```bash
sudo apt install docker.io -y
```

Po instalaci spusťte následující příkaz, který ověří, jestli je na virtuálním počítači správně spuštěný Docker:

```bash
sudo docker run -it hello-world
```

Výstup:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Instalace rozhraní příkazového řádku Azure CLI

Podle postupu v části [instalace Azure CLI pomocí apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) nainstalujte rozhraní příkazového řádku Azure do svého virtuálního počítače s Ubuntu. V tomto článku se ujistěte, že instalujete verzi 2.0.58 nebo novější.

Ukončete připojení SSH.

## <a name="allow-access-from-a-virtual-network"></a>Povolení přístupu z virtuální sítě

V této části nakonfigurujete registr kontejneru tak, aby povoloval přístup z podsítě ve službě Azure Virtual Network. K dispozici jsou ekvivalentní kroky pomocí Azure CLI a Azure Portal.

### <a name="allow-access-from-a-virtual-network---cli"></a>Povolení přístupu z virtuální sítě – rozhraní příkazového řádku

#### <a name="add-a-service-endpoint-to-a-subnet"></a>Přidání koncového bodu služby do podsítě

Když vytvoříte virtuální počítač, Azure ve výchozím nastavení vytvoří virtuální síť ve stejné skupině prostředků. Název virtuální sítě je založený na názvu virtuálního počítače. Například pokud pojmenujete virtuální počítač *myDockerVM*, výchozí název virtuální sítě je *myDockerVMVNET*s podsítí s názvem *myDockerVMSubnet*. Ověřte to ve Azure Portal nebo pomocí příkazu [AZ Network VNet list][az-network-vnet-list] :

```azurecli
az network vnet list --resource-group myResourceGroup --query "[].{Name: name, Subnet: subnets[0].name}"
```

Výstup:

```console
[
  {
    "Name": "myDockerVMVNET",
    "Subnet": "myDockerVMSubnet"
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

#### <a name="change-default-network-access-to-registry"></a>Změna výchozího přístupu k síti do registru

Ve výchozím nastavení umožňuje služba Azure Container Registry připojení z hostitelů v libovolné síti. Chcete-li omezit přístup k vybrané síti, změňte výchozí akci na odepřít přístup. Název registru nahraďte následujícím příkazem [AZ ACR Update][az-acr-update] :

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>Přidat síťové pravidlo do registru

Pomocí příkazu [AZ ACR Network-Rule Add][az-acr-network-rule-add] přidejte do registru síťové pravidlo, které umožňuje přístup z podsítě virtuálního počítače. V následujícím příkazu nahraďte název registru kontejneru a ID prostředku podsítě: 

 ```azurecli
az acr network-rule add --name mycontainerregistry --subnet <subnet-resource-id>
```

Pokračujte [v ověřování přístupu k registru](#verify-access-to-the-registry).

### <a name="allow-access-from-a-virtual-network---portal"></a>Povolení přístupu z virtuální sítě – portál

#### <a name="add-service-endpoint-to-subnet"></a>Přidat koncový bod služby do podsítě

Když vytvoříte virtuální počítač, Azure ve výchozím nastavení vytvoří virtuální síť ve stejné skupině prostředků. Název virtuální sítě je založený na názvu virtuálního počítače. Například pokud pojmenujete virtuální počítač *myDockerVM*, výchozí název virtuální sítě je *myDockerVMVNET*s podsítí s názvem *myDockerVMSubnet*.

Postup přidání koncového bodu služby pro Azure Container Registry do podsítě:

1. Do vyhledávacího pole v horní části [Azure Portal][azure-portal]zadejte *virtuální sítě*. Pokud se ve výsledcích hledání zobrazí **virtuální sítě** , vyberte ji.
1. V seznamu virtuálních sítí vyberte virtuální síť, ve které je nasazený virtuální počítač, například *myDockerVMVNET*.
1. V části **Nastavení**vyberte **podsítě**.
1. Vyberte podsíť, ve které je nasazený virtuální počítač, například *myDockerVMSubnet*.
1. V části **koncové body služby**vyberte **Microsoft. ContainerRegistry**.
1. Vyberte **Uložit**.

![Přidat koncový bod služby do podsítě][acr-subnet-service-endpoint] 

#### <a name="configure-network-access-for-registry"></a>Konfigurace přístupu k síti pro registr

Ve výchozím nastavení umožňuje služba Azure Container Registry připojení z hostitelů v libovolné síti. Chcete-li omezit přístup k virtuální síti:

1. Na portálu přejděte do registru kontejneru.
1. V části **Nastavení**vyberte **Brána firewall a virtuální sítě**.
1. Chcete-li odepřít přístup ve výchozím nastavení, vyberte možnost povolit přístup z **vybraných sítí**. 
1. Vyberte **Přidat existující virtuální síť**a vyberte virtuální síť a podsíť, kterou jste nakonfigurovali pomocí koncového bodu služby. Vyberte **Přidat**.
1. Vyberte **Uložit**.

![Konfigurace služby Virtual Network pro Registry kontejneru][acr-vnet-portal]

Pokračujte [v ověřování přístupu k registru](#verify-access-to-the-registry).

## <a name="allow-access-from-an-ip-address"></a>Povolení přístupu z IP adresy

V této části nakonfigurujte registr kontejneru tak, aby povoloval přístup z konkrétní IP adresy nebo rozsahu. K dispozici jsou ekvivalentní kroky pomocí Azure CLI a Azure Portal.

### <a name="allow-access-from-an-ip-address---cli"></a>Povolení přístupu z IP adresy – CLI

#### <a name="change-default-network-access-to-registry"></a>Změna výchozího přístupu k síti do registru

Pokud jste to ještě neudělali, aktualizujte konfiguraci registru tak, aby odepřel přístup ve výchozím nastavení. Název registru nahraďte následujícím příkazem [AZ ACR Update][az-acr-update] :

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="remove-network-rule-from-registry"></a>Odebrat pravidlo sítě z registru

Pokud jste dříve přidali síťové pravidlo, které povoluje přístup z podsítě virtuálního počítače, odeberte koncový bod služby a síťové pravidlo pro danou podsíť. Nahraďte název registru kontejneru a ID prostředku podsítě, kterou jste získali v předchozím kroku, v příkazu [AZ ACR Network-Rule Remove][az-acr-network-rule-remove] : 

```azurecli
# Remove service endpoint

az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints ""

# Remove network rule

az acr network-rule remove --name mycontainerregistry --subnet <subnet-resource-id>
```

#### <a name="add-network-rule-to-registry"></a>Přidat síťové pravidlo do registru

Pomocí příkazu [AZ ACR Network-Rule Add][az-acr-network-rule-add] přidejte do svého registru síťové pravidlo, které umožňuje přístup z IP adresy virtuálního počítače. V následujícím příkazu nahraďte název registru kontejneru a veřejnou IP adresu virtuálního počítače.

```azurecli
az acr network-rule add --name mycontainerregistry --ip-address <public-IP-address>
```

Pokračujte [v ověřování přístupu k registru](#verify-access-to-the-registry).

### <a name="allow-access-from-an-ip-address---portal"></a>Povolení přístupu z IP adresy – portál

#### <a name="remove-existing-network-rule-from-registry"></a>Odebrat stávající síťové pravidlo z registru

Pokud jste dříve přidali síťové pravidlo, které povoluje přístup z podsítě virtuálního počítače, odeberte stávající pravidlo. Pokud chcete získat přístup k registru z jiného virtuálního počítače, přeskočte tuto část.

* Aktualizujte nastavení podsítě pro odebrání koncového bodu služby podsítě pro Azure Container Registry. 

  1. V [Azure Portal][azure-portal]přejděte do virtuální sítě, ve které je nasazený virtuální počítač.
  1. V části **Nastavení**vyberte **podsítě**.
  1. Vyberte podsíť, ve které je nasazený virtuální počítač.
  1. V části **koncové body služby**odeberte zaškrtávací políčko **Microsoft. ContainerRegistry**. 
  1. Vyberte **Uložit**.

* Odeberte pravidlo sítě, které umožňuje podsíti přístup k registru.

  1. Na portálu přejděte do registru kontejneru.
  1. V části **Nastavení**vyberte **Brána firewall a virtuální sítě**.
  1. V části **virtuální sítě**vyberte název virtuální sítě a pak vyberte **Odebrat**.
  1. Vyberte **Uložit**.

#### <a name="add-network-rule-to-registry"></a>Přidat síťové pravidlo do registru

1. Na portálu přejděte do registru kontejneru.
1. V části **Nastavení**vyberte **Brána firewall a virtuální sítě**.
1. Pokud jste to ještě neudělali, vyberte možnost povolení přístupu z **vybraných sítí**. 
1. V části **virtuální sítě**se ujistěte, že není vybraná žádná síť.
1. V části **Brána firewall**zadejte veřejnou IP adresu virtuálního počítače. Případně zadejte rozsah adres v zápisu CIDR, který obsahuje IP adresu virtuálního počítače.
1. Vyberte **Uložit**.

![Konfigurace pravidla brány firewall pro Registry kontejneru][acr-vnet-firewall-portal]

Pokračujte [v ověřování přístupu k registru](#verify-access-to-the-registry).

## <a name="verify-access-to-the-registry"></a>Ověřte přístup k registru.

Až několik minut počkejte, než se konfigurace aktualizuje, ověřte, že virtuální počítač má přístup k registru kontejneru. Vytvořte připojení SSH k vašemu VIRTUÁLNÍmu počítači a spuštěním příkazu [AZ ACR Login][az-acr-login] se přihlaste k vašemu registru. 

```bash
az acr login --name mycontainerregistry
```

Můžete provést operace v registru, jako je `docker pull` třeba spustit, a načíst z registru ukázkový obraz. Nahraďte image a hodnotu značky, která je vhodná pro váš registr, s předponou názvu přihlašovacího serveru registru (všechna malá písmena):

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker úspěšně načte image do virtuálního počítače.

Tento příklad ukazuje, že máte přístup k privátnímu registru kontejneru prostřednictvím pravidla přístupu k síti. K registru ale nelze přistupovat z jiného hostitele přihlášení, který nemá nakonfigurováno pravidlo přístupu k síti. Pokud se pokusíte přihlásit z jiného hostitele pomocí `az acr login` příkazu nebo `docker login` příkazu, výstup je podobný následujícímu:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Obnovit výchozí přístup k registru

Pokud chcete obnovit registr tak, aby byl ve výchozím nastavení povolený přístup, odeberte všechna nakonfigurovaná Síťová pravidla. Pak nastavte výchozí akci na povolený přístup. K dispozici jsou ekvivalentní kroky pomocí Azure CLI a Azure Portal.

### <a name="restore-default-registry-access---cli"></a>Obnovit výchozí přístup k registru – rozhraní příkazového řádku

#### <a name="remove-network-rules"></a>Odebrat Síťová pravidla

Chcete-li zobrazit seznam síťových pravidel nakonfigurovaných pro váš registr, spusťte následující příkaz [AZ ACR Network-Rule list][az-acr-network-rule-list] :

```azurecli
az acr network-rule list--name mycontainerregistry 
```

Pro každé konfigurované pravidlo spusťte příkaz [AZ ACR Network-Rule Remove][az-acr-network-rule-remove] a odeberte ho. Příklad:

```azurecli
# Remove a rule that allows access for a subnet. Substitute the subnet resource ID.

az acr network-rule remove \
  --name mycontainerregistry \
  --subnet /subscriptions/ \
  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet

# Remove a rule that allows access for an IP address or CIDR range such as 23.45.1.0/24.

az acr network-rule remove \
  --name mycontainerregistry \
  --ip-address 23.45.1.0/24
```

#### <a name="allow-access"></a>Allow access

Název registru nahraďte následujícím příkazem [AZ ACR Update][az-acr-update] :
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

### <a name="restore-default-registry-access---portal"></a>Obnovit výchozí přístup k registru – portál


1. Na portálu přejděte do registru kontejneru a vyberte možnost **Brána firewall a virtuální sítě**.
1. V části **virtuální sítě**vyberte každou virtuální síť a pak vyberte **Odebrat**.
1. V části **Brána firewall**vyberte rozsah adres a pak vyberte ikonu Odstranit.
1. V části **Povolení přístupu z**vyberte možnost **všechny sítě**. 
1. Vyberte **Uložit**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste vytvořili všechny prostředky Azure ve stejné skupině prostředků a už je nepotřebujete, můžete je případně odstranit pomocí jednoho příkazu [AZ Group Delete](/cli/azure/group) :

```azurecli
az group delete --name myResourceGroup
```

Pokud chcete prostředky vyčistit na portálu, přejděte do skupiny prostředků myResourceGroup. Po načtení skupiny prostředků klikněte na **Odstranit skupinu prostředků** a odeberte skupinu prostředků a prostředky, které jsou tam uložené.

## <a name="next-steps"></a>Další kroky

V tomto článku jsme popisovali několik prostředků virtuální sítě a funkcí, ale krátce. Dokumentace k Azure Virtual Network zahrnuje i rozsáhlá témata:

* [Virtuální síť](https://docs.microsoft.com/azure/virtual-network/manage-virtual-network)
* [Podsíť](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)
* [Koncové body služby](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

<!-- IMAGES -->

[acr-subnet-service-endpoint]: ./media/container-registry-vnet/acr-subnet-service-endpoint.png
[acr-vnet-portal]: ./media/container-registry-vnet/acr-vnet-portal.png
[acr-vnet-firewall-portal]: ./media/container-registry-vnet/acr-vnet-firewall-portal.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/r/microsoft/aci-helloworld/
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
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az-acr-network-rule-add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az-acr-network-rule-remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az-acr-network-rule-list
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-update]: /cli/azure/acr#az-acr-update
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
