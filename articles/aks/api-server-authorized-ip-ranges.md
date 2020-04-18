---
title: Rozsahy IP adres serveru API ve službě Azure Kubernetes Service (AKS)
description: Zjistěte, jak zabezpečit cluster pomocí rozsahu IP adres pro přístup k serveru API ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: 570d842409fc019d24446e091f83402f4c288d7c
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640055"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Zabezpečený přístup k serveru API pomocí autorizovaných rozsahů IP adres ve službě Azure Kubernetes Service (AKS)

V Kubernetes server rozhraní API přijímá požadavky na provádění akcí v clusteru, jako je například vytvoření prostředků nebo škálování počtu uzlů. Server rozhraní API je centrální způsob interakce s clusterem a jeho správě. Chcete-li zlepšit zabezpečení clusteru a minimalizovat útoky, server rozhraní API by měl být přístupný pouze z omezené sady rozsahů adres IP.

Tento článek ukazuje, jak používat rozsahy IP adres autorizovaného serveru API k omezení, které IP adresy a CIDR mohou přistupovat k rovině řízení.

> [!IMPORTANT]
> V nových clusterech jsou podporovány rozsahy adres IP autorizovaného serveru API pouze v *modulu* pro vyrovnávání zatížení standardní sku. Existující clustery s nakonfigurovaným *imperací základního* správce min. a autorizovanými rozsahy IP adres serveru API budou fungovat tak, jak jsou, ale nelze je migrovat do *standardního* zařízení pro vyrovnávání zatížení skladových položk. Tyto existující clustery budou také pokračovat v práci, pokud jsou upgradovány jejich verze Kubernetes nebo řídicí roviny.

## <a name="before-you-begin"></a>Než začnete

Rozsahy IP adres serveru API fungují pouze pro nové clustery AKS, které vytvoříte. Tento článek ukazuje, jak vytvořit cluster AKS pomocí azure CLI.

Potřebujete nainstalované a nakonfigurované azure CLI verze 2.0.76 nebo novější. Spuštěním `az --version` najděte verzi. Pokud potřebujete nainstalovat nebo upgradovat, přečtěte si informace [o instalaci příkazového příkazového příkazu k webu Azure][install-azure-cli].

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Přehled rozsahů IP adres autorizovaných serverem API

Kubernetes API server je, jak jsou vystaveny základní rozhraní API Kubernetes. Tato součást poskytuje interakci pro nástroje `kubectl` pro správu, jako je například řídicí panel Kubernetes. AKS poskytuje hlavní server clusteru s jedním tenantem s vyhrazeným serverem ROZHRANÍ API. Ve výchozím nastavení je serveru rozhraní API přiřazena veřejná IP adresa a měli byste řídit přístup pomocí ovládacích prvků přístupu založených na rolích (RBAC).

Chcete-li zabezpečit přístup k jinak veřejně přístupnému řídicímu systému AKS / serveru API, můžete povolit a používat autorizované rozsahy IP adres. Tyto autorizované rozsahy IP adres umožňují komunikaci pouze s definovanými rozsahy IP adres se serverem rozhraní API. Požadavek na server rozhraní API z adresy IP, která není součástí těchto autorizovaných rozsahů IP adres, je blokován. Pokračujte v používání RBAC k autorizaci uživatelů a akcí, které požadují.

Další informace o serveru rozhraní API a dalších součástech clusteru naleznete [v základních konceptech Kubernetes pro AKS][concepts-clusters-workloads].

## <a name="create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled"></a>Vytvoření clusteru AKS s povolenými rozsahy IP serverů API

Rozsahy IP adres serveru API fungují pouze pro nové clustery AKS. Vytvořte cluster pomocí [az aks vytvořit][az-aks-create] a zadejte *parametr --api-server-authorized-ip-ranges,* který poskytuje seznam autorizovaných rozsahů IP adres. Tyto rozsahy IP adres jsou obvykle rozsahy adres používané místními sítěmi nebo veřejnými IP adresami. Když zadáte rozsah CIDR, začněte s první IP adresou v rozsahu. Například *137.117.106.90/29* je platný rozsah, ale ujistěte se, že zadáte první adresu IP v rozsahu, například *137.117.106.88/29*.

> [!IMPORTANT]
> Ve výchozím nastavení používá cluster [standardní vyrovnávání zatížení skladové položky,][standard-sku-lb] který můžete použít ke konfiguraci odchozí brány. Pokud povolíte rozsahy IP adres autorizovaného serveru API během vytváření clusteru, veřejná IP adresa pro váš cluster je také povolena ve výchozím nastavení kromě rozsahů, které zadáte. Pokud zadáte *""* nebo žádnou hodnotu pro *--api-server-authorized-ip-ranges*, budou zakázány rozsahy IP autorizovaných serveru API. Všimněte si, že pokud používáte PowerShell, použijte *--api-server-authorized-ip-ranges=""* (se znaménkem rovná se), abyste se vyhnuli problémům s analýzou.

