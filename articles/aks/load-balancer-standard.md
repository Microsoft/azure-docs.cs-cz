---
title: Použití nástroje pro vyrovnávání zatížení Standard SKU ve službě Azure Kubernetes (AKS)
description: Naučte se používat nástroj pro vyrovnávání zatížení se standardní SKU k vystavování služeb pomocí Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 09/27/2019
ms.author: zarhoads
ms.openlocfilehash: c2d652b31c264d7b17fcf303564c327d09d416f9
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929133"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Použití nástroje pro vyrovnávání zatížení Standard SKU ve službě Azure Kubernetes (AKS)

Pokud chcete zajistit přístup k vašim aplikacím ve službě Azure Kubernetes Service (AKS), můžete vytvořit a použít Azure Load Balancer. Nástroj pro vyrovnávání zatížení, který běží na AKS, se dá použít jako interní nebo externí nástroj pro vyrovnávání zatížení. Interní nástroj pro vyrovnávání zatížení zpřístupňuje službu Kubernetes jenom aplikacím běžícím ve stejné virtuální síti jako cluster AKS. Externí nástroj pro vyrovnávání zatížení přijímá jednu nebo více veřejných IP adres pro příchozí provoz a zpřístupňuje službu Kubernetes externě pomocí veřejných IP adres.

Azure Load Balancer je k dispozici ve dvou SKU – *Basic* a *Standard*. Ve výchozím nastavení se *standardní* SKU používá při vytváření clusteru AKS. Použití nástroje pro vyrovnávání zatížení *Standard* SKU poskytuje další funkce, jako je větší velikost fondu back-endu a zóny dostupnosti. Před výběrem, který se má použít, je důležité porozumět rozdílům mezi *standardními* a *základními* nástroji pro vyrovnávání zatížení. Po vytvoření clusteru AKS již nelze změnit SKLADOVOU položku Nástroje pro vyrovnávání zatížení pro daný cluster. Další informace o jednotkách *Basic* a *Standard* najdete v tématu [porovnání skladové položky služby Vyrovnávání zatížení Azure][azure-lb-comparison].

Tento článek předpokládá základní znalost konceptů Kubernetes a Azure Load Balancer. Další informace najdete v tématu [Kubernetes Core koncepty pro Azure Kubernetes Service (AKS)][kubernetes-concepts] a [co je Azure Load Balancer?][azure-lb].

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít spuštěnou verzi Azure CLI 2.0.74 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

## <a name="before-you-begin"></a>Než začnete
V tomto článku se předpokládá, že máte cluster AKS s Azure Load Balancerem *standardní* SKU. Pokud potřebujete cluster AKS, přečtěte si rychlý Start AKS a [použijte Azure CLI][aks-quickstart-cli] nebo [Azure Portal][aks-quickstart-portal].

Instanční objekt služby AKS potřebuje také oprávnění ke správě síťových prostředků, pokud použijete existující podsíť nebo skupinu prostředků. Obecně přiřaďte roli *Přispěvatel sítě* k instančnímu objektu u delegovaných prostředků. Další informace o oprávněních najdete v tématu [delegování přístupu AKS k ostatním prostředkům Azure][aks-sp].

### <a name="limitations"></a>Omezení

Při vytváření a správě clusterů AKS, které podporují Nástroj pro vyrovnávání zatížení se *standardní* SKU, platí následující omezení:

