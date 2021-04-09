---
title: Vytvoření privátního clusteru služby Azure Kubernetes
description: Zjistěte, jak vytvořit privátní cluster služby Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 3/5/2021
ms.openlocfilehash: 21d839df04c868d2c21932f96a6b72a32b0404e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104771851"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>Vytvoření privátního clusteru služby Azure Kubernetes

V privátním clusteru má řídicí plocha nebo Server rozhraní API interní IP adresy, které jsou definované v [RFC1918 přidělování adres privátního internetového](https://tools.ietf.org/html/rfc1918) dokumentu. Pomocí privátního clusteru můžete zajistit, aby síťový provoz mezi serverem API a fondy uzlů zůstaly jenom v privátní síti.

Rovina ovládacího prvku nebo Server API se nachází v předplatném Azure spravovaném službou Azure Kubernetes (AKS). Cluster nebo fond uzlů zákazníka je v předplatném zákazníka. Server a cluster nebo fond uzlů můžou vzájemně komunikovat prostřednictvím [služby privátního propojení Azure][private-link-service] ve virtuální síti serveru API a privátního koncového bodu, který je vystavený v PODSÍTI clusteru AKS zákazníka.

## <a name="region-availability"></a>Dostupnost v oblastech

Privátní cluster je dostupný ve veřejných oblastech, Azure Government a v oblastech Azure Čína 21Vianet, kde [se podporuje AKS](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service).

> [!NOTE]
> Azure Government weby jsou podporovány, ale US Gov – Texas aktuálně nejsou podporovány z důvodu chybějící podpory privátních odkazů.

## <a name="prerequisites"></a>Požadavky

* Azure CLI verze 2.2.0 nebo novější
* Služba privátního propojení je podporována pouze u standardních Azure Load Balancer. Základní Azure Load Balancer nejsou podporované.  
* Pokud chcete použít vlastní server DNS, přidejte Azure DNS IP 168.63.129.16 jako nadřazený server DNS do vlastního serveru DNS.

## <a name="create-a-private-aks-cluster"></a>Vytvoření privátního clusteru AKS

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků nebo pro svůj cluster AKS použijte existující skupinu prostředků.

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>Výchozí základní sítě 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Kde `--enable-private-cluster` je povinný příznak pro soukromý cluster. 

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
Kde `--enable-private-cluster` je povinný příznak pro soukromý cluster. 

> [!NOTE]
> Pokud adresa mostu Docker (172.17.0.1/16) koliduje s podsítí CIDR, změňte adresu mostu Docker odpovídajícím způsobem.

## <a name="configure-private-dns-zone"></a>Konfigurovat zónu Privátní DNS 

Následující parametry lze využít ke konfiguraci Privátní DNS zóny.

- Výchozí hodnota je "System". Pokud je argument--Private-DNS-Zone vynechán, AKS vytvoří zónu Privátní DNS ve skupině prostředků uzlu.
- Možnost None znamená, že AKS nevytvoří zónu Privátní DNS.  To vyžaduje, abyste zanesli vlastní server DNS a nakonfigurovali překlad DNS pro privátní plně kvalifikovaný název domény.  Pokud neprovedete konfiguraci překladu DNS, dá se služba DNS přeložit jenom v rámci uzlů agentů a po nasazení způsobí problémy s clusterem. 
- CUSTOM_PRIVATE_DNS_ZONE_RESOURCE_ID vyžaduje, abyste v tomto formátu pro globální cloud Azure vytvořili zónu Privátní DNS: `privatelink.<region>.azmk8s.io` . Budete potřebovat ID prostředku, které Privátní DNS zóna dopředá.  Navíc budete potřebovat identitu přiřazenou uživatelem nebo instanční objekt s alespoň `private dns zone contributor` `vnet contributor` rolemi a.
- "plně kvalifikovaný název domény (FQDN) lze využít s" CUSTOM_PRIVATE_DNS_ZONE_RESOURCE_ID "pouze k poskytnutí možností subdomény `privatelink.<region>.azmk8s.io`

### <a name="prerequisites"></a>Požadavky

* Verze Preview verze AKS 0.5.3 nebo novější
* Rozhraní API verze 2020-11-01 nebo novější

### <a name="create-a-private-aks-cluster-with-private-dns-zone-preview"></a>Vytvoření privátního clusteru AKS s využitím zóny Privátní DNS (Preview)

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone [system|none]
```

### <a name="create-a-private-aks-cluster-with-a-custom-private-dns-zone-preview"></a>Vytvoření privátního clusteru AKS s vlastní zónou Privátní DNS (Preview)

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone <custom private dns zone ResourceId> --fqdn-subdomain <subdomain-name>
```
## <a name="options-for-connecting-to-the-private-cluster"></a>Možnosti připojení k privátnímu clusteru

Koncový bod serveru rozhraní API nemá žádnou veřejnou IP adresu. Pokud chcete spravovat Server API, budete muset použít virtuální počítač, který má přístup k Azure Virtual Network AKS (VNet) clusteru. K dispozici je několik možností pro vytvoření síťového připojení k privátnímu clusteru.

* Vytvořte virtuální počítač ve stejné službě Azure Virtual Network (VNet) jako cluster AKS.
* Použijte virtuální počítač v samostatné síti a nastavte [partnerský vztah virtuálních sítí][virtual-network-peering].  Další informace o této možnosti najdete v části níže.
* Použijte [expresní trasu nebo připojení k síti VPN][express-route-or-VPN] .

Nejjednodušší možností je vytvoření virtuálního počítače ve stejné virtuální síti jako cluster AKS.  Expresní směrování a sítě VPN přidávají náklady a vyžadují další složitost sítě.  Partnerský vztah virtuálních sítí vyžaduje, abyste naplánovali rozsahy směrování sítě, aby se zajistilo, že se překrývají rozsahy.

## <a name="virtual-network-peering"></a>Peering virtuálních sítí

Jak už bylo zmíněno, partnerský vztah virtuálních sítí je jedním ze způsobů, jak získat přístup k privátnímu clusteru. Pokud chcete použít partnerský vztah virtuálních sítí, musíte nastavit propojení mezi virtuální sítí a privátní zónou DNS.
    
1. V Azure Portal přejít do skupiny prostředků uzlu.  
2. Vyberte privátní zónu DNS.   
3. V levém podokně vyberte odkaz **virtuální síť** .  
4. Vytvořte nový odkaz pro přidání virtuální sítě virtuálního počítače do privátní zóny DNS. Může to trvat několik minut, než se odkaz na zónu DNS stane dostupným.  
5. V Azure Portal přejděte do skupiny prostředků, která obsahuje virtuální síť vašeho clusteru.  
6. V pravém podokně vyberte virtuální síť. Název virtuální sítě je ve tvaru *AKS-VNet- \**.  
7. V levém podokně vyberte **partnerské vztahy**.  
8. Vyberte **Přidat**, přidejte virtuální síť virtuálního počítače a vytvořte partnerský vztah.  
9. Do virtuální sítě, ve které máte virtuální počítač, vyberte **partnerské vztahy**, vyberte virtuální síť AKS a vytvořte partnerský vztah. Pokud se rozsahy adres ve virtuální síti AKS a v konfliktu virtuální sítě virtuálního počítače, partnerský vztah se nezdařil. Další informace najdete v tématu  [partnerský vztah virtuálních sítí][virtual-network-peering].

## <a name="hub-and-spoke-with-custom-dns"></a>Střed a Paprskový s vlastním DNS

[Architektury hub a paprsků](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) se běžně používají k nasazení sítí v Azure. V mnoha těchto nasazeních jsou nastavení DNS v paprskovém virtuální sítě nakonfigurovaná tak, aby odkazovala na centrální službu DNS pro překládání, aby bylo možné místní a řešení DNS založené na Azure. Při nasazování clusteru AKS do takového síťového prostředí se musí vzít v úvahu několik zvláštních hledisek, které je potřeba vzít v úvahu.

![Centrum privátních clusterů a paprskový](media/private-clusters/aks-private-hub-spoke.png)

1. Ve výchozím nastavení platí, že při zřizování privátního clusteru se ve skupině prostředků spravovaného clusterem vytvoří privátní koncový bod (1) a privátní zóna DNS (2). Cluster používá záznam A v privátní zóně k překladu IP adresy privátního koncového bodu pro komunikaci se serverem rozhraní API.

2. Privátní zóna DNS je propojená jenom s virtuální sítí, ke které jsou připojené uzly clusteru (3). To znamená, že privátní koncový bod může být vyřešen pouze hostiteli v propojené virtuální síti. V případech, kdy ve virtuální síti není nakonfigurované žádné vlastní DNS (výchozí nastavení), to funguje bez problémů jako hostitelé – bod na 168.63.129.16 pro DNS, který dokáže vyřešit záznamy v privátní zóně DNS z důvodu propojení.

3. Ve scénářích, kdy virtuální síť, která obsahuje váš cluster, má vlastní nastavení DNS (4), nasazení clusteru se nepovede, pokud privátní zóna DNS není propojená s virtuální sítí, která obsahuje vlastní překladače DNS (5). Tento odkaz se dá vytvořit ručně, až se privátní zóna vytvoří během zřizování clusteru nebo přes automatizaci při detekci vytváření zóny pomocí mechanismů nasazení založeného na událostech (například Azure Event Grid a Azure Functions).

> [!NOTE]
> Pokud používáte [Vlastní směrovací tabulku s kubenet](./configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet) a přineste si vlastní DNS s privátním clusterem, vytvoření clusteru se nezdaří. Aby bylo možné vytvořit úspěšné vytvoření clusteru, bude nutné přidružit [cestu](./configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet) v rámci skupiny prostředků uzlu k podsíti v případě, že se vytvoření clusteru nepovedlo.

## <a name="limitations"></a>Omezení 
* Pro koncový bod privátního serveru API se nedají použít rozsahy autorizovaných IP adres, které se vztahují jenom na veřejný server API.
* [Omezení služby privátního propojení Azure][private-link-service] se vztahují na soukromé clustery.
* Pro Azure DevOps agenty hostované Microsoftem s privátními clustery není podporována podpora. Zvažte použití [agentů pro samoobslužné hostování](/azure/devops/pipelines/agents/agents?tabs=browser). 
* Pro zákazníky, kteří potřebují povolit Azure Container Registry pro práci s privátními AKS, musí mít Container Registry virtuální síť partnerský vztah s virtuální sítí clusteru agenta.
* Žádná podpora pro převod stávajících clusterů AKS na privátní clustery
* Odstraněním nebo úpravou privátního koncového bodu v podsíti zákazníka dojde k zastavení fungování clusteru. 
* Jakmile si zákazníci aktualizují záznam A na svých vlastních serverech DNS, budou tyto lusky i po migraci dál překládat apiserver plně kvalifikovaný název domény na starší IP adresu, dokud se nerestartují. Zákazníci musí po migraci řídicí plochy restartovat hostNetwork lusky a výchozí-DNSPolicy lusky.
* V případě údržby na rovině ovládacího prvku se může vaše [IP adresa AKS](./limit-egress-traffic.md) změnit. V takovém případě musíte aktualizovat záznam A odkazující na server rozhraní API privátní IP adresu na svém vlastním serveru DNS a restartovat jakékoli vlastní lusky nebo nasazení pomocí hostNetwork.

<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: ../private-link/private-link-service-overview.md#limitations
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/tutorial-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md
[devops-agents]: /azure/devops/pipelines/agents/agents
[availability-zones]: availability-zones.md
