---
title: Počet povolených IP adres serveru API ve službě Azure Kubernetes (AKS)
description: Naučte se zabezpečit cluster pomocí rozsahu IP adres pro přístup k serveru rozhraní API ve službě Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.openlocfilehash: 9828682fa71d023356b174d528c2137ed29f368d
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94682498"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Zabezpečený přístup k serveru rozhraní API pomocí rozsahů povolených IP adres ve službě Azure Kubernetes Service (AKS)

V Kubernetes Server rozhraní API přijímá požadavky na provádění akcí v clusteru, jako je například vytváření prostředků nebo škálování počtu uzlů. Server rozhraní API je ústředním prostředkem pro interakci s clusterem a jeho správu. Pro zlepšení zabezpečení clusteru a minimalizaci útoků by měl být Server API přístupný jenom z omezené sady rozsahů IP adres.

V tomto článku se dozvíte, jak pomocí rozsahů povolených IP adres serveru API omezit, které IP adresy a CIDRs můžou získat přístup k rovině řízení.

## <a name="before-you-begin"></a>Než začnete

V tomto článku se dozvíte, jak vytvořit cluster AKS pomocí Azure CLI.

Potřebujete nainstalovanou a nakonfigurovanou verzi Azure CLI 2.0.76 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

### <a name="limitations"></a>Omezení

