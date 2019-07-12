---
title: Omezení přístupu k registru kontejneru Azure z virtuální sítě
description: Povolit přístup do služby Azure container registry pouze z prostředků ve virtuální síti Azure nebo z veřejné rozsahy IP adres.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/01/2019
ms.author: danlep
ms.openlocfilehash: e6e0cdd73a5a2999f78599a06cc7ee397ecc3b4b
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806594"
---
# <a name="restrict-access-to-an-azure-container-registry-using-an-azure-virtual-network-or-firewall-rules"></a>Omezení přístupu ke službě Azure container registry pomocí služby Azure virtual network nebo pravidla brány firewall

[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) poskytuje zabezpečené a privátní sítě pro Azure a místním prostředkům. Omezením přístupu pro váš privátní registr kontejnerů Azure z virtuální sítě Azure, zajistíte tím, že pouze prostředky ve virtuální síti přístup k registru. Pro scénáře, mezi různými místy můžete také nakonfigurovat pravidla brány firewall umožňující přístup k registru pouze z konkrétních IP adres.

Tento článek ukazuje dva scénáře, chcete-li vytvořit pravidla přístupu k síti a omezit tak přístup do služby Azure container registry: z virtuálním počítači nasazeném ve virtuální síti nebo z veřejné IP adresy Virtuálního počítače.

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi preview a některé [platí omezení](#preview-limitations). Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.
>

## <a name="preview-limitations"></a>Omezení verze Preview

* Pouze **Premium** registru kontejneru můžete nakonfigurovat pravidla přístupu k síti. Informace o úrovních služeb registru najdete v tématu [skladové jednotky Azure Container Registry](container-registry-skus.md). 

* Pouze [Azure Kubernetes Service](../aks/intro-kubernetes.md) clusteru nebo Azure [virtuálního počítače](../virtual-machines/linux/overview.md) slouží jako hostitel pro přístup k registru kontejneru ve službě virtual network. *Další služby Azure, včetně Azure Container Instances se aktuálně nepodporují.*

* [Úlohy služby ACR](container-registry-tasks-overview.md) operace nejsou aktuálně podporovány v registru kontejneru přistupovat ve virtuální síti.

* Každý registr podporuje maximálně 100 pravidel virtuální sítě.

## <a name="prerequisites"></a>Požadavky

* Použití Azure CLI kroky v tomto článku, Azure CLI verze 2.0.58 nebo později se vyžaduje. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli].

* Pokud ještě nemáte registr kontejnerů, vytvořte si ho (vyžaduje SKU úrovně Premium) a push Ukázkový obrázek, jako `hello-world` z Docker Hubu. Například použít [webu Azure portal][quickstart-portal] or the [Azure CLI][quickstart-cli] vytvořte registr. 

* Pokud chcete omezit přístup k registru pomocí virtuální sítě v rámci jiného předplatného Azure, musíte se zaregistrovat poskytovatele prostředků pro službu Azure Container Registry v tomto předplatném. Příklad:

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

## <a name="about-network-rules-for-a-container-registry"></a>O pravidlech sítě pro registr kontejneru

Služby Azure container registry ve výchozím nastavení akceptuje připojení přes internet z hostitelů s jakoukoli sítí. S virtuální sítí můžete povolit pouze Azure prostředky, například AKS cluster nebo virtuálního počítače Azure na bezpečný přístup k registru, bez přecházení mezi ohraničení sítě. Můžete také nakonfigurovat pravidla brány firewall sítě na seznam povolených konkrétní veřejného Internetu rozsahy IP adres. 

