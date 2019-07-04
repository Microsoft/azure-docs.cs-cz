---
title: Rozhraní API server ověřen rozsahy IP adres ve službě Azure Kubernetes Service (AKS)
description: Zjistěte, jak zabezpečit cluster pomocí rozsahu IP adres pro přístup k rozhraní API serveru ve službě Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: iainfou
ms.openlocfilehash: d425fc3e037b43321a278fb16781e86dfafef15c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441533"
---
# <a name="preview---secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Ve verzi Preview - zabezpečený přístup k serveru pomocí rozhraní API oprávnění rozsahy IP adres ve službě Azure Kubernetes Service (AKS)

V systému Kubernetes API server přijímá požadavky k provádění akcí k vytváření prostředků a škálovat počet uzlů v clusteru, jako. Rozhraní API serveru je centrální způsob, jak pracovat s a správu clusteru. Pokud chcete zlepšit zabezpečení clusteru a minimalizaci útoků, musí být API server pouze přístupný na omezenou skupinu rozsahů IP adres.

Tento článek ukazuje, jak používat rozsahy IP adres pomocí rozhraní API serveru oprávnění omezit požadavky na řízení. Tato funkce je aktuálně ve verzi Preview.

> [!IMPORTANT]
> Funkce AKS ve verzi preview jsou samoobslužných služeb, vyjádřit výslovný souhlas. Jsou poskytovány shromažďovat zpětnou vazbu a chyb z naší komunitě. Ve verzi preview nejsou tyto funkce určené k použití v produkčním prostředí. Funkce ve verzi public preview spadají pod "co možná nejlepší" podporu. Pomoc od týmů AKS technická podpora je k dispozici během pracovní doby tichomořské časové pásmo (PST) pouze. Další informace najdete v tématu následující články o podpoře:
>
> * [Zásady podpory AKS][aks-support-policies]
> * [Nejčastější dotazy k podpoře Azure][aks-faq]

## <a name="before-you-begin"></a>Než začnete

Rozhraní API serveru autorizované rozsahy IP adres fungovat pouze pro nové AKS clustery, které vytvoříte. V tomto článku se dozvíte, jak vytvořit cluster AKS pomocí Azure CLI.

Musí mít Azure CLI verze 2.0.61 nebo později nainstalována a nakonfigurována. Spustit `az --version` k vyhledání verze. Pokud potřebujete instalaci nebo upgrade, naleznete v tématu [instalace Azure CLI][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Instalace rozšíření aks ve verzi preview rozhraní příkazového řádku

Ke konfiguraci rozšíření rozsahy IP adres serveru oprávnění rozhraní API, je nutné *aks ve verzi preview* CLI verze rozšíření 0.4.1 nebo vyšší. Nainstalujte *aks ve verzi preview* pomocí rozšíření Azure CLI [přidat rozšíření az][az-extension-add] command, then check for any available updates using the [az extension update][az-extension-update] příkaz:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-feature-flag-for-your-subscription"></a>Zaregistrujte příznak funkce pro vaše předplatné

Jak používat rozhraní API server ověřen rozsahy IP adres, nejprve povolte příznak funkce v rámci předplatného. K registraci *APIServerSecurityPreview* příznak funkce, použijte [az funkce register][az-feature-register] příkaz, jak je znázorněno v následujícím příkladu:

> [!CAUTION]
> Při registraci funkce v rámci předplatného nelze nyní zrušit registraci této funkce. Po povolení některé funkce ve verzi preview se výchozí hodnoty lze pro všechny clustery AKS, pak jste vytvořili v rámci předplatného. Nepovolí funkce ve verzi preview na předplatná pro produkční prostředí. Testování funkce ve verzi preview a shromažďování zpětné vazby pomocí samostatné předplatné.

```azurecli-interactive
az feature register --name APIServerSecurityPreview --namespace Microsoft.ContainerService
```

Trvá několik minut, než se stav zobrazíte *registrované*. Vy můžete zkontrolovat stav registrace pomocí [seznam funkcí az][az-feature-list] příkaz:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/APIServerSecurityPreview')].{Name:name,State:properties.state}"
```

Až to budete mít, aktualizujte registraci *Microsoft.ContainerService* poskytovatele prostředků pomocí [az provider register][az-provider-register] příkaz:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Omezení

Při konfiguraci rozšíření rozsahy IP adres serveru oprávnění rozhraní API se vztahují následující omezení:

* Nelze použít aktuálně Azure Dev prostory komunikaci se serverem pro rozhraní API se taky zablokuje.

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Přehled rozhraní API serveru oprávnění rozsahy IP adres

Na serveru Kubernetes API je, jak jsou vystaveny základní rozhraní Kubernetes API. Tato součást poskytuje interakce pro nástroje pro správu, například `kubectl` nebo řídicí panel Kubernetes. AKS poskytuje hlavní server clusteru s jedním tenantem pomocí vyhrazeného serveru rozhraní API. Ve výchozím nastavení API server je přiřazena veřejná IP adresa a by měl řízení přístupu pomocí řízení přístupu na základě rolí (RBAC).

Zabezpečení přístupu k rovině řízení jinak veřejně přístupné AKS / API server můžete povolit a používat oprávnění rozsahy IP adres. Tyto autorizované rozsahy IP adres povolíte jenom definované rozsahy IP adres pro komunikaci se serverem rozhraní API. Požadavku na API server z IP adresy, který není součástí těchto oprávnění rozsahy IP adres je blokován. Měli byste pokračovat pomocí funkce RBAC pak ověřovat uživatele a akce, kterou vyžadují.

Další informace o serveru rozhraní API a další součásti clusteru, najdete v tématu [Kubernetes základní koncepty pro AKS][concepts-clusters-workloads].

## <a name="create-an-aks-cluster"></a>Vytvoření clusteru AKS

IP adresa serveru oprávnění rozhraní API rozsahy funkční pouze u nových clusterů AKS. Autorizované rozsahy IP adres nelze povolit, protože operace vytvoření součástí clusteru. Pokud se pokusíte povolit oprávnění rozsahy IP adres jako součást clusteru vytvořit proces, nelze získat přístup k rozhraní API serveru během nasazení, jako výstupní IP adresou není definovaný v tomto okamžiku jsou uzly clusteru.

Nejprve vytvořte cluster pomocí [az aks vytvořit][az-aks-create] příkazu. Následující příklad vytvoří jeden uzel clusteru s názvem *myAKSCluster* ve skupině prostředků s názvem *myResourceGroup*.

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location eastus

# Create an AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --generate-ssh-keys

# Get credentials to access the cluster
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="create-outbound-gateway-for-firewall-rules"></a>Vytvoření brány odchozí pravidla brány firewall

Aby bylo zajištěno, že uzly v clusteru můžete spolehlivě komunikovat se serverem rozhraní API povolit oprávnění rozsahy IP adres v další části, vytvořte Azure brány firewall pro použití jako odchozí brány. IP adresa brány Azure se pak přidá do seznamu rozhraní API IP adres ověřených serverů v další části.

> [!WARNING]
> Použití brány Firewall Azure může mít za následek snížit velké náklady na za období měsíčního fakturačního cyklu. Nutnost používat Brána Firewall služby Azure by měly být pouze nutné v tomto období počáteční verzi preview. Další informace a náklady na plánování najdete v tématu [Brána Firewall služby Azure ceny][azure-firewall-costs].

Nejprve získejte *MC_* název skupiny prostředků pro AKS cluster a virtuální sítě. Potom vytvořte podsíť pomocí [az podsíti virtuální sítě vytvořit][az-network-vnet-subnet-create] příkazu. Následující příklad vytvoří podsíť s názvem *AzureFirewallSubnet* s rozsahem CIDR *10.200.0.0/16*:

```azurecli-interactive
# Get the name of the MC_ cluster resource group
MC_RESOURCE_GROUP=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query nodeResourceGroup -o tsv)