Funkce rozsahů povolených IP adres serveru API má následující omezení:
- U clusterů vytvořených po uplynutí verze Preview se rozsahy IP adres serveru API v říjnu 2019 nepřesunuly, ale v nástroji pro vyrovnávání zatížení *Standard* SKU se podporují jenom tyto rozsahy IP adres serveru API. Stávající clustery s nakonfigurovanými rozsahy služby Load Balancer *základní* skladové položky a povolenými rozsahy IP adres serveru API budou nadále fungovat, jak je, ale nelze je migrovat do nástroje pro vyrovnávání zatížení *Standard* SKU. Tyto existující clustery budou i nadále fungovat, pokud se upgradují jejich verze Kubernetes nebo řídicí plocha. Rozsahy IP adres autorizovaných serverem API se pro privátní clustery nepodporují.
- Tato funkce není kompatibilní s clustery, které používají [veřejné IP adresy pro fondy uzlů uzlů v rámci funkce Preview](use-multiple-node-pools.md#assign-a-public-ip-per-node-for-your-node-pools-preview).

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Přehled povolených IP adres serveru rozhraní API

Kubernetes API Server je způsob, jakým jsou vystavená základní rozhraní Kubernetes API. Tato součást poskytuje interakci pro nástroje pro správu, například `kubectl` nebo řídicí panel Kubernetes. AKS poskytuje rovinu řízení clusteru s jedním klientem a vyhrazeným serverem API. Ve výchozím nastavení je serveru rozhraní API přiřazena veřejná IP adresa a měli byste řídit přístup pomocí Kubernetes řízení přístupu založeného na rolích (Kubernetes RBAC) nebo Azure RBAC.

Chcete-li zabezpečit přístup k jinému veřejně přístupné rovině AKS ovládacího prvku nebo serveru rozhraní API, můžete povolit a použít autorizované rozsahy IP adres. Tyto autorizované rozsahy IP adres povolují komunikaci se serverem API jenom definované rozsahy IP adres. Požadavek na server rozhraní API z IP adresy, která není součástí těchto povolených rozsahů IP adres, je blokovaný. Nadále používejte Kubernetes RBAC nebo Azure RBAC k autorizaci uživatelů a akcí, které požadují.

Další informace o serveru rozhraní API a dalších součástech clusteru najdete v tématu [základní koncepty Kubernetes pro AKS][concepts-clusters-workloads].

## <a name="create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled"></a>Vytvoření clusteru AKS s povoleným rozsahem IP adres autorizovaným serverem API

Vytvořte cluster pomocí funkce [AZ AKS Create][az-aks-create] a zadáním *`--api-server-authorized-ip-ranges`* parametru uveďte seznam autorizovaných rozsahů IP adres. Tyto rozsahy IP adres jsou obvykle rozsahy adres používané vašimi místními sítěmi nebo veřejnými IP adresami. Když zadáte rozsah CIDR, začněte první IP adresou v rozsahu. Například *137.117.106.90/29* je platný rozsah, ale ujistěte se, že jste zadali první IP adresu v rozsahu, například *137.117.106.88/29*.

> [!IMPORTANT]
> Ve výchozím nastavení používá váš cluster nástroj pro [Vyrovnávání zatížení Standard SKU][standard-sku-lb] , který můžete použít ke konfiguraci odchozí brány. Když povolíte rozsah IP adres povolených serverem API během vytváření clusteru, ve výchozím nastavení se kromě rozsahů, které určíte, povoluje taky veřejná IP adresa pro váš cluster. Pokud zadáte *""* nebo žádnou hodnotu pro *`--api-server-authorized-ip-ranges`* , rozsahy povolených IP adres serveru API budou zakázané. Všimněte si, že pokud používáte PowerShell, použijte *`--api-server-authorized-ip-ranges=""`* (se znaménkem rovná se), abyste se vyhnuli jakýmkoli problémům s analýzou.

Následující příklad vytvoří cluster s jedním uzlem s názvem *myAKSCluster* ve skupině prostředků s názvem *myResourceGroup* s povolenými rozsahy IP adres povolených serverů API. Povolené rozsahy IP adres jsou *73.140.245.0/24*:

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
> Tyto rozsahy byste měli přidat do seznamu povolených:
> - Veřejná IP adresa brány firewall
> - Libovolný rozsah, který představuje sítě, ze kterých budete spravovat cluster.
> - Pokud používáte Azure Dev Spaces v clusteru AKS, musíte na [základě vaší oblasti zapnout další rozsahy][dev-spaces-ranges].
>
> Horní limit počtu rozsahů IP adres, které můžete zadat, je 200.
>
> Tato pravidla můžou trvat až 2min, než se rozšíří. Po otestování připojení prosím povolte až tuto dobu.

### <a name="specify-the-outbound-ips-for-the-standard-sku-load-balancer"></a>Zadejte odchozí IP adresy pro nástroj pro vyrovnávání zatížení Standard SKU.

Pokud při vytváření clusteru AKS zadáte odchozí IP adresy nebo předpony pro cluster, jsou povoleny také tyto adresy nebo předpony. Například:

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

V předchozím příkladu jsou povoleny všechny IP adresy, které jsou zadány v parametru, *`--load-balancer-outbound-ip-prefixes`* spolu s IP adresami v *`--api-server-authorized-ip-ranges`* parametru.

Místo toho můžete zadat *`--load-balancer-outbound-ip-prefixes`* parametr, který povoluje předpony IP adres odchozího nástroje pro vyrovnávání zatížení.

### <a name="allow-only-the-outbound-public-ip-of-the-standard-sku-load-balancer"></a>Povolí jenom odchozí veřejnou IP adresu pro nástroj pro vyrovnávání zatížení Standard SKU.

Pokud povolíte rozsah IP adres povolených serverem API během vytváření clusteru, kromě rozsahů, které zadáte, je ve výchozím nastavení povolená i odchozí veřejná IP adresa pro nástroj pro vyrovnávání zatížení Standard SKU pro váš cluster. Pokud chcete v nástroji pro vyrovnávání zatížení Standard SKU povoluje jenom odchozí veřejnou IP adresu, použijte při zadávání parametru parametr *0.0.0.0/32* *`--api-server-authorized-ip-ranges`* .

V následujícím příkladu je povolená jenom odchozí veřejná IP adresa standardního nástroje pro vyrovnávání zatížení (SKU) a přístup k serveru rozhraní API je možný jenom z uzlů v clusteru.

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

## <a name="update-a-clusters-api-server-authorized-ip-ranges"></a>Aktualizace rozsahů povolených IP adres serveru API clusteru

Chcete-li aktualizovat rozsahy IP adres ověřených serverem API v existujícím clusteru, použijte příkaz [AZ AKS Update][az-aks-update] a použijte,--pro vyrovnávání *`--api-server-authorized-ip-ranges`* zatížení – odchozí-IP-prefixy *, *`--load-balancer-outbound-ips`* nebo--Load---Outbound-IP-Prefixes* .

Následující příklad aktualizuje rozsahy IP adres ověřených serverem API v clusteru s názvem *myAKSCluster* ve skupině prostředků s názvem *myResourceGroup*. Rozsah IP adres, který se má autorizovat, je *73.140.245.0/24*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges  73.140.245.0/24
```

Při zadání parametru můžete použít taky *0.0.0.0/32* , *`--api-server-authorized-ip-ranges`* aby se povolila jenom veřejná IP adresa standardního nástroje pro vyrovnávání zatížení (SKU).

## <a name="disable-authorized-ip-ranges"></a>Zakázat autorizované rozsahy IP adres

Pokud chcete zakázat rozsahy povolených IP adres, použijte příkaz [AZ AKS Update][az-aks-update] a zadáním prázdného rozsahu zakažte rozsahy IP adres autorizovaných serverem API. Například:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="how-to-find-my-ip-to-include-in---api-server-authorized-ip-ranges"></a>Jak najít moji IP adresu, kterou chcete zahrnout do `--api-server-authorized-ip-ranges` ?

Aby bylo možné získat přístup k serveru rozhraní API z této služby, musíte přidat své vývojové počítače, nástroje nebo IP adresy automatizace do seznamu clusterů AKS se schválenými rozsahy IP adres. 

Další možností je nakonfigurovat JumpBox s potřebnými nástroji v samostatné podsíti ve virtuální síti brány firewall. Předpokládá se, že vaše prostředí má bránu firewall s příslušnou sítí a že jste do autorizovaných rozsahů přidali IP adresy brány firewall. Podobně platí, že pokud máte vynucené tunelování z podsítě AKS do podsítě brány firewall, nemusíte mít k dispozici také JumpBox v podsíti clusteru.

Pomocí následujícího příkazu přidejte další IP adresu ke schváleným rozsahům.

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)
# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32
```

>> [!NOTE]
> Výše uvedený příklad připojí rozsah IP adres autorizovaných serverů rozhraní API v clusteru. Pokud chcete zakázat rozsahy povolených IP adres, použijte příkaz AZ AKS Update a zadejte prázdný rozsah. 

Další možností je použít následující příkaz v systémech Windows k získání veřejné adresy IPv4 nebo můžete použít postup v části [Vyhledání IP adresy](https://support.microsoft.com/en-gb/help/4026518/windows-10-find-your-ip-address).

```azurepowershell-interactive
Invoke-RestMethod http://ipinfo.io/json | Select -exp ip
```

Tuto adresu můžete najít také tak, že v internetovém prohlížeči vyhledáte "to je moje IP adresa".

## <a name="next-steps"></a>Další kroky

V tomto článku jste povolili rozsahy IP adres ověřených serverem API. Tento přístup je jednou ze součástí, jak můžete spustit zabezpečený cluster AKS.

Další informace najdete v tématu [koncepty zabezpečení pro aplikace a clustery v AKS][concepts-security] a [osvědčené postupy pro zabezpečení a upgrady clusteru v AKS][operator-best-practices-cluster-security].

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[dev-spaces-ranges]: ../dev-spaces/configure-networking.md#aks-cluster-network-requirements
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
