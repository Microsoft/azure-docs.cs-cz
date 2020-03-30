---
title: Omezení přístupu pomocí virtuální sítě
description: Povolit přístup k registru kontejneru Azure jenom z prostředků ve virtuální síti Azure nebo z oblastí veřejných IP adres.
ms.topic: article
ms.date: 07/01/2019
ms.openlocfilehash: a6b89b074c25ea0948597ede7e5681b100c7f429
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74454344"
---
# <a name="restrict-access-to-an-azure-container-registry-using-an-azure-virtual-network-or-firewall-rules"></a>Omezení přístupu k registru kontejnerů Azure pomocí virtuální sítě Azure nebo pravidel brány firewall

[Virtuální síť Azure](../virtual-network/virtual-networks-overview.md) poskytuje zabezpečené privátní sítě pro vaše azure a místní prostředky. Omezením přístupu k privátnímu registru kontejnerů Azure z virtuální sítě Azure zajistíte, že k registru přistupují jenom prostředky ve virtuální síti. U scénářů mezi místními prostory můžete také nakonfigurovat pravidla brány firewall tak, aby umožňovala přístup k registru pouze z určitých adres IP.

Tento článek ukazuje dva scénáře konfigurace pravidel přístupu k síti příchozí sítě v registru kontejneru: z virtuálního počítače nasazeného ve virtuální síti nebo z veřejné IP adresy virtuálního počítače.

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview a platí určitá [omezení](#preview-limitations). Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.
>

Pokud místo toho potřebujete nastavit pravidla přístupu pro prostředky k dosažení registru kontejnerů zpoza brány firewall, přečtěte si informace [o konfiguraci pravidel pro přístup k registru kontejnerů Azure za bránou firewall](container-registry-firewall-access-rules.md).


## <a name="preview-limitations"></a>Omezení náhledu

* Pomocí pravidel přístupu k síti lze nakonfigurovat pouze registr kontejneru **Premium.** Informace o vrstvách služby registru naleznete [v tématu Azure Container Registry SKU](container-registry-skus.md). 

* Jenom cluster [služby Azure Kubernetes](../aks/intro-kubernetes.md) nebo [virtuální počítač](../virtual-machines/linux/overview.md) Azure lze použít jako hostitele pro přístup k registru kontejnerů ve virtuální síti. *Ostatní služby Azure včetně instancí kontejnerů Azure nejsou momentálně podporované.*

* [Operace úloh ACR](container-registry-tasks-overview.md) nejsou aktuálně podporovány v registru kontejneru, ke který se přistupuje ve virtuální síti.

* Každý registr podporuje maximálně 100 pravidel virtuální sítě.

## <a name="prerequisites"></a>Požadavky

* Chcete-li použít kroky Azure CLI v tomto článku, Azure CLI verze 2.0.58 nebo novější je vyžadováno. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli].

* Pokud ještě nemáte registr kontejnerů, vytvořte ho (vyžadována stojka `hello-world` Premium) a vysuňte ukázkovou bitovou kopii, například z Docker Hubu. Například použijte [portál Azure][quickstart-portal] nebo [Azure CLI][quickstart-cli] k vytvoření registru. 

* Pokud chcete omezit přístup k registru pomocí virtuální sítě v jiném předplatném Azure, musíte zaregistrovat poskytovatele prostředků pro Azure Container Registry v tomto předplatném. Například:

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

## <a name="about-network-rules-for-a-container-registry"></a>Pravidla sítě pro registr kontejnerů

Registr kontejnerů Azure ve výchozím nastavení přijímá připojení přes Internet od hostitelů v libovolné síti. Pomocí virtuální sítě můžete povolit zabezpečený přístup k registru jenom prostředkům Azure, jako je cluster AKS nebo virtuální počítač Azure, aniž byste překročili hranici sítě. Můžete také nakonfigurovat pravidla síťové brány firewall tak, aby umožňovala pouze určité rozsahy veřejných ip adres sítě. 

