---
title: Vytvoření privátního clusteru služby Azure Kubernetes
description: Zjistěte, jak vytvořit privátní cluster služby Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 2/21/2020
ms.openlocfilehash: b8b4f8062d9f60648e22ab4eb0be78eb47159834
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79205171"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>Vytvoření privátního clusteru služby Azure Kubernetes

V privátním clusteru má Řídicí rovina nebo Server rozhraní API interní IP adresy, které jsou definované v dokumentu [RFC1918-Address Allocation for Private Internets](https://tools.ietf.org/html/rfc1918) . Pomocí privátního clusteru můžete zajistit, aby síťový provoz mezi vaším serverem API a fondy uzlů zůstaly jenom v privátní síti.

Rovina ovládacího prvku nebo Server API se nachází v předplatném Azure spravovaném službou Azure Kubernetes (AKS). Cluster nebo fond uzlů zákazníka je v předplatném zákazníka. Server a cluster nebo fond uzlů můžou vzájemně komunikovat prostřednictvím [služby privátního propojení Azure][private-link-service] ve virtuální síti serveru API a privátního koncového bodu, který je vystavený v PODSÍTI clusteru AKS zákazníka.

## <a name="prerequisites"></a>Požadavky

* Azure CLI verze 2.2.0 nebo novější

## <a name="create-a-private-aks-cluster"></a>Vytvoření privátního clusteru AKS

### <a name="create-a-resource-group"></a>Vytvořit skupinu prostředků

Vytvořte skupinu prostředků nebo pro svůj cluster AKS použijte existující skupinu prostředků.

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>Výchozí základní sítě 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Kde *--Enable-Private-cluster* je povinný příznak pro privátní cluster. 

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
Kde *--Enable-Private-cluster* je povinný příznak pro privátní cluster. 

> [!NOTE]
> Pokud adresa mostu Docker (172.17.0.1/16) koliduje s podsítí CIDR, změňte adresu mostu Docker odpovídajícím způsobem.

## <a name="options-for-connecting-to-the-private-cluster"></a>Možnosti připojení k privátnímu clusteru

Koncový bod serveru rozhraní API nemá žádnou veřejnou IP adresu. Pokud chcete spravovat Server API, budete muset použít virtuální počítač, který má přístup k Azure Virtual Network AKS (VNet) clusteru. K dispozici je několik možností pro vytvoření síťového připojení k privátnímu clusteru.

* Vytvořte virtuální počítač ve stejné službě Azure Virtual Network (VNet) jako cluster AKS.
* Použijte virtuální počítač v samostatné síti a nastavte [partnerský vztah virtuálních sítí][virtual-network-peering].  Další informace o této možnosti najdete v části níže.
* Použijte [expresní trasu nebo připojení k síti VPN][express-route-or-VPN] .

Nejjednodušší možností je vytvoření virtuálního počítače ve stejné virtuální síti jako cluster AKS.  Expresní směrování a sítě VPN přidávají náklady a vyžadují další složitost sítě.  Partnerský vztah virtuálních sítí vyžaduje, abyste naplánovali rozsahy směrování sítě, aby se zajistilo, že se překrývají rozsahy.

## <a name="virtual-network-peering"></a>Partnerské vztahy virtuálních sítí

Jak už bylo zmíněno, partnerský vztah virtuálních sítí je jedním ze způsobů, jak získat přístup k privátnímu clusteru. Pokud chcete použít partnerský vztah virtuálních sítí, musíte nastavit propojení mezi virtuální sítí a privátní zónou DNS.
    
1. V Azure Portal otevřete skupinu prostředků MC_ *.  
2. Vyberte privátní zónu DNS.   
3. V levém podokně vyberte odkaz **virtuální síť** .  
4. Vytvořte nový odkaz pro přidání virtuální sítě virtuálního počítače do privátní zóny DNS. Může to trvat několik minut, než se odkaz na zónu DNS stane dostupným.  
5. Vraťte se do skupiny prostředků MC_ * v Azure Portal.  
6. V pravém podokně vyberte virtuální síť. Název virtuální sítě je ve formátu *AKS-VNet-\** .  
7. V levém podokně vyberte **partnerské vztahy**.  
8. Vyberte **Přidat**, přidejte virtuální síť virtuálního počítače a vytvořte partnerský vztah.  
9. Do virtuální sítě, ve které máte virtuální počítač, vyberte **partnerské vztahy**, vyberte virtuální síť AKS a vytvořte partnerský vztah. Pokud se rozsahy adres ve virtuální síti AKS a v konfliktu virtuální sítě virtuálního počítače, partnerský vztah se nezdařil. Další informace najdete v tématu [partnerský vztah virtuálních sítí][virtual-network-peering].

## <a name="dependencies"></a>Závislosti  

* Služba privátního propojení je podporována pouze u standardních Azure Load Balancer. Základní Azure Load Balancer nejsou podporované.  
* Pokud chcete použít vlastní server DNS, nasaďte server služby Active Directory se službou DNS pro přeposílání na tento protokol IP 168.63.129.16

## <a name="limitations"></a>Omezení 
* Pro koncový bod privátního serveru API se nedají použít rozsahy povolených IP adres, které se vztahují jenom na veřejný server API.
* Zóny dostupnosti jsou v současné době podporovány v některých oblastech, další informace najdete na začátku tohoto dokumentu. 
* [Omezení služby pro privátní propojení Azure][private-link-service] platí pro privátní clustery, privátní koncové body Azure a koncové body služby virtuální sítě, které se v tuto chvíli nepodporují ve stejné virtuální síti.
* Není podporovaná podpora virtuálních uzlů v privátním clusteru pro dálku privátního Azure Container Instances (ACI) v privátní virtuální síti Azure.
* Žádná podpora pro integraci Azure DevOps není v krabici s privátními clustery
* Pro zákazníky, kteří potřebují povolit Azure Container Registry pro práci s privátními AKS, musí mít Container Registry virtuální síť partnerský vztah s virtuální sítí clusteru agenta.
* Žádná aktuální podpora pro Azure Dev Spaces
* Žádná podpora pro převod stávajících clusterů AKS na privátní clustery
* Odstraněním nebo úpravou privátního koncového bodu v podsíti zákazníka dojde k zastavení fungování clusteru. 
* Služba Azure Monitor for Containers Live data není v současné době podporovaná.


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: /private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md

