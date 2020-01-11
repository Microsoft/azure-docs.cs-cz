---
title: Vytvoření privátního clusteru služby Azure Kubernetes
description: Zjistěte, jak vytvořit privátní cluster služby Azure Kubernetes Service (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 12/10/2019
ms.author: mlearned
ms.openlocfilehash: d7b1d82f88afd8ac3d94cbdd2d117834c12d0b96
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2020
ms.locfileid: "75867155"
---
# <a name="create-a-private-azure-kubernetes-service-cluster-preview"></a>Vytvoření privátního clusteru služby Azure Kubernetes (Preview)

V privátním clusteru má Řídicí rovina nebo Server rozhraní API interní IP adresy, které jsou definované v dokumentu [RFC1918-Address Allocation for Private Internets](https://tools.ietf.org/html/rfc1918) . Pomocí privátního clusteru můžete zajistit, aby síťový provoz mezi vaším serverem API a fondy uzlů zůstaly jenom v privátní síti.

Rovina ovládacího prvku nebo Server API se nachází v předplatném Azure spravovaném službou Azure Kubernetes (AKS). Cluster nebo fond uzlů zákazníka je v předplatném zákazníka. Server a cluster nebo fond uzlů můžou vzájemně komunikovat prostřednictvím [služby privátního propojení Azure][private-link-service] ve virtuální síti serveru API a privátního koncového bodu, který je vystavený v PODSÍTI clusteru AKS zákazníka.

> [!IMPORTANT]
> Funkce AKS ve verzi Preview jsou samoobslužné a jsou nabízeny na základě výslovného souhlasu. Verze Preview jsou *k dispozici* *tak, jak* jsou, a jsou vyloučené z smlouvy o úrovni služeb (SLA) a omezené záruky. AKS verze Preview jsou částečně pokryté zákaznickou podporou, a to s ohledem na *nejlepší úsilí* . Proto funkce nejsou určeny pro použití v produkčním prostředí. Další informace najdete v následujících článcích podpory:
>
> * [Zásady podpory AKS](support-policies.md)
> * [Nejčastější dotazy k podpoře Azure](faq.md)

## <a name="prerequisites"></a>Požadavky

* Azure CLI verze 2.0.77 nebo novější a verze rozšíření Azure CLI AKS ve verzi Preview 0.4.18

## <a name="currently-supported-regions"></a>Aktuálně podporované oblasti
* Západní USA
* Západní USA 2
* Východ USA 2
* Střední Kanada
* Severní Evropa
* Západní Evropa
* Austrálie – východ

## <a name="install-the-latest-azure-cli-aks-preview-extension"></a>Nainstalovat nejnovější rozšíření Azure CLI AKS Preview

Pokud chcete používat privátní clustery, potřebujete rozšíření Azure CLI AKS Preview verze 0.4.18 nebo novější. Nainstalujte rozšíření Azure CLI AKS Preview pomocí příkazu [AZ Extension Add][az-extension-add] a potom zkontrolujte, jestli jsou dostupné aktualizace, pomocí následujícího příkazu [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```
> [!CAUTION]
> Když zaregistrujete funkci v rámci předplatného, nemůžete tuto funkci v tuto chvíli zrušit. Po povolení některých funkcí ve verzi Preview můžete použít výchozí nastavení pro všechny clustery AKS vytvořené v rámci předplatného. Nepovolujte funkce ve verzi Preview u produkčních předplatných. Použijte samostatné předplatné k testování funkcí ve verzi Preview a získejte zpětnou vazbu.

```azurecli-interactive
az feature register --name AKSPrivateLinkPreview --namespace Microsoft.ContainerService
```

Může trvat několik minut, než se stav registrace zobrazí jako *zaregistrované*. Stav můžete zjistit pomocí následujícího příkazu [AZ Feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSPrivateLinkPreview')].{Name:name,State:properties.state}"
```

Když je stav zaregistrován, aktualizujte registraci poskytovatele prostředků *Microsoft. ContainerService* pomocí následujícího příkazu [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
az provider register --namespace Microsoft.Network
```
## <a name="create-a-private-aks-cluster"></a>Vytvoření privátního clusteru AKS

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

## <a name="connect-to-the-private-cluster"></a>Připojit k privátnímu clusteru
Koncový bod serveru rozhraní API nemá žádnou veřejnou IP adresu. V důsledku toho musíte ve virtuální síti vytvořit virtuální počítač Azure a připojit se k serveru rozhraní API. Uděláte to takto:

1. Získejte přihlašovací údaje pro připojení ke clusteru.

   ```azurecli-interactive
   az aks get-credentials --name MyManagedCluster --resource-group MyResourceGroup
   ```

1. Proveďte jednu z následujících akcí:
   * Vytvořte virtuální počítač ve stejné virtuální síti jako cluster AKS.  
   * Vytvořte virtuální počítač v jiné virtuální síti a navázat tuto virtuální síť pomocí virtuální sítě clusteru AKS.

     Pokud vytvoříte virtuální počítač v jiné virtuální síti, nastavte propojení mezi touto virtuální sítí a privátní zónou DNS. Postupujte následovně:
    
     a. V Azure Portal otevřete skupinu prostředků MC_ *.  
     b. Vyberte privátní zónu DNS.   
     c. V levém podokně vyberte odkaz **virtuální síť** .  
     d. Vytvořte nový odkaz pro přidání virtuální sítě virtuálního počítače do privátní zóny DNS. Může to trvat několik minut, než se odkaz na zónu DNS stane dostupným.  
     e. Vraťte se do skupiny prostředků MC_ * v Azure Portal.  
     f. V pravém podokně vyberte virtuální síť. Název virtuální sítě je ve formátu *AKS-VNet-\** .  
     g. V levém podokně vyberte **partnerské vztahy**.  
     h. Vyberte **Přidat**, přidejte virtuální síť virtuálního počítače a vytvořte partnerský vztah.  
     i. Do virtuální sítě, ve které máte virtuální počítač, vyberte **partnerské vztahy**, vyberte virtuální síť AKS a vytvořte partnerský vztah. Pokud se rozsahy adres ve virtuální síti AKS a v konfliktu virtuální sítě virtuálního počítače, partnerský vztah se nezdařil. Další informace najdete v tématu [partnerský vztah virtuálních sítí][virtual-network-peering].

1. Přístup k virtuálnímu počítači přes Secure Shell (SSH).
1. Nainstalujte nástroj Kubectl a spusťte příkazy Kubectl.


## <a name="dependencies"></a>Závislosti  
* Služba privátního propojení je podporována pouze u standardních Azure Load Balancer. Základní Azure Load Balancer nejsou podporované.  

## <a name="limitations"></a>Omezení 
* [Omezení služby pro privátní propojení Azure][private-link-service] platí pro privátní clustery, privátní koncové body Azure a koncové body služby virtuální sítě, které se v tuto chvíli nepodporují ve stejné virtuální síti.
* Neexistují žádné podpory virtuálních uzlů v privátním clusteru k vystavování privátních instancí infrastruktury ACI (Application orientované infrastruktura) v privátní virtuální síti Azure.
* Žádná podpora pro integraci Azure DevOps není integrovaná s privátními clustery.
* Pro zákazníky, kteří potřebují povolit Azure Container Registry pro práci s privátními AKS, musí mít Container Registry virtuální síť partnerský vztah s virtuální sítí clusteru agenta.
* Žádná aktuální podpora pro Azure Dev Spaces.
* Žádná podpora pro převod stávajících clusterů AKS na privátní clustery.  
* Odstraněním nebo úpravou privátního koncového bodu v podsíti zákazníka dojde k zastavení fungování clusteru. 
* Služba Azure Monitor for Containers Live data není v současné době podporovaná.
* Využití *vlastního DNS* není momentálně podporované.


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: https://docs.microsoft.com/azure/private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md