* Pro povolení odchozího provozu z clusteru AKS se vyžaduje aspoň jedna předpona veřejné IP adresy nebo IP adresy. K udržení připojení mezi řídicí rovinou a uzly agenta a také s cílem udržet kompatibilitu s předchozími verzemi AKS se vyžaduje taky předpona veřejných IP adres nebo IP adres. Máte následující možnosti, jak zadat veřejné IP adresy nebo předpony IP pomocí nástroje pro vyrovnávání zatížení *Standard* SKU:
    * Poskytněte vlastní veřejné IP adresy.
    * Zadejte vlastní předpony veřejných IP adres.
    * Zadejte číslo až 100, aby mohl cluster AKS vytvořit tento počet veřejných IP adres *Standard* SKU ve stejné skupině prostředků, která se vytvořila jako cluster AKS, který se obvykle jmenuje s *MC_* na začátku. AKS přiřadí veřejnou IP adresu k nástroji pro vyrovnávání zatížení *Standard* SKU. Ve výchozím nastavení se jedna veřejná IP adresa automaticky vytvoří ve stejné skupině prostředků jako cluster AKS, pokud není zadaná žádná veřejná IP adresa, předpona veřejné IP adresy nebo počet IP adres. Je také nutné, abyste povolili veřejné adresy a nevytvořili Azure Policy, která zakazuje vytváření IP adres.
* Při použití *standardní* skladové položky (SKU) pro nástroj pro vyrovnávání zatížení je nutné použít Kubernetes verze 1,13 nebo vyšší.
* Definování SKU nástroje pro vyrovnávání zatížení se dá provést jenom při vytváření clusteru AKS. SKU nástroje pro vyrovnávání zatížení nelze změnit po vytvoření clusteru AKS.
* V jednom clusteru můžete použít jenom jednu SKU nástroje pro vyrovnávání zatížení.

## <a name="configure-the-load-balancer-to-be-internal"></a>Konfigurace nástroje pro vyrovnávání zatížení jako interního

Nástroj pro vyrovnávání zatížení můžete také nakonfigurovat tak, aby byl interní a nezveřejňuje veřejnou IP adresu. Pokud chcete nástroj pro vyrovnávání zatížení nakonfigurovat jako interní, přidejte `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` jako anotaci do služby *Vyrovnávání* zatížení. [Tady][internal-lb-yaml]vidíte ukázkový manifest YAML a další podrobnosti o interním nástroji pro vyrovnávání zatížení.

## <a name="scale-the-number-of-managed-public-ips"></a>Škálování počtu spravovaných veřejných IP adres

Pokud používáte nástroj pro vyrovnávání zatížení *standardní* skladové položky se spravovanými odchozími veřejnými IP adresami, které se ve výchozím nastavení vytvoří, můžete škálovat počet spravovaných odchozích veřejných IP adres pomocí parametru *Vyrovnávání zatížení – spravovaná-IP-Count* .

Chcete-li aktualizovat existující cluster, spusťte následující příkaz. Tento parametr je také možné nastavit v době vytvoření clusteru a mít několik spravovaných odchozích veřejných IP adres.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

Výše uvedený příklad nastaví počet spravovaných odchozích veřejných IP adres na *2* pro cluster *myAKSCluster* v *myResourceGroup*. 

K nastavení počátečního počtu spravovaných odchozích veřejných IP adres při vytváření clusteru pomocí parametru `--load-balancer-managed-outbound-ip-count` a jeho nastavení na požadovanou hodnotu můžete použít taky parametr *Managed-IP-Count nástroje pro vyrovnávání zatížení* . Výchozí počet spravovaných odchozích veřejných IP adres je 1.

## <a name="provide-your-own-public-ips-or-prefixes-for-egress"></a>Poskytněte vlastní veřejné IP adresy nebo předpony pro odchozí přenosy

Při použití *standardního* nástroje pro vyrovnávání zatížení SKU cluster AKS automaticky vytvoří veřejnou IP adresu ve stejné skupině prostředků vytvořené pro cluster AKS a přiřadí veřejnou IP adresu k nástroji pro vyrovnávání zatížení *Standard* SKU. Alternativně můžete přiřadit vlastní veřejnou IP adresu při vytváření clusteru nebo můžete aktualizovat vlastnosti nástroje pro vyrovnávání zatížení existujícího clusteru.

Když nasazujete víc IP adres nebo předpon, můžete při definování IP adresy za jediným objektem nástroje pro vyrovnávání zatížení definovat více služeb. Koncový bod odchozího přenosu konkrétních uzlů bude záviset na službě, ke které jsou přidruženy.