# Get the name of the virtual network used by the cluster
VNET_NAME=$(az network vnet list \
    --resource-group $MC_RESOURCE_GROUP \
    --query [0].name -o tsv)

# Create a subnet in the virtual network for Azure Firewall
az network vnet subnet create \
    --resource-group $MC_RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name AzureFirewallSubnet \
    --address-prefixes 10.200.0.0/16
```

Pokud chcete vytvořit bránu Firewall Azure, nainstalujte *azure firewall* pomocí rozšíření rozhraní příkazového řádku [přidat rozšíření az][az-extension-add] command. Then, create a firewall using the [az network firewall create][az-network-firewall-create] příkaz. Následující příklad vytvoří firewall k Azure s názvem *myAzureFirewall*:

```azurecli-interactive
# Install the CLI extension for Azure Firewall
az extension add --name azure-firewall

# Create an Azure firewall
az network firewall create \
    --resource-group $MC_RESOURCE_GROUP\
    --name myAzureFirewall
```

Azure bránu firewall se přiřadí veřejnou IP adresu, která výchozí přenos prochází přes. Vytvoření pomocí veřejné adresy [az network public-ip vytvořit][az-network-public-ip-create] command, then create an IP configuration on the firewall using the [az network firewall ip-config create][az-network-firewall-ip-config-create] , který se vztahuje veřejnou IP adresu:

```azurecli-interactive
# Create a public IP address for the firewall
FIREWALL_PUBLIC_IP=$(az network public-ip create \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewallPublicIP \
    --sku Standard \
    --query publicIp.ipAddress -o tsv)

# Associated the firewall with virtual network
az network firewall ip-config create \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewallIPConfig \
    --vnet-name $VNET_NAME \
    --firewall-name myAzureFirewall \
    --public-ip-address myAzureFirewallPublicIP
```

Teď vytvořte pravidlo brány firewall Azure sítě, které *povolit* všechny *TCP* provozu pomocí [sítě pravidlo az síťové brány firewall vytvořit][az-network-firewall-network-rule-create] příkazu. Následující příklad vytvoří pravidlo sítě *AllowTCPOutbound* pro provoz s libovolný zdroj nebo cíl adresy:

```azurecli-interactive
az network firewall network-rule create \
    --resource-group $MC_RESOURCE_GROUP \
    --firewall-name myAzureFirewall \
    --name AllowTCPOutbound \
    --collection-name myAzureFirewallCollection \
    --priority 200 \
    --action Allow \
    --protocols TCP \
    --source-addresses '*' \
    --destination-addresses '*' \
    --destination-ports '*'