Následující příklad vytvoří cluster s jedním uzlem s názvem *myAKSCluster* ve skupině prostředků s názvem *myResourceGroup* s povolenými rozsahy IP serverů API. Povolené rozsahy IP adres jsou *73.140.245.0/24*:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --generate-ssh-keys
```

> [!NOTE]
> Tyto rozsahy byste měli přidat do seznamu povolených oblastí:
> - Veřejná IP adresa brány firewall
> - Libovolný rozsah, který představuje sítě, ze kterých budete cluster spravovat
> - Pokud používáte Azure Dev Spaces v clusteru AKS, musíte povolit [další rozsahy založené na vaší oblasti][dev-spaces-ranges].

> Horní limit pro počet rozsahů IP adres, které můžete zadat, je 3500. 

### <a name="specify-the-outbound-ips-for-the-standard-sku-load-balancer"></a>Určení odchozích IP adresy pro standardní účetní jednotku skladového vytížení

Pokud při vytváření clusteru AKS zadáte odchozí adresy IP nebo předpony pro cluster, budou povoleny také tyto adresy nebo předpony. Příklad:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2> \
    --generate-ssh-keys
```

Ve výše uvedeném příkladu jsou povoleny všechny IP adresy uvedené v parametru *--load-balancer-outbound-ip-prefix* spolu s IP adresami v parametru *--api-server-authorized-ip-ranges.*

Případně můžete zadat parametr *--load-balancer-outbound-ip-prefixes,* který umožní výstupní předpony IP vykladače zatížení.

### <a name="allow-only-the-outbound-public-ip-of-the-standard-sku-load-balancer"></a>Povolit pouze odchozí veřejnou IP adresu standardního účetního vytížení skladové položky

Pokud povolíte rozsahy IP adres autorizovaného serveru API během vytváření clusteru, je ve výchozím nastavení kromě rozsahů, které zadáte, povolena také odchozí veřejná IP adresa pro standardní vyrovnávání zatížení skladových aktiv pro váš cluster. Chcete-li povolit pouze odchozí veřejnou IP adresu standardního vykladače zatížení skladové položky, použijte při zadávání parametru *--api-server-authorized-ip-ranges* hodnotu *0.0.0.0/32.*

V následujícím příkladu je povolena pouze odchozí veřejná IP adresa standardního vyrovnávání zatížení skladové položky a přístup k serveru rozhraní API lze získat pouze z uzlů v clusteru.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 0.0.0.0/32 \
    --generate-ssh-keys
```

## <a name="update-a-clusters-api-server-authorized-ip-ranges"></a>Aktualizace rozsahů IP serverů API serveru rozhraní API clusteru

Chcete-li aktualizovat rozsahy IP adres serveru API v existujícím clusteru, použijte příkaz [az aks update][az-aks-update] a použijte *parametry --api-server-authorized-ip-ranges*, *--load-balancer-outbound-ip-prefixes*, *--load-balancer-outbound-ip-prefixes.* *--load-balancer-outbound-ip-prefixes*

Následující příklad aktualizuje rozsahy IP adres serveru API v clusteru s názvem *myAKSCluster* ve skupině prostředků s názvem *myResourceGroup*. Rozsah IP adres, který chcete autorizovat, je *73.140.245.0/24*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges  73.140.245.0/24
```

Můžete také použít *0.0.0.0/32* při zadávání *parametru --api-server-authorized-ip-ranges,* který umožňuje pouze veřejnou IP adresu standardního vyvažovače zatížení Skladové položky.

## <a name="disable-authorized-ip-ranges"></a>Zakázání autorizovaných rozsahů IP adres

Chcete-li zakázat autorizované rozsahy IP adres, použijte [aktualizaci az aks][az-aks-update] a zadejte prázdnou oblast pro zakázání rozsahů IP serverů API. Příklad:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste povolili rozsahy IP serverů API. Tento přístup je jednou částí způsobu spuštění zabezpečeného clusteru AKS.

Další informace naleznete [v tématu Koncepty zabezpečení pro aplikace a clustery v AKS][concepts-security] a [doporučené postupy pro zabezpečení clusteru a upgrady v AKS][operator-best-practices-cluster-security].

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[dev-spaces-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[route-tables]: ../virtual-network/manage-route-table.md
[standard-sku-lb]: load-balancer-standard.md