Omezení přístupu k registru, nejprve změňte výchozí akce registru tak, aby zakazuje všechna síťová připojení. Pak přidejte pravidla přístupu k síti. Klienti udělen přístup prostřednictvím pravidel sítě musí i nadále [ověřování registru kontejneru](https://docs.microsoft.com/azure/container-registry/container-registry-authentication) a oprávnění pro přístup k datům.

### <a name="service-endpoint-for-subnets"></a>Koncový bod služby pro podsítě

Pokud chcete povolit přístup z podsítě ve virtuální síti, je třeba přidat [koncový bod služby](../virtual-network/virtual-network-service-endpoints-overview.md) pro službu Azure Container Registry. 

Služeb s více tenanty, jako je Azure Container Registry, použijte jednu sadu IP adres pro všechny zákazníky. Koncový bod služby přiřadí koncový bod pro přístup k registru. Tento koncový bod poskytuje provoz optimální trasu k prostředku, přes páteřní sítě Azure. Identity virtuální síť a podsíť také přenášejí spolu s každou žádostí.

### <a name="firewall-rules"></a>Pravidla brány firewall

Pravidla protokolu IP sítě, zadejte povolenou internet rozsahy adres pomocí zápisu CIDR, například *16.17.18.0/24* nebo jednotlivé IP adresy jako *16.17.18.19*. Pravidla síťových IP jsou povolené jenom pro *veřejné* internetové IP adresy. Rozsahy IP adres vyhrazené pro privátní sítě (jak jsou definovány v dokumentu RFC 1918) nejsou povoleny v pravidlech IP.

## <a name="create-a-docker-enabled-virtual-machine"></a>Vytvoření virtuálního počítače s podporou Dockeru

Pro účely tohoto článku použijte virtuálního počítače se systémem Ubuntu s podporou Dockeru pro přístup k službě Azure container registry. Chcete-li použít ověřování pomocí Azure Active Directory k registru, také nainstalovat [rozhraní příkazového řádku Azure][azure-cli] na virtuálním počítači. Pokud už máte virtuální počítač Azure, přeskočte tento krok vytváření.

Můžete použít stejnou skupinu prostředků pro váš virtuální počítač a vašeho registru kontejneru. Tento instalační program usnadňuje vyčištění po uplynutí ale není povinné. Pokud budete chtít vytvořit samostatné skupiny prostředků pro virtuální počítač a virtuální sítě, spusťte [vytvořit skupiny az][az-group-create]. Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v *westcentralus* umístění:

```azurecli
az group create --name myResourceGroup --location westus
```

Teď nasadit výchozí virtuální počítač Azure s Ubuntu s [az vm vytvořit][az-vm-create]. Následující příklad vytvoří virtuální počítač s názvem *myDockerVM*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Vytvoření virtuálního počítače trvá několik minut. Po dokončení příkazu si poznamenejte `publicIpAddress` zobrazí rozhraní příkazového řádku Azure. Tuto adresu používejte k vytvoření připojení SSH k virtuálnímu počítači a volitelně pro pozdější nastavení pravidla brány firewall.

### <a name="install-docker-on-the-vm"></a>Nainstalovat Docker na virtuálním počítači

Po spuštění virtuálního počítače vytvořte připojení SSH k virtuálnímu počítači. Nahraďte *publicIpAddress* veřejnou IP adresou vašeho virtuálního počítače.

```bash
ssh azureuser@publicIpAddress
```

Spusťte následující příkaz k instalaci Dockeru ve virtuálním počítači se systémem Ubuntu:

```bash
sudo apt install docker.io -y
```

Po dokončení instalace spusťte následující příkaz k ověření, že je správně spuštěna Dockeru ve virtuálním počítači:

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

Postupujte podle kroků v [instalace Azure CLI pomocí apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) instalace rozhraní příkazového řádku Azure ve vašem virtuálním počítači se systémem Ubuntu. Pro účely tohoto článku, ujistěte se, že instalujete verzi 2.0.58 nebo novější.

Ukončete připojení SSH.

## <a name="allow-access-from-a-virtual-network"></a>Povolení přístupu z virtuální sítě

V této části konfigurace vašeho registru kontejneru, pokud chcete povolit přístup z podsítě ve virtuální síti Azure. Jsou k dispozici ekvivalentní kroky pomocí Azure CLI a webu Azure portal.

### <a name="allow-access-from-a-virtual-network---cli"></a>Povolení přístupu z virtuální sítě – rozhraní příkazového řádku

#### <a name="add-a-service-endpoint-to-a-subnet"></a>Přidání koncového bodu služby podsítě

Při vytváření virtuálního počítače Azure ve výchozím nastavení vytvoří virtuální síť ve stejné skupině prostředků. Název virtuální sítě je založen na názvu virtuálního počítače. Například, pokud je název vašeho virtuálního počítače *myDockerVM*, výchozí název virtuální sítě je *myDockerVMVNET*, s podsítí *myDockerVMSubnet*. Na webu Azure Portal nebo pomocí to ověřit [az network vnet seznamu][az-network-vnet-list] příkaz:

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

Použití [az network vnet podsíť aktualizace][az-network-vnet-subnet-update] příkaz pro přidání **Microsoft.ContainerRegistry** koncový bod služby pro vaši podsíť. Nahraďte názvy virtuální sítě a podsítě v následujícím příkazu:

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Použití [az network vnet podsíť zobrazit][az-network-vnet-subnet-show] příkaz, který načte ID prostředku podsítě. Budete ho potřebovat v pozdějším kroku, a nakonfigurujte pravidlo přístupu k síti.

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

#### <a name="change-default-network-access-to-registry"></a>Změnit výchozí síťový přístup k registru

Ve výchozím nastavení služby Azure container registry umožňuje připojení z hostitele s jakoukoli sítí. Omezení přístupu k vybrané síti, změňte výchozí akce pro odepření přístupu. Nahraďte názvem svého registru v následujícím [az acr update][az-acr-update] příkaz:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>Přidat pravidlo sítě do registru

Použití [az acr-pravidlo sítě přidat][az-acr-network-rule-add] příkaz pro přidání pravidlo sítě do vašeho registru, který umožňuje přístup z podsítě Virtuálních počítačů. Nahraďte názvem registru kontejneru a ID prostředku podsítě v následujícím příkazu: 

 ```azurecli
az acr network-rule add --name mycontainerregistry --subnet <subnet-resource-id>
```

I nadále [ověřit přístup k registru](#verify-access-to-the-registry).

### <a name="allow-access-from-a-virtual-network---portal"></a>Povolení přístupu z virtuální sítě – portál

#### <a name="add-service-endpoint-to-subnet"></a>Přidání koncového bodu služby podsítě

Při vytváření virtuálního počítače Azure ve výchozím nastavení vytvoří virtuální síť ve stejné skupině prostředků. Název virtuální sítě je založen na názvu virtuálního počítače. Například, pokud je název vašeho virtuálního počítače *myDockerVM*, výchozí název virtuální sítě je *myDockerVMVNET*, s podsítí *myDockerVMSubnet*.

Chcete-li přidat koncový bod služby pro službu Azure Container Registry k podsíti:

1. Do vyhledávacího pole v horní části [webu Azure portal][azure-portal], zadejte *virtuálních sítí*. Když **virtuální sítě** nezobrazí ve výsledcích hledání, vyberte ji.
1. Ze seznamu virtuálních sítí, vyberte virtuální síť, které váš virtuální počítač se nasadí, jako například *myDockerVMVNET*.
1. V části **nastavení**vyberte **podsítě**.
1. Vyberte podsíť, které váš virtuální počítač se nasadí, jako například *myDockerVMSubnet*.
1. V části **koncové body služby**vyberte **Microsoft.ContainerRegistry**.
1. Vyberte **Uložit**.

![Přidání koncového bodu služby podsítě][acr-subnet-service-endpoint] 

#### <a name="configure-network-access-for-registry"></a>Konfigurovat přístup k síti pro registr

Ve výchozím nastavení služby Azure container registry umožňuje připojení z hostitele s jakoukoli sítí. Chcete-li omezit přístup k virtuální síti:

1. Na portálu přejděte do vašeho registru kontejneru.
1. V části **nastavení**vyberte **brány Firewall a virtuální sítě**.
1. Ve výchozím nastavení odepření přístupu, zvolit povolení přístupu z **vybrané sítě**. 
1. Vyberte **přidat existující virtuální síť**a vyberte virtuální síť a podsíť nakonfigurovanou koncového bodu služby. Vyberte **Přidat**.
1. Vyberte **Uložit**.

![Konfigurace virtuální sítě pro registr kontejneru][acr-vnet-portal]

I nadále [ověřit přístup k registru](#verify-access-to-the-registry).

## <a name="allow-access-from-an-ip-address"></a>Povolit přístup z IP adresy

V této části konfigurace vašeho registru kontejneru, pokud chcete povolit přístup z konkrétní IP adresu nebo rozsah. Jsou k dispozici ekvivalentní kroky pomocí Azure CLI a webu Azure portal.

### <a name="allow-access-from-an-ip-address---cli"></a>Povolit přístup z IP adresy – rozhraní příkazového řádku

#### <a name="change-default-network-access-to-registry"></a>Změnit výchozí síťový přístup k registru

Pokud jste tak již neučinili, aktualizujte registr konfigurace k odepření přístupu ve výchozím nastavení. Nahraďte názvem svého registru v následujícím [az acr update][az-acr-update] příkaz:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="remove-network-rule-from-registry"></a>Odebrat pravidlo sítě z registru

Pokud jste přidali dříve, pravidlo sítě pro povolení přístupu z podsítě Virtuálních počítačů, odeberte koncového bodu služby podsítě a sítě pravidlo. Nahraďte názvem registru kontejneru a ID prostředku, který jste získali v dřívějším kroku v podsíti [az acr-pravidlo sítě odebrat][az-acr-network-rule-remove] příkaz: 

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

#### <a name="add-network-rule-to-registry"></a>Přidat pravidlo sítě do registru

Použití [az acr-pravidlo sítě přidat][az-acr-network-rule-add] příkaz pro přidání pravidlo sítě do vašeho registru, který umožňuje přístup z IP adresy Virtuálního počítače. Nahraďte názvem registru kontejneru a veřejnou IP adresu virtuálního počítače v následujícím příkazu.

```azurecli
az acr network-rule add --name mycontainerregistry --ip-address <public-IP-address>
```

I nadále [ověřit přístup k registru](#verify-access-to-the-registry).

### <a name="allow-access-from-an-ip-address---portal"></a>Povolit přístup z IP adresy – portál

#### <a name="remove-existing-network-rule-from-registry"></a>Odeberte existující pravidlo sítě z registru

Pokud jste přidali dříve, pravidlo sítě pro povolení přístupu z podsítě Virtuálních počítačů, odeberte existující pravidlo. Tuto část přeskočte, pokud chcete získat přístup k registru z různých virtuálních počítačů.

* Aktualizujte nastavení podsítě k odebrání koncového bodu služby podsítě pro službu Azure Container Registry. 

  1. V [webu Azure portal][azure-portal], přejděte na virtuální síť, ve kterém je nasazen virtuální počítač.
  1. V části **nastavení**vyberte **podsítě**.
  1. Vyberte podsíť, ve kterém je nasazen virtuální počítač.
  1. V části **koncové body služby**, odeberte zaškrtávací políčko pro **Microsoft.ContainerRegistry**. 
  1. Vyberte **Uložit**.

* Odeberte pravidlo sítě, která umožňuje podsítě pro přístup k registru.

  1. Na portálu přejděte do vašeho registru kontejneru.
  1. V části **nastavení**vyberte **brány Firewall a virtuální sítě**.
  1. V části **virtuální sítě**, vyberte název virtuální sítě a pak vyberte **odebrat**.
  1. Vyberte **Uložit**.

#### <a name="add-network-rule-to-registry"></a>Přidat pravidlo sítě do registru

1. Na portálu přejděte do vašeho registru kontejneru.
1. V části **nastavení**vyberte **brány Firewall a virtuální sítě**.
1. Pokud jste tak již neučinili, zvolit povolení přístupu z **vybrané sítě**. 
1. V části **virtuální sítě**, ujistěte se, vybraná žádná síť.
1. V části **brány Firewall**, zadejte veřejnou IP adresu virtuálního počítače. Nebo zadejte rozsah adres v zápisu CIDR, který obsahuje IP adresu Virtuálního počítače.
1. Vyberte **Uložit**.

![Konfigurace pravidla brány firewall pro službu container registry][acr-vnet-firewall-portal]

I nadále [ověřit přístup k registru](#verify-access-to-the-registry).

## <a name="verify-access-to-the-registry"></a>Ověřit přístup k registru

Po čekání na několik minut, než se konfigurace, které chcete aktualizovat, ověřte, že můžete virtuální počítač přístup k registru kontejneru. Vytvořte připojení SSH k virtuálnímu počítači a spusťte [az acr login][az-acr-login] příkazu se přihlaste k vašemu registru. 

```bash
az acr login --name mycontainerregistry
```

Operace s registrem můžete provádět, jako je spuštění `docker pull` načítat ukázka image z registru. Nahraďte image a značky hodnotu odpovídající pro svůj registr s předponou přihlašovací server registru (malými písmeny) jméno:

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker úspěšně načte bitovou kopii k virtuálnímu počítači.

Tento příklad ukazuje, že dostanete privátním registru kontejneru pomocí pravidla přístupu k síti. V registru však nelze přistupovat z jiné přihlašovací údaje hostitele, který nebude mít nakonfigurované pravidlo přístupu k síti. Pokud se pokusíte přihlásit z jiného hostitele pomocí `az acr login` příkazu nebo `docker login` příkazu, výstup je podobný následujícímu:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Obnovit výchozí přístup k registru

Obnovit registru tak, aby ve výchozím nastavení přístup, odeberte pravidla všechny sítě, které jsou nakonfigurované. Nastavte výchozí akce pro povolení přístupu. Jsou k dispozici ekvivalentní kroky pomocí Azure CLI a webu Azure portal.

### <a name="restore-default-registry-access---cli"></a>Obnovit výchozí přístup k registru – rozhraní příkazového řádku

#### <a name="remove-network-rules"></a>Odebrat pravidel sítě

Pokud chcete zobrazit seznam pravidel sítě nakonfigurované pro váš registr, spusťte následující příkaz [az acr pravidlo sítě seznamu][az-acr-network-rule-list] příkaz:

```azurecli
az acr network-rule list--name mycontainerregistry 
```

U jednotlivých pravidel, který je nakonfigurovaný, spusťte [az acr-pravidlo sítě odebrat][az-acr-network-rule-remove] příkazu odeberte ji. Příklad:

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

#### <a name="allow-access"></a>Povolit přístup

Nahraďte názvem svého registru v následujícím [az acr update][az-acr-update] příkaz:
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

### <a name="restore-default-registry-access---portal"></a>Obnovení výchozí přístup k registru – portál


1. Na portálu přejděte do vašeho registru kontejneru a vyberte **brány Firewall a virtuální sítě**.
1. V části **virtuální sítě**, vyberte každou virtuální síť a pak vyberte **odebrat**.
1. V části **brány Firewall**, vyberte každý rozsah adres a pak vyberte ikonu Odstranit.
1. V části **povolit přístup z**vyberte **všechny sítě**. 
1. Vyberte **Uložit**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste vytvořili všechny prostředky Azure ve stejném prostředku, skupiny a už je nepotřebujete, můžete volitelně můžete odstranit prostředky s využitím jediného [odstranění skupiny az](/cli/azure/group) příkaz:

```azurecli
az group delete --name myResourceGroup
```

Pokud chcete vyčistit prostředky na portálu, přejděte do skupiny prostředků myResourceGroup. Po načtení skupiny prostředků klikněte na **odstranit skupinu prostředků** k odebrání skupiny prostředků a prostředky v ní uloženy.

## <a name="next-steps"></a>Další postup

Několik prostředky virtuální sítě a funkce byly popisovaných v tomto článku, i když stručně. Dokumentace ke službě Azure Virtual Network výrazně obsahuje tato témata:

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