```

Chcete-li přidružit síťovou trasu Azure bránu firewall, získáte stávající informace o tabulce směrování, interní IP adresa brány Azure a IP adresa serveru rozhraní API. Tyto IP adresy jsou určené v další části a řídit, jak se mají směrovat provoz pro komunikaci clusterů.

```azurecli-interactive
# Get the AKS cluster route table
ROUTE_TABLE=$(az network route-table list \
    --resource-group $MC_RESOURCE_GROUP \
    --query "[?contains(name,'agentpool')].name" -o tsv)

# Get internal IP address of the firewall
FIREWALL_INTERNAL_IP=$(az network firewall show \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewall \
    --query ipConfigurations[0].privateIpAddress -o tsv)

# Get the IP address of API server endpoint
K8S_ENDPOINT_IP=$(kubectl get endpoints -o=jsonpath='{.items[?(@.metadata.name == "kubernetes")].subsets[].addresses[].ip}')
```

Nakonec vytvořte trasu v existující AKS sítě směrovací tabulky pomocí [az network route-table postupu vytvoření][az-network-route-table-route-create] příkaz, který umožní provoz určený zařízení Azure brány firewall pro komunikaci serveru rozhraní API.

```azurecli-interactive
az network route-table route create \
    --resource-group $MC_RESOURCE_GROUP \
    --route-table-name $ROUTE_TABLE \
    --name AzureFirewallAPIServer \
    --address-prefix $K8S_ENDPOINT_IP/32 \
    --next-hop-ip-address $FIREWALL_INTERNAL_IP \
    --next-hop-type VirtualAppliance

echo "Public IP address for the Azure Firewall instance that should be added to the list of API server authorized addresses is:" $FIREWALL_PUBLIC_IP
```

Poznamenejte si veřejnou IP adresu zařízení Brána Firewall služby Azure. Tato adresa se přidá do seznamu rozhraní API oprávnění rozsahy IP adres serveru v další části.

## <a name="enable-authorized-ip-ranges"></a>Povolit oprávnění rozsahy IP adres

Pokud chcete povolit rozšíření rozsahy IP adres serveru oprávnění rozhraní API, poskytovat seznam autorizovaných rozsahy IP adres. Když zadáte rozsah CIDR, začněte s první IP adresou v rozsahu. Například *137.117.106.90/29* platný rozsah je ale ujistěte se, že zadáte první IP adresou v rozsahu, například *137.117.106.88/29*.

Použití [az aks aktualizovat][az-aks-update] příkaz a zadejte *– rozhraní api-server oprávnění – – rozsahy ip* povolit. Tyto rozsahy IP adres jsou obvykle rozsahy adres použitými ve vaší místní sítě. Přidejte veřejnou IP adresu své vlastní brány Azure získaný v předchozím kroku, jako například *20.42.25.196/32*.

Následující příklad umožňuje rozhraní API oprávnění rozsahy IP adres serveru v clusteru s názvem *myAKSCluster* ve skupině prostředků s názvem *myResourceGroup*. Rozsahy IP adres k autorizaci jsou *20.42.25.196/32* (brány Azure veřejnou IP adresu), pak *172.0.0.10/16* a *168.10.0.10/18*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges 20.42.25.196/32,172.0.0.10/16,168.10.0.10/18
```

## <a name="update-or-disable-authorized-ip-ranges"></a>Aktualizace nebo zakázání autorizované rozsahy IP adres

Aktualizace nebo zakázání autorizované rozsahy IP adres, znovu použijete [az aks aktualizovat][az-aks-update] příkazu. Zadejte aktualizovaný rozsah CIDR, který chcete povolit, nebo zadat, že chcete zakázat API server prázdného rozsahu oprávnění rozsahy IP adres, jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>Další postup

V tomto článku jste povolili rozšíření rozsahy IP adres serveru oprávnění rozhraní API. Tento přístup je jednou ze součástí sady spouštění zabezpečeného clusteru AKS.

Další informace najdete v tématu [koncepty zabezpečení pro aplikace a clustery ve službě AKS][concepts-security] and [Best practices for cluster security and upgrades in AKS][operator-best-practices-cluster-security].

<!-- LINKS - external -->
[azure-firewall-costs]: https://azure.microsoft.com/pricing/details/azure-firewall/

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[create-aks-sp]: kubernetes-service-principal.md#manually-create-a-service-principal
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-firewall-create]: /cli/azure/ext/azure-firewall/network/firewall#ext-azure-firewall-az-network-firewall-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-firewall-ip-config-create]: /cli/azure/ext/azure-firewall/network/firewall/ip-config#ext-azure-firewall-az-network-firewall-ip-config-create
[az-network-firewall-network-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/network-rule#ext-azure-firewall-az-network-firewall-network-rule-create
[az-network-route-table-route-create]: /cli/azure/network/route-table/route#az-network-route-table-route-create
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
