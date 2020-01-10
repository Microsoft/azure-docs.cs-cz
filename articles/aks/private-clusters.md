---
title: Privátní cluster služby Azure Kubernetes
description: Zjistěte, jak vytvořit privátní cluster služby Azure Kubernetes Service (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 12/10/2019
ms.author: mlearned
ms.openlocfilehash: 6152becb8debd0700ddab6190284514c6d6cf69d
ms.sourcegitcommit: 8b37091efe8c575467e56ece4d3f805ea2707a64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75830050"
---
# <a name="public-preview---private-azure-kubernetes-service-cluster"></a>Public Preview – privátní cluster služby Azure Kubernetes

V privátním clusteru budou mít Řídicí rovina/Server rozhraní API interní IP adresy definované v [RFC1918](https://tools.ietf.org/html/rfc1918).  Pomocí privátního clusteru můžete zajistit, aby síťový provoz mezi serverem API a fondy uzlů zůstaly jenom v privátní síti.

Komunikace mezi řídicí rovinou/serverem rozhraní API, která je v rámci předplatného Azure spravovaného AKS, a fond clusterů/uzlů, který je v rámci zákaznického předplatného, může vzájemně komunikovat prostřednictvím [služby privátního propojení][private-link-service] ve virtuální síti serveru API a privátního koncového bodu vystaveného v PODSÍTI clusteru AKS zákazníka.

> [!IMPORTANT]
> Funkce služby AKS ve verzi Preview jsou samoobslužné přihlašovací. Verze Preview jsou k dispozici "tak jak jsou" a "jako dostupné" a jsou vyloučeny ze smluv o úrovni služeb a omezené záruky. AKS verze Preview jsou částečně pokryté zákaznickou podporou na základě nejlepšího úsilí. V takovém případě tyto funkce nejsou určeny pro použití v produkčním prostředí. Další informace o tom, jak se zaregistrují, najdete v následujících článcích podpory:
>
> * [Zásady podpory AKS](support-policies.md)
> * [Nejčastější dotazy k podpoře Azure](faq.md)

## <a name="before-you-begin"></a>Než začnete

* Potřebujete Azure CLI verze 2.0.77 nebo novější a rozšíření 0.4.18 AKS-Preview.

## <a name="current-supported-regions"></a>Aktuální podporované oblasti
* Západní USA
* Západní USA 2
* Východ USA 2
* Střední Kanada
* Severní Evropa
* Západní Evropa
* Austrálie – východ

## <a name="install-latest-aks-cli-preview-extension"></a>Nainstalovat nejnovější rozšíření AKS CLI Preview

Pokud chcete používat privátní clustery, potřebujete rozšíření *AKS-Preview* CLI verze 0.4.18 nebo vyšší. Nainstalujte rozšíření Azure CLI *AKS-Preview* pomocí příkazu [AZ Extension Add][az-extension-add] a potom zkontrolujte všechny dostupné aktualizace pomocí příkazu [AZ Extension Update][az-extension-update] ::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```
> [!CAUTION]
> Když zaregistrujete funkci v rámci předplatného, nemůžete tuto funkci v tuto chvíli zrušit. Po povolení některých funkcí verze Preview se můžou použít výchozí hodnoty pro všechny clustery AKS vytvořené v rámci předplatného. Nepovolujte funkce ve verzi Preview u produkčních předplatných. Použijte samostatné předplatné k testování funkcí ve verzi Preview a získejte zpětnou vazbu.

```azurecli-interactive
az feature register --name AKSPrivateLinkPreview --namespace Microsoft.ContainerService
```

Zobrazení stavu *registrace*může trvat několik minut. Stav registrace můžete zjistit pomocí příkazu [AZ Feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSPrivateLinkPreview')].{Name:name,State:properties.state}"
```

Když je stav zaregistrován, aktualizujte registraci poskytovatele prostředků *Microsoft. ContainerService* pomocí příkazu [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
az provider register --namespace Microsoft.Network
```
## <a name="create-a-private-aks-cluster"></a>Vytvoření privátního clusteru AKS

#### <a name="default-basic-networking"></a>Výchozí základní sítě 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Kde--Enable-Private-cluster je povinný příznak pro privátní cluster 

#### <a name="advanced-networking"></a>Pokročilé sítě  

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
Kde--Enable-Private-cluster je povinný příznak pro privátní cluster 

## <a name="steps-to-connect-to-the-private-cluster"></a>Postup připojení k privátnímu clusteru
Koncový bod serveru API nemá žádnou veřejnou IP adresu. V důsledku toho budou muset uživatelé vytvořit virtuální počítač Azure ve virtuální síti a připojit se k serveru rozhraní API. Kroky v

* Získání přihlašovacích údajů pro připojení ke clusteru

   ```azurecli-interactive
   az aks get-credentials --name MyManagedCluster --resource-group MyResourceGroup
   ```
* Vytvořte virtuální počítač ve stejné virtuální síti, jako je cluster AKS, nebo vytvořte virtuální počítač v jiné virtuální síti a jako partnerský virtuální síť s Clusterovou sítí AKS.
* Pokud vytvoříte virtuální počítač v jiné virtuální síti, budete muset nastavit propojení mezi touto virtuální sítí a zónou Privátní DNS.
    * Přejít na skupinu prostředků MC_ * na portálu 
    * klikněte na zónu Privátní DNS. 
    * v levém podokně vyberte odkaz virtuální síť.
    * Vytvořte nový odkaz pro přidání virtuální sítě virtuálního počítače do zóny Privátní DNS *(pro zpřístupnění odkazu zóny DNS trvá několik minut)* .
    * Přejít zpět na skupinu prostředků MC_ * na portálu
    * v pravém podokně vyberte virtuální síť. Název virtuální sítě bude ve formátu AKS-VNET-*.
    * Výběr partnerských vztahů v levém podokně
    * klikněte na Přidat a přidejte virtuální síť virtuálního počítače a vytvořte partnerský vztah.
    * Přejděte do virtuální sítě, kde máte virtuální počítač, a potom klikněte na partnerské vztahy a vyberte virtuální síť AKS a vytvořte partnerský vztah. Pokud se rozsahy adres ve virtuální síti AKS a virtuální síti virtuálního počítače neprojeví, partnerský vztah selže. Další informace o partnerském vztahu virtuálních sítí najdete v tomto [dokumentu][virtual-network-peering] .
* Přihlaste se k virtuálnímu počítači přes SSH
* Instalace nástroje Kubectl a spuštění příkazů Kubectl

## <a name="dependencies"></a>Závislosti  
* Pouze standardně, bez podpory pro základní nástroj pro vyrovnávání zatížení  

## <a name="limitations"></a>Omezení 
* Stejná [omezení služby privátního propojení Azure][private-link-service] platí pro privátní clustery, koncové body privátních koncových bodů a Virtual Network služby Azure se v současné době nepodporují ve stejné virtuální síti.
* Žádná podpora virtuálních uzlů v privátním clusteru pro ACIí privátních instancí v privátní virtuální síti Azure
* Žádná podpora pro integraci Azure DevOps není v krabici s privátními clustery
* Pokud zákazníci potřebují, aby ACR spolupracovali s privátními AKS, musí být virtuální síť ACR s partnerským vztahem k virtuální síti clusteru agenta.
* Žádná aktuální podpora pro Azure Dev Spaces
* Žádná podpora pro převod stávajících clusterů AKS na soukromé clustery  
* Odstranění nebo úprava privátního koncového bodu v podsíti zákazníka způsobí, že cluster přestane fungovat. 
* Služba Azure Monitor for Containers Live data není v současné době podporovaná.
* Používání vlastního DNS není momentálně podporované.


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: https://docs.microsoft.com/azure/private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md

