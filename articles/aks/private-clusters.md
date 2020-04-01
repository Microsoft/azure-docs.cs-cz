---
title: Vytvoření privátního clusteru služby Azure Kubernetes
description: Zjistěte, jak vytvořit privátní cluster služby Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 2/21/2020
ms.openlocfilehash: 87f52c5a749b531e5b0656e0b30ff0fe9c1a57bf
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398054"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>Vytvoření privátního clusteru služby Azure Kubernetes

V privátním clusteru má řídicí rovina nebo server rozhraní API interní adresy IP, které jsou definovány v dokumentu [RFC1918 – Address Allocation for Private Internets.](https://tools.ietf.org/html/rfc1918) Pomocí privátního clusteru můžete zajistit, aby síťový provoz mezi serverem rozhraní API a fondy uzlů zůstal pouze v privátní síti.

Rovina ovládacího prvku nebo server rozhraní API se nachází v předplatném Azure Kubernetes Service (AKS)spravovaném službou Azure. Cluster nebo fond uzlů zákazníka je v předplatném zákazníka. Server a fond clusteru nebo uzlu mohou vzájemně komunikovat prostřednictvím [služby Azure Private Link][private-link-service] ve virtuální síti serveru API a privátního koncového bodu, který je vystaven v podsíti clusteru AKS zákazníka.

## <a name="prerequisites"></a>Požadavky

* Azure CLI verze 2.2.0 nebo novější

## <a name="create-a-private-aks-cluster"></a>Vytvoření privátního clusteru AKS

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků nebo použijte existující skupinu prostředků pro cluster AKS.

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>Výchozí základní síť 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Kde *--enable-private-cluster* je povinný příznak pro privátní cluster. 

### <a name="advanced-networking"></a>Pokročilé sítě  

```azurecli-interactive
az aks create \
    --resource-group <private-cluster-resource-group> \
    --name <private-cluster-name> \
    --load-balancer-sku standard \
    --enable-private-cluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 
```
Kde *--enable-private-cluster* je povinný příznak pro privátní cluster. 

> [!NOTE]
> Pokud se most Dockeru přikončuje cidr (172.17.0.1/16) do konfliktu s cidr, změňte adresu mostu Docker.

## <a name="options-for-connecting-to-the-private-cluster"></a>Možnosti připojení k privátnímu clusteru

Koncový bod serveru API nemá žádnou veřejnou IP adresu. Chcete-li spravovat server API, budete muset použít virtuální počítač, který má přístup k virtuální síti Azure (Virtuální síť) clusteru AKS. Existuje několik možností pro navázání síťového připojení k privátnímu clusteru.

* Vytvořte virtuální počítač ve stejné virtuální síti Azure (VNet) jako cluster AKS.
* Použijte virtuální počítač v samostatné síti a nastavte [partnerský vztah virtuální sítě][virtual-network-peering].  Další informace o této možnosti naleznete v části níže.
* Použijte připojení [expresní trasy nebo sítě VPN.][express-route-or-VPN]

Vytvoření virtuálního virtuálního virtuálního virtuálního clusteru jako clusteru AKS je nejjednodušší možnost.  Expresní trasy a sítě VIRTUÁLNÍ sítě zvyšují náklady a vyžadují další složitost sítě.  Partnerský vztah virtuální sítě vyžaduje, abyste naplánovali rozsahy CIDR sítě, abyste zajistili, že neexistují žádné překrývající se rozsahy.

## <a name="virtual-network-peering"></a>Partnerské vztahy virtuálních sítí

Jak již bylo zmíněno, partnerský vztah virtuální sítě je jedním ze způsobů, jak získat přístup k privátnímu clusteru. Pokud chcete použít partnerský vztah virtuální sítě, musíte nastavit propojení mezi virtuální sítí a privátní zónou DNS.
    
1. Přejděte do skupiny prostředků MC_* na webu Azure Portal.  
2. Vyberte soukromou zónu DNS.   
3. V levém podokně vyberte propojení **Virtuální síť.**  
4. Vytvořte nový odkaz pro přidání virtuální sítě virtuálního počítače do privátní zóny DNS. Připojení zóny DNS trvá několik minut.  
5. Vraťte se do skupiny prostředků MC_* na webu Azure Portal.  
6. V pravém podokně vyberte virtuální síť. Název virtuální sítě je ve formě *aks-vnet-\**.  
7. V levém podokně vyberte **Partnerské partnerské partnerské strany**.  
8. Vyberte **Přidat**, přidejte virtuální síť virtuálního počítače a vytvořte partnerský vztah.  
9. Přejděte do virtuální sítě, kde máte virtuální počítač, vyberte **partnerské partnerské partnery**, vyberte virtuální síť AKS a pak vytvořte partnerský vztah. Pokud se adresa vychýlivek ve virtuální síti AKS a kolizi virtuální sítě virtuálního počítače, partnerský vztah se nezdaří. Další informace naleznete v [tématu Partnerský vztah virtuální sítě][virtual-network-peering].

## <a name="hub-and-spoke-with-custom-dns"></a>Rozbočovač a paprsek s vlastním DNS

[Architektury rozbočovače a paprsků](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) se běžně používají k nasazení sítí v Azure. V mnoha z těchto nasazení nastavení DNS ve virtuálních sítích paprsku jsou nakonfigurovány tak, aby odkazovaly na centrální předávání DNS, aby bylo možné místní a azure-založené rozlišení DNS. Při nasazování clusteru AKS do takového síťového prostředí, existují některé zvláštní aspekty, které je třeba vzít v úvahu.

![Centrum privátního clusteru a paprskový](media/private-clusters/aks-private-hub-spoke.png)

1. Ve výchozím nastavení se při zřízení privátního clusteru vytvoří soukromý koncový bod (1) a soukromá zóna DNS (2) ve skupině spravovaných prostředků clusteru. Cluster používá záznam A v privátní zóně k vyřešení IP adresy privátního koncového bodu pro komunikaci se serverem rozhraní API.

2. Privátní zóna DNS je propojena jenom s virtuální sítí, ke které jsou uzly clusteru připojeny (3). To znamená, že soukromý koncový bod lze vyřešit pouze hostitelé v této propojené virtuální síti. Ve scénářích, kde není na virtuální síti (výchozí) nakonfigurován žádný vlastní DNS, to funguje bez problémů jako hostitelé na čísle 168.63.129.16 pro DNS, který může vyřešit záznamy v privátní zóně DNS kvůli propojení.

3. Ve scénářích, kde virtuální síť obsahující váš cluster má vlastní nastavení DNS (4), nasazení clusteru se nezdaří, pokud privátní zóna DNS je propojena s virtuální sítí, která obsahuje vlastní překladače DNS (5). Tento odkaz lze vytvořit ručně po vytvoření privátní zóny během zřizování clusteru nebo prostřednictvím automatizace při zjišťování vytvoření zóny pomocí zásad Azure nebo jiných mechanismů nasazení založených na událostech (například Azure Event Grid a Azure Functions).

## <a name="dependencies"></a>Závislosti  

* Služba Private Link je podporovaná pouze ve standardním azure balanceru. Základní Azure Balancer není podporován.  
* Chcete-li použít vlastní server DNS, přidejte jako server DNS na vlastním serveru DNS adresu Azure DNS IP 168.63.129.16.

## <a name="limitations"></a>Omezení 
* Ip autorizované rozsahy nelze použít na koncový bod serveru privátní ho rozhraní API, platí pouze pro veřejný server API
* Zóny dostupnosti jsou aktuálně podporovány pro určité oblasti, viz začátek tohoto dokumentu 
* [Omezení služby Azure Private Link][private-link-service] platí pro privátní clustery, koncové body Azure privátní a koncové body virtuální sítě, které nejsou aktuálně podporované ve stejné virtuální síti.
* Žádná podpora virtuálních uzlů v privátním clusteru pro rotaci privátní instance kontejnerů Azure (ACI) v privátní virtuální síti Azure
* Žádná podpora integrace Azure DevOps po vybalení s privátními clustery
* Pro zákazníky, kteří potřebují povolit Azure Container Registry pro práci s privátní AKS, virtuální síť registru kontejnerů musí být partnerský s virtuální sítí clusteru agenta.
* Žádná aktuální podpora azure dev spaces
* Žádná podpora pro převod existujících clusterů AKS na privátní clustery
* Odstranění nebo úprava soukromého koncového bodu v podsíti zákazníka způsobí, že cluster přestane fungovat. 
* Azure Monitor pro kontejnery Živá data není momentálně podporovaná.


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: /azure/private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md

