---
title: Počet povolených IP adres serveru API ve službě Azure Kubernetes (AKS)
description: Naučte se zabezpečit cluster pomocí rozsahu IP adres pro přístup k serveru rozhraní API ve službě Azure Kubernetes (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: 8418499cc3e094162ac7483aaa6c71e74db95ae1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73472956"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Zabezpečený přístup k serveru rozhraní API pomocí rozsahů povolených IP adres ve službě Azure Kubernetes Service (AKS)

V Kubernetes Server rozhraní API přijímá požadavky na provádění akcí v clusteru, jako je například vytváření prostředků nebo škálování počtu uzlů. Server rozhraní API je ústředním prostředkem pro interakci s clusterem a jeho správu. Pro zlepšení zabezpečení clusteru a minimalizaci útoků by měl být Server API přístupný jenom z omezené sady rozsahů IP adres.

V tomto článku se dozvíte, jak pomocí rozsahů povolených IP adres serveru API omezit, které IP adresy a CIDRs můžou získat přístup k rovině řízení.

> [!IMPORTANT]
> V nových clusterech se rozsahy povolených IP adres serveru API podporují jenom v nástroji pro vyrovnávání zatížení *Standard* SKU. Stávající clustery s nakonfigurovanými rozsahy služby Load Balancer *základní* skladové položky a povolenými rozsahy IP adres serveru API budou dál fungovat tak, jak jsou. Existující clusers můžou být také upgradované a budou fungovat i nadále.

## <a name="before-you-begin"></a>Než začnete

V tomto článku se předpokládá, že pracujete s clustery, které používají [kubenet][kubenet].  S clustery založenými na [CNI (Azure Container Networking Interface)][cni-networking] nebudete mít potřebnou směrovací tabulku potřebnou k zabezpečení přístupu.  Směrovací tabulku budete muset vytvořit ručně.  Další informace najdete v tématu [Správa směrovacích tabulek](https://docs.microsoft.com/azure/virtual-network/manage-route-table) .

Schválené rozsahy IP adres serveru API fungují jenom pro nové clustery AKS, které vytvoříte. V tomto článku se dozvíte, jak vytvořit cluster AKS pomocí Azure CLI.

Potřebujete nainstalovanou a nakonfigurovanou verzi Azure CLI 2.0.76 nebo novější. Pro nalezení verze spusťte `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI][install-azure-cli].

## <a name="limitations"></a>Omezení

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
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --generate-ssh-keys

# Get credentials to access the cluster
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="update-cluster-with-authorized-ip-ranges"></a>Aktualizovat cluster s povolenými rozsahy IP adres

Ve výchozím nastavení používá váš cluster nástroj pro [Vyrovnávání zatížení Standard SKU][standard-sku-lb], který můžete použít ke konfiguraci odchozí brány. Použijte příkaz [AZ Network Public-IP list][az-network-public-ip-list] a zadejte skupinu prostředků clusteru AKS, který obvykle začíná na *MC_* . Tím se zobrazí veřejná IP adresa pro váš cluster, což umožňuje. Pomocí příkazu [AZ AKS Update][az-aks-update] zadejte parametr *--API-Server-autorizovaná-IP-Ranges* , který umožní IP adresu clusteru. Například:

```azurecli-interactive
RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SLB_PublicIP=$(az network public-ip list --resource-group $RG --query [].ipAddress -o tsv)
az aks update --api-server-authorized-ip-ranges $SLB_PublicIP --resource-group myResourceGroup --name myAKSCluster
```

Pokud chcete povolit rozsahy IP adres autorizovaných serverem API, použijte příkaz [AZ AKS Update][az-aks-update] a zadáním parametru *--API-Server-Adresa_IP-IP-Ranges* zadejte seznam autorizovaných rozsahů IP adres. Tyto rozsahy IP adres jsou obvykle rozsahy adres používané vašimi místními sítěmi nebo veřejnými IP adresami. Když zadáte rozsah CIDR, začněte první IP adresou v rozsahu. Například *137.117.106.90/29* je platný rozsah, ale ujistěte se, že jste zadali první IP adresu v rozsahu, například *137.117.106.88/29*.

Následující příklad povoluje rozsahy IP adres ověřených serverem API v clusteru s názvem *myAKSCluster* ve skupině prostředků s názvem *myResourceGroup*. Rozsahy IP adres, které se mají autorizovat, jsou *172.0.0.0/16* (rozsah adres pod/uzly) a *168.10.0.0/18* (ServiceCidr):

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges 172.0.0.0/16,168.10.0.0/18
```

> [!NOTE]
> Tyto rozsahy byste měli přidat do seznamu povolených:
> - Veřejná IP adresa brány firewall
> - CIDR služby
> - Rozsah adres pro podsítě, uzly a lusky
> - Libovolný rozsah, který představuje sítě, ze kterých budete spravovat cluster.

## <a name="disable-authorized-ip-ranges"></a>Zakázat autorizované rozsahy IP adres

Pokud chcete zakázat rozsahy povolených IP adres, použijte příkaz [AZ AKS Update][az-aks-update] a zadáním prázdného rozsahu zakažte rozsahy IP adres autorizovaných serverem API. Například:

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
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[standard-sku-lb]: load-balancer-standard.md