Chcete-li omezit přístup k registru, nejprve změňte výchozí akci registru tak, aby odepřela všechna síťová připojení. Potom přidejte pravidla přístupu k síti. Klienti, kterým byl udělen přístup prostřednictvím síťových pravidel, musí pokračovat v [ověřování v registru kontejnerů](https://docs.microsoft.com/azure/container-registry/container-registry-authentication) a musí mít oprávnění k přístupu k datům.

### <a name="service-endpoint-for-subnets"></a>Koncový bod služby pro podsítě

Chcete-li povolit přístup z podsítě ve virtuální síti, je třeba přidat [koncový bod služby](../virtual-network/virtual-network-service-endpoints-overview.md) pro službu Azure Container Registry. 

Víceklientské služby, jako je Azure Container Registry, používají jednu sadu IP adres pro všechny zákazníky. Koncový bod služby přiřadí koncový bod pro přístup k registru. Tento koncový bod poskytuje provoz optimální trasu k prostředku přes páteřní síť Azure. Identity virtuální sítě a podsítě jsou také přenášeny s každým požadavkem.

### <a name="firewall-rules"></a>Pravidla brány firewall

Pro pravidla sítě IP poskytněte povolené rozsahy internetových adres pomocí zápisu CIDR, například *16.17.18.0/24* nebo jednotlivé IP adresy jako *16.17.18.19*. Pravidla sítě IP jsou povolena pouze pro *veřejné* internetové IP adresy. Rozsahy IP adres vyhrazené pro privátní sítě (podle definice v rfc 1918) nejsou v pravidlech IP povoleny.

## <a name="create-a-docker-enabled-virtual-machine"></a>Vytvoření virtuálního počítače s podporou Dockeru

V tomto článku použijte virtuální počítač Ubuntu s podporou Dockeru pro přístup k registru kontejnerů Azure. Pokud chcete do registru použít ověřování Azure Active Directory, nainstalujte na virtuální počítač také [rozhraní příkazového příkazu Konazure.][azure-cli] Pokud už máte virtuální počítač Azure, přeskočte tento krok vytvoření.

Můžete použít stejnou skupinu prostředků pro váš virtuální počítač a registr kontejnerů. Toto nastavení zjednodušuje vyčištění na konci, ale není vyžadováno. Pokud se rozhodnete vytvořit samostatnou skupinu prostředků pro virtuální počítač a virtuální síť, spusťte [vytvoření skupiny AZ][az-group-create]. Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *westcentralus:*

```azurecli
az group create --name myResourceGroup --location westus
```

Nyní nasadit výchozí Ubuntu Azure virtuální počítač s [az vm vytvořit][az-vm-create]. Následující příklad vytvoří virtuální hod s názvem *myDockerVM*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Vytvoření virtuálního počítače trvá několik minut. Po dokončení příkazu, poznamenejte si `publicIpAddress` zobrazení v příkazovém příkazu Azure. Pomocí této adresy můžete vytvořit připojení SSH k virtuálnímu počítači a volitelně pro pozdější nastavení pravidel brány firewall.

### <a name="install-docker-on-the-vm"></a>Instalace Dockeru na virtuální ms

Po spuštění virtuálního virtuálního provozu vytvořte připojení SSH k virtuálnímu virtuálnímu jemu. Nahraďte *publicIpAddress* veřejnou IP adresou vašeho virtuálního počítače.

```bash
ssh azureuser@publicIpAddress
```

Spusťte následující příkaz pro instalaci Dockeru na virtuální počítač Ubuntu:

```bash
sudo apt install docker.io -y
```

Po instalaci spusťte následující příkaz a ověřte, že Docker na virtuálním počítači funguje správně:

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

Postupujte podle kroků v [části Instalace nastavení příkazového příkazu Azure s aptem](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) k instalaci příkazového příkazového příkazu Azure do virtuálního počítače Ubuntu. V tomto článku se ujistěte, že nainstalujete verzi 2.0.58 nebo novější.

Ukončete připojení SSH.

## <a name="allow-access-from-a-virtual-network"></a>Povolení přístupu z virtuální sítě

V této části nakonfigurujte registr kontejnerů tak, aby umožňoval přístup z podsítě ve virtuální síti Azure. K dispozici jsou ekvivalentní kroky pomocí azure cli a portálu Azure.

### <a name="allow-access-from-a-virtual-network---cli"></a>Povolení přístupu z virtuální sítě – cli

#### <a name="add-a-service-endpoint-to-a-subnet"></a>Přidání koncového bodu služby do podsítě

Když vytvoříte virtuální počítač, Azure ve výchozím nastavení vytvoří virtuální síť ve stejné skupině prostředků. Název virtuální sítě je založen na názvu virtuálního počítače. Pokud například pojmenujete virtuální počítač *myDockerVM*, výchozí název virtuální sítě je *myDockerVMVNET*, s podsítí s názvem *myDockerVMSubnet*. Ověřte to na webu Azure Portal nebo pomocí příkazu [az network vnet list:][az-network-vnet-list]

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

Pomocí příkazu [aktualizace podsítě sítě AZ][az-network-vnet-subnet-update] přidejte koncový bod služby **Microsoft.ContainerRegistry** do podsítě. Nahraďte názvy virtuální sítě a podsítě následujícím příkazem:

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Pomocí příkazu [az sítě pro zobrazení virtuální sítě][az-network-vnet-subnet-show] načtěte ID prostředku podsítě. Potřebujete to v pozdějším kroku ke konfiguraci pravidla přístupu k síti.

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

#### <a name="change-default-network-access-to-registry"></a>Změna výchozího přístupu k registru v síti

Ve výchozím nastavení umožňuje registr kontejnerů Azure připojení z hostitelů v libovolné síti. Chcete-li omezit přístup k vybrané síti, změňte výchozí akci a odepřít přístup. Nahraďte název registru v následujícím příkazu [az acr update:][az-acr-update]

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>Přidání pravidla sítě do registru

Pomocí příkazu [az acr network-rule add][az-acr-network-rule-add] přidejte do registru síťové pravidlo, které umožňuje přístup z podsítě virtuálního počítače. V následujícím příkazu nahraďte název registru kontejneru a ID prostředku podsítě: 

 ```azurecli
az acr network-rule add --name mycontainerregistry --subnet <subnet-resource-id>
```

Pokračujte [v ověření přístupu k registru](#verify-access-to-the-registry).

### <a name="allow-access-from-a-virtual-network---portal"></a>Povolení přístupu z virtuální sítě – portál

#### <a name="add-service-endpoint-to-subnet"></a>Přidání koncového bodu služby do podsítě

Když vytvoříte virtuální počítač, Azure ve výchozím nastavení vytvoří virtuální síť ve stejné skupině prostředků. Název virtuální sítě je založen na názvu virtuálního počítače. Pokud například pojmenujete virtuální počítač *myDockerVM*, výchozí název virtuální sítě je *myDockerVMVNET*, s podsítí s názvem *myDockerVMSubnet*.

Přidání koncového bodu služby pro registr kontejnerů Azure do podsítě:

1. Do vyhledávacího pole v horní části [portálu Azure][azure-portal]zadejte *virtuální sítě*. Když se **virtuální sítě** zobrazí ve výsledcích hledání, vyberte je.
1. Ze seznamu virtuálních sítí vyberte virtuální síť, ve které se virtuální počítač nasazuje, například *myDockerVMVNET*.
1. V části **Nastavení**vyberte **Podsítě**.
1. Vyberte podsíť, ve které se váš virtuální počítač nasazuje, například *myDockerVMSubnet*.
1. V části **Koncové body služby**vyberte **microsoft.containerregistry**.
1. Vyberte **Uložit**.

![Přidání koncového bodu služby do podsítě][acr-subnet-service-endpoint] 

#### <a name="configure-network-access-for-registry"></a>Konfigurace přístupu k síti pro registr

Ve výchozím nastavení umožňuje registr kontejnerů Azure připojení z hostitelů v libovolné síti. Omezení přístupu k virtuální síti:

1. Na portálu přejděte do registru kontejnerů.
1. V části **Nastavení**vyberte **bránu firewall a virtuální sítě**.
1. Chcete-li odepřít přístup ve výchozím nastavení, zvolte povolit přístup z **vybraných sítí**. 
1. Vyberte **Přidat existující virtuální síť**a vyberte virtuální síť a podsíť, kterou jste nakonfigurovali pomocí koncového bodu služby. Vyberte **Přidat**.
1. Vyberte **Uložit**.

![Konfigurace virtuální sítě pro registr kontejnerů][acr-vnet-portal]

Pokračujte [v ověření přístupu k registru](#verify-access-to-the-registry).

## <a name="allow-access-from-an-ip-address"></a>Povolit přístup z adresy IP

V této části nakonfigurujte registr kontejnerů tak, aby umožňoval přístup z určité adresy IP nebo rozsahu. K dispozici jsou ekvivalentní kroky pomocí azure cli a portálu Azure.

### <a name="allow-access-from-an-ip-address---cli"></a>Povolit přístup z IP adresy – CLI

#### <a name="change-default-network-access-to-registry"></a>Změna výchozího přístupu k registru v síti

Pokud jste tak ještě neučinili, aktualizujte konfiguraci registru a odepřete přístup ve výchozím nastavení. Nahraďte název registru v následujícím příkazu [az acr update:][az-acr-update]

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="remove-network-rule-from-registry"></a>Odebrat síťové pravidlo z registru

Pokud jste dříve přidali síťové pravidlo umožňující přístup z podsítě virtuálního aplikace, odeberte koncový bod služby podsítě a síťové pravidlo. Nahraďte název registru kontejneru a ID prostředku podsítě, kterou jste načetli v dřívějším kroku v příkazu [odebrání pravidla sítě AZ ACR:][az-acr-network-rule-remove] 

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

#### <a name="add-network-rule-to-registry"></a>Přidání pravidla sítě do registru

Pomocí příkazu [az acr network-rule add][az-acr-network-rule-add] přidejte do registru síťové pravidlo, které umožňuje přístup z IP adresy virtuálního počítače. V následujícím příkazu nahraďte název registru kontejnerů a veřejnou IP adresu virtuálního počítačů.

```azurecli
az acr network-rule add --name mycontainerregistry --ip-address <public-IP-address>
```

Pokračujte [v ověření přístupu k registru](#verify-access-to-the-registry).

### <a name="allow-access-from-an-ip-address---portal"></a>Povolit přístup z IP adresy - portál

#### <a name="remove-existing-network-rule-from-registry"></a>Odebrání existujícího pravidla sítě z registru

Pokud jste dříve přidali síťové pravidlo, které umožňuje přístup z podsítě virtuálního aplikace, odeberte existující pravidlo. Tuto část přeskočte, pokud chcete získat přístup k registru z jiného virtuálního virtuálního serveru.

* Aktualizujte nastavení podsítě a odeberte koncový bod služby podsítě pro registr kontejnerů Azure. 

  1. Na [webu Azure Portal][azure-portal]přejděte do virtuální sítě, kde se váš virtuální počítač nasazuje.
  1. V části **Nastavení**vyberte **Podsítě**.
  1. Vyberte podsíť, ve které se váš virtuální počítač nasazuje.
  1. V části **Koncové body služby**odeberte toto políčko pro **Microsoft.ContainerRegistry**. 
  1. Vyberte **Uložit**.

* Odeberte síťové pravidlo, které umožňuje podsíti přístup k registru.

  1. Na portálu přejděte do registru kontejnerů.
  1. V části **Nastavení**vyberte **bránu firewall a virtuální sítě**.
  1. V části **Virtuální sítě**vyberte název virtuální sítě a pak vyberte **Odebrat**.
  1. Vyberte **Uložit**.

#### <a name="add-network-rule-to-registry"></a>Přidání pravidla sítě do registru

1. Na portálu přejděte do registru kontejnerů.
1. V části **Nastavení**vyberte **bránu firewall a virtuální sítě**.
1. Pokud jste tak ještě neučinili, zvolte povolit přístup z **vybraných sítí**. 
1. V části **Virtuální sítě**zkontrolujte, zda není vybrána žádná síť.
1. V části **Brána firewall**zadejte veřejnou IP adresu virtuálního počítače. Nebo zadejte rozsah adres v zápisu CIDR, který obsahuje IP adresu virtuálního počítačů.
1. Vyberte **Uložit**.

![Konfigurovat pravidlo brány firewall pro registr kontejnerů][acr-vnet-firewall-portal]

Pokračujte [v ověření přístupu k registru](#verify-access-to-the-registry).

## <a name="verify-access-to-the-registry"></a>Ověření přístupu k registru

Po několika minutách čekání na aktualizaci konfigurace ověřte, zda má virtuální počítače přístup k registru kontejneru. Vytvořte připojení SSH k virtuálnímu počítači a spusťte příkaz [az acr login][az-acr-login] pro přihlášení do registru. 

```bash
az acr login --name mycontainerregistry
```

Můžete provádět operace registru, `docker pull` jako je například spustit navádění ukázkový obrázek z registru. Nahraďte obrázek a hodnotu značky vhodnou pro váš registr s předponou s názvem přihlašovacího serveru registru (všechna malá písmena):

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker úspěšně natáhne image do virtuálního mísu.

Tento příklad ukazuje, že můžete přistupovat k registru privátního kontejneru prostřednictvím pravidla přístupu k síti. Registr však nelze získat přístup z jiného hostitele přihlášení, který nemá nakonfigurované pravidlo přístupu k síti. Pokud se pokusíte přihlásit `az acr login` z `docker login` jiného hostitele pomocí příkazu nebo příkazu, výstup je podobný následujícímu:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Obnovení výchozího přístupu k registru

Chcete-li obnovit registr, který umožňuje přístup ve výchozím nastavení, odeberte všechna nakonfigurovaná síťová pravidla. Potom nastavte výchozí akci, která umožní přístup. K dispozici jsou ekvivalentní kroky pomocí azure cli a portálu Azure.

### <a name="restore-default-registry-access---cli"></a>Obnovení výchozího přístupu k registru – cli

#### <a name="remove-network-rules"></a>Odebrání síťových pravidel

Chcete-li zobrazit seznam síťových pravidel nakonfigurovaných pro váš registr, spusťte následující příkaz [az acr network-rule:][az-acr-network-rule-list]

```azurecli
az acr network-rule list--name mycontainerregistry 
```

Pro každé pravidlo, které je nakonfigurováno, spusťte příkaz [az acr network-rule remove][az-acr-network-rule-remove] a odeberte jej. Například:

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

Nahraďte název registru v následujícím příkazu [az acr update:][az-acr-update]
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

### <a name="restore-default-registry-access---portal"></a>Obnovit výchozí přístup k registru – portál


1. Na portálu přejděte do registru kontejnerů a vyberte **bránu firewall a virtuální sítě**.
1. V části **Virtuální sítě**vyberte jednotlivé virtuální sítě a pak vyberte **Odebrat**.
1. V části **Brána firewall**vyberte každý rozsah adres a pak vyberte ikonu Odstranit.
1. V části **Povolit přístup z**položky vyberte **Všechny sítě**. 
1. Vyberte **Uložit**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste vytvořili všechny prostředky Azure ve stejné skupině prostředků a už je nepotřebujete, můžete je volitelně odstranit pomocí jediného [příkazu odstranění skupiny az:](/cli/azure/group)

```azurecli
az group delete --name myResourceGroup
```

Chcete-li vyčistit prostředky na portálu, přejděte do skupiny prostředků myResourceGroup. Po načtení skupiny prostředků odejměte skupinu prostředků kliknutím na **Odstranit skupinu prostředků** odeberte skupinu prostředků a prostředky, které jsou v ní uloženy.

## <a name="next-steps"></a>Další kroky

V tomto článku bylo popsáno několik prostředků a funkcí virtuální sítě, i když krátce. Dokumentace k virtuální síti Azure rozsáhle popisuje tato témata:

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
