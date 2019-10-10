---
title: Počet povolených IP adres serveru API ve službě Azure Kubernetes (AKS)
description: Naučte se zabezpečit cluster pomocí rozsahu IP adres pro přístup k serveru rozhraní API ve službě Azure Kubernetes (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: 5819a6c6d73b2ee51fc72d2b56d99b0efb3ea0be
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241137"
---
# <a name="preview---secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Náhled – zabezpečený přístup k serveru rozhraní API pomocí povolených rozsahů IP adres ve službě Azure Kubernetes Service (AKS)

V Kubernetes Server rozhraní API přijímá požadavky na provádění akcí v clusteru, jako je například vytváření prostředků nebo škálování počtu uzlů. Server rozhraní API je ústředním prostředkem pro interakci s clusterem a jeho správu. Pro zlepšení zabezpečení clusteru a minimalizaci útoků by měl být Server API přístupný jenom z omezené sady rozsahů IP adres.

V tomto článku se dozvíte, jak používat rozsahy IP adres autorizovaných serverem API k omezení požadavků na řídicí plochu. Tato funkce je aktuálně ve verzi Preview.

> [!IMPORTANT]
> Funkce služby AKS ve verzi Preview jsou samoobslužné přihlašovací. Verze Preview jsou k dispozici "tak jak jsou" a "jako dostupné" a jsou vyloučeny ze smluv o úrovni služeb a omezené záruky. AKS verze Preview jsou částečně pokryté zákaznickou podporou na základě nejlepšího úsilí. V takovém případě tyto funkce nejsou určeny pro použití v produkčním prostředí. Další informace o tom, jak se zaregistrují, najdete v následujících článcích podpory:
>
> * [Zásady podpory AKS][aks-support-policies]
> * [Nejčastější dotazy k podpoře Azure][aks-faq]

## <a name="before-you-begin"></a>Před zahájením

Tento článek předpokládá, že pracujete s clustery, které používají [kubenet][kubenet].  S clustery založenými na [CNI (Azure Container Networking Interface)][cni-networking] nebudete mít potřebnou směrovací tabulku potřebnou k zabezpečení přístupu.  Směrovací tabulku budete muset vytvořit ručně.  Další informace najdete v tématu [Správa směrovacích tabulek](https://docs.microsoft.com/azure/virtual-network/manage-route-table) .

Schválené rozsahy IP adres serveru API fungují jenom pro nové clustery AKS, které vytvoříte. V tomto článku se dozvíte, jak vytvořit cluster AKS pomocí Azure CLI.

Potřebujete nainstalovanou a nakonfigurovanou verzi Azure CLI 2.0.61 nebo novější. Vyhledejte verzi spuštěním @ no__t-0. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Nainstalovat rozšíření CLI AKS-Preview

Pokud chcete nakonfigurovat rozsahy IP adres autorizovaných serverem API, potřebujete rozšíření *AKS-Preview* CLI verze 0.4.1 nebo vyšší. Nainstalujte rozšíření Azure CLI *AKS-Preview* pomocí příkazu [AZ Extension Add][az-extension-add] a potom zkontrolujte, jestli nejsou dostupné aktualizace, pomocí příkazu [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-feature-flag-for-your-subscription"></a>Zaregistrovat příznak funkce pro vaše předplatné

Pokud chcete použít rozsahy IP adres ověřených serverem API, nejdřív u svého předplatného povolte příznak funkce. Chcete-li zaregistrovat příznak funkce *APIServerSecurityPreview* , použijte příkaz [AZ Feature Register][az-feature-register] , jak je znázorněno v následujícím příkladu:

> [!CAUTION]
> Když zaregistrujete funkci v rámci předplatného, nemůžete tuto funkci v tuto chvíli zrušit. Po povolení některých funkcí verze Preview se můžou použít výchozí hodnoty pro všechny clustery AKS vytvořené v rámci předplatného. Nepovolujte funkce ve verzi Preview u produkčních předplatných. Použijte samostatné předplatné k testování funkcí ve verzi Preview a získejte zpětnou vazbu.

```azurecli-interactive
az feature register --name APIServerSecurityPreview --namespace Microsoft.ContainerService
```

Zobrazení stavu v *registraci*trvá několik minut. Stav registrace můžete zjistit pomocí příkazu [AZ Feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/APIServerSecurityPreview')].{Name:name,State:properties.state}"
```

Až budete připraveni, aktualizujte registraci poskytovatele prostředků *Microsoft. ContainerService* pomocí příkazu [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Omezení (limity)

Při konfiguraci rozsahů IP adres autorizovaných serverem API platí následující omezení:

* V tuto chvíli nemůžete použít Azure Dev Spaces, protože komunikace se serverem API je taky blokovaná.

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Přehled povolených IP adres serveru rozhraní API

Kubernetes API Server je způsob, jakým jsou vystavená základní rozhraní Kubernetes API. Tato součást poskytuje interakci pro nástroje pro správu, jako je například `kubectl` nebo řídicí panel Kubernetes. AKS poskytuje hlavní server s jedním nájemcem, který má vyhrazený server API. Ve výchozím nastavení je serveru rozhraní API přiřazena veřejná IP adresa a měli byste řídit přístup pomocí řízení přístupu na základě role (RBAC).

Chcete-li zabezpečit přístup k jinému veřejně přístupné rovině AKS ovládacího prvku nebo serveru rozhraní API, můžete povolit a použít autorizované rozsahy IP adres. Tyto autorizované rozsahy IP adres povolují komunikaci se serverem API jenom definované rozsahy IP adres. Požadavek na server rozhraní API z IP adresy, která není součástí těchto povolených rozsahů IP adres, je blokovaný. K následnému autorizaci uživatelů a akcí, které požadují, byste měli nadále používat RBAC.

Další informace o serveru rozhraní API a dalších součástech clusteru najdete v tématu [základní koncepty Kubernetes pro AKS][concepts-clusters-workloads].

## <a name="create-an-aks-cluster"></a>Vytvoření clusteru AKS

Rozsahy povolených IP adres serveru API fungují jenom pro nové clustery AKS. V rámci operace vytvoření clusteru nemůžete povolit rozsahy povolených IP adres. Pokud se pokusíte v rámci procesu vytváření clusteru povolit rozsahy povolených IP adres, uzly clusteru během nasazování nebudou moci získat přístup k serveru rozhraní API, protože výstupní IP adresa není v tomto okamžiku definována.

Nejdřív vytvořte cluster pomocí příkazu [AZ AKS Create][az-aks-create] . Následující příklad vytvoří cluster s jedním uzlem s názvem *myAKSCluster* ve skupině prostředků s názvem *myResourceGroup*.

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

## <a name="create-outbound-gateway-for-firewall-rules"></a>Vytvořit odchozí bránu pro pravidla brány firewall

Pokud chcete zajistit, aby uzly v clusteru spolehlivě komunikovaly se serverem rozhraní API, když v další části povolíte povolené rozsahy IP adres, vytvořte bránu Azure firewall pro použití jako odchozí brány. Do seznamu povolených IP adres serveru API se pak přidá IP adresa brány Azure firewall v další části.

> [!WARNING]
> Použití Azure Firewall může během měsíčního fakturačního cyklu způsobit významné náklady. Požadavek na použití Azure Firewall by měl být v tomto počátečním období verze Preview nezbytný. Další informace a plánování nákladů najdete v tématu [Azure firewall ceny][azure-firewall-costs].
>
> Případně, pokud váš cluster používá nástroj [pro vyrovnávání zatížení Standard SKU][standard-sku-lb], nemusíte konfigurovat Azure firewall jako odchozí bránu. Použijte příkaz [AZ Network Public-IP list][az-network-public-ip-list] a zadejte skupinu prostředků clusteru AKS, který obvykle začíná na *MC_* . Tím se zobrazí veřejná IP adresa pro váš cluster, který můžete vyřadit do seznamu povolených. Například:
>
> ```azurecli-interactive
> RG=$(az aks show --resource-group myResourceGroup --name myAKSClusterSLB --query nodeResourceGroup -o tsv)
> SLB_PublicIP=$(az network public-ip list --resource-group $RG --query [].ipAddress -o tsv)
> az aks update --api-server-authorized-ip-ranges $SLB_PublicIP --resource-group myResourceGroup --name myAKSClusterSLB
> ```

Nejprve Získejte název skupiny prostředků *MC_* pro cluster AKS a virtuální síť. Pak vytvořte podsíť pomocí příkazu [AZ Network VNet Subnet Create][az-network-vnet-subnet-create] . Následující příklad vytvoří podsíť s názvem *AzureFirewallSubnet* s rozsahem CIDR *10.200.0.0/16*:

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

Pokud chcete vytvořit Azure Firewall, nainstalujte rozšíření *Azure-firewall* CLI pomocí příkazu [AZ Extension Add][az-extension-add] . Pak vytvořte bránu firewall pomocí příkazu [AZ Network firewall Create][az-network-firewall-create] . Následující příklad vytvoří bránu Azure firewall s názvem *myAzureFirewall*:

```azurecli-interactive
# Install the CLI extension for Azure Firewall
az extension add --name azure-firewall

# Create an Azure firewall
az network firewall create \
    --resource-group $MC_RESOURCE_GROUP\
    --name myAzureFirewall
```

Brána Azure firewall má přiřazenou veřejnou IP adresu, pomocí které se neprovádí přenos toků. Pomocí příkazu [AZ Network Public-IP Create][az-network-public-ip-create] Vytvořte veřejnou adresu a pak v bráně firewall vytvořte konfiguraci protokolu IP pomocí příkazu [AZ Network firewall IP-config Create][az-network-firewall-ip-config-create] , který se použije pro veřejnou IP adresu:

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

Teď vytvořte pravidlo sítě brány firewall Azure, abyste *povolili* všechny přenosy *TCP* pomocí příkazu [AZ Network firewall Network-Rule Create][az-network-firewall-network-rule-create] . Následující příklad vytvoří síťové pravidlo s názvem *AllowTCPOutbound* pro provoz s jakoukoli zdrojovou nebo cílovou adresou:

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

K přidružení brány firewall Azure k síťové trase Získejte existující informace o směrovací tabulce, interní IP adresu brány firewall Azure a pak IP adresu serveru rozhraní API. Tyto IP adresy jsou uvedené v další části, které určují, jak se má provoz směrovat na komunikaci s clustery.

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

Nakonec vytvořte trasu v existující tabulce směrování sítě AKS pomocí příkazu [AZ Network Route-Table Route Create][az-network-route-table-route-create] , který umožňuje provozu používat zařízení brány firewall Azure pro komunikaci serveru API.

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

Poznamenejte si veřejnou IP adresu vašeho zařízení Azure Firewall. Tato adresa se přidá do seznamu rozsahů povolených IP adres serveru API v další části.

## <a name="enable-authorized-ip-ranges"></a>Povolit rozsahy povolených IP adres

Pokud chcete povolit rozsahy IP adres autorizovaných serverem API, zadejte seznam autorizovaných rozsahů IP adres. Když zadáte rozsah CIDR, začněte první IP adresou v rozsahu. Například *137.117.106.90/29* je platný rozsah, ale ujistěte se, že jste zadali první IP adresu v rozsahu, například *137.117.106.88/29*.

Použijte příkaz [AZ AKS Update][az-aks-update] a určete *Rozsah--API-Server-povoleno-IP-rozsahy* , které chcete povolit. Tyto rozsahy IP adres jsou obvykle rozsahy adres používané vašimi místními sítěmi. Přidejte veřejnou IP adresu vlastní brány Azure firewall získanou v předchozím kroku, například *20.42.25.196/32*.

Následující příklad povoluje rozsahy IP adres ověřených serverem API v clusteru s názvem *myAKSCluster* ve skupině prostředků s názvem *myResourceGroup*. Rozsahy IP adres, které se mají autorizovat, jsou *20.42.25.196/32* (veřejná IP adresa brány firewall Azure), potom *172.0.0.0/16* (rozsah adres pod/uzly) a *168.10.0.0/18* (ServiceCidr):

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges 20.42.25.196/32,172.0.0.0/16,168.10.0.0/18
```

> [!NOTE]
> Tyto rozsahy byste měli přidat do seznamu povolených:
> - Veřejná IP adresa brány firewall
> - CIDR služby
> - Rozsah adres pro podsítě, uzly a lusky
> - Libovolný rozsah, který představuje sítě, ze kterých budete spravovat cluster.

## <a name="update-or-disable-authorized-ip-ranges"></a>Aktualizovat nebo zakázat schválené rozsahy IP adres

Pokud chcete aktualizovat nebo zakázat rozsahy autorizovaných IP adres, znovu použijte příkaz [AZ AKS Update][az-aks-update] . Zadejte aktualizovaný rozsah CIDR, který chcete povolit, nebo zadejte prázdný rozsah pro zákaz rozsahů IP adres autorizovaných serverem API, jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste povolili rozsahy IP adres ověřených serverem API. Tento přístup je jednou ze součástí, jak můžete spustit zabezpečený cluster AKS.

Další informace najdete v tématu [koncepty zabezpečení pro aplikace a clustery v AKS][concepts-security] a [osvědčené postupy pro zabezpečení a upgrady clusteru v AKS][operator-best-practices-cluster-security].

<!-- LINKS - external -->
[azure-firewall-costs]: https://azure.microsoft.com/pricing/details/azure-firewall/
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md

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
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-firewall-create]: /cli/azure/ext/azure-firewall/network/firewall#ext-azure-firewall-az-network-firewall-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-network-firewall-ip-config-create]: /cli/azure/ext/azure-firewall/network/firewall/ip-config#ext-azure-firewall-az-network-firewall-ip-config-create
[az-network-firewall-network-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/network-rule#ext-azure-firewall-az-network-firewall-network-rule-create
[az-network-route-table-route-create]: /cli/azure/network/route-table/route#az-network-route-table-route-create
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[standard-sku-lb]: load-balancer-standard.md