> [!IMPORTANT]
> Pro odchozí přenosy s vaší *standardní* SKU nástroje pro vyrovnávání zatížení musíte použít veřejné IP adresy *Standard* SKU. SKU svých veřejných IP adres můžete ověřit pomocí příkazu [AZ Network Public-IP show][az-network-public-ip-show] :
>
> ```azurecli-interactive
> az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
> ```

Pomocí příkazu [AZ Network Public-IP show zobrazíte][az-network-public-ip-show] seznam ID veřejných IP adres.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

Výše uvedený příkaz zobrazuje ID veřejné IP adresy *myPublicIP* ve skupině prostředků *myResourceGroup* .

Pomocí příkazu *AZ AKS Update* s parametrem *Load Balancer-Outbound-IPS* aktualizujte svůj cluster pomocí veřejných IP adres.

V následujícím příkladu se používá parametr *Vyrovnávání zatížení – odchozí IP adresy* s ID z předchozího příkazu.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Můžete také použít předpony veřejných IP adres pro výstup pomocí nástroje pro vyrovnávání zatížení *Standard* SKU. V následujícím příkladu se pomocí příkazu [AZ Network Public-IP prefix show zobrazí][az-network-public-ip-prefix-show] seznam ID předpon veřejných IP adres:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

Výše uvedený příkaz zobrazuje ID předpony veřejné IP adresy *myPublicIPPrefix* ve skupině prostředků *myResourceGroup* .

V následujícím příkladu se používá parametr *Vyrovnávání zatížení-odchozí-IP-předpony* s ID z předchozího příkazu.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

> [!IMPORTANT]
> Veřejné IP adresy a předpony IP adres musí být ve stejné oblasti a v rámci stejného předplatného jako cluster AKS. 

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>Definování vlastní veřejné IP adresy nebo předpon v době vytvoření clusteru

Možná budete chtít uvést vlastní IP adresy nebo předpony IP pro výstup v době vytváření clusteru pro podporu scénářů, jako je například povolený koncový bod odchozích dat. Přidejte stejné parametry uvedené výše do vašeho kroku vytvoření clusteru a definujte vlastní veřejné IP adresy a předpony IP adres na začátku životního cyklu clusteru.

Pomocí příkazu *AZ AKS Create* s parametrem *Load Balancer-Outbound-IPS* vytvořte nový cluster s vašimi veřejnými IP adresami na začátku.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Pomocí příkazu *AZ AKS Create* s parametrem *Load Balance-Outbound-IP-prefixs* vytvořte nový cluster s předponami veřejné IP adresy na začátku.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

## <a name="show-the-outbound-rule-for-your-load-balancer"></a>Zobrazit odchozí pravidlo pro nástroj pro vyrovnávání zatížení

Pokud chcete zobrazit odchozí pravidlo vytvořené v nástroji pro vyrovnávání zatížení, použijte příkaz [AZ Network Outbound-rule list][az-network-lb-outbound-rule-list] a zadejte skupinu prostředků uzlu clusteru AKS:

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

Předchozí příkazy zobrazí seznam odchozích pravidel pro nástroj pro vyrovnávání zatížení, například:

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

V příkladu výstupu je *AllocatedOutboundPorts* 0. Hodnota pro *AllocatedOutboundPorts* znamená, že se přidělení portu SNAT vrátí na automatické přiřazení na základě velikosti fondu back-endu. Další podrobnosti najdete v tématu [Load Balancer odchozích pravidel][azure-lb-outbound-rules] a [odchozích připojení v Azure][azure-lb-outbound-connections] .

## <a name="next-steps"></a>Další kroky

Další informace o službách Kubernetes Services najdete v [dokumentaci ke službám Kubernetes][kubernetes-services].

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-network-lb-outbound-rule-list]: /cli/azure/network/lb/outbound-rule?view=azure-cli-latest#az-network-lb-outbound-rule-list
[az-network-public-ip-show]: /cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus
[azure-lb-outbound-rules]: ../load-balancer/load-balancer-outbound-rules-overview.md#snatports
[azure-lb-outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#snat
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
